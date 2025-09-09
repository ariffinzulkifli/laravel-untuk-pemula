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
