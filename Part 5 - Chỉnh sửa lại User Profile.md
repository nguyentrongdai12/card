Part 5 - Chỉnh sửa lại User Profile
Tại part 4 bảng User Profile còn thiếu 1 số thông tin, cập nhật thêm: họ và tên, địa chỉ email cho bảng
Tạo 1 migration mới 
```
php artisan make:migration add_first_name_and_email_to_user_profiles
```
Mở file migration vừa được tạo trong ```database/migrations``` để chỉnh sửa
```
...
public function up()
    {
        Schema::table('user_profiles', function (Blueprint $table) {
            $table->string('full_name')->after('id'); // Thêm trường họ và tên sau cột id
            $table->string('email')->unique()->after('full_name'); // Thêm trường email và đặt unique
        });
    }
...
public function down()
    {
        Schema::table('user_profiles', function (Blueprint $table) {
            $table->dropColumn(['full_name', 'email']); // Xóa các cột đã thêm
        });
    }
...
```
Cập nhật bảng
```
php artisan migrate
```
Mở UserProfileResource để thêm các tùy chỉnh mới cho 2 trường mới: ```app/Filament/Resources/UserProfileResource.php```
```
...
public static function form(Form $form): Form
    {
        return $form
            ->schema([
                Forms\Components\TextInput::make('username')->label('Tên đăng nhập')
                    ->required()
                    ->maxLength(255),
                Forms\Components\TextInput::make('full_name')->label('Họ và tên')
                    ->required()
                    ->maxLength(255),
                Forms\Components\TextInput::make('email')->label('Email')
                    ->required()
                    ->email()
                    ->maxLength(255),
                Forms\Components\TextInput::make('password')->label('Mật khẩu')
                    ->password()
                    ->required()
                    ->maxLength(255),
                Forms\Components\FileUpload::make('cover_image')->label('Ảnh bìa')
                    ->image(),
                Forms\Components\TextInput::make('avatar')->label('Ảnh đại diện')
                    ->maxLength(255)
                    ->default(null),
                Forms\Components\Textarea::make('description')->label('Mô tả')
                    ->columnSpanFull(),
            ]);
    }
...
public static function table(Table $table): Table
    {
        return $table
            ->columns([
                Tables\Columns\TextColumn::make('username')->label('Tên đăng nhập')
                    ->searchable(),
                Tables\Columns\TextColumn::make('full_name')->label('Họ và tên')
                    ->searchable(),
                Tables\Columns\TextColumn::make('email')->label('Email')
                    ->searchable(),
                Tables\Columns\ImageColumn::make('cover_image')->label('Ảnh bìa'),
                Tables\Columns\TextColumn::make('avatar')->label('Ảnh đại diện')
                    ->searchable(),
            ])
            ->filters([
                //
            ])
            ->actions([
                Tables\Actions\EditAction::make(),
            ])
            ->bulkActions([
                Tables\Actions\BulkActionGroup::make([
                    Tables\Actions\DeleteBulkAction::make(),
                ]),
            ]);
    }
```
Điều chỉnh Model: ```app/Models/UserProfile.php```
```
...
    protected $fillable = [
        'username',
        'full_name',
        'email',
        'password',
        'cover_image',
        'avatar',
        'description',
    ];
...
```
