# Morphing

Morphing adalah proses yang digunakan Livewire untuk memperbarui DOM secara efisien dengan membandingkan HTML lama dan baru, lalu hanya mengubah bagian yang berbeda.

---

## Daftar Isi

- [Cara Kerja Morphing](#cara-kerja-morphing)
- [Keterbatasan Morphing](#keterbatasan-morphing)
- [Menyisipkan Elemen Intermediate](#menyisipkan-elemen-intermediate)
- [Internal Look-ahead](#internal-look-ahead)
- [Injecting Morph Markers](#injecting-morph-markers)

---

## Cara Kerja Morphing

Saat komponen dirender ulang, Livewire:

1. Merender HTML baru di server
2. Mengirim HTML baru ke browser
3. Membandingkan HTML baru dengan DOM saat ini
4. Hanya memperbarui elemen yang berubah

Ini jauh lebih efisien daripada mengganti seluruh DOM.

---

## Keterbatasan Morphing

Morphing menggunakan algoritma yang membandingkan elemen satu per satu. Algoritma ini memiliki keterbatasan:

### Menyisipkan Elemen Intermediate

```html
<!-- Sebelum -->
<div>A</div>
<div>B</div>
<div>C</div>

<!-- Setelah (menyisipkan X) -->
<div>A</div>
<div>X</div>
<!-- Baru -->
<div>B</div>
<div>C</div>
```

Tanpa `wire:key`, algoritma mungkin memodifikasi elemen yang salah.

**Solusi:** Gunakan `wire:key`

```html
@foreach ($items as $item)
<div wire:key="{{ $item->id }}">{{ $item->name }}</div>
@endforeach
```

---

## Internal Look-ahead

Livewire menggunakan look-ahead untuk mendeteksi penyisipan:

1. Memeriksa beberapa elemen ke depan untuk menemukan kecocokan
2. Jika ditemukan, menyisipkan elemen baru
3. Jika tidak, memodifikasi elemen saat ini

---

## Injecting Morph Markers

Livewire menyuntikkan marker HTML untuk membantu morphing:

```html
<!-- Marker untuk loop -->
<!--[if BLOCK]> <![endif]-->
@foreach ($items as $item)
<div>{{ $item->name }}</div>
@endforeach
<!--[if ENDBLOCK]> <![endif]-->
```

Marker ini tidak terlihat di browser dan membantu algoritma morphing bekerja lebih akurat.
