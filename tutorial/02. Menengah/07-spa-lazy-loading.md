# Step 7: SPA Navigation & Lazy Loading

## 🎯 Tujuan

- Menggunakan wire:navigate untuk SPA-like experience
- Persist elements across navigation

---

## 📝 Langkah 7.1: wire:navigate

Tambahkan `wire:navigate` pada link:

```html
<a href="/posts/{{ $post->slug }}" wire:navigate> {{ $post->title }} </a>
```

Ini memberikan:

- ✅ Tanpa full page reload
- ✅ Prefetch saat hover
- ✅ Loading indicator

---

## 📝 Langkah 7.2: Loading Progress Bar

Update layout dengan progress bar:

```html
<head>
  <style>
    [wire\:loading] {
      display: none;
    }
    [wire\:navigate] {
      cursor: pointer;
    }
  </style>
</head>
<body>
  <!-- Progress bar -->
  <div
    x-data="{ loading: false }"
    x-on:livewire:navigating.window="loading = true"
    x-on:livewire:navigated.window="loading = false"
  >
    <div
      x-show="loading"
      class="fixed top-0 left-0 h-1 bg-blue-500 transition-all"
      x-transition
      style="width: 100%"
    ></div>
  </div>
</body>
```

---

## 📝 Langkah 7.3: @persist untuk Audio Player (contoh)

```html
@persist('player')
<audio id="player" src="/music.mp3" controls></audio>
@endpersist
```

Element akan tetap ada saat navigasi.

---

## 📝 Langkah 7.4: Lazy Loading Components

```html
<livewire:comment-section :post="$post" lazy />
```

Dengan placeholder:

```php
public static function placeholder()
{
    return '<div class="animate-pulse h-32 bg-gray-200 rounded"></div>';
}
```

---

## ✅ Checkpoint

- [x] SPA-like navigation dengan wire:navigate
- [x] Progress bar indicator
- [x] Lazy loading components

---

## ⬅️ [Step 6](06-events-realtime.md) | ➡️ [Step 8](08-testing-finalisasi.md)
