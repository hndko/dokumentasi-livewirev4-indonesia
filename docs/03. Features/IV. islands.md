# Islands

[Mulai belajar di Laracasts](https://laracasts.com/series/everything-new-in-livewire-4)

Islands memungkinkan Anda membuat region terisolasi dalam komponen Livewire yang diupdate secara independen. Saat aksi terjadi di dalam island, hanya island tersebut yang di-render ulang — bukan seluruh komponen.

Ini memberikan keuntungan performa dari memecah komponen menjadi bagian-bagian lebih kecil tanpa overhead membuat komponen child terpisah, mengelola props, atau berurusan dengan komunikasi komponen.

---

## Daftar Isi

- [Penggunaan Dasar](#penggunaan-dasar)
- [Lazy Loading](#lazy-loading)
- [Named Islands](#named-islands)
- [Mode Append dan Prepend](#mode-append-dan-prepend)
- [Nested Islands](#nested-islands)
- [Always Render with Parent](#always-render-with-parent)
- [Skip Initial Render](#skip-initial-render)
- [Island Polling](#island-polling)
- [Pertimbangan](#pertimbangan)

---

## Penggunaan Dasar

Untuk membuat island, bungkus bagian template Blade Anda dengan direktif `@island`:

```php
<?php

use Livewire\Attributes\Computed;
use Livewire\Component;
use App\Models\Revenue;

new class extends Component {
    #[Computed]
    public function revenue()
    {
        // Kalkulasi atau query mahal...
        return Revenue::yearToDate();
    }
};
?>

<div>
    @island
        <div>
            Revenue: {{ $this->revenue }}

            <button type="button" wire:click="$refresh">Refresh</button>
        </div>
    @endisland

    <div>
        <!-- Konten lain... -->
    </div>
</div>
```

Saat tombol "Refresh" diklik, hanya island yang berisi kalkulasi revenue yang akan di-render ulang.

---

## Lazy Loading

Untuk menunda render awal island sampai setelah halaman dimuat:

```php
@island(lazy: true)
    <div>
        Revenue: {{ $this->revenue }}
        <button type="button" wire:click="$refresh">Refresh</button>
    </div>
@endisland
```

### Lazy vs Deferred Loading

```html
{{-- Dimuat saat di-scroll ke view --}} @island(lazy: true)
<!-- ... -->
@endisland {{-- Dimuat langsung setelah page load --}} @island(defer: true)
<!-- ... -->
@endisland
```

### Custom Loading States

```html
@island(lazy: true) @placeholder
<!-- Loading indicator -->
<div class="animate-pulse">
  <div class="h-32 bg-gray-200 rounded"></div>
</div>
@endplaceholder

<div>
  Revenue: {{ $this->revenue }}
  <button type="button" wire:click="$refresh">Refresh</button>
</div>
@endisland
```

---

## Named Islands

Untuk memicu island dari tempat lain di komponen, beri nama dan referensikan menggunakan `wire:island`:

```html
<div>
  @island(name: 'revenue')
  <div>Revenue: {{ $this->revenue }}</div>
  @endisland

  <button type="button" wire:click="$refresh" wire:island="revenue">
    Refresh revenue
  </button>
</div>
```

Saat Anda memiliki beberapa island dengan nama yang sama, mereka terhubung bersama dan akan selalu di-render sebagai grup.

---

## Mode Append dan Prepend

Alih-alih mengganti konten sepenuhnya, islands dapat menambahkan konten baru:

```php
<?php

use Livewire\Attributes\Computed;
use Livewire\Component;
use App\Models\Activity;

new class extends Component {
    public $page = 1;

    public function loadMore()
    {
        $this->page++;
    }

    #[Computed]
    public function activities()
    {
        return Activity::latest()
            ->forPage($this->page, 10)
            ->get();
    }
};
?>

<div>
    @island(name: 'feed')
        @foreach ($this->activities as $activity)
            <x-activity-item wire:key="{{ $activity->id }}" :activity="$activity" />
        @endforeach
    @endisland

    <button type="button" wire:click="loadMore" wire:island.append="feed">
        Load more
    </button>
</div>
```

Mode yang tersedia:

- `wire:island.append` - Tambah ke akhir
- `wire:island.prepend` - Tambah ke awal

---

## Nested Islands

Islands dapat bersarang di dalam satu sama lain. Island dalam di-skip secara default saat island luar di-render ulang:

```html
@island(name: 'revenue')
<div>
  Total revenue: {{ $this->revenue }} @island(name: 'breakdown')
  <div>
    Monthly breakdown: {{ $this->monthlyBreakdown }}
    <button type="button" wire:click="$refresh">Refresh breakdown</button>
  </div>
  @endisland

  <button type="button" wire:click="$refresh">Refresh revenue</button>
</div>
@endisland
```

---

## Always Render with Parent

Gunakan parameter `always` untuk memaksa island diupdate setiap kali komponen parent diupdate:

```html
@island(always: true)
<div>
  Revenue: {{ $this->revenue }}
  <button type="button" wire:click="$refresh">Refresh revenue</button>
</div>
@endisland
```

---

## Skip Initial Render

Parameter `skip` mencegah island di-render awalnya, sempurna untuk konten on-demand:

```html
@island(skip: true) @placeholder
<button type="button" wire:click="$refresh">Load revenue details</button>
@endplaceholder

<div>
  Revenue: {{ $this->revenue }}
  <button type="button" wire:click="$refresh">Refresh</button>
</div>
@endisland
```

---

## Island Polling

Gunakan `wire:poll` dalam island untuk refresh hanya island tersebut pada interval:

```html
@island(skip: true)
<div wire:poll.3s>
  Revenue: {{ $this->revenue }}
  <button type="button" wire:click="$refresh">Refresh</button>
</div>
@endisland
```

---

## Pertimbangan

**Data scope:** Islands memiliki akses ke properti dan method komponen, tetapi tidak ke variabel template yang didefinisikan di luar island:

```html
@php $localVariable = 'Ini tidak akan tersedia di island'; @endphp @island {{--
❌ Ini akan error --}} {{ $localVariable }} {{-- ✅ Properti komponen berfungsi
--}} {{ $this->revenue }} @endisland
```

**Islands tidak dapat digunakan dalam loop atau kondisional:**

```html
{{-- ❌ Ini tidak akan berfungsi --}} @foreach ($items as $item) @island {{
$item->name }} @endisland @endforeach {{-- ✅ Sebaliknya, letakkan
loop/kondisional di dalam island --}} @island @foreach ($this->items as $item)
{{ $item->name }} @endforeach @endisland
```

**Kapan menggunakan islands:**

- Kalkulasi mahal yang tidak boleh memblokir load halaman awal
- Region independen dengan interaksinya sendiri
- Update real-time yang hanya mempengaruhi bagian UI
- Bottleneck performa di komponen besar
