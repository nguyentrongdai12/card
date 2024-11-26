Part 2: Tạo view cơ bản
1. Tạo controller để xử lý về sau
```
php artisan make:controller CardController
```
  Tạo hàm xử lý trang home: Hàm này sẽ gửi ra view home để làm trang chủ
```
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class CardController extends Controller
{
    public function home()
    {
        return view('home');
    }
}
```
2. Tạo route cho trang chủ: Routes\web.php
```
<?php

use Illuminate\Support\Facades\Route;
use App\Http\Controllers\CardController;

Route::get('/', CardController::home)->name('home');
```
3. Tạo view trang chủ: resource\views\home.blade.php
```
Nội dung lấy từ template demo pricing của BS5 (Bootstrap 5)
https://getbootstrap.com/docs/5.0/examples/pricing/
```
4. Đưa CSS vào laravel
- Tải Bootstrap về tại: https://getbootstrap.com/docs/5.3/getting-started/download/
- Giải nén, đưa 2 thư mục css và js vừa giải nén vào thư mục public của laravel
- Khai báo CSS tại thẻ head của view trang chủ: resource\views\home.blade.php
```
<link rel="stylesheet" href="{{ asset('css/bootstrap.min.css') }}">
<link rel="stylesheet" href="{{ asset('css/pricing.css') }}">
```
- Tạo liên kết tượng trưng cho storage của laravel
```
php artisan storage:link
```
Hoàn tất trang chủ demo
