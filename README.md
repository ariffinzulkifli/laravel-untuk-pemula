# Laravel untuk Pemula

Panduan komprehensif ini akan membimbing anda membina sistem blog lengkap dalam Laravel, merangkumi model, migration, controller, view, dan operasi CRUD. Pada akhir tutorial ini, anda akan mempunyai blog yang berfungsi sepenuhnya dengan kategori, pos, dan semua ciri penting.

## Kandungan
1. [Menyediakan Kategori](#menyediakan-kategori)
2. [Mencipta Model Blog](#mencipta-model-blog)
3. [Menyediakan Routes](#menyediakan-routes)
4. [Membina Antara Muka Blog](#membina-antara-muka-blog)
5. [Operasi CRUD](#operasi-crud)

---

## Menyediakan Kategori

### Langkah 1: Cipta Model Category dan Migration

Pertama, kita akan mencipta model Category dengan fail migration yang sepadan. Flag `-m` secara automatik menghasilkan fail migration.

```bash
php artisan make:model Category -m
```

**Apa yang dilakukan:** Mencipta kedua-dua model `Category` (`app/Models/Category.php`) dan fail migration dalam `database/migrations/`.

### Langkah 2: Konfigurasikan Migration Categories

Buka fail migration yang dihasilkan dan tambahkan field berikut untuk menentukan struktur kategori kita:

```php
$table->string('name');
```

**Penjelasan:** Ini mencipta kolum `name` untuk menyimpan nama kategori seperti "Tailwind CSS", "Responsive Design", dll.

### Langkah 3: Jalankan Migration

```bash
php artisan migrate
```

### Langkah 4: Isi Data Sampel

Menggunakan Adminer (atau mana-mana alat pengurusan pangkalan data), tambahkan kategori sampel ke jadual `categories`. Tetapkan kedua-dua `created_at` dan `updated_at` kepada **sekarang**:

1. **Tailwind CSS**
2. **Responsive Design** 
3. **Web Performance**

**Tip Pro:** Dalam pengeluaran, pertimbangkan menggunakan Laravel Seeders daripada entri pangkalan data manual untuk kawalan versi yang lebih baik.

---

## Mencipta Model Blog

### Langkah 1: Jana Model Blog dengan Scaffold Penuh

Flag `-mcr` mencipta model, migration, dan resource controller sekaligus:

```bash
php artisan make:model Blog -mcr
```

**Apa yang anda dapat:**
- `app/Models/Blog.php` - Model Eloquent
- `database/migrations/..._create_blogs_table.php` - Migration pangkalan data
- `app/Http/Controllers/BlogController.php` - Resource controller dengan method CRUD

### Langkah 2: Reka Bentuk Struktur Pangkalan Data Blog

Dalam fail migration blogs, tambahkan field-field ini untuk mencipta struktur pos blog yang komprehensif:

```php
$table->string('title');
$table->text('text');
$table->string('author');
$table->foreignId('category_id')->constrained();
```

**Penjelasan Field:**
- `title` - Tajuk pos blog (varchar)
- `text` - Kandungan blog penuh (menyokong HTML)
- `author` - Nama pengarang
- `category_id` - Foreign key yang menghubungkan ke jadual categories

### Langkah 3: Gunakan Migration

```bash
php artisan migrate
```

### Langkah 4: Tambah Pos Blog Sampel

Menggunakan Adminer, cipta pos blog sampel dengan nilai-nilai ini (tetapkan `created_at` dan `updated_at` kepada **sekarang**):

**1. Tajuk:**
```
Understanding Tailwind CSS v4
```

**2. Kandungan (format HTML):**
```html
<article class="prose max-w-none prose-img:rounded-md">
    <p>Tailwind CSS v4 has officially arrived and it brings a host of improvements that promise faster development, cleaner utilities, and a more customizable design experience.</p>
    <p>With this latest version, the core team has focused on making the developer experience even smoother. One of the most noticeable changes is the removal of legacy browser support, allowing for more modern CSS features and a smaller core library.</p>

    <h2>Utility-First, But Smarter</h2>
    <p>Tailwind continues to embrace its utility-first philosophy, but with smarter defaults and improved flexibility. Utilities are now more consistent and easier to compose, thanks to enhanced configuration support and plugin capabilities.</p>

    <h2>Performance Upgrades</h2>
    <p>You'll notice snappier build times and smaller final CSS files thanks to optimizations in how Tailwind processes your HTML and class usage. The Just-In-Time (JIT) engine, introduced in v3, is now the default and even more refined in v4.</p>

    <blockquote>
        "Tailwind CSS v4 is a true leap forward. It gives developers the power to build beautiful UIs without writing custom CSS." — A Frontend Developer
    </blockquote>

    <p>Whether you're working on a personal blog, a SaaS dashboard, or an eCommerce platform, Tailwind v4 will make your workflow cleaner and faster. Try it out today!</p>
</article>
```

**3. Pengarang:**
```
Amir Hamzah
```

**4. ID Kategori:**
```
1
```

---

## Menyediakan Routes

### Daftarkan Blog Resource Routes

Tambahkan baris ini ke fail `routes/web.php` anda untuk mencipta semua route RESTful secara automatik:

```php
Route::resource('blog', BlogController::class);
```

**Jangan lupa import controller:**
```php
use App\Http\Controllers\BlogController;
```

**Routes yang Dicipta:**
- `GET /blog` - Senaraikan semua pos (index)
- `GET /blog/create` - Paparkan form cipta
- `POST /blog` - Simpan pos baru
- `GET /blog/{id}` - Paparkan pos tunggal
- `GET /blog/{id}/edit` - Paparkan form edit
- `PUT/PATCH /blog/{id}` - Kemaskini pos
- `DELETE /blog/{id}` - Padam pos

---

## Membina Antara Muka Blog

### Halaman Senarai Pos Blog

Cipta `resources/views/blog/index.blade.php` untuk memaparkan semua pos blog. Kita akan tunjukkan dua pendekatan:

#### Pendekatan Asas dengan `@foreach`

```html
@extends('layouts.blog')

@section('post')
<!-- Blog Post Item -->
@foreach ($posts as $post)
<article class="flex mb-8 space-x-6">
    <img src="https://placehold.co/150" alt="Blog Post 1" class="w-[150px] h-[150px] object-cover rounded-md flex-shrink-0" />
    <div>
        <h3 class="text-xl font-semibold mb-2 hover:text-blue-600 cursor-pointer"><a href="{{ route('blog.show', $post) }}">{{ $post->title }}</a></h3>
        <p class="text-gray-700 line-clamp-3">
            Tailwind CSS v4 introduces new utilities and improvements to help you build faster and cleaner designs with ease.
        </p>
    </div>
</article>
@endforeach
@endsection
```

#### Pendekatan Dipertingkat dengan `@forelse` (Disyorkan)

Pendekatan ini mengendalikan keadaan kosong dengan baik dan termasuk fungsi edit/padam:

```html
@extends('layouts.blog')

@section('post')
<!-- Blog Post Item -->
@forelse ($posts as $post)
<article class="flex mb-8 space-x-6">
    <img src="https://placehold.co/150" alt="Blog Post 1" class="w-[150px] h-[150px] object-cover rounded-md flex-shrink-0" />
    <div>
        <h3 class="text-xl font-semibold mb-2 flex items-center gap-2">
            <!-- Post Title -->
            <a href="{{ route('blog.show', $post) }}" class="hover:text-blue-600 cursor-pointer">
                {{ $post->title }}
            </a>
            <!-- Edit Icon -->
            <a href="{{ route('blog.edit', $post) }}" class="text-gray-400 hover:text-blue-500" title="Edit post">
                ✎
            </a>
        </h3>
        <p class="text-gray-700 line-clamp-3">
            Tailwind CSS v4 introduces new utilities and improvements to help you build faster and cleaner designs with ease.
        </p>
    </div>
</article>
@empty
<article class="flex mb-8 space-x-6">
    <div>
        <h3 class="text-xl font-semibold mb-2 hover:text-blue-600 cursor-pointer">Tiada pos tersedia.</h3>
        <p class="text-gray-700 line-clamp-3">
            Tailwind CSS v4 introduces new utilities and improvements to help you build faster and cleaner designs with ease.
        </p>
    </div>
</article>
@endforelse
@endsection
```

**Ciri-ciri Utama:**
- `@forelse` mengendalikan kedua-dua keadaan berisi dan kosong
- Ikon edit inline untuk akses pantas
- Reka bentuk responsif dengan Flexbox
- Kesan hover untuk UX yang lebih baik

### Logik Controller untuk Blog Index

Kemaskini `app/Http/Controllers/BlogController.php`:

```php
public function index()
{
    $categories = Category::all();
    $posts = Blog::when(request('category_id'), function($query){
        $query->where('category_id', request('category_id'));
    })->orderBy('id', 'asc')->get();
    return view('blog.index', compact('categories', 'posts'));
}
```

**Ciri-ciri Dijelaskan:**
- `Category::all()` - Muatkan semua kategori untuk penapisan
- `when()` - Query bersyarat berdasarkan parameter URL
- `orderBy('id', 'asc')` - Susun pos mengikut ID menaik
- Penapisan kategori melalui parameter URL `?category_id=1`

---

## Paparan Pos Blog Individu

### View Pos Tunggal

Cipta `resources/views/blog/show.blade.php`:

```html
@extends('layouts.utama')

@section('kandungan')
<!-- Main Content -->
<main class="flex-grow px-6 py-10 max-w-3xl mx-auto">
    <!-- Blog Title -->
    <h1 class="text-4xl font-bold mb-4">{{ $post->title }}</h1>

    <!-- Meta Info -->
    <div class="text-sm text-gray-500 mb-6">
    Oleh <span class="text-gray-700 font-medium">{{ $post->author }}</span> &bull; Diterbitkan pada <span>{{ \Carbon\Carbon::parse($post->created_at)->format('d F Y') }}</span>
    </div>

    <!-- Featured Image -->
    <img src="https://placehold.co/800x400" alt="Featured image" class="w-full h-auto rounded-md mb-8" />

    <!-- Blog Post Body -->
    {!! $post->text !!}
</main>
@endsection
```

**Ciri-ciri Utama:**
- Hierarki tipografi profesional
- Paparan pengarang dan tarikh penerbitan
- Format tarikh Carbon untuk tarikh yang mudah dibaca
- Sintaks `{!! !!}` untuk render kandungan HTML dengan selamat

### Method Show Controller

```php
public function show(Blog $blog)
{
    $post = $blog;
    return view('blog.show', compact('post'));
}
```

**Sihir Laravel:** Route Model Binding secara automatik mencari pos blog mengikut ID dan menyuntiknya sebagai `$blog`.

---

## Operasi CRUD

### Mencipta Pos Baru

#### Langkah 1: Jadikan Model Mass Assignable

Pertama, kemaskini `app/Models/Blog.php` untuk membenarkan mass assignment:

```php
protected $fillable = [
    'title',
    'author',
    'category_id',
    'text',
];
```

**Nota Keselamatan:** `$fillable` melindungi daripada kelemahan mass assignment dengan mentakrifkan secara eksplisit field mana yang boleh diberikan secara pukal.

#### Langkah 2: Cipta Method Controller Form

Tambahkan method-method ini ke `BlogController.php`:

```php
public function create()
{
    $categories = Category::all();
    return view('blog.create', compact('categories'));
}
```

```php
public function store(Request $request)
{
    $validated = $request->validate([
        'title' => 'required|string|max:255',
        'author' => 'required|string|max:100',
        'category_id' => 'nullable|exists:categories,id',
        'text' => 'required|string',
    ]);

    $post = Blog::create([
        'title' => $validated['title'],
        'author' => $validated['author'],
        'category_id' => $validated['category_id'] ?? null,
        'text' => $validated['text'],
    ]);

    return redirect()->route('blog.index')->with('success', 'Pos berjaya dicipta!');
}
```

**Peraturan Validasi Dijelaskan:**
- `required|string|max:255` - Field teks wajib dengan maksimum 255 aksara
- `nullable|exists:categories,id` - Pilihan tetapi mesti ID kategori yang sah jika disediakan
- Redirect automatik dengan mesej kejayaan

### Mengemaskini Pos

#### View Form Edit

Cipta `resources/views/blog/edit.blade.php`:

```html
@extends('layouts.utama')

@section('kandungan')
    <main class="flex-grow px-6 py-10 max-w-3xl mx-auto">
    <h1 class="text-3xl font-bold mb-6">Edit Post</h1>

    <!-- Display Validation Errors -->
    @if ($errors->any())
        <div class="mb-6 bg-red-100 border border-red-400 text-red-700 px-4 py-3 rounded">
            <ul class="list-disc list-inside">
                @foreach ($errors->all() as $error)
                    <li>{{ $error }}</li>
                @endforeach
            </ul>
        </div>
    @endif

    <!-- Edit Post Form -->
    <form action="{{ route('blog.update', $post->id) }}" method="POST" class="space-y-6">
        @csrf
        @method('PUT')

        <!-- Title -->
        <div>
            <label for="title" class="block font-medium text-gray-700 mb-1">Tajuk</label>
            <input type="text" name="title" id="title"
                class="w-full border border-gray-300 rounded-md px-4 py-2 focus:outline-none focus:ring-2 focus:ring-blue-500"
                value="{{ old('title', $post->title) }}" required>
        </div>

        <!-- Author -->
        <div>
            <label for="author" class="block font-medium text-gray-700 mb-1">Pengarang</label>
            <input type="text" name="author" id="author"
                class="w-full border border-gray-300 rounded-md px-4 py-2 focus:outline-none focus:ring-2 focus:ring-blue-500"
                value="{{ old('author', $post->author) }}" required>
        </div>

        <!-- Category (if available) -->
        @if (!empty($categories))
        <div>
            <label for="category_id" class="block font-medium text-gray-700 mb-1">Kategori</label>
            <select name="category_id" id="category_id"
                    class="w-full border border-gray-300 rounded-md px-4 py-2 focus:outline-none focus:ring-2 focus:ring-blue-500">
                <option value="">-- Pilih Kategori --</option>
                @foreach ($categories as $category)
                    <option value="{{ $category->id }}"
                        @selected(old('category_id', $post->category_id) == $category->id)>
                        {{ $category->name }}
                    </option>
                @endforeach
            </select>
        </div>
        @endif

        <!-- Post Text -->
        <div>
            <label for="text" class="block font-medium text-gray-700 mb-1">Kandungan</label>
            <textarea name="text" id="text" rows="10"
                    class="w-full border border-gray-300 rounded-md px-4 py-2 focus:outline-none focus:ring-2 focus:ring-blue-500"
                    required>{{ old('text', $post->text) }}</textarea>
        </div>

        <!-- Submit Button -->
        <div>
            <button type="submit"
                    class="inline-block bg-blue-600 hover:bg-blue-700 text-white font-medium px-6 py-2 rounded">
                Kemaskini Pos
            </button>
        </div>
    </form>
</main>

@endsection
```

**Ciri-ciri Form:**
- Pengendalian ralat dengan mesej ralat yang bergaya
- `@method('PUT')` untuk HTTP verb yang betul
- Helper `old()` mengekalkan data form pada ralat validasi
- Directive `@selected` untuk keadaan pemilihan dropdown

#### Method Controller Edit

```php
public function edit(Blog $blog)
{
    $categories = Category::all(); // optional
    $post = $blog;
    return view('blog.edit', compact('post', 'categories'));
}
```

```php
public function update(Request $request, Blog $blog)
{
    $validated = $request->validate([
        'title' => 'required|string|max:255',
        'author' => 'required|string|max:100',
        'category_id' => 'nullable|exists:categories,id',
        'text' => 'required|string',
    ]);

    $blog->update($validated);

    return redirect()->route('blog.index')->with('success', 'Pos berjaya dikemaskini!');
}
```

### Memadam Pos

#### Tambah Butang Padam ke View Index

Tambahkan kod ini selepas ikon edit dalam `resources/views/blog/index.blade.php`:

```html
<!-- Delete Icon Form -->
<form action="{{ route('blog.destroy', $post) }}" method="POST"
    onsubmit="return confirm('Adakah anda pasti mahu memadam pos ini?');"
    class="inline">
    @csrf
    @method('DELETE')
    <button type="submit" title="Padam Pos" class="text-gray-400 hover:text-red-500 ml-1">
        🗑️
    </button>
</form>
```

**Ciri-ciri Keselamatan:**
- Dialog pengesahan JavaScript
- Method HTTP DELETE yang betul
- Form inline untuk mengelak isu susun atur

#### Method Controller Delete

```php
public function destroy(Blog $blog)
{
    $blog->delete();
    return redirect()->route('blog.index')->with('success', 'Pos berjaya dipadamkan!');
}
```

---

## Ringkasan

Anda kini mempunyai sistem blog Laravel yang lengkap dengan:

✅ **Struktur Pangkalan Data** - Kategori dan pos Blog dengan hubungan yang betul  
✅ **Operasi CRUD** - Fungsi Cipta, Baca, Kemaskini, Padam  
✅ **Antara Muka Pengguna** - Reka bentuk yang bersih dan responsif dengan Tailwind CSS  
✅ **Validasi Data** - Validasi sebelah pelayan untuk semua form  
✅ **Pengendalian Ralat** - Paparan ralat yang anggun dan keadaan kosong  
✅ **Pengalaman Pengguna** - Dialog pengesahan dan mesej kejayaan  
