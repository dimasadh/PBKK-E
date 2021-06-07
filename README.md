# Blades

[Kembali](readme.md)

## Latar belakang topik

Blade adalah fitur yang disediakan Laravel untuk proses templating sederhana namun sangat bermanfaat dalam proses pengembangan tampilan halaman web. Tidak seperti fitur templating PHP populer lainnya, Blade tidak membatasi pemrogram untuk menggunakan kode PHP biasa dalam membuat kode untuk tampilan. Semua tampilan Blade dikompilasi ke dalam kode PHP biasa dan kemudian disimpan dalam cache hingga diubah, yang berarti Blade pada dasarnya tidak menambahkan beban atau overhead pada saat aplikasi dijalankan.

## Konsep-konsep

Blade merupakan sebuah template engine bawaan Laravel yang memungkinkan kita mengolah data tanpa turut campur dengan kode murni PHP. File tampilan blade menggunakan ekstensi file .blade.php dan biasanya disimpan di direktori resources / views. Dalam hal ini Blade pada Laravel menggunakan basis template inheritance dan sections.

### A. Displaying Data
Kita bisa menampilkan data pada views dengan cara :

Buat sebuah route yang mengirimkan data ke sebuah views

```
Route::get('/', function () {
    return view('welcome', ['name' => 'Samantha']);
});
```
Kemudian tampilkan data tersebut pada views menggunakan {{ }}
```
Hello, {{ $name }}.
```
`{{ $nama_variable }}` sama dengan `<?php echo $nama_variable; ?>`, jadi `{{ }}` adalah echo di PHP.

Karena banyak framework JavaScript juga menggunakan kurung kurawal `{{ }}` untuk menunjukkan ekspresi yang diberikan harus ditampilkan di browser, Anda dapat menggunakan simbol `@` untuk memberi tahu mesin rendering Blade bahwa ekspresi harus tetap tidak tersentuh.
```
<h1>Laravel</h1>

Hello, @{{ name }}.
```

#### Rendering JSON
Biasanya, ketika kita passing sebuah array dengan tujuan untuk merendernya dengan JSON, kita menggunakan code :
```
<script>
    var app = <?php echo json_encode($array); ?>;
</script>
```
Namun, daripada menggunakan fungsi `json_encode()`, kita bisa menggunakan `@json` pada Blade seperti contoh dibawah ini :
```
<script>
    var app = @json($array);
</script>

```

#### Blade & JavaScript Frameworks
Karena banyak JavaScript juga menggunakan "tanda kurung kurawal", kita dapat menambahkan symbol `@` untuk memberitahu Blade rendering engine agar expression tersebut jangan disentuh. Contoh:
```
<!-- Blade template -->
<h1>Laravel</h1>
Hello, @{{ name }}.
@@json()

<!-- HTML output -->
<h1>Laravel</h1>
Hello, {{ name }}.
@json()
```

Jika kita ingin menampilkan variabel JavaScript dengan jumlah banyak pada template, maka kita dapat menggunakan `@verbatim` directive sehingga kita tidak perlu menambahkan `@` secara terus menerus:
```
@verbatim
    <div class="container">
        Hello, {{ name }}.
    </div>
@endverbatim
```

### B. Blade Directives
Blade juga menyediakan shortcuts yang nyaman untuk struktur kontrol PHP umum, seperti pernyataan if else dan loop. Shortcuts ini menyediakan cara yang singkat untuk bekerja dengan struktur kontrol PHP dan juga tetap familiar dengan bahasa PHP yang biasanya.

#### If statements
Kita bisa membangun sebuah if statemets menggunakan `@if`, `@elseif`, `@else`, dan juga `@endif`. Seperti contoh dibawah :
```
@if (count($records) === 1)
    I have one record!
@elseif (count($records) > 1)
    I have multiple records!
@else
    I don't have any records!
@endif
```
Kita juga dapat menggunakan `@unless`, `@isset`, `@empty` untuk membangun sebuah if statemets.
```
@unless (Auth::check())
    You are not signed in.
@endunless

@isset($records)
    // $records is defined and is not null...
@endisset

@empty($records)
    // $records is "empty"...
@endempty
```

