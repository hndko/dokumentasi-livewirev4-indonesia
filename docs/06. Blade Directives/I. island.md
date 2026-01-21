# @island

Direktif `@island` membuat region terisolasi dalam komponen yang diupdate secara independen, tanpa merender ulang seluruh komponen.

---

## Daftar Isi

- [Penggunaan Dasar](#penggunaan-dasar)
- [Lazy Loading Islands](#lazy-loading-islands)
- [Custom Loading States](#custom-loading-states)
- [Named Islands](#named-islands)
- [Mengapa Menggunakan Islands](#mengapa-menggunakan-islands)
- [Reference](#reference)

---

## Penggunaan Dasar

```html
<div>
  <h1>Dashboard</h1>

  @island
  <div>
    Revenue: {{ $this->revenue }}
    <button wire:click="$refresh">Refresh</button>
  </div>
  @endisland

  <div>Konten lain...</div>
</div>
```

Saat button diklik, hanya island yang dirender ulang, bukan seluruh komponen.

---

## Lazy Loading Islands

```html
@island(lazy: true)
<div>Konten yang dimuat saat terlihat</div>
@endisland @island(defer: true)
<div>Konten yang dimuat setelah page load</div>
@endisland
```

### Lazy vs Deferred

| Parameter     | Perilaku                          |
| ------------- | --------------------------------- |
| `lazy: true`  | Dimuat saat di-scroll ke viewport |
| `defer: true` | Dimuat segera setelah page load   |

---

## Custom Loading States

```html
@island(lazy: true) @placeholder
<div class="animate-pulse bg-gray-200 h-32 rounded"></div>
@endplaceholder

<div>Konten sebenarnya</div>
@endisland
```

---

## Named Islands

```html
@island(name: 'stats')
<div>Stats: {{ $this->stats }}</div>
@endisland

<button wire:click="$refresh" wire:island="stats">Refresh Stats</button>
```

---

## Mengapa Menggunakan Islands

- Kalkulasi mahal yang tidak boleh memblokir load awal
- Region independen dengan interaksi sendiri
- Update real-time yang hanya mempengaruhi bagian UI

---

## Reference

| Parameter | Deskripsi                   |
| --------- | --------------------------- |
| `name`    | Nama island                 |
| `lazy`    | Lazy load saat visible      |
| `defer`   | Load setelah page load      |
| `always`  | Selalu render dengan parent |
| `skip`    | Skip render awal            |
