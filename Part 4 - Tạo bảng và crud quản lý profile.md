Part 4 - Tạo bảng và crud quản lý profile
Tạo bảng profile của admin, ở đây quản lý toàn bộ profile của người dùng
Tên bảng user_profiles
```
php artisan make:migration create_user_profiles_table
```
Mở file migration mới tạo ra trong database/migrations và tiến hành cấu hình trường:
```
...
    public function up(): void
    {
        Schema::create('user_profiles', function (Blueprint $table) {
            $table->id(); // ID tự tăng
            $table->string('username')->unique(); // Tên tài khoản
            $table->string('password'); // Mật khẩu
            $table->string('cover_image')->nullable(); // Ảnh bìa
            $table->string('avatar')->nullable(); // Avatar
            $table->text('description')->nullable(); // Mô tả
            $table->timestamps(); // Thời gian tạo và cập nhật
        });
    }
...
```
Chạy migration để tạo bảng:
``` 
php artisan migrate
```
Tạo model UserProfile
```
php artisan make:model UserProfile
```
Mở file Model: ``` App\Models\UserProfile.php ```
```
...
use Illuminate\Database\Eloquent\Factories\HasFactory;

class UserProfile extends Model
{
    use HasFactory;
    protected $table = 'user_profiles';

    protected $fillable = [
        'username',
        'password',
        'cover_image',
        'avatar',
        'description',
    ];

    public function setPasswordAttribute($value)
    {
        $this->attributes['password'] = bcrypt($value);
    }
}
...
```
Tạo Resource cho Model UserProfile
```
php artisan make:filament-resource UserProfile --generate
```
Tại đây chúng ta chỉ quản lý xem thông tin của người dùng, không được phép chỉnh sửa vào hồ sơ của người dùng, vi vậy sẽ khóa các chứ năng thêm xóa sửa đi và chỉ được xem
Sử dụng Shield để chỉnh sửa lại quyền super_admin không thao tác với dữ liệu này, sau đó chạy lệnh để tạo quyền cho Resource UserProfileResource
```
php artisan shield:generate --resource=UserProfileResource
```
