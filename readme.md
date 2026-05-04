APP_NAME="Kasir Laravel"
APP_ENV=local
APP_KEY=
APP_DEBUG=true
APP_URL=http://localhost

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=kasir_db
DB_USERNAME=root
DB_PASSWORD=
<?php
use App\Http\Controllers\KasirController;
use Illuminate\Support\Facades\Route;

Route::get('/', [KasirController::class, 'index']);
Route::post('/product', [KasirController::class, 'store'])->name('product.store');
Route::delete('/product/{product}', [KasirController::class, 'destroy'])->name('product.destroy');
<?php
namespace App\Models;
use Illuminate\Database\Eloquent\Model;

class Product extends Model {
    protected $fillable = ['kode', 'nama', 'harga', 'stok'];
}
<?php
namespace App\Http\Controllers;
use App\Models\Product;
use Illuminate\Http\Request;

class KasirController extends Controller {
    public function index() {
        $products = Product::latest()->get();
        return view('kasir', compact('products'));
    }

    public function store(Request $request) {
        $request->validate([
            'kode' => 'required|unique:products,kode',
            'nama' => 'required',
            'harga' => 'required|numeric|min:0',
            'stok' => 'required|numeric|min:0'
        ]);
        Product::create($request->all());
        return back()->with('sukses', 'Produk berhasil ditambah!');
    }

    public function destroy(Product $product) {
        $product->delete();
        return back()->with('sukses', 'Produk berhasil dihapus!');
    }
}
<?php
use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration {
    public function up(): void {
        Schema::create('products', function (Blueprint $table) {
            $table->id();
            $table->string('kode')->unique();
            $table->string('nama');
            $table->integer('harga');
            $table->integer('stok');
            $table->timestamps();
        });
    }
    public function down(): void {
        Schema::dropIfExists('products');
    }
};
<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Kasir Laravel</title>
    <script src="https://cdn.tailwindcss.com"></script>
</head>
<body class="bg-gray-100 p-4 md:p-8">
    <div class="max-w-5xl mx-auto">
        <h1 class="text-2xl md:text-3xl font-bold mb-6">KASIR LARAVEL UMKM</h1>

        @if(session('sukses'))
            <div class="bg-green-100 border border-green-400 text-green-700 px-4 py-3 rounded mb-4">
                {{ session('sukses') }}
            </div>
        @endif

        @if($errors->any())
            <div class="bg-red-100 border border-red-400 text-red-700 px-4 py-3 rounded mb-4">
                @foreach($errors->all() as $error)
                    <p>{{ $error }}</p>
                @endforeach
            </div>
        @endif

        <div class="bg-white p-6 rounded-lg shadow mb-6">
            <h2 class="text-xl font-bold mb-4">Tambah Produk Baru</h2>
            <form method="POST" action="{{ route('product.store') }}" class="grid grid-cols-1 md:grid-cols-5 gap-4">
                @csrf
                <input type="text" name="kode" placeholder="Kode Barang" class="border p-2 rounded" value="{{ old('kode') }}" required>
                <input type="text" name="nama" placeholder="Nama Produk" class="border p-2 rounded" value="{{ old('nama') }}" required>
                <input type="number" name="harga" placeholder="Harga Jual" class="border p-2 rounded" value="{{ old('harga') }}" required>
                <input type="number" name="stok" placeholder="Stok" class="border p-2 rounded" value="{{ old('stok') }}" required>
                <button type="submit" class="bg-blue-600 text-white p-2 rounded hover:bg-blue-700">Simpan</button>
            </form>
        </div>

        <div class="bg-white p-6 rounded-lg shadow">
            <h2 class="text-xl font-bold mb-4">Daftar Produk</h2>
            <div class="overflow-x-auto">
                <table class="w-full">
                    <thead>
                        <tr class="bg-gray-200">
                            <th class="p-3 text-left">Kode</th>
                            <th class="p-3 text-left">Nama Produk</th>
                            <th class="p-3 text-left">Harga</th>
                            <th class="p-3 text-left">Stok</th>
                            <th class="p-3 text-left">Aksi</th>
                        </tr>
                    </thead>
                    <tbody>
                        @forelse($products as $p)
                        <tr class="border-b hover:bg-gray-50">
                            <td class="p-3">{{ $p->kode }}</td>
                            <td class="p-3">{{ $p->nama }}</td>
                            <td class="p-3">Rp {{ number_format($p->harga) }}</td>
                            <td class="p-3">{{ $p->stok }}</td>
                            <td class="p-3">
                                <form method="POST" action="{{ route('product.destroy', $p->id) }}" onsubmit="return confirm('Yakin hapus?')">
                                    @csrf @method('DELETE')
                                    <button type="submit" class="bg-red-500 text-white px-3 py-1 rounded text-sm hover:bg-red-600">Hapus</button>
                                </form>
                            </td>
                        </tr>
                        @empty
                        <tr><td colspan="5" class="p-3 text-center text-gray-500">Belum ada data produk</td></tr>
                        @endforelse
                    </tbody>
                </table>
            </div>
        </div>
    </div>
</body>
</html>
# Laravel Kasir UMKM
Source code aplikasi kasir sederhana pake Laravel 11. 

## Fitur
1. Tambah produk: kode, nama, harga, stok
2. Hapus produk
3. Validasi input
4. Tampilan responsive pake Tailwind CDN

## Cara Install di Laptop
1. `git clone https://github.com/linles263-sudo/laravel-kasir-UMKM.git`
2. `cd laravel-kasir-UMKM`
3. `composer install`
4. `cp .env.example .env`
5. `php artisan key:generate`
6. Setting database di file `.env`
7. `php artisan migrate`
8. `php artisan serve`

Buka http://localhost:8000

## Dibuat untuk belajar
Project ini 100% siap dipelajari. Silakan fork & modif sesukamu.
