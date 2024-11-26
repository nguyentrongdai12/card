Part 2: Tạo view cơ bản
1. Tạo controller để xử lý về sau
```
php artisan make:controller CardController
```
  Tạo hàm xử lý trang home:
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