##### Authentication Directives
Kita bisa menggunakan `@auth` dan `@guest` untuk mengetahui dengan cepat apakah pengguna yang mengakses saat ini terautentikasi atau hanya guest
```
@auth
    // The user is authenticated...
@endauth

@guest
    // The user is not authenticated...
@endguest
```

##### Environtment Directives
Kita bisa menambahkan suatu konten tergantung dengan environtment apa yang sedang dijalankan pada aplikasi tersebut dengan menggunakan @env
```
@env('staging')
    // The application is running in "staging"...
@endenv

@env(['staging', 'production'])
    // The application is running in "staging" or "production"...
@endenv
```

##### Section Directives
Kita bisa mengetahui apakah template inheritance section mempunyai section tertentu menggunakan @hasSection
```
@hasSection('navigation')
    <div class="pull-right">
        @yield('navigation')
    </div>

    <div class="clearfix"></div>
@endif
```

#### Switch statements
Kita bisa membangun sebuah Switch statements menggunakan `@switch`, `@case`, `@break`, `@default` dan `@endswtich`
```
@switch($i)
    @case(1)
        First case...
        @break

    @case(2)
        Second case...
        @break

    @default
        Default case...
@endswitch
```

#### Loops
Blade memberikan directive yang simple untuk membangun sebuah looping PHP. Kita juga dapat melanjutkan atau memberhentikan suatu loop menggunakan `@continue` dan `@break` seperti contoh dibawah ini
```
@for ($i = 0; $i < 10; $i++)
    The current value is {{ $i }}
@endfor

@foreach ($users as $user)
    <p>This is user {{ $user->id }}</p>
@endforeach

@forelse ($users as $user)
    <li>{{ $user->name }}</li>
@empty
    <p>No users</p>
@endforelse

@while (true)
    <p>I'm looping forever.</p>
@endwhile
```

#### Comments
Blade juga membolehkan kita untuk menetapkan sebuah komemntar pada views. Namun tidak seperti HTML comments, Blade comments tidak akan masuk kedalam HTML pada aplikasi kita.
```
{{-- This comment will not be present in the rendered HTML --}}
```

#### Including Subviews
`@include` yang disediakan oleh Blade membolehkan kita untuk memasukkan sebuah Blade view didalam view lain.

Buatlah sebuah subview sederhana di resources/views/hello.blade.php
```
<html>
  <body>
  <h1>Hello from include!</h1>
  </body>
</html>
```
Kemudian pada view utama, kita bisa menambahkan `@include('parentFolder.childFolder.mypage')`
```
<html>
  @include('hello')
</html>
```
Kita juga bisa mengirimkan data tambahan pada subview 
```
<html>
  @include('view.name', ['status' => 'complete'])
</html>
```
Jika kita menambahkan `@include` pada view yang tidak ada, Laravel akan mengirimkan pesan error. Untuk menghindari hal ini, kita dapat menggunakan `@includeIf`
```
@includeIf('view.name', ['status' => 'complete'])
```
Jika kita ingin menambahkan `@include` hanya jika ketika diberikan boolean yang bernilai true atau false saja, kita bisa menggunakan `@includeWhen` dan `@includeUnless`
```
@includeWhen($boolean, 'view.name', ['status' => 'complete'])

@includeUnless($boolean, 'view.name', ['status' => 'complete'])
```
#### Raw PHP
Kita bisa memasukkan kode php dengan menggunakan @php
```
@php
    $counter = 1;
@endphp
```

### C. Component
Hampir semua sistem modern terdiri dari entities kecil yang self-contained, independent, dan reusable. Masing-masing entitas ini memiliki fungsi khusus yang disediakan untuk sistem secara keseluruhan. Komponen Laravel adalah entities kecil dengan interface yang terdefinisi dengan baik. Ini berfungsi sebagai building block untuk sistem software yang besar. Semua data terkait dienkapsulasi dalam unit yang dapat digunakan kembali. 

