Part 8 - Chức năng đăng nhập cho người dùng
khá phức tạp vì liên quan đến 2 hệ thống đăng nhập, follow theo các file sau:
- File CardController ```app/Http/Controllers/CardController.php```
```
use Illuminate\Support\Facades\Auth;
public function home()
    {
    $loggedInUser = Auth::guard('user')->user();
    return view('home', compact('loggedInUser'));
    }
```
- File UserProfileController ```app/Http/Controllers/UserProfileController.php```
```
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

    public function loginsubmit(Request $request)
    {
        // Xác thực dữ liệu đăng nhập
        $credentials = $request->only('username', 'password');
                    
        // Kiểm tra thông tin tài khoản và mật khẩu
        if (Auth::guard('user')->attempt($credentials)) {
            // Nếu đăng nhập thành công, chuyển hướng người dùng đến trang chính
            $loggedInUser = Auth::guard('user')->user();
            return redirect()->route('home');
        }

        // Nếu không thành công, trả về lại trang đăng nhập với thông báo lỗi
        return redirect()->route('login')->withErrors([
            'username' => 'Tài khoản hoặc mật khẩu không chính xác.',
        ]);
        
    }

    public function userlogout()
    {
        Auth::guard('user')->logout();
        return redirect()->route('home');
        //return "Đăng xuất";
    }
```
- File UserProfile ```app/Models/UserProfile.php```
```
use Illuminate\Foundation\Auth\User as Authenticatable;
use Illuminate\Notifications\Notifiable;

use Notifiable;
protected $hidden = [
        'password', 'remember_token',
    ];

    protected $casts = [
        'email_verified_at' => 'datetime',
    ];
```
- File ```config/auth.php``` Thêm mới 1 hệ thống đăng nhập mới dành cho người dùng tách biệt với quản trị
```
'guards' => [
        'web' => [
            'driver' => 'session',
            'provider' => 'users',
        ],
        'user' => [
            'driver' => 'session',
            'provider' => 'users_customer',
        ],
    ],
'providers' => [
        'users' => [
            'driver' => 'eloquent',
            'model' => env('AUTH_MODEL', App\Models\User::class),
        ],
        'users_customer' => [
            'driver' => 'eloquent',
            'model' => App\Models\UserProfile::class,
        ],

        // 'users' => [
        //     'driver' => 'database',
        //     'table' => 'users',
        // ],
    ],
```
- File ```routes/web.php```
```
<?php

use Illuminate\Support\Facades\Route;
use App\Http\Controllers\CardController;
use App\Http\Controllers\UserProfileController;
use Illuminate\Support\Facades\Auth;

Route::get('/', [CardController::class, 'home'])->name('home');
Route::get('/login', function () {
    if (Auth::guard('user')->check()) {
        $loggedInUser = Auth::guard('user')->user();
        return redirect()->route('home');
    }

    // Nếu người dùng chưa đăng nhập, hiển thị trang đăng nhập
    return view('userlogin');
})->name('login');
Route::post('/login', [UserProfileController::class, 'loginsubmit'])->name('loginsubmit');
Route::post('/logout', [UserProfileController::class, 'userlogout'])->name('userlogout');
Route::get('/profile/{slug}', [UserProfileController::class, 'viewprofile'])->name('viewprofile');
```
- File ```resources/views/userlogin.blade.php``` tạo form đăng nhập
```
<main class="form-signin">
  <form action="{{ route('loginsubmit') }}" method="POST">
    @csrf <!-- Thêm CSRF token để bảo mật -->
    <h1 class="h3 mb-3 fw-normal">Card - Đăng nhập</h1>

    <div class="form-floating">
      <input type="text" class="form-control" id="floatingInput" name="username" placeholder="" required>
      <label for="floatingInput">Tên tài khoản</label>
    </div>
    <div class="form-floating">
      <input type="password" class="form-control" id="floatingPassword" name="password" placeholder="Password" required>
      <label for="floatingPassword">Mật khẩu</label>
    </div>

    <div class="checkbox mb-3">
      <label>
        <input type="checkbox" name="remember_me" value="1"> Ghi nhớ tài khoản
      </label>
    </div>
    <button class="w-100 btn btn-lg btn-primary" type="submit">Đăng nhập</button>
  </form>
</main>
```
- File trang chủ ```resources/views/home.blade.php``` tạo phần hiển thị người dùng đã đăng nhập, nút đăng nhập, đăng xuất, link đi đến profile
```
<nav class="d-inline-flex mt-2 mt-md-0 ms-md-auto">
        <a class="me-3 py-2 text-red text-decoration-none" href="{{route('viewprofile', $loggedInUser->slug ?? '')}}"> {{ $loggedInUser->full_name ?? '' }}</a>  
        @if(!$loggedInUser)      
        <form action="{{ route('login') }}" method="POST">
            @csrf
            <button type="submit" class="btn btn-success me-3 py-2">Đăng nhập</button>
        </form>
        @endif
        @if($loggedInUser)
        <form action="{{ route('userlogout') }}" method="POST">
            @csrf
            <button type="submit" class="btn btn-warning me-3 py-2">Đăng xuất</button>
        </form>
        @endif
      </nav>
```
