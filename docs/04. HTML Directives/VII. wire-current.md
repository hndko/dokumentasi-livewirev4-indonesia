# wire:current

Direktif `wire:current` memungkinkan Anda menerapkan class CSS ke link berdasarkan apakah cocok dengan URL saat ini.

---

## Daftar Isi

- [Penggunaan Dasar](#penggunaan-dasar)
- [Exact Matching](#exact-matching)
- [Strict Matching](#strict-matching)
- [Reference](#reference)

---

## Penggunaan Dasar

```html
<nav>
  <a href="/dashboard" wire:navigate wire:current="font-bold text-blue-600">
    Dashboard
  </a>
  <a href="/posts" wire:navigate wire:current="font-bold text-blue-600">
    Posts
  </a>
</nav>
```

---

## Exact Matching

Secara default, `wire:current` menggunakan "starts with" matching. Gunakan `.exact` untuk exact matching:

```html
<a href="/posts" wire:navigate wire:current.exact="font-bold"> All Posts </a>
```

Dengan `.exact`, link `/posts` hanya akan aktif di URL `/posts`, bukan `/posts/1`.

---

## Strict Matching

Gunakan `.strict` untuk matching yang mempertimbangkan trailing slash:

```html
<a href="/posts/" wire:navigate wire:current.strict="font-bold"> Posts </a>
```

---

## Reference

### Modifiers

| Modifier  | Deskripsi                        |
| --------- | -------------------------------- |
| `.exact`  | Exact URL matching               |
| `.strict` | Strict matching (trailing slash) |
