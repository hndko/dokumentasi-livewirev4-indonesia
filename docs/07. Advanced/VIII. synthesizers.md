# Synthesizers

Synthesizers memungkinkan Anda mendukung tipe properti kustom di Livewire dengan mendefinisikan cara hydrate dan dehydrate objek.

---

## Daftar Isi

- [Memahami Synthesizers](#memahami-synthesizers)
- [Registering Custom Synthesizer](#registering-custom-synthesizer)
- [Supporting Data Binding](#supporting-data-binding)

---

## Memahami Synthesizers

Synthesizer adalah kelas yang menangani konversi antara:

- **Dehydration**: PHP object → JSON (untuk dikirim ke browser)
- **Hydration**: JSON → PHP object (untuk direkonstruksi di server)

Livewire sudah menyertakan synthesizers untuk:

- Collections
- Carbon dates
- Eloquent Models
- Stringables

---

## Registering Custom Synthesizer

```php
use Livewire\Mechanisms\HandleComponents\Synthesizers\Synth;

class MoneySynth extends Synth
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
            [], // metadata
        ];
    }

    public function hydrate($value)
    {
        return new Money($value['amount'], $value['currency']);
    }
}
```

Registrasikan di service provider:

```php
use Livewire\Livewire;

public function boot()
{
    Livewire::propertySynthesizer(MoneySynth::class);
}
```

---

## Supporting Data Binding

Untuk mendukung `wire:model` pada properti kustom:

```php
class MoneySynth extends Synth
{
    // ...

    public function get(&$target, $key)
    {
        if ($key === 'amount') return $target->amount;
        if ($key === 'currency') return $target->currency;
    }

    public function set(&$target, $key, $value)
    {
        if ($key === 'amount') {
            $target->amount = $value;
        }
    }
}
```

Sekarang Anda dapat binding ke nested properties:

```html
<input wire:model="price.amount" /> <input wire:model="price.currency" />
```
