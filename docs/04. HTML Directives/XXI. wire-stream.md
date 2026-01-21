# wire:stream

Direktif `wire:stream` memungkinkan streaming konten real-time ke elemen tertentu.

---

## Daftar Isi

- [Penggunaan Dasar](#penggunaan-dasar)
- [Streaming Chat Bot](#streaming-chat-bot)
- [Replace vs Append](#replace-vs-append)
- [Reference](#reference)

---

## Penggunaan Dasar

```html
<div wire:stream="output"></div>
```

```php
public function generate()
{
    $this->stream(
        to: 'output',
        content: 'Hello ',
    );

    sleep(1);

    $this->stream(
        to: 'output',
        content: 'World!',
    );
}
```

---

## Streaming Chat Bot

```php
public function ask()
{
    foreach ($this->streamFromAI($this->question) as $chunk) {
        $this->stream(
            to: 'response',
            content: $chunk,
        );
    }
}
```

```html
<div>
  <input wire:model="question" />
  <button wire:click="ask">Ask</button>

  <div wire:stream="response"></div>
</div>
```

---

## Replace vs Append

Secara default, konten di-append. Gunakan `replace: true` untuk mengganti:

```php
$this->stream(
    to: 'output',
    content: $newContent,
    replace: true,
);
```

---

## Reference

### Sintaks

```
wire:stream="targetName"
```

### Modifiers

| Modifier   | Deskripsi                     |
| ---------- | ----------------------------- |
| `.replace` | Ganti konten alih-alih append |
