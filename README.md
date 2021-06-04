# Blades

[Kembali](readme.md)

## Latar belakang topik

Blade adalah fitur yang disediakan Laravel untuk proses templating sederhana namun sangat bermanfaat dalam proses pengembangan tampilan halaman web. Tidak seperti fitur templating PHP populer lainnya, Blade tidak membatasi pemrogram untuk menggunakan kode PHP biasa dalam membuat kode untuk tampilan. Semua tampilan Blade dikompilasi ke dalam kode PHP biasa dan kemudian disimpan dalam cache hingga diubah, yang berarti Blade pada dasarnya tidak menambahkan beban atau overhead pada saat aplikasi dijalankan.

## Konsep-konsep

Blade merupakan sebuah template engine bawaan Laravel yang memungkinkan kita mengolah data tanpa turut campur dengan kode murni PHP. File tampilan blade menggunakan ekstensi file .blade.php dan biasanya disimpan di direktori resources / views. Dalam hal ini Blade pada Laravel menggunakan basis template inheritance dan sections.


## Langkah-langkah tutorial

### Menampilkan Data
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

Karena banyak framework JavaScript juga menggunakan kurung kurawal `{{ }}` untuk menunjukkan ekspresi yang diberikan harus ditampilkan di browser, Anda dapat menggunakan simbol @ untuk memberi tahu mesin rendering Blade bahwa ekspresi harus tetap tidak tersentuh.
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

### Blade Directives
Blade juga menyediakan shortcuts yang nyaman untuk struktur kontrol PHP umum, seperti pernyataan if else dan loop. Shortcuts ini menyediakan cara yang singkat untuk bekerja dengan struktur kontrol PHP dan juga tetap familiar dengan bahasa PHP yang biasanya.

#### If statements
Kita bisa membangun sebuah if statemets menggunakan @if, @elseif, @else, dan juga @endif. Seperti contoh dibawah :
```
@if (count($records) === 1)
    I have one record!
@elseif (count($records) > 1)
    I have multiple records!
@else
    I don't have any records!
@endif
```
Kita juga dapat menggunakan @unless, @isset, @empty untuk membangun sebuah if statemets.
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
Kita bisa menggunakan @auth dan @guest untuk mengetahui dengan cepat apakah pengguna yang mengakses saat ini terautentikasi atau hanya guest
```
@auth
    // The user is authenticated...
@endauth

@guest
    // The user is not authenticated...
@endguest
```

##### Environtment Directives
Kita bisa mengetahui apakah aplikasi saat ini sedang berjalan pada environtment tertentu menggunakan @env
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
Kita bisa membangun sebuah Switch statements menggunakan @switch, @case, @break, @default dan @endswtich
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
Blade memberikan directive yang simple untuk membangun sebuah looping PHP. Kita juga dapat melanjutkan atau memberhentikan suatu loop menggunakan @continue dan @break seperti contoh dibawah ini
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
@include yang disediakan oleh Blade membolehkan kita untuk memasukkan sebuah Blade view didalam view lain.

Buatlah sebuah subview sederhana di resources/views/hello
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
Jika kita menambahkan @include pada view yang tidak ada, Laravel akan mengirimkan pesan error. Untuk menghindari hal ini, kita dapat menggunakan @includeIf
```
@includeIf('view.name', ['status' => 'complete'])
```
Jika kita ingin menambahkan @include hanya jika ketika diberikan boolean yang bernilai true atau false saja, kita bisa menggunakan @includeWhen dan @includeUnless
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
