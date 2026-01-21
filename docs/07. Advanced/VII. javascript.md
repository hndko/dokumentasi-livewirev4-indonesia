# JavaScript

Panduan lengkap untuk menggunakan JavaScript dengan Livewire, termasuk objek `$wire`, interceptors, dan hooks.

---

## Daftar Isi

- [Menggunakan JavaScript di Komponen](#menggunakan-javascript-di-komponen)
- [Objek $wire](#objek-wire)
- [Interceptors](#interceptors)
- [Global Livewire Events](#global-livewire-events)
- [JavaScript Hooks](#javascript-hooks)
- [Common Patterns](#common-patterns)
- [Best Practices](#best-practices)

---

## Menggunakan JavaScript di Komponen

### Executing Scripts

```html
<div>
  @script
  <script>
    console.log("Component initialized");
  </script>
  @endscript
</div>
```

### Menggunakan $wire dari Scripts

```html
@script
<script>
  $wire.on("saved", () => {
    alert("Data saved!");
  });
</script>
@endscript
```

---

## Objek $wire

`$wire` adalah proxy JavaScript untuk komponen Livewire:

```javascript
// Mengakses properti
$wire.count;

// Memanggil method
$wire.increment();

// Menetapkan properti
$wire.count = 5;

// Mendengarkan events
$wire.on("saved", callback);

// Mendapatkan ID komponen
$wire.id;

// Mendapatkan snapshot
$wire.snapshot;
```

---

## Interceptors

### Action Interceptors

```javascript
Livewire.intercept("action", (action, next) => {
  console.log("Before action:", action.method);
  return next(action);
});
```

### Message Interceptors

```javascript
Livewire.intercept("message", (message, next) => {
  console.log("Sending:", message);
  return next(message);
});
```

### Request Interceptors

```javascript
Livewire.intercept("request", (request, next) => {
  request.headers["X-Custom"] = "value";
  return next(request);
});
```

---

## Global Livewire Events

```javascript
Livewire.on("component.init", (component) => {
  console.log("Component initialized:", component.id);
});

Livewire.on("component.updated", (component) => {
  console.log("Component updated:", component.id);
});
```

---

## JavaScript Hooks

### Component Initialization

```javascript
Livewire.hook("component.init", ({ component }) => {
  // Dipanggil saat komponen diinisialisasi
});
```

### DOM Element Initialization

```javascript
Livewire.hook("element.init", ({ el, component }) => {
  // Dipanggil untuk setiap elemen
});
```

### DOM Morph Hooks

```javascript
Livewire.hook("morph.updated", ({ el }) => {
  // Dipanggil setelah DOM dimorph
});
```

---

## Common Patterns

### Integrating Third-party Libraries

```html
@script
<script>
  let chart = new Chart($wire.$el.querySelector("canvas"), {
    // config
  });

  $wire.on("data-updated", (data) => {
    chart.data = data;
    chart.update();
  });
</script>
@endscript
```

---

## Best Practices

1. Gunakan `@script` untuk scripts spesifik komponen
2. Bersihkan event listeners saat komponen dihapus
3. Hindari menyimpan state di JavaScript - gunakan Livewire
4. Gunakan `$wire` untuk komunikasi dengan server