Untuk membuat sebuah component, kita dapat menuliskan perintah berikut
```
php artisan make:component Header
```
Perintah ini akan membuat sebuah template untuk component. Component yang kita buat akan berada pada direktori `app/View/Components`. Disini kita dapat menambahkan logic ataupun fungsi yang dibutuhkan pada component. View untuk component akan berada di direktori `resources/views/components`. 

Kita juga dapat mendaftarkan component kita secara manual dengan menambahkannya pada `boot` method pada service provider dengan contoh sebagai berikut.
```
use Illuminate\Support\Facades\Blade;

/**
 * Bootstrap your package's services.
 */
public function boot()
{
    Blade::component('header', Alert::class);
}
```
Setelah terdaftar, kita dapat memanggil component tersebut dengan menggunakan tag alias :
```
<x-header>
```

#### Rendering component
Berikut ini contoh memanggil component header yang telah dibuat sebelumnya. Pada `resources/views/components/header.blade.php` tuliskan code berikut
```
<div>
    <!-- Simplicity is the consequence of refined emotions. - Jean D'Alembert -->
    <h1>This is header<h1>
</div>
```
Pada `resources/views/main.blade.php` tuliskan code berikut
```
<html>
    <body>
        <x-header>
    </body>
</html>
```
Maka pada view main akan menampilkan component header.

#### Passing data ke Components
Kita dapat melakukan passing data ke Component menggunakan HTML attributes. Yaitu dengan menggunakan PHP Expression `:` dan diikuti dengan variable yang akan dikirimkan
```
<x-header :message=”$message”/>
```
Namun kita juga perlu melakukan define semua variable data yang diperlukan pada class constructor component tersebut. Semua variable public pada component akan dapat diakses pada component’s view.
```
<?php

namespace App\View\Components;

use Illuminate\View\Component;

class Header extends Component
{
    /**
     * Create a new component instance.
     *
     * @return void
     */
    public $message;

    public function __construct($message)
    {
        $this->message = $message;
    }

    /**
     * Get the view / contents that represent the component.
     *
     * @return \Illuminate\Contracts\View\View|\Closure|string
     */
    public function render()
    {
        return view('components.header');
    }
}


```
Setelah component dirender, kita dapat menampilkan konten data pada public variable component yang sudah dibuat.
```
<div>
    <!-- Simplicity is the consequence of refined emotions. - Jean D'Alembert -->
    <h1>This is header<h1>
    <h3>Message : </h3><br>
    <p> {{ $message }}
</div>

```

#### Escaping Attribute Rendering
Beberapa JavaScript framework juga menggunakan colon-prefixed attributes, kita bisa menggunakan double colon (::) prefix untuk memberi tahu Blade bahwa attribute ini bukan merupakan PHP expression. Sebagai contoh
```
<x-button ::class="{ danger: isDeleting }">
    Submit
</x-button>
```
Maka HTML yang akan dirender oleh Blade menjadi :
```
<button :class="{ danger: isDeleting }">
    Submit
</button>
```

#### Component Methods
Selain public variable, semua public method pada component juga dapat dipanggil. Sebagai contoh component header yang telah kita buat memiliki fungsi isGreen :
```
public function isGreen($value)
    {
        if ($value == 1) return true;
        else return false;
    }

```
Kita bisa memanggil fungsi tersebut dari template component `resources/views/components/header.blade.php` seperti berikut
```
<div>
    <!-- Simplicity is the consequence of refined emotions. - Jean D'Alembert -->
    <h1>This is header<h1>
    <h3>Message : </h3><br>
    <p> {{ $message }}
    {{-- @if ($isGreen($value) == true)
        <p style="color:green">Green</p>
    @else
        <p style="color:red">Not green</p>
    @endif --}}
</div>
```

