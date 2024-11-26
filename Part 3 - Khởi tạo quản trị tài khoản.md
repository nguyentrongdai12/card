Part 3 - Khởi tạo quản trị tài khoản
1. Sử dụng filament shield để quản lý tài khoản và phân quyền theo các role: https://filamentphp.com/plugins/bezhansalleh-shield
- Cài đặt bezhansalleh-shield:
```
composer require bezhansalleh/filament-shield
```
- Xuất bản cấu hình và thiết lập mô hình:
```
php artisan vendor:publish --tag="filament-shield-config"

//config/filament-shield.php
...
    'auth_provider_model' => [
        'fqcn' => 'App\\Models\\User',
    ],
...

```
- Thêm HasRoles cho Model User: HasRoles
```
use Spatie\Permission\Traits\HasRoles;
 
class User extends Authenticatable
{
    /** @use HasFactory<\Database\Factories\UserFactory> */
    use HasRoles, HasFactory, Notifiable;

    /**
     * The attributes that are mass assignable.
     *
     * @var array<int, string>
     */
    protected $fillable = [
        'name',
        'email',
        'password',
    ];
...
}
```
- Thiết lập Shield:
```
php artisan shield:setup
```
- Cài đặt cho filament
```
php artisan shield:install admin
php artisan make:filament-resource User --generate
php artisan make:policy UserPolicy --model=User
php artisan shield:generate --resource=UserResource
```
- Đăng ký policy: \App\Providers\AuthServiceProvider
```
 protected $policies = [
        \App\Models\User::class => \App\Policies\UserPolicy::class,
    ];
```
