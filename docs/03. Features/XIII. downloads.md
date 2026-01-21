# File Downloads

[Mulai belajar di Laracasts](https://laracasts.com/series/everything-new-in-livewire-4)

Livewire memungkinkan Anda memicu download file dari aksi komponen.

---

## Daftar Isi

- [Penggunaan Dasar](#penggunaan-dasar)
- [Streaming Downloads](#streaming-downloads)
- [Testing File Downloads](#testing-file-downloads)

---

## Penggunaan Dasar

```php
<?php

use Livewire\Component;

new class extends Component {
    public function downloadReport()
    {
        return $this->download(
            storage_path('reports/report.pdf'),
            'monthly-report.pdf'
        );
    }
};
?>

<button wire:click="downloadReport">Download</button>
```

---

## Streaming Downloads

Untuk file yang dihasilkan dinamis tanpa menyimpan ke disk:

```php
public function exportUsers()
{
    return response()->streamDownload(function () {
        $csv = "ID,Name,Email\n";
        foreach (User::all() as $user) {
            $csv .= "{$user->id},{$user->name},{$user->email}\n";
        }
        echo $csv;
    }, 'users.csv');
}
```

---

## Testing File Downloads

```php
Livewire::test('reports.download')
    ->call('downloadReport')
    ->assertFileDownloaded('report.pdf');
```
