Part 6 - Tạo 1 trang hiển thị profile cơ bản
1. Tạo controller
  Tạo 1 controller mới có tên UserProfileController để thực hiện các công việc liên quan đến Profile của user
```
php artisan make:controller UserProfileController
```
Mở file ```app/Http/Controllers/UserProfileController.php```
Tạo 1 chức năng hiển thị view ViewProfile, tại đây chúng ta lấy thông tin từ bảng UserProfile lên để hiển thị (hiển thị demo, chức năng theo từng tài khoản sẽ cập nhật tiếp về sau)
```
...
use App\Models\UserProfile;

class UserProfileController extends Controller
{
...
    public function viewprofile()
    {
        $user_profiles = UserProfile::where('email', 'nguyentrongdai12@gmail.com')->get();
        return view('viewprofile', compact(['user_profiles']));
    }
...
}
...
```
3. Tạo route
  Mở file ```routes/web.php``` và thêm 1 route
```
<?php

use Illuminate\Support\Facades\Route;
use App\Http\Controllers\CardController;
use App\Http\Controllers\UserProfileController;

Route::get('/', [CardController::class, 'home'])->name('home');
Route::get('/profile', [UserProfileController::class, 'viewprofile'])->name('viewprofile');
```  
5. Tạo View
   Tạo 1 file mới tại ```resources/views/viewprofile.blade.php```
   Tại đây chúng ta tạo 1 trang cơ bản như sau:
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.7.0/css/font-awesome.min.css">
</head>
<style>
    .card {
    box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2);
    max-width: 300px;
    margin: auto;
    text-align: center;
    }

    .title {
    color: grey;
    font-size: 18px;
    }

    button {
    border: none;
    outline: 0;
    display: inline-block;
    padding: 8px;
    color: white;
    background-color: #000;
    text-align: center;
    cursor: pointer;
    width: 100%;
    font-size: 18px;
    }

    a {
    text-decoration: none;
    font-size: 22px;
    color: black;
    }

    button:hover, a:hover {
    opacity: 0.7;
    }
</style>
<body>
    <div class="card">
    <img src="img.jpg" alt="John" style="width:100%">
    <h1>John Doe</h1>
    <p class="title">CEO & Founder, Example</p>
    <p>Harvard University</p>
    <a href="#"><i class="fa fa-dribbble"></i></a>
    <a href="#"><i class="fa fa-twitter"></i></a>
    <a href="#"><i class="fa fa-linkedin"></i></a>
    <a href="#"><i class="fa fa-facebook"></i></a>
    <p><button>Contact</button></p>
    </div>
</body>
</html>
```  
7. Hiển thị dữ liệu lên view
