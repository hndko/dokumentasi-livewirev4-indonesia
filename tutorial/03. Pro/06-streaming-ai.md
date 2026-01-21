# Step 6: Streaming & AI Chat

## 🎯 Tujuan

- Implementasi streaming untuk response bertahap
- Contoh AI chat dengan typing effect

---

## 📝 Langkah 6.1: Generate AiChat Component

```bash
php artisan make:livewire AiChat
```

---

## 📝 Langkah 6.2: AiChat Component

```php
<?php
// app/Livewire/AiChat.php

namespace App\Livewire;

use Livewire\Component;

class AiChat extends Component
{
    public string $question = '';
    public array $messages = [];

    public function ask()
    {
        if (empty($this->question)) return;

        // Add user message
        $this->messages[] = ['role' => 'user', 'content' => $this->question];
        $this->messages[] = ['role' => 'assistant', 'content' => ''];

        $question = $this->question;
        $this->question = '';

        // Simulate streaming
        $response = "Ini jawaban untuk: {$question}";

        foreach (str_split($response) as $char) {
            usleep(30000); // 30ms delay
            $this->stream(to: 'ai-response', content: $char);
        }
    }

    public function render()
    {
        return view('livewire.ai-chat');
    }
}
```

---

## 📝 Langkah 6.3: AiChat View

```html
<div class="max-w-xl mx-auto bg-white rounded-lg shadow">
  <div class="p-4 border-b font-bold">🤖 AI Chat</div>

  <!-- Messages -->
  <div class="p-4 h-80 overflow-y-auto space-y-3">
    @foreach ($messages as $i => $msg)
    <div class="{{ $msg['role'] === 'user' ? 'text-right' : '' }}">
      <span
        class="inline-block px-3 py-2 rounded-lg
                    {{ $msg['role'] === 'user' ? 'bg-blue-500 text-white' : 'bg-gray-100' }}"
      >
        @if ($msg['role'] === 'assistant' && $loop->last)
        <span wire:stream="ai-response">{{ $msg['content'] }}</span>
        <span wire:loading wire:target="ask">▌</span>
        @else {{ $msg['content'] }} @endif
      </span>
    </div>
    @endforeach
  </div>

  <!-- Input -->
  <form wire:submit="ask" class="p-4 border-t flex gap-2">
    <input
      type="text"
      wire:model="question"
      placeholder="Tanya sesuatu..."
      class="flex-1 px-3 py-2 border rounded"
    />
    <button class="px-4 py-2 bg-blue-500 text-white rounded">Kirim</button>
  </form>
</div>
```

---

## 📝 Penjelasan

| Method                                 | Deskripsi                    |
| -------------------------------------- | ---------------------------- |
| `$this->stream(to: 'x', content: 'y')` | Stream content ke target     |
| `wire:stream="x"`                      | Target untuk menerima stream |

---

## ✅ Checkpoint

- [x] Streaming response
- [x] Typing effect

---

## ⬅️ [Step 5](05-draggable-widgets.md) | ➡️ [Step 7: Synthesizers](07-custom-synthesizers.md)
