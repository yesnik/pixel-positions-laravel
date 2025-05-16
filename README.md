# Pixel Positions Laravel

This app was created with tutorial "30 days to Learn Laravel", episodes 27 - 30:
https://laracasts.com/series/30-days-to-learn-laravel-11/episodes/27

# 27. From Design to Blade

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