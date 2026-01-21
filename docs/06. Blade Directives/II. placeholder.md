# @placeholder

Direktif `@placeholder` mendefinisikan konten yang ditampilkan saat komponen lazy atau island sedang dimuat.

---

## Daftar Isi

- [Penggunaan dengan Lazy Components](#penggunaan-dengan-lazy-components)
- [Penggunaan dengan Islands](#penggunaan-dengan-islands)
- [Skeleton Placeholders](#skeleton-placeholders)

---

## Penggunaan dengan Lazy Components

```html
<div>
  @placeholder
  <div class="animate-pulse">
    <div class="h-4 bg-gray-200 rounded w-3/4 mb-2"></div>
    <div class="h-4 bg-gray-200 rounded w-1/2"></div>
  </div>
  @endplaceholder

  <!-- Konten sebenarnya -->
  <h1>{{ $title }}</h1>
  <p>{{ $content }}</p>
</div>
```

---

## Penggunaan dengan Islands

```html
@island(lazy: true) @placeholder
<div class="animate-pulse bg-gray-200 h-32 rounded"></div>
@endplaceholder

<div>Revenue: {{ $this->revenue }}</div>
@endisland
```

---

## Skeleton Placeholders

Contoh skeleton yang lebih detail:

```html
@placeholder
<div class="animate-pulse space-y-4">
  <!-- Avatar + Name -->
  <div class="flex items-center space-x-3">
    <div class="w-10 h-10 bg-gray-200 rounded-full"></div>
    <div class="h-4 bg-gray-200 rounded w-24"></div>
  </div>

  <!-- Content lines -->
  <div class="space-y-2">
    <div class="h-4 bg-gray-200 rounded w-full"></div>
    <div class="h-4 bg-gray-200 rounded w-5/6"></div>
    <div class="h-4 bg-gray-200 rounded w-4/6"></div>
  </div>

  <!-- Button -->
  <div class="h-10 bg-gray-200 rounded w-32"></div>
</div>
@endplaceholder
```
