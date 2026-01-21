# Step 7: Custom Synthesizers

## 🎯 Tujuan

- Membuat custom type yang bisa digunakan sebagai property
- Implementasi Money value object

---

## 📝 Langkah 7.1: Buat Money Value Object

```php
<?php
// app/Support/Money.php

namespace App\Support;

class Money
{
    public function __construct(
        public int $amount,
        public string $currency = 'IDR'
    ) {}

    public function formatted(): string
    {
        return $this->currency . ' ' . number_format($this->amount);
    }
}
```

---

## 📝 Langkah 7.2: Buat Synthesizer

```php
<?php
// app/Livewire/Synthesizers/MoneySynthesizer.php

namespace App\Livewire\Synthesizers;

use App\Support\Money;
use Livewire\Mechanisms\HandleComponents\Synthesizers\Synth;

class MoneySynthesizer extends Synth
{
    public static $key = 'money';

    public static function match($target)
    {
        return $target instanceof Money;
    }

    public function dehydrate($target)
    {
        return [
            ['amount' => $target->amount, 'currency' => $target->currency],
            [],
        ];
    }

    public function hydrate($value)
    {
        return new Money($value['amount'], $value['currency']);
    }

    public function get(&$target, $key)
    {
        return match($key) {
            'amount' => $target->amount,
            'currency' => $target->currency,
        };
    }

    public function set(&$target, $key, $value)
    {
        if ($key === 'amount') $target->amount = (int) $value;
    }
}
```

---

## 📝 Langkah 7.3: Register Synthesizer

```php
// app/Providers/AppServiceProvider.php
use App\Livewire\Synthesizers\MoneySynthesizer;
use Livewire\Livewire;

public function boot()
{
    Livewire::propertySynthesizer(MoneySynthesizer::class);
}
```

---

## 📝 Langkah 7.4: Gunakan di Component

```php
use App\Support\Money;

class PriceEditor extends Component
{
    public Money $price;

    public function mount()
    {
        $this->price = new Money(100000, 'IDR');
    }
}
```

```html
<input type="number" wire:model="price.amount" />
<p>{{ $price->formatted() }}</p>
```

---

## ✅ Checkpoint

- [x] Custom Money type
- [x] Synthesizer untuk hydration/dehydration
- [x] Binding ke nested properties

---

## ⬅️ [Step 6](06-streaming-ai.md) | ➡️ [Step 8: Tips](08-advanced-tips.md)
