Part 10 - Xây dựng tính năng đăng ký tài khoản. Chúng ta cần làm các công việc sau
1. Tạo view đăng ký
2. Tạo form đăng ký
3. Tạo route đăng ký
4. Tạo chức năng đăng ký trong controller
5. xây dựng tính năng xác thực tài khoản qua email sau khi đăng ký để kích hoạt

- Tạo view đăng ký bằng cách tạo 1 file mới tại ```resources/views/usersignup.blade.php``` Nội dung giao diện ta lấy demo của Bootstrap (điều chỉnh sau). Ta lấy lại giao diện đăng nhập và chỉnh sửa form
```
  <main class="form-signin">
  <form action="{{ route('usersignupsubmit') }}" method="POST" onsubmit="return validateForm()">
    @csrf <!-- Thêm CSRF token để bảo mật -->
    <h1 class="h3 mb-3 fw-normal">Card - Đăng ký</h1>

    <div class="form-floating">
      <input type="text" class="form-control" id="username" name="username" placeholder="" required>
      <label for="username">Tên tài khoản</label>
    </div>
    <div class="form-floating">
      <input type="email" class="form-control" id="email" name="email" placeholder="example@youremail.com" required>
      <label for="email">Địa chỉ Email</label>
    </div>
    <div class="form-floating">
      <input type="password" class="form-control" id="password" name="password" placeholder="Password" required>
      <label for="password">Mật khẩu</label>
    </div>
    <div class="form-floating">
      <input type="password" class="form-control" id="password2" name="password2" placeholder="Password" required>
      <label for="password2">Nhập lại mật khẩu</label>
    </div>
    <button class="w-100 btn btn-lg btn-primary" type="submit">Đăng ký</button>
  </form>
    @if ($errors->any())
        <div class="alert alert-danger">
            <ul>
                @foreach ($errors->all() as $error)
                    <li>{{ $error }}</li>
                @endforeach
            </ul>
        </div>
    @endif
</main>
```
- Tạo route cho việc đăng ký (cập nhật lại các route trước ```routes/web.php```
```
<?php

use Illuminate\Support\Facades\Route;
use App\Http\Controllers\CardController;
use App\Http\Controllers\UserProfileController;
use Illuminate\Support\Facades\Auth;

// route home
Route::get('/', [CardController::class, 'home'])->name('home');
Route::get('/login', [UserProfileController::class, 'userlogin'])->name('userlogin');
Route::post('/login', [UserProfileController::class, 'loginsubmit'])->name('loginsubmit');

//route logout
Route::get('/logout', [UserProfileController::class, 'userlogout'])->name('userlogout');

//route register
Route::get('/signup', [UserProfileController::class, 'usersignup'])->name('usersignup');
Route::post('/signup', [UserProfileController::class, 'usersignupsubmit'])->name('usersignupsubmit');

//route profile
Route::get('/profile/{slug}', [UserProfileController::class, 'viewprofile'])->name('viewprofile');
```
- Chỉnh sửa lại UserProfileController ```app/Http/Controllers/UserProfileController.php```
```
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\Models\UserProfile;
use Illuminate\Support\Facades\Auth;
use Illuminate\Support\Facades\Hash;
use Illuminate\Support\Str;

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

    public function userlogin()
    {
        if (Auth::guard('user')->check()) {
            $loggedInUser = Auth::guard('user')->user();
            //return view('viewprofile', compact(['user_profiles']));
            return "đúng";
        }

        // Nếu người dùng chưa đăng nhập, hiển thị trang đăng nhập
        return view('userlogin');
    }

    public function loginsubmit(Request $request)
    {
        // Xác thực dữ liệu đăng nhập
        $credentials = $request->only('username', 'password');
        $remember = $request->has('remember_me');
                    
        // Kiểm tra thông tin tài khoản và mật khẩu
        if (Auth::guard('user')->attempt($credentials, $remember)) {
            // Nếu đăng nhập thành công, chuyển hướng người dùng đến trang chính
            $loggedInUser = Auth::guard('user')->user();

            //return "Đăng nhập thành công! Người dùng: " . $loggedInUser->username;
            return redirect()->route('home');
        }

        // Nếu không thành công, trả về lại trang đăng nhập với thông báo lỗi
        // return redirect()->route('login')->withErrors([
        //     'username' => 'Tài khoản hoặc mật khẩu không chính xác.',
        // ])->withInput($request->only('username'));

        return back()->withErrors([
            'username' => 'Tài khoản hoặc mật khẩu không chính xác.',
        ])->withInput($request->only('username'));
        
    }

    public function userlogout()
    {
        Auth::guard('user')->logout();
        return redirect()->route('home');
        //return "Đăng xuất";
    }

    public function usersignup()
    {
        return view('usersignup');
    }

    public function usersignupsubmit(Request $request)
    {
        
        // Lưu người dùng mới vào cơ sở dữ liệu
        UserProfile::create([
            'username' => $request->username,
            'full_name' => $request->username,
            'email' => $request->email,
            'password' => $request->password,
            'slug' => $this->generateUniqueSlug($request->username),
        ]);

        return redirect()->route('userlogin')->with('success', 'Đăng ký thành công! Vui lòng đăng nhập.');
    }

    private function generateUniqueSlug($username)
    {
        // Chuyển tên thành slug
        $slug = Str::slug($username);

        // Kiểm tra xem slug đã tồn tại chưa
        $originalSlug = $slug;
        $count = 1;

        while (UserProfile::where('slug', $slug)->exists()) {
            $slug = $originalSlug . '-' . $count;
            $count++;
        }

        return $slug;
    }

}
```
- Chỉnh sửa trang chủ ```resources/views/home.blade.php```
```
<nav class="d-inline-flex mt-2 mt-md-0 ms-md-auto">
        <a class="me-3 py-2 text-red text-decoration-none" href="{{route('viewprofile', $loggedInUser->slug ?? '')}}"> {{ $loggedInUser->full_name ?? '' }}</a>  
        @if(!$loggedInUser) 
            <a href="{{ route('userlogin') }}" class="btn btn-success me-3 py-2">Đăng nhập</a>
        @endif
        @if($loggedInUser)
            <a href="{{ route('userlogout') }}" class="btn btn-warning me-3 py-2">Đăng xuất</a>
        @endif
</nav>
```
- Thêm thông báo lỗi khi đăng nhập ```resources/views/userlogin.blade.php```
```
  @if ($errors->any())
        <div class="alert alert-danger">
            <ul>
                @foreach ($errors->all() as $error)
                    <li>{{ $error }}</li>
                @endforeach
            </ul>
        </div>
    @endif
```
