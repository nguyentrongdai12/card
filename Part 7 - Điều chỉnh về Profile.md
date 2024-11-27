Part 7 - Điều chỉnh về Profile
Chúng ta cần thêm 1 trường là slug được diễn giải từ tên của người dùng, ví dụ: Nguyễn Văn A -> nguyenvana
Sử dụng slug này làm link profile riêng cho từng cá nhân
Bài toán: nếu có người dùng trùng tên với nhau thì sẽ thêm số thứ tự vào sau tên: nguyen-van-a01, nguyen-van-a02 .v.v.
- Tạo migration cập nhật thêm trường slug cho bảng UserProfile
```
php artisan make:migration add_slug_to_user_profiles
```
- Mở file vừa tạo trong ```database/migrations```
```
...
public function up(): void
    {
        Schema::table('user_profiles', function (Blueprint $table) {
            $table->string('slug')->unique()->nullable()->after('full_name'); // Thêm cột slug và đảm bảo duy nhất
        });
    }
...
public function down(): void
    {
        Schema::table('user_profiles', function (Blueprint $table) {
            $table->dropColumn('slug'); // Xóa cột slug nếu rollback migration
        });
    }
...
```
- Chạy migration cập nhật bảng
```
php artisan migrate
```
- Tạo slug trong model: ```app/Models/UserProfile.php```
```
...
protected $fillable = [
        'username',
        'full_name',
        'slug',
        'email',
        'password',
        'cover_image',
        'avatar',
        'description',
    ];
...
```
- Cập nhật lại ```app/Filament/Resources/UserProfileResource.php```
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
                    ->live(onBlur: true)
                    ->afterStateUpdated(fn (Set $set, ?string $state) => $set('slug', Str::slug($state)))
                    ->maxLength(255),
                Forms\Components\TextInput::make('slug')->unique(ignoreRecord: true)
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
```
- Điều chỉnh lại route ```routes/web.php```
```
...
Route::get('/{slug}', [UserProfileController::class, 'viewprofile'])->name('viewprofile');
...
```
- Điều chỉnh lại controller ```app/Http/Controllers/UserProfileController.php```
```
...
class UserProfileController extends Controller
{
    public function viewprofile($slug)
    {
        $user_profiles = UserProfile::where('slug', $slug)->first();
        // Nếu không tìm thấy profile, có thể trả về lỗi hoặc trang không tìm thấy
        if (!$user_profiles) {
            abort(404, 'User not found');
        }

        // Trả về view với profile của người dùng
        return view('viewprofile', compact(['user_profiles']));
    }
}
...
```
- Như vậy khi truy cập ```tenmien/slug``` sẽ hiển thị profile với view tương ứng và dữ liệu, nếu không sẽ đưa ra trang lỗi 404