#### Component Attributes
Kita telah mengetahui bagaimana cara melakukan passing data ke sebuah component. Namun kita juga dapat juga menambahkan HTML attribute tambahan seperti `class` yang mana bukan merupakan data yang dibutuhkan fungsi yang disediakan component. Seperti contoh kita ingin menambahkan sebuah `class` kedalam component yang ingin kita panggil :
```
<x-header :message=”$message class=”color:blue”/>
```
Semua attribute yang bukan merupakan constructor dari component akan otomatis masuk kedalam “attribute bag” yang tersedia pada variable `$attributes`. Kita dapat menggunakannya pada view component seperti dibawah ini:
```
<div {{ $attributes }}>
	<!-- Components content -->
</div>
```

#### Merge Classess
Kita telah mengetahui bagaimana cara melakukan passing data ke sebuah component. Namun kita juga dapat juga menambahkan HTML attribute tambahan seperti `class` yang mana bukan merupakan data yang dibutuhkan fungsi yang disediakan component. Seperti contoh kita ingin menambahkan sebuah `class` kedalam component yang ingin kita panggil :
```
<x-header :message=”$message class=”color:blue”/>
```
Semua attribute yang bukan merupakan constructor dari component akan otomatis masuk kedalam “attribute bag” yang tersedia pada variable `$attributes`. Kita dapat menggunakannya pada view component seperti dibawah ini:
```
<div {{ $attributes }}>
	<!-- Components content -->
</div>
```

#### Retrieving & Filtering Attributes
Kita dapat melakukan filter terhadap attributes pada component. Kita bisa menggunakan fungsi `whereStartsWith` untuk mendapatkan semua attributes yang keys nya dimulai dengan string yang diberikan:
```
{{ $attributes->whereStartsWith('wire:model') }}
```
Sebaliknya, kita juga dapat menggunakan fungsi `whereDoesntStartWith` untuk mengecualikan semua attributes yang dimulaki dengan string yang diberikan:
```
{{ $attributes->whereDoesntStartWith('wire:model') }}
```
Jika kita ingin mengetahui apakah suatu attribute terdapat pada component, kita bisa menggunakan fungsi `has` dimana akan mengembalikan nilai Boolean yang menjelaskan apakah attribute tersebut ada atau tidak.
```
@if ($attributes->has('class'))
    <div>Class attribute is present</div>
@endif
```
Kita juga dapat mengambil attribute spesifik dengan menggunakan fungsi `get`
```
{{ $attributes->get('class') }}
```

#### Reserved Keywords
Secara default, beberapa keywords sudah direserved untuk penggunakan internal Blade untuk merender component. Keyword berikut ini tidak dapat didefinisikan sebagai public properties ataupun nama fungsi pada component kita:
```
-	data
-	render
-	resolverView
-	shouldRender
-	view
-	withAttributes
-	withName
```

#### Slots
Kita dapat menambahkan konten tambahan kedalam component kita melalui `slots`. Slots component dirender dengan melakukan echoing variable $slot. Misalkan component `header` berisikan code berikut:
```
<!-- /resources/views/component/Header.php -->
<div class=”color:purple”>
	{{ $slot }}
</div>
```
Kita bisa passing konten ke `slot` dengan menambahkan konten didalam tag component
```
<x-header>
	<h3>Ini pesan dari slot</h3>
</x-header>
```

### D. Building Layout
Kebanyakan aplikasi web maintain sebuah layout yang sama untuk berbagai halaman. Hal tersebut akan melelahkan dan susah untuk di maintain jika kita harus menulis ulang semua layout HTML di setiap view yang kita buat. Untuk menghindari perulangan penulisan layout dan mempermudah maintain setiap view nya, kita dapat menggunakan `layout` pada aplikasi kita.

