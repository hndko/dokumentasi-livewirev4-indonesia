# wire:init

Direktif `wire:init` memanggil aksi segera setelah komponen dirender.

---

## Daftar Isi

- [Penggunaan Dasar](#penggunaan-dasar)
- [Reference](#reference)

---

## Penggunaan Dasar

```html
<div wire:init="loadPosts">
  <div wire:loading>Loading posts...</div>

  @foreach ($posts as $post)
  <div>{{ $post->title }}</div>
  @endforeach
</div>
```

Method `loadPosts` akan dipanggil segera setelah komponen dirender, memungkinkan Anda menunda operasi yang mahal.

```php
class ShowPosts extends Component
{
    public $posts = [];

    public function loadPosts()
    {
        $this->posts = Post::all();
    }
}
```

---

## Reference

### Sintaks

```
wire:init="methodName"
wire:init="methodName(param1, param2)"
```
