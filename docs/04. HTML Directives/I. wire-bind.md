# wire:bind

Direktif `wire:bind` mengikat atribut HTML secara dinamis ke properti komponen atau ekspresi. Tidak seperti sintaks atribut Blade, `wire:bind` memperbarui atribut secara reaktif di klien tanpa memerlukan render ulang penuh.

Jika Anda familiar dengan direktif `x-bind` Alpine, keduanya pada dasarnya sama.

---

## Daftar Isi

- [Penggunaan Dasar](#penggunaan-dasar)
- [Kasus Penggunaan Umum](#kasus-penggunaan-umum)
  - [Binding Styles](#binding-styles)
  - [Binding href](#binding-href)
  - [Binding Disabled State](#binding-disabled-state)
  - [Binding Data Attributes](#binding-data-attributes)

---

## Penggunaan Dasar

```html
<input
  wire:model="message"
  wire:bind:class="message.length > 240 && 'text-red-500'"
/>
```

Saat pengguna mengetik, `wire:bind:class` bereaksi terhadap panjang pesan dan menerapkan class secara instan di klien.

---

## Kasus Penggunaan Umum

### Binding Styles

```html
<div wire:bind:style="{ 'color': textColor, 'font-size': fontSize + 'px' }">
  Styled text
</div>
```

### Binding href

```html
<a wire:bind:href="url">Dynamic link</a>
```

### Binding Disabled State

```html
<button wire:bind:disabled="isArchived">Delete</button>
```

### Binding Data Attributes

```html
<div wire:bind:data-count="count">...</div>
```

---

## Reference

| Sintaks                              | Deskripsi                |
| ------------------------------------ | ------------------------ |
| `wire:bind:[attribute]="expression"` | Bind atribut ke ekspresi |
