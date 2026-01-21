# wire:click

Livewire menyediakan direktif `wire:click` untuk memanggil method komponen (aksi) saat pengguna mengklik elemen tertentu.

---

## Daftar Isi

- [Penggunaan Dasar](#penggunaan-dasar)
- [Passing Parameter](#passing-parameter)
- [Menggunakan pada Links](#menggunakan-pada-links)
- [Mencegah Re-renders](#mencegah-re-renders)
- [Mempertahankan Posisi Scroll](#mempertahankan-posisi-scroll)
- [Eksekusi Paralel](#eksekusi-paralel)
- [Reference](#reference)

---

## Penggunaan Dasar

```php
class ShowInvoice extends Component
{
    public Invoice $invoice;

    public function download()
    {
        return response()->download(
            $this->invoice->file_path,
            'invoice.pdf'
        );
    }
}
```

```html
<button type="button" wire:click="download">Download Invoice</button>
```

---

## Passing Parameter

```html
<button wire:click="delete({{ $post->id }})">Delete</button>
```

> **Perhatian:** Parameter aksi harus diperlakukan seperti input HTTP request dan tidak boleh dipercaya. Selalu otorisasi kepemilikan sebelum memperbarui data.

---

## Menggunakan pada Links

Saat menggunakan `wire:click` pada tag `<a>`, tambahkan `.prevent` untuk mencegah perilaku default link:

```html
<a href="#" wire:click.prevent="show">View Details</a>
```

---

## Mencegah Re-renders

Gunakan `.renderless` untuk melewati render ulang komponen setelah aksi selesai:

```html
<button wire:click.renderless="trackClick">Track Event</button>
```

---

## Mempertahankan Posisi Scroll

Gunakan `.preserve-scroll` untuk mempertahankan posisi scroll saat ini:

```html
<button wire:click.preserve-scroll="loadMore">Load More</button>
```

---

## Eksekusi Paralel

Gunakan `.async` untuk mengizinkan aksi berjalan secara paralel:

```html
<button wire:click.async="process">Process</button>
```

---

## Reference

### Sintaks

```
wire:click="methodName"
wire:click="methodName(param1, param2)"
```

### Modifiers

| Modifier           | Deskripsi                    |
| ------------------ | ---------------------------- |
| `.prevent`         | Mencegah perilaku default    |
| `.stop`            | Menghentikan propagasi event |
| `.renderless`      | Melewati render ulang        |
| `.preserve-scroll` | Mempertahankan posisi scroll |
| `.async`           | Eksekusi paralel             |
