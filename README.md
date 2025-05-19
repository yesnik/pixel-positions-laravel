# Pixel Positions Laravel

This app was created with tutorial "30 days to Learn Laravel", episodes 27 - 30:
https://laracasts.com/series/30-days-to-learn-laravel-11/episodes/27

## 27. From Design to Blade

Create a template `resources\views\components\layout.blade.php`.

There we can use an image from `resources/images` folder:

```html
<img src="{{ Vite::asset('resources/images/logo.svg') }}" alt="">
```

For this we need to edit config `resources/js/app.js`:

```js
import.meta.glob([
    '../images/**'
]);
```

Run `npm run build` to build assets for production.

Run `npm run dev` for development.

We can change black color value - edit `resources/css/app.css`:

```css
@theme {
    --color-black: #060606;
}
```

Add white bottom border with 10% opacity: 

```html
<nav class="border-b border-white/10"></nav>
```

Add margin-top, max-width to a block:

```html
<main class="mt-10 max-w-[986px]"></main>
```
Add padding 4px, white background with 5% opacity:

```html
<div class="p-4 bg-white/5"></div>
```
Tag design:
- background: white, with opacity 10%
- on hover: bg 25%
- padding left, right 2rem
- padding top, bottom 1rem
- border rounded
- extra small text
- transition on colors

```html
<a href="" class="bg-white/10 hover:bg-white/25 px-2 py-1 rounded-xl text-xs transition-colors duration-300">Tag</a>
```

## 28. Blade and Tailwind Techniques for Your Laravel Views

### Add new font

Edit `resources/views/components/layout.blade.php`. Add font Hanken Grotesk in `<header>`:

```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Hanken+Grotesk:ital,wght@0,100..900;1,100..900&display=swap" rel="stylesheet">
```

Edit `resources/css/app.css`:

```css
@theme {
    --font-hanken-grotesk: 'Hanken Grotesk', sans-serif;
}
```
After this we can use font in a template `layout.blade.php`:

```html
<body class="font-hanken-grotesk">
</body>
```

### Add custom `.text-2xs`

Edit `resources/css/app.css`:

```css
@theme {
    --text-2xs: .625rem;
}
```

### Add hover group

Add class `.group` on the parent element. Then use this class to apply hover effect on the child element:

```html
<div class="hover:border-blue-800 group">
    <h3 class="group-hover:text-blue-600">Video Producer</h3>
</div>
```

### Variables in a component

File `views/components/panel.blade.php`:

```php
@php
    $classes = 'p-4 bg-white/5 rounded-xl border border-transparent hover:border-blue-800 group transition-colors duration-300';
@endphp

<div {{ $attributes(['class' => $classes]) }}>
    {{ $slot }}
</div>
```

Also we can add support of custom attribute `size` for a component:

```php
@props(['size' => 'base'])

@php
    $classes = 'bg-white/10 hover:bg-white/25 rounded-xl font-bold transition-colors duration-300';
    
    if ($size === 'base') {
        $classes .= " px-5 py-1 text-sm";
    }

    if ($size === 'small') {
        $classes .= " px-3 py-1 text-2xs";
    }
@endphp

<a href="#" class="{{ $classes }}">
    {{ $slot }}
</a>
```
Later we can use it:

```html
<x-tag size="small">Backend</x-tag>
```

### Input with white border

```html
<section class="text-center">
    <h1 class="font-bold text-4xl">Let's Find Your Next Job</h1>

    <form action="" class="mt-6">
        <input type="text" placeholder="Web Developer..." class="rounded-xl bg-white/20 border-white/10 px-5 py-4 w-full max-w-xl">
    </form>
</section>
```

## 29. Jobs, Tags, TDD, Oh My!

Generage a migration:

```
php artisan make:migration create_employers_table
```

To change table name for Laravel Jobs, edit `config/queue.php`:

```php
    'database' => [
        'table' => env('DB_QUEUE_TABLE', 'queued_jobs'),
    ],
    'batching' => [
        'table' => 'queued_job_batches',
    ],
    'failed' => [
        'table' => 'queued_failed_jobs',
    ],
```

Also rename table names in the default migration file `database\migrations\0001_01_01_000002_create_queued_jobs_table.php`.

Rerun migrations:

```
php artisan migrate:fresh
```

Generate a model, controller, factory, seeder, policy:

```
php artisan make:model Employer -cfs --policy
```
Generate factory, migration, seeder, request, controller, policy for a Job model:

```
php artisan make:model Job --all
```

### Unit Tests

Config Unit Tests, ensure `phpunit.xml` has these values:

```xml
    <env name="DB_CONNECTION" value="sqlite"/>
    <env name="DB_DATABASE" value=":memory:"/>
```

Generate a test file:

```
php artisan make:test JobTest --unit
```

Run tests:

```
php artisan test
```

To fix error `A facade root has not been set.` edit `tests/Pest.php`:

```php
pest()->extend(Tests\TestCase::class)
    ->use(Illuminate\Foundation\Testing\RefreshDatabase::class)
    ->in('Feature', 'Unit');
```

### Factory

We can generate unique tag in the factory:

```php
class TagFactory extends Factory
{
    /**
     * Define the model's default state.
     *
     * @return array<string, mixed>
     */
    public function definition(): array
    {
        return [
            'name' => fake()->unique()->name,
        ];
    }
}
```

### Migration for a user_tag pivot table

```php
Schema::create('job_tag', function (Blueprint $table) {
    $table->id();
    $table->foreignIdFor(Job::class)->constrained()->cascadeOnDelete();
    $table->foreignIdFor(Tag::class)->constrained()->cascadeOnDelete();
    $table->timestamps();
});
```

### Disable mass asignment feature

Edit `app\Providers\AppServiceProvider.php`:

```php
public function boot(): void
{
    Model::unguard();
}
```

### Display tags in the template

```php
@foreach ($tags as $tag)
    <x-tag :tag="$tag" />
@endforeach
```

If attribute name equals variable name we can use short shortcut:

```php
<x-tag :$tag />
```

### Group by attribute

```php
$jobs = Job::all()->groupBy('featured');
```