#### Layout menggunakan Component
Salah satu kegunaan Blade Component adalah dapat diimplementasikan sebagai layout. Sebagai contoh misalkan kita akan membuat sebuah aplikasi “todo list”. Kita akan mendefine layout component sebagai berikut:
```
<!-- resources/views/components/layout.blade.php -->

<html>
    <head>
        <title>{{ $title ?? 'Todo Manager' }}</title>
    </head>
    <body>
        <h1>Todos</h1>
        <hr/>
        {{ $slot }}
    </body>
</html>
```
Setelah `layout` component di definsikan, kita dapat membuat Blade view yang menggunakan component tersebut. Seperti contoh berikut ini:
```
<!-- resources/views/tasks.blade.php -->

<x-layout>
    @foreach ($tasks as $task)
        {{ $task }}
    @endforeach
</x-layout>
```
Yang terakhir adalah kita hanya perlu melakukan routing `task` view melalui route:
```
use App\Models\Task;

Route::get('/tasks', function () {
    return view('tasks', ['tasks' => Task::all()]);
});
```

#### Layout Menggunakan Template Inheritance
Selain menggunakan Blade Component, kita juga dapat membuat sebuah layout menggunakan “template inheritance”. Untuk memulainya, kita akan membuat sebuah page layout sederhana seperti berikut:
```
<!-- resources/views/layouts/app.blade.php -->

<html>
    <head>
        <title>App Name - @yield('title')</title>
    </head>
    <body>
        @section('sidebar')
            This is the master sidebar.
        @show

        <div class="container">
            @yield('content')
        </div>
    </body>
</html>
```
Seperti yang terlihat, code diatas terdiri dari HTML mark-up seperti biasa. Namun, terdapat juga `@section` dan `@yield` directives. `@section` directive digunakan untuk mendefinisikan sebuah konten yang terdapat pada section, dan kemudian `@yield` directive digunakan untuk menampilkan konten pada section yang diberikan.

Setelah kita mendefinisikan layout, selanjutnya kita akan membuat child page yang merupakan inherits dari layout. Kita menggunakan `@extends` untuk menentukan layout mana yang akan di “inherit” oleh child page ini. Dimana dengan extend sebuah Blade layout, kita dapat memasukkan konten kedalam section layout menggunakan `@section` directive. Dan untuk menampilkan konten dari sebuah section, kita dapat menggunakan `@yield`:
```
<!-- resources/views/child.blade.php -->

@extends('layouts.app')

@section('title', 'Page Title')

@section('sidebar')
    @parent

    <p>This is appended to the master sidebar.</p>
@endsection

@section('content')
    <p>This is my body content.</p>
@endsection
```
Pada contoh diatas, section `sidebar` menggunakan `@parent` directive untuk menambahkan (daripada menimpa/overwriting) konten kedalam sidebar layout. `@parent` directive akan menampilkan konten dari layout yang kita definisikan pada `app.blade.php` saat view di render.


### E. Forms
#### CSRF Field
CSRF adalah cross site request forgery. CSRF ini merupakan salah satu lubang di web app yang bekerja dengan cara mengeksploitasi suatu aksi dan eksploitasi ini memanfaatkan otentikasi milik salah satu user. Blade dapat melindunginya dengan menggunakan @csrf pada form yang digunakan pada aplikasi.
```
<form method="POST" action="/profile">
    @csrf

    ...
</form>
```
#### Method Field
HTML Form tidak dapat membuat request `PUT`, `PATCH`, atau `DELETE`. Namun kita dapat menggunakan `@method` untuk membuat field tersebut.
```
<form action="/foo/bar" method="POST">
    @method('PUT')

    ...
</form>
```
#### Validation Errors
`@error` method dapat digunakan untuk mengetahui apakah terdapat validasi pesan error pada attribute yang diberikan. Kita dapat echo `$message` untuk menampilkan pesan error:
```
<!-- /resources/views/post/create.blade.php -->

<label for="title">Post Title</label>

<input id="title" type="text" class="@error('title') is-invalid @enderror">

@error('title')
    <div class="alert alert-danger">{{ $message }}</div>
@enderror
```
Karena `@error` merupakan if statement, kita dapat juga menggunakan else ketika tidak terdapat error:
```
<!-- /resources/views/auth.blade.php -->

<label for="email">Email address</label>

<input id="email" type="email" class="@error('email') is-invalid @else is-valid @enderror">
```

