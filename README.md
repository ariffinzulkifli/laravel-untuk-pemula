# laravel-untuk-pemula

## Make Model Category
```bash
php artisan make:model Category -m
```
In categories migration file add
```php
$table->string('name');
```
```bash
php artisan migrate
```
Adminer: in categories table add new items `name`, both `created_at` and `updated_at` are set to **now**:
1. Tailwind CSS
2. Responsive Design
3. Web Performance

## Make Model Blog
```bash
php artisan make:model Blog -mcr
```
In blogs migration file add
```php
$table->string('title');
$table->text('text');
$table->string('author');
$table->foreignId('category_id')->constrained();
```

```bash
php artisan migrate
```

Adminer: add new item, both `created_at` and `updated_at` are set to **now**
1. `title`
```
Understanding Tailwind CSS v4
```
2. `text`
```html
<article class="prose max-w-none prose-img:rounded-md">
    <p>Tailwind CSS v4 has officially arrived and it brings a host of improvements that promise faster development, cleaner utilities, and a more customizable design experience.</p>
    <p>With this latest version, the core team has focused on making the developer experience even smoother. One of the most noticeable changes is the removal of legacy browser support, allowing for more modern CSS features and a smaller core library.</p>

    <h2>Utility-First, But Smarter</h2>
    <p>Tailwind continues to embrace its utility-first philosophy, but with smarter defaults and improved flexibility. Utilities are now more consistent and easier to compose, thanks to enhanced configuration support and plugin capabilities.</p>

    <h2>Performance Upgrades</h2>
    <p>You’ll notice snappier build times and smaller final CSS files thanks to optimizations in how Tailwind processes your HTML and class usage. The Just-In-Time (JIT) engine, introduced in v3, is now the default and even more refined in v4.</p>

    <blockquote>
        “Tailwind CSS v4 is a true leap forward. It gives developers the power to build beautiful UIs without writing custom CSS.” — A Frontend Developer
    </blockquote>

    <p>Whether you're working on a personal blog, a SaaS dashboard, or an eCommerce platform, Tailwind v4 will make your workflow cleaner and faster. Try it out today!</p>
</article>
```
3. `author`
```
Amir Hamzah
```
4. `category_id`
```
1
```

## Add Blog Resources to Routes
```php
Route::resource('blog', BlogController::class);
```
Add
```php
use App\Http\Controllers\BlogController;
```

## Blog Post List
resources/views/blog/index.blade.php
```html
@extends('layouts.blog')

@section('post')
<!-- Blog Post Item -->
{{-- @foreach ($posts as $post)
<article class="flex mb-8 space-x-6">
    <img src="https://placehold.co/150" alt="Blog Post 1" class="w-[150px] h-[150px] object-cover rounded-md flex-shrink-0" />
    <div>
        <h3 class="text-xl font-semibold mb-2 hover:text-blue-600 cursor-pointer"><a href="{{ route('blog.show', $post) }}">{{ $post->title }}</a></h3>
        <p class="text-gray-700 line-clamp-3">
            Tailwind CSS v4 introduces new utilities and improvements to help you build faster and cleaner designs with ease.
        </p>
    </div>
</article>
@endforeach --}}
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
        <h3 class="text-xl font-semibold mb-2 hover:text-blue-600 cursor-pointer">No post available.</h3>
        <p class="text-gray-700 line-clamp-3">
            Tailwind CSS v4 introduces new utilities and improvements to help you build faster and cleaner designs with ease.
        </p>
    </div>
</article>
@endforelse
@endsection
```

app/Http/Controllers/BlogController.php
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

## Blog Post Show
resources/views/blog/show.blade.php
```html
@extends('layouts.utama')

@section('kandungan')
<!-- Main Content -->
<main class="flex-grow px-6 py-10 max-w-3xl mx-auto">
    <!-- Blog Title -->
    <h1 class="text-4xl font-bold mb-4">{{ $post->title }}</h1>

    <!-- Meta Info -->
    <div class="text-sm text-gray-500 mb-6">
    By <span class="text-gray-700 font-medium">{{ $post->author }}</span> &bull; Published on <span>{{ \Carbon\Carbon::parse($post->created_at)->format('d F Y') }}</span>
    </div>

    <!-- Featured Image -->
    <img src="https://placehold.co/800x400" alt="Featured image" class="w-full h-auto rounded-md mb-8" />

    <!-- Blog Post Body -->
    {!! $post->text !!}
</main>
@endsection
```
app/Http/Controllers/BlogController.php
```php
public function show(Blog $blog)
{
    $post = $blog;
    return view('blog.show', compact('post'));
}
```

## Blog Post Create
```html
```

app/Http/Controllers/BlogController.php
```php
```

## Blog Post Update
```html
```

app/Http/Controllers/BlogController.php
```php
```

## Blog Post Delete
```html
```

app/Http/Controllers/BlogController.php
```php
```
