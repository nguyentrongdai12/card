Project Card
Part 1: KHởi tạo các môi trường ban đầu
1. Khởi tạo Laravel
```
laravel new card
```
2. Chỉnh sửa .env:
```
APP_NAME=Card
APP_ENV=local
APP_KEY=base64:D29r5Odxcr/3M2JtVCXU2Tb7zeoem+f+m7NBmtMtMa8=
APP_DEBUG=true
APP_TIMEZONE=Asia/Ho_Chi_Minh
APP_URL=https://card.cdb-tech.com

APP_LOCALE=vi
APP_FALLBACK_LOCALE=vi
APP_FAKER_LOCALE=vi_VN

APP_MAINTENANCE_DRIVER=file
# APP_MAINTENANCE_STORE=database

PHP_CLI_SERVER_WORKERS=4

BCRYPT_ROUNDS=12

LOG_CHANNEL=stack
LOG_STACK=single
LOG_DEPRECATIONS_CHANNEL=null
LOG_LEVEL=debug

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=carddata
DB_USERNAME=cardadmin
DB_PASSWORD=
```
3. Khởi tọa bảng ban đầu:
```
php artisan migrate
```

4. Cài đặt filament:
```
composer require filament/filament:"^3.2" -W
php artisan filament:install --panels
```

5. Tạo tài khoản admin
```
php artisan make:filament-user
Admin
admin@admin.localhost
mật khẩu
```

6. Cài đặt livewrire
```
composer require livewire/livewire
php artisan vendor:publish --force --tag=livewire:assets
```

7. Public config Filament
```
php artisan vendor:publish --tag=filament-config
```
8. Clear cache
```
php artisan filament:optimize
```

9. Tiến hành truy cập: card.cdb-tech.com/admin để đăng nhập, đăng nhập thành công !!!
10. Cấu hình cơ bản cho admin Filament: Đường dẫn: App\Providers\Filament\AdminPanelProvider
```
use Filament\Support\Enums\MaxWidth;

class AdminPanelProvider extends PanelProvider
{
    public function panel(Panel $panel): Panel
    {
        return $panel
            ->default()
            ->font('Roboto')
            ->id('admin')
            ->path('admin')
            ->login()
            ->profile()
            ->colors([
                'primary' => Color::Amber,
            ])
            ->discoverResources(in: app_path('Filament/Resources'), for: 'App\\Filament\\Resources')
            ->discoverPages(in: app_path('Filament/Pages'), for: 'App\\Filament\\Pages')
            ->pages([
                Pages\Dashboard::class,
            ])
            ->discoverWidgets(in: app_path('Filament/Widgets'), for: 'App\\Filament\\Widgets')
            ->widgets([
                Widgets\AccountWidget::class,
                Widgets\FilamentInfoWidget::class,
            ])
            ->middleware([
                EncryptCookies::class,
                AddQueuedCookiesToResponse::class,
                StartSession::class,
                AuthenticateSession::class,
                ShareErrorsFromSession::class,
                VerifyCsrfToken::class,
                SubstituteBindings::class,
                DisableBladeIconComponents::class,
                DispatchServingFilamentEvent::class,
            ])
            ->sidebarCollapsibleOnDesktop()
            ->breadcrumbs(false)
            ->maxContentWidth(MaxWidth::Full)
            ->authMiddleware([
                Authenticate::class,
            ]);
    }
}
```