### F. Service Injection
`@inject` directive digunakan untuk mendapatkan service dari Laravel service container. Argument pertama yang dipass pada `@inject` adalah nama variabel yang akan menerima service, dan argument kedua adalah nama Class atau Interface dari service yang ingin dipanggil:
```
@inject('metrics', 'App\Services\MetricsService')

<div>
    Monthly Revenue: {{ $metrics->monthlyRevenue() }}.
</div>
```

## Langkah-langkah tutorial
### Displaying Data dan If Statement
Berikut ini merupakan cara menampilkan data yang dikirimkan ke view dan juga penggunaan Blade Directives pada file `resources/views/control.blade.php`:
```
<!-- resources/views/control.blade.php -->
<html>
    <body>
        <!-- Menampilkan data yang di pass ke view -->
        <h2> Halo, saya {{ $name }} sedang menampilkan data </h2>

        <!-- Penggunaan If Statement -->
        <h3>If</h3>
        @if (count($arrays) == 1)
            I have one record!
        @elseif (count($arrays) > 1)
            I have multiple records!
        @else
            I don't have any records!
        @endif --}}

        <h3>Switch</h3>
        @switch($arrays)
            @case(1)
                First case...
                @break

            @case(2)
                Second case...
                @break

            @default
                Variabel yang diberikan bukanlah sebuah integer
        @endswitch

        <!-- Penggunaan Looping -->
        <h3>For</h3>
        @for ($i = 0; $i < 3; $i++)
            The current value is {{ $i }}
        @endfor

        <h3>Foreach</h3>
        @foreach ($arrays as $array)
            <p>This is user {{ $array }}</p>
        @endforeach

        <h3>While</h3>
        {{-- @while (true)
            <p>I'm looping forever.</p>
            @break
        @endwhile --}}

        <!-- Melakukan include view dari hello.blade.php -->
        {{-- @include('hello') --}}
        
        <!-- Melakukan passing data pada view yang ingin di include -->
        @include('hello', ['data' => 'test'])
    </body>
</html>
```
Dapat dilihat bahwa kita juga melakukan include view `hello.blade.php` dan juga passing data `data` kedalamnya. Kita perlu untuk membuat view `hello.blade.php` untuk menampilkan view 'hello' tersebut, sebagai contoh seperti berikut:
```
<!-- resources/views/hello.blade.php -->
<html>
  <body>
  <h1>This is your included view</h1>

  <!-- Menampilkan data yang diterima dari view utama -->
  <h3>This is your data: {{$data}}</h3>
  </body>
</html>
```
Setelah kita membuat view nya, kita perlu membuat route agar view dapat dibuka melalui browser. Dapat terlihat bahwa pada `control.blade.php` kita memanggil data `$name` dan `$arrays`, sehingga kita perlu melakukan passing data tersebut ke view `control`. Kita akan melakukannya pada `web.php`:
```
<!-- routes/web.php -->
<?php

use Illuminate\Support\Facades\Route;

/*
|--------------------------------------------------------------------------
| Web Routes
|--------------------------------------------------------------------------
|
| Here is where you can register web routes for your application. These
| routes are loaded by the RouteServiceProvider within a group which
| contains the "web" middleware group. Now create something great!
|
*/

Route::get('/', function () {
    return view('welcome');
});

// menggunakan Blade Directives (if, loop, dll)
Route::get('/control', function () {
    $arr = array('a' => 1, 'b' => 2, 'c' => 3, 'd' => 4, 'e' => 5);
    return view('control', ['name' => 'Samantha' ,'arrays' => $arr]);
});
```
Setelah semua yang diperlukan dibuat, maka akan tampil hasil akhir seperti berikut:
![image](https://user-images.githubusercontent.com/61277501/121039885-ab431e80-c7db-11eb-9052-c49f395aa6b5.png)
