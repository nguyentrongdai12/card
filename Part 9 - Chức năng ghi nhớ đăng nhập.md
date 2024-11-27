Part 9 - Chức năng ghi nhớ đăng nhập
Mặc định laravel sẽ ghi nhớ theo cookie, tuy nhiên ta cần cấu hình và code thêm để hoạt động luôn chính xác, tránh gây phiền hà cho người dùng khi người dùng chưa đăng xuất nhưng vẫn phải đăng nhập lại
- Tiến hành tạo trường ```remember_token``` cho bảng User Profile
```
php artisan make:migration add_remember_token_to_user_profiles
```
- Mở file migration vừa được tạo tại ```database/migrations```
```
public function up(): void
    {
        Schema::table('user_profiles', function (Blueprint $table) {
            Schema::table('user_profiles', function (Blueprint $table) {
                $table->rememberToken()->after('password')->nullable(); // Thêm cột remember_token
            });
        });
    }
    public function down(): void
    {
        Schema::table('user_profiles', function (Blueprint $table) {
            Schema::table('user_profiles', function (Blueprint $table) {
                $table->dropColumn('remember_token'); // Xóa cột remember_token nếu rollback
            });
        });
    }
```
- Chạy migrate để cập nhật bảng
```
php artisan migrate
```
- Điều chỉnh lại file ```app/Http/Controllers/UserProfileController.php``` ghi nhớ token người dùng đăng nhập vào bảng
```
    public function loginsubmit(Request $request)
    {
        // Xác thực dữ liệu đăng nhập
        $credentials = $request->only('username', 'password');

        // Lấy giá trị từ checkbox "Remember Me"
        $remember = $request->has('remember_me');
                    
        // Kiểm tra thông tin tài khoản và mật khẩu
        if (Auth::guard('user')->attempt($credentials, $remember)) {
            // Nếu đăng nhập thành công, chuyển hướng người dùng đến trang chính
            $loggedInUser = Auth::guard('user')->user();
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
```
