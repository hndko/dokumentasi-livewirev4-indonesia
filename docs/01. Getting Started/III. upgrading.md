# Panduan Upgrade

[Mulai belajar di Laracasts](https://laracasts.com/series/everything-new-in-livewire-4)

Livewire v4 memperkenalkan beberapa peningkatan dan optimasi sambil mempertahankan kompatibilitas mundur sebisa mungkin. Panduan ini akan membantu Anda melakukan upgrade dari Livewire v3 ke v4.

Sebagian besar aplikasi dapat melakukan upgrade ke v4 dengan perubahan minimal. Breaking changes utamanya adalah pembaruan konfigurasi dan perubahan signature method yang hanya mempengaruhi penggunaan lanjutan.

> **Tips:** Ingin menghemat waktu? Anda dapat menggunakan [Laravel Shift](https://laravelshift.com) untuk membantu mengautomasi upgrade aplikasi Anda.

---

## Daftar Isi

- [Instalasi](#instalasi)
- [Perubahan Dampak Tinggi](#perubahan-dampak-tinggi)
  - [Update File Konfigurasi](#update-file-konfigurasi)
  - [Perubahan Routing](#perubahan-routing)
  - [wire:model Sekarang Mengabaikan Event Child secara Default](#wiremodel-sekarang-mengabaikan-event-child-secara-default)
  - [Gunakan wire:navigate:scroll](#gunakan-wirenavigatescroll)
  - [Tag Komponen Harus Ditutup](#tag-komponen-harus-ditutup)
- [Perubahan Dampak Sedang](#perubahan-dampak-sedang)
  - [wire:transition Sekarang Menggunakan View Transitions API](#wiretransition-sekarang-menggunakan-view-transitions-api)
  - [Peningkatan Performa](#peningkatan-performa)
  - [Update Hooks Mengkonsolidasi Perubahan Array/Object](#update-hooks-mengkonsolidasi-perubahan-arrayobject)
  - [Perubahan Signature Method](#perubahan-signature-method)
- [Perubahan Dampak Rendah](#perubahan-dampak-rendah)
  - [Deprecation JavaScript](#deprecation-javascript)
- [Upgrade Volt](#upgrade-volt)
- [Fitur Baru di v4](#fitur-baru-di-v4)
- [Mendapatkan Bantuan](#mendapatkan-bantuan)

---

## Instalasi

Update `composer.json` Anda untuk require Livewire v4:

```bash
composer require livewire/livewire:^4.0
```

Setelah update, bersihkan cache aplikasi Anda:

```bash
php artisan optimize:clear
```

Untuk gambaran lengkap semua perubahan kode antara v3 dan v4, Anda dapat mereview diff lengkap di GitHub: [Compare 3.x to main →](https://github.com/livewire/livewire/compare/3.x...main)

---

# Perubahan Dampak Tinggi

Perubahan-perubahan ini paling mungkin mempengaruhi aplikasi Anda dan harus ditinjau dengan teliti.

---

## Update File Konfigurasi

Beberapa key konfigurasi telah diganti nama, direorganisasi, atau memiliki default baru. Update file `config/livewire.php` Anda:

Untuk referensi, Anda dapat melihat file config v4 lengkap di GitHub: [livewire.php →](https://github.com/livewire/livewire/blob/main/config/livewire.php)

### Key Konfigurasi yang Diganti Nama

**Konfigurasi Layout:**

```php
// Sebelum (v3)
'layout' => 'components.layouts.app',

// Sesudah (v4)
'component_layout' => 'layouts::app',
```

Layout sekarang menggunakan namespace `layouts::` secara default, menunjuk ke `resources/views/layouts/app.blade.php`.

**Konfigurasi Placeholder:**

```php
// Sebelum (v3)
'lazy_placeholder' => 'livewire.placeholder',

// Sesudah (v4)
'component_placeholder' => 'livewire.placeholder',
```

### Default yang Berubah

**Perilaku smart wire:key:**

```php
// Sekarang default ke true (sebelumnya false di v3)
'smart_wire_keys' => true,
```

Ini membantu mencegah masalah `wire:key` pada komponen yang bersarang dalam. **Catatan:** Anda tetap perlu menambahkan `wire:key` secara manual dalam loop — pengaturan ini tidak menghilangkan kebutuhan tersebut.

[Pelajari lebih lanjut tentang wire:key →](https://livewire.laravel.com/docs/4.x/nesting#rendering-children-in-a-loop)

### Opsi Konfigurasi Baru

**Lokasi Komponen:**

```php
'component_locations' => [
    resource_path('views/components'),
    resource_path('views/livewire'),
],
```

Mendefinisikan di mana Livewire mencari komponen single-file dan multi-file (berbasis view).

**Namespace Komponen:**

```php
'component_namespaces' => [
    'layouts' => resource_path('views/layouts'),
    'pages' => resource_path('views/pages'),
],
```

Membuat namespace kustom untuk mengorganisir komponen berbasis view (contoh: `<livewire:pages::dashboard />`).

**Default Perintah Make:**

```php
'make_command' => [
    'type' => 'sfc',      // Opsi: 'sfc', 'mfc', atau 'class'
    'emoji' => true,      // Apakah menggunakan prefix emoji ⚡
],
```

Konfigurasikan format komponen default dan penggunaan emoji. Set `type` ke `'class'` untuk mencocokkan perilaku v3.

**Mode CSP-safe:**

```php
'csp_safe' => false,
```

Aktifkan mode Content Security Policy untuk menghindari pelanggaran `unsafe-eval`. Saat diaktifkan, Livewire menggunakan [Alpine CSP build](https://alpinejs.dev/advanced/csp). **Catatan:** Mode ini membatasi ekspresi JavaScript kompleks dalam direktif seperti `wire:click="addToCart($event.detail.productId)"` atau referensi global seperti `window.location`.

---

## Perubahan Routing

Untuk komponen halaman penuh, pendekatan routing yang direkomendasikan telah berubah:

```php
// Sebelum (v3) - masih bekerja tapi tidak direkomendasikan
Route::get('/dashboard', Dashboard::class);

// Sesudah (v4) - direkomendasikan untuk semua tipe komponen
Route::livewire('/dashboard', Dashboard::class);

// Untuk komponen berbasis view, Anda dapat menggunakan nama komponen
Route::livewire('/dashboard', 'pages::dashboard');
```

Menggunakan `Route::livewire()` sekarang adalah metode yang lebih disukai dan **diperlukan** agar komponen single-file dan multi-file bekerja dengan benar sebagai komponen halaman penuh.

[Pelajari lebih lanjut tentang routing →](https://livewire.laravel.com/docs/4.x/components#page-components)

---

## wire:model Sekarang Mengabaikan Event Child secara Default

Di v3, `wire:model` akan merespon event input/change yang bubble up dari elemen child. Ini menyebabkan perilaku tidak terduga saat menggunakan `wire:model` pada elemen container (seperti modal atau accordion) yang berisi input form — membersihkan input di dalamnya akan bubble up dan berpotensi menutup modal.

Di v4, `wire:model` sekarang hanya mendengarkan event yang berasal langsung dari elemen itu sendiri (setara dengan perilaku modifier `.self`).

Jika Anda memiliki kode yang bergantung pada menangkap event dari elemen child, tambahkan modifier `.deep`:

```html
<!-- Sebelum (v3) - mendengarkan event child secara default -->
<div wire:model="value">
  <input type="text" />
</div>

<!-- Sesudah (v4) - tambahkan .deep untuk mengembalikan perilaku lama -->
<div wire:model.deep="value">
  <input type="text" />
</div>
```

Perubahan ini terutama mempengaruhi penggunaan non-standar `wire:model` pada elemen container. Binding input form standar (input, select, textarea) tidak terpengaruh.

---

## Gunakan wire:navigate:scroll

Saat menggunakan `wire:scroll` untuk mempertahankan scroll dalam container yang dapat di-scroll pada request `wire:navigate` di v3, Anda perlu menggunakan `wire:navigate:scroll` di v4:

```html
@persist('sidebar') -
<div class="overflow-y-scroll" wire:scroll>
  +
  <div class="overflow-y-scroll" wire:navigate:scroll>
    <!-- ... -->
  </div>
  @endpersist
</div>
```

---

## Tag Komponen Harus Ditutup

Di v3, tag komponen Livewire akan merender bahkan tanpa ditutup dengan benar. Di v4, dengan penambahan dukungan slot, tag komponen harus ditutup dengan benar — jika tidak Livewire menginterpretasikan konten berikutnya sebagai konten slot dan komponen tidak akan merender:

```html
<!-- Sebelum (v3) - tag tidak ditutup -->
<livewire:component-name>
  <!-- Sesudah (v4) - Tag self-closing -->
  <livewire:component-name
/></livewire:component-name>
```

[Pelajari lebih lanjut tentang merender komponen →](https://livewire.laravel.com/docs/4.x/components#rendering-components)
[Pelajari lebih lanjut tentang slot →](https://livewire.laravel.com/docs/4.x/nesting#slots)

---

# Perubahan Dampak Sedang

Perubahan-perubahan ini mungkin mempengaruhi bagian tertentu dari aplikasi Anda tergantung pada fitur mana yang Anda gunakan.

---

## wire:transition Sekarang Menggunakan View Transitions API

Di v3, `wire:transition` adalah wrapper di sekitar direktif `x-transition` Alpine, mendukung modifier seperti `.opacity`, `.scale`, `.duration.200ms`, dan `.origin.top`.

Di v4, `wire:transition` menggunakan [View Transitions API](https://developer.mozilla.org/en-US/docs/Web/API/View_Transitions_API) native browser sebagai gantinya. Penggunaan dasar masih bekerja — elemen akan fade in dan out secara smooth — tetapi **semua modifier telah dihapus**.

```html
<!-- Ini masih bekerja di v4 -->
<div wire:transition>...</div>

<!-- Modifier ini tidak lagi didukung -->
-
<div wire:transition.opacity>...</div>
-
<div wire:transition.scale.origin.top>...</div>
-
<div wire:transition.duration.500ms>...</div>
```

[Pelajari lebih lanjut tentang wire:transition →](https://livewire.laravel.com/docs/4.x/wire-transition)

---

## Peningkatan Performa

Livewire v4 mencakup peningkatan performa signifikan pada sistem penanganan request:

- **Non-blocking polling:** `wire:poll` tidak lagi memblokir request lain atau diblokir oleh request lain
- **Parallel live updates:** Request `wire:model.live` sekarang berjalan secara paralel, memungkinkan pengetikan lebih cepat dan hasil lebih cepat

Peningkatan-peningkatan ini terjadi secara otomatis — tidak diperlukan perubahan pada kode Anda.

---

## Update Hooks Mengkonsolidasi Perubahan Array/Object

Saat mengganti seluruh array atau object dari frontend (contoh: `$wire.items = ['new', 'values']`), Livewire sekarang mengirim satu update terkonsolidasi alih-alih update granular untuk setiap indeks.

**Sebelum:** Setting `$wire.items = ['a', 'b']` pada array dengan 4 item akan memicu hooks `updatingItems`/`updatedItems` beberapa kali — sekali untuk setiap perubahan indeks plus penghapusan `__rm__`.

**Sesudah:** Operasi yang sama memicu hooks sekali dengan nilai array penuh yang baru, mencocokkan perilaku v2.

Jika kode Anda bergantung pada hooks indeks individual yang dijalankan saat mengganti seluruh array, Anda mungkin perlu menyesuaikan. Perubahan item tunggal (seperti `wire:model="items.0"`) masih memicu hooks granular seperti yang diharapkan.

---

## Perubahan Signature Method

Jika Anda memperluas fungsionalitas inti Livewire atau menggunakan method-method ini secara langsung, perhatikan perubahan signature ini:

### Streaming

Parameter method `stream()` urutannya telah berubah:

```php
// Sebelum (v3)
$this->stream(to: '#container', content: 'Hello', replace: true);

// Sesudah (v4)
$this->stream(content: 'Hello', replace: true, el: '#container');
```

Jika Anda menggunakan parameter bernama, perhatikan bahwa `to:` telah diganti nama menjadi `el:`. Jika Anda menggunakan parameter posisional:

```php
// Sebelum (v3) - parameter posisional
$this->stream('#container', 'Hello');

// Sesudah (v4) - parameter posisional/bernama
$this->stream('Hello', el: '#container');
```

[Pelajari lebih lanjut tentang streaming →](https://livewire.laravel.com/docs/4.x/wire-stream)

### Component Mounting (internal)

Jika Anda memperluas `LivewireManager` atau memanggil method `mount()` secara langsung:

```php
// Sebelum (v3)
public function mount($name, $params = [], $key = null)

// Sesudah (v4)
public function mount($name, $params = [], $key = null, $slots = [])
```

Perubahan ini menambahkan dukungan untuk passing slot saat mounting komponen dan umumnya tidak akan mempengaruhi sebagian besar aplikasi.

---

# Perubahan Dampak Rendah

Perubahan-perubahan ini hanya mempengaruhi aplikasi yang menggunakan fitur lanjutan atau kustomisasi.

---

## Deprecation JavaScript

### Deprecated: method $wire.$js()

Method `$wire.$js()` untuk mendefinisikan aksi JavaScript telah deprecated:

```javascript
// Deprecated (v3)
$wire.$js("bookmark", () => {
  // Toggle bookmark...
});

// Baru (v4)
$wire.$js.bookmark = () => {
  // Toggle bookmark...
};
```

Syntax baru lebih bersih dan lebih intuitif.

### Deprecated: $js tanpa prefix

Penggunaan `$js` dalam script tanpa prefix `$wire.$js` atau `this.$js` telah deprecated:

```javascript
// Deprecated (v3)
$js("bookmark", () => {
  // Toggle bookmark...
});

// Baru (v4)
$wire.$js.bookmark = () => {
  // Toggle bookmark...
};
// Atau
this.$js.bookmark = () => {
  // Toggle bookmark...
};
```

Baik `$wire.$js('bookmark', ...)` maupun `$js('bookmark', ...)` akan tetap bekerja di v4 untuk kompatibilitas mundur, tetapi Anda harus bermigrasi ke syntax baru saat nyaman.

### Deprecated: Hooks commit dan request

Hooks `commit` dan `request` telah deprecated demi sistem interceptor baru yang menyediakan kontrol lebih granular dan performa lebih baik.

Hooks yang deprecated akan tetap bekerja di v4 untuk kompatibilitas mundur, tetapi Anda harus bermigrasi ke sistem baru saat nyaman.

**Migrasi dari hook commit:**

```javascript
// LAMA - Deprecated
Livewire.hook("commit", ({ component, commit, respond, succeed, fail }) => {
  respond(() => {
    /* Berjalan setelah response diterima */
  });
  succeed(({ snapshot, effects }) => {
    /* Berjalan setelah response sukses */
  });
  fail(() => {
    /* Berjalan jika request gagal */
  });
});

// BARU - Direkomendasikan
Livewire.interceptMessage(
  ({ component, message, onFinish, onSuccess, onError, onFailure }) => {
    onFinish(() => {
      /* Setara dengan respond() */
    });
    onSuccess(({ payload }) => {
      /* Setara dengan succeed() */
      /* Akses snapshot via payload.snapshot */
      /* Akses effects via payload.effects */
    });
    onError(() => {
      /* Setara dengan fail() untuk error server */
    });
    onFailure(() => {
      /* Setara dengan fail() untuk error jaringan */
    });
  },
);
```

**Migrasi dari hook request:**

```javascript
// LAMA - Deprecated
Livewire.hook(
  "request",
  ({ url, options, payload, respond, succeed, fail }) => {
    respond(({ status, response }) => {
      /* Berjalan saat response diterima */
    });
    succeed(({ status, json }) => {
      /* Berjalan pada response sukses */
    });
    fail(({ status, content, preventDefault }) => {
      /* Berjalan pada response gagal */
    });
  },
);

// BARU - Direkomendasikan
Livewire.interceptRequest(
  ({ request, onResponse, onSuccess, onError, onFailure }) => {
    onResponse(({ response }) => {
      /* Setara dengan respond() */
    });
    onSuccess(({ response, responseJson }) => {
      /* Setara dengan succeed() */
    });
    onError(({ response, responseBody, preventDefault }) => {
      /* Setara dengan fail() untuk error server */
    });
    onFailure(({ error }) => {
      /* Setara dengan fail() untuk error jaringan */
    });
  },
);
```

### Perbedaan Kunci

1. **Penanganan error lebih granular:** Sistem baru memisahkan kegagalan jaringan (`onFailure`) dari error server (`onError`)
2. **Lifecycle hooks lebih baik:** Message interceptor menyediakan hooks tambahan seperti `onSync`, `onMorph`, dan `onRender`
3. **Dukungan pembatalan:** Baik message maupun request dapat dibatalkan/diabort
4. **Component scoping:** Message interceptor dapat di-scope ke komponen tertentu menggunakan `$wire.intercept(...)`

Untuk dokumentasi lengkap tentang sistem interceptor baru, lihat [dokun JavaScript Interceptors](https://livewire.laravel.com/docs/4.x/javascript#interceptors).

---

# Upgrade Volt

Livewire v4 sekarang mendukung komponen single-file, yang menggunakan syntax yang sama dengan komponen berbasis class Volt. Ini berarti Anda dapat bermigrasi dari Volt ke komponen single-file bawaan Livewire.

## Update Import Komponen

Ganti semua instance `Livewire\Volt\Component` dengan `Livewire\Component`:

```php
// Sebelum (Volt)
use Livewire\Volt\Component;

new class extends Component { ... }

// Sesudah (Livewire v4)
use Livewire\Component;

new class extends Component { ... }
```

## Update Definisi Route

Ganti `Volt::route()` dengan `Route::livewire()` di file routes Anda:

```php
// Sebelum (Volt)
use Livewire\Volt\Volt;

Volt::route('/dashboard', 'dashboard');

// Sesudah (Livewire v4)
use Illuminate\Support\Facades\Route;

Route::livewire('/dashboard', 'dashboard');
```

## Update File Test

Ganti semua instance `Livewire\Volt\Volt` dengan `Livewire\Livewire` dan ubah `Volt::test()` menjadi `Livewire::test()`:

```php
// Sebelum (Volt)
use Livewire\Volt\Volt;

Volt::test('counter')

// Sesudah (Livewire v4)
use Livewire\Livewire;

Livewire::test('counter')
```

## Hapus Volt Service Provider

Hapus file Volt service provider:

```bash
rm app/Providers/VoltServiceProvider.php
```

Kemudian hapus dari array providers di `bootstrap/providers.php`:

```php
// Sebelum
return [
    App\Providers\AppServiceProvider::class,
    App\Providers\VoltServiceProvider::class,
];

// Sesudah
return [
    App\Providers\AppServiceProvider::class,
];
```

## Hapus Package Volt

Uninstall package Volt:

```bash
composer remove livewire/volt
```

## Install Livewire v4

Setelah menyelesaikan perubahan di atas, install Livewire v4. Komponen berbasis class Volt yang ada akan bekerja tanpa modifikasi karena menggunakan syntax yang sama dengan komponen single-file Livewire.

---

# Fitur Baru di v4

Livewire v4 memperkenalkan beberapa fitur powerful baru yang dapat Anda mulai gunakan segera:

## Fitur Komponen

### Komponen Single-file dan Multi-file

v4 memperkenalkan format komponen baru di samping pendekatan berbasis class tradisional. Komponen single-file menggabungkan PHP dan Blade dalam satu file, sementara komponen multi-file mengorganisir PHP, Blade, JavaScript, dan test dalam direktori.

Secara default, file komponen berbasis view diberi prefix emoji ⚡ untuk membedakannya dari file Blade biasa di editor dan pencarian Anda. Ini dapat dinonaktifkan via config `make_command.emoji`.

```bash
php artisan make:livewire create-post           # Single-file (default)
php artisan make:livewire create-post --mfc     # Multi-file
php artisan livewire:convert create-post        # Convert antar format
```

[Pelajari lebih lanjut tentang format komponen →](https://livewire.laravel.com/docs/4.x/components)

### Slot dan Attribute Forwarding

Komponen sekarang mendukung slot dan attribute bag forwarding otomatis menggunakan `{{ $attributes }}`, membuat komposisi komponen lebih fleksibel.

[Pelajari lebih lanjut tentang nesting komponen →](https://livewire.laravel.com/docs/4.x/nesting)

### JavaScript dalam Komponen Berbasis View

Komponen berbasis view sekarang dapat menyertakan tag `<script>` tanpa wrapper `@script`. Script-script ini disajikan sebagai file cached terpisah untuk performa lebih baik dan binding `$wire` otomatis:

```html
<div>
  <!-- Your component template -->
</div>

<script>
  // $wire secara otomatis di-bind sebagai 'this'
  this.count++; // Sama dengan $wire.count++

  // $wire masih tersedia jika lebih disukai
  $wire.save();
</script>
```

[Pelajari lebih lanjut tentang JavaScript dalam komponen →](https://livewire.laravel.com/docs/4.x/javascript)

## Islands

Islands memungkinkan Anda membuat region terisolasi dalam komponen yang update secara independen, meningkatkan performa secara dramatis tanpa membuat komponen child terpisah.

```html
@island(name: 'stats', lazy: true)
<div>{{ $this->expensiveStats }}</div>
@endisland
```

[Pelajari lebih lanjut tentang islands →](https://livewire.laravel.com/docs/4.x/islands)

## Peningkatan Loading

### Deferred Loading

Selain lazy loading (berbasis viewport), komponen sekarang dapat di-defer untuk load segera setelah page load awal:

```html
<livewire:revenue defer />
```

```php
#[Defer]
class Revenue extends Component { ... }
```

### Bundled Loading

Kontrol apakah beberapa komponen lazy/deferred load secara paralel atau dibundle bersama:

```html
<livewire:revenue lazy.bundle /> <livewire:expenses defer.bundle />
```

```php
#[Lazy(bundle: true)]
class Revenue extends Component { ... }
```

[Pelajari lebih lanjut tentang lazy dan deferred loading →](https://livewire.laravel.com/docs/4.x/lazy)

## Async Actions

Jalankan aksi secara paralel tanpa memblokir request lain menggunakan modifier `.async` atau atribut `#[Async]`:

```html
<button wire:click.async="logActivity">Track</button>
```

```php
#[Async]
public function logActivity() { ... }
```

[Pelajari lebih lanjut tentang async actions →](https://livewire.laravel.com/docs/4.x/actions#parallel-execution-with-async)

## Direktif dan Modifier Baru

### wire:sort - Drag-and-drop Sorting

Dukungan bawaan untuk list yang dapat disort dengan drag-and-drop:

```html
<ul wire:sort="updateOrder">
  @foreach ($items as $item)
  <li wire:sort:item="{{ $item->id }}" wire:key="{{ $item->id }}">
    {{ $item->name }}
  </li>
  @endforeach
</ul>
```

[Pelajari lebih lanjut tentang wire:sort →](https://livewire.laravel.com/docs/4.x/wire-sort)

### wire:intersect - Viewport Intersection

Jalankan aksi saat elemen masuk atau keluar viewport:

```html
<!-- Penggunaan dasar -->
<div wire:intersect="loadMore">...</div>

<!-- Dengan modifier -->
<div wire:intersect.once="trackView">...</div>
<div wire:intersect:leave="pauseVideo">...</div>
<div wire:intersect.half="loadMore">...</div>
<div wire:intersect.full="startAnimation">...</div>

<!-- Dengan opsi -->
<div wire:intersect.margin.200px="loadMore">...</div>
<div wire:intersect.threshold.50="trackScroll">...</div>
```

Modifier yang tersedia:

- `.once` - Fire hanya sekali
- `.half` - Tunggu sampai setengah visible
- `.full` - Tunggu sampai fully visible
- `.threshold.X` - Persentase visibilitas kustom (0-100)
- `.margin.Xpx` atau `.margin.X%` - Intersection margin

[Pelajari lebih lanjut tentang wire:intersect →](https://livewire.laravel.com/docs/4.x/wire-intersect)

### wire:ref - Element References

Mudah referensi dan berinteraksi dengan elemen di template Anda:

```html
<div wire:ref="modal">
  <!-- Modal content -->
</div>

<button wire:click="$js.scrollToModal">Scroll to modal</button>

<script>
  this.$js.scrollToModal = () => {
    this.$refs.modal.scrollIntoView();
  };
</script>
```

[Pelajari lebih lanjut tentang wire:ref →](https://livewire.laravel.com/docs/4.x/wire-ref)

### Modifier .renderless

Skip re-rendering komponen langsung dari template:

```html
<button wire:click.renderless="trackClick">Track</button>
```

Ini adalah alternatif untuk atribut `#[Renderless]` untuk aksi yang tidak perlu update UI.

### Modifier .preserve-scroll

Pertahankan posisi scroll selama update untuk mencegah layout jumps:

```html
<button wire:click.preserve-scroll="loadMore">Load More</button>
```

### Atribut data-loading

Setiap elemen yang memicu network request secara otomatis menerima atribut `data-loading`, membuatnya mudah untuk styling loading states dengan Tailwind:

```html
<button
  wire:click="save"
  class="data-loading:opacity-50 data-loading:pointer-events-none"
>
  Save Changes
</button>
```

[Pelajari lebih lanjut tentang loading states →](https://livewire.laravel.com/docs/4.x/loading-states)

## Peningkatan JavaScript

### Magic Property $errors

Akses error bag komponen Anda dari JavaScript:

```html
<div wire:show="$errors.has('email')">
  <span wire:text="$errors.first('email')"></span>
</div>
```

[Pelajari lebih lanjut tentang validasi →](https://livewire.laravel.com/docs/4.x/validation)

### Magic $intercept

Intercept dan modifikasi request Livewire dari JavaScript:

```html
<script>
  this.$intercept('save', ({ ... }) => {
      // ...
  })
</script>
```

[Pelajari lebih lanjut tentang JavaScript interceptors →](https://livewire.laravel.com/docs/4.x/javascript#interceptors)

### Island Targeting dari JavaScript

Trigger render island langsung dari template:

```html
<button wire:click="loadMore" wire:island.append="stats">Load more</button>
```

[Pelajari lebih lanjut tentang islands →](https://livewire.laravel.com/docs/4.x/islands)

---

# Mendapatkan Bantuan

Jika Anda mengalami masalah selama upgrade:

- Periksa [dokumentasi](https://livewire.laravel.com) untuk panduan fitur detail
- Kunjungi [GitHub discussions](https://github.com/livewire/livewire/discussions) untuk dukungan komunitas
