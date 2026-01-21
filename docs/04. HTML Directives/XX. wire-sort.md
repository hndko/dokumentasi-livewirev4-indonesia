# wire:sort

Direktif `wire:sort` memungkinkan pengguna mengurutkan ulang item dengan drag-and-drop.

---

## Daftar Isi

- [Penggunaan Dasar](#penggunaan-dasar)
- [Sorting Across Groups](#sorting-across-groups)
- [Sort Handles](#sort-handles)
- [Ignoring Elements](#ignoring-elements)
- [Reference](#reference)

---

## Penggunaan Dasar

```html
<ul wire:sort="updateOrder">
  @foreach ($items as $item)
  <li wire:key="{{ $item->id }}" wire:sort.item="{{ $item->id }}">
    {{ $item->name }}
  </li>
  @endforeach
</ul>
```

```php
public function updateOrder($items)
{
    foreach ($items as $item) {
        Item::find($item['value'])->update(['order' => $item['order']]);
    }
}
```

---

## Sorting Across Groups

```html
<div wire:sort="updateOrder" wire:sort.group="tasks">
  <div wire:sort.item="1">Task 1</div>
  <div wire:sort.item="2">Task 2</div>
</div>

<div wire:sort="updateOrder" wire:sort.group="tasks">
  <div wire:sort.item="3">Task 3</div>
  <div wire:sort.item="4">Task 4</div>
</div>
```

---

## Sort Handles

Batasi drag ke handle tertentu:

```html
<ul wire:sort="updateOrder">
  @foreach ($items as $item)
  <li wire:key="{{ $item->id }}" wire:sort.item="{{ $item->id }}">
    <span wire:sort.handle>⋮⋮</span>
    {{ $item->name }}
  </li>
  @endforeach
</ul>
```

---

## Ignoring Elements

```html
<ul wire:sort="updateOrder">
  <li wire:sort.ignore>Header (tidak bisa di-drag)</li>
  @foreach ($items as $item)
  <li wire:key="{{ $item->id }}" wire:sort.item="{{ $item->id }}">
    {{ $item->name }}
  </li>
  @endforeach
</ul>
```

---

## Reference

### Sintaks

```
wire:sort="methodName"
wire:sort.item="value"
wire:sort.handle
wire:sort.group="groupName"
wire:sort.ignore
```
