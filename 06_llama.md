# llama

- [fork llama - llama-cpp-turboquant](https://github.com/TheTom/llama-cpp-turboquant)
- [fork llama - ik_llama.cpp](https://github.com/ikawrakow/ik_llama.cpp)
- [fork llama - turboquant build for AMD](https://github.com/TheTom/llama-cpp-turboquant/blob/feature/turboquant-kv-cache/docs/build.md#hip)
- [fork llama - turboquant docker](https://github.com/TheTom/llama-cpp-turboquant/blob/feature/turboquant-kv-cache/docs/docker.md)
- [models - YTan2000/Qwen3.6-27B-TQ3_4S - turboquant llama.cpp - 13gb](https://huggingface.co/YTan2000/Qwen3.6-27B-TQ3_4S)
- [models - bartowski/Qwen_Qwen3.6-35B-A3B-GGUF - turboquant llama.cpp - 21gb](https://huggingface.co/bartowski/Qwen_Qwen3.6-35B-A3B-GGUF)
- [models - abovespec/Qwen3.6-35B-A3B-IQ4_K_R4-GGUF - ik_llama.cpp - 19gb](https://huggingface.co/abovespec/Qwen3.6-35B-A3B-IQ4_K_R4-GGUF)
- [models - mlx-community/Qwen3.5-27B-Claude-4.6-Opus-Distilled-MLX-4bit - 15.1gb](https://huggingface.co/mlx-community/Qwen3.5-27B-Claude-4.6-Opus-Distilled-MLX-4bit)
- [models - lmstudio-community/Qwen3.6-35B-A3B-GGUF - 21.2gb](https://huggingface.co/lmstudio-community/Qwen3.6-35B-A3B-GGUF)

```bash
docker run --rm --gpus all \
    --ulimit memlock=-1 \
    --cap-add=IPC_LOCK \
    -v /root:/root \
    -w /root/llama-cpp-turboquant \
    -p 8080:8080 \
    nvidia/cuda:12.4.1-devel-ubuntu22.04 \
    ./build/bin/llama-server \
    -m /root/models/Qwen_Qwen3.6-35B-A3B-Q4_K_M.gguf \
    --port 8080 --host 0.0.0.0 \
    --cache-type-k turbo4 --cache-type-v turbo3 \
    --n-cpu-moe 36 \
    -ngl 99 \
    --no-mmap --mlock \
    --jinja \
    -c 256000
```

| Flag | Penjelasan teknis | Dampak / Kenapa dipakai |
|------|-------------------|-------------------------|
| `--rm` | Hapus container setelah proses selesai (clean‑up otomatis). | Praktis untuk satu‑jalan (debug) – tidak menyisakan data stale. |
| `--gpus all` | Beri akses ke semua GPU yang terdeteksi Docker‑engine. | Diperlukan bila Anda ingin memakai **CUDA** untuk offload layer. |
| `--ulimit memlock=-1` | Hapus batas *memlock* sistem (Berkeley `ulimit`). Nilai `-1` = **unlimited** – proses boleh meng‑lock halaman memori. | Diperlukan ketika Anda menjalankan **`--mlock`** (memastikan semua memori model dan cache tetap di RAM tidak dapat ditransfer ke swap). Tanpa ini, proses mungkin gagal karena kernel membatasi *locked‑memory*. |
| `--cap-add=IPC_LOCK` | Tambahkan kemampuan **IPC locking** pada kontainer (Linux capability). | Berguna ketika `--ulimit memlock` tidak cukup di‑allow; Docker harus diberikan capability ini agar proses bisa `mlock`. |
| `-v /root:/root` | Mount direktori host **/root** ke dalam container sebagai `/root`. | Bisa untuk menaruh file model (`/root/models/...`) dan build artefak (`/root/llama-cpp-turboquant`). |
| `-w /root/llama-cpp-turboquant` | Set **working directory** di dalam container. | Memberi konteks untuk mengeksekusi binary yang tercari — di folder ini biasanya terdapat hasil compile (`./build`). |
| `nvidia/cuda:12.4.1-devel-ubuntu22.04` | *Base image* yang sudah ter‑install **CUDA 12.4** (driver & compiler). | Dengan base ini Anda bisa membangun (atau langsung mengeksekusi) kode yang menggunakan CUDA. |
| `./build/bin/llama-server` | **Executable** Anda jalankan. Path biasanya hasil `cmake && make` pada repo `llama.cpp`. | Ini adalah proses yang akan menerima request HTTP. |
| `-m /root/models/Qwen_Qwen3.6-35B-A3B-Q4_K_M.gguf` | **Model file** yang akan dipakai. Format **GGUF** (`q4_k_m` significa 4‑bit quantization, level `k`). Model berukuran sekitar 20 GB. | Model di‑load di sini; `--m` atau `--model` (setara). |
| `--port 8080 --host 0.0.0.0` | Port TCP yang dibuka (8080) dan *bind* ke semua antarmuka jaringan (`0.0.0.0`). | Membuat server terjangkau di luar container (misal lewat `localhost:8080` di host). |
| `--cache-type-k turbo4` & `--cache-type-v turbo3` | **Cache‑type** *key* dan *value* dengan **turbo**‑level **4** dan **3** masing‑masing. Ini meng‑optimalkan penyimpanan **kv‑cache** (lazy‑load ke GPU memori). | Mengurangi memori cache, meningkatkan bandwidth, sehingga Anda dapat menjaga *context size* besar tanpa habisin VRAM. |
| `--n-cpu-moe 36` | **Thread CPU** yang dialokasikan untuk operasi **MoE**. | Model *Qwen‑3* bahwa arsitektur dasarnya **bukan** MoE; flag ini sebenarnya akan *di‑abaikan*. (Jika Anda menggunakan model MoE, angka ini memberi ruang bagi *experts* yang di‑compute di CPU.) |
| `-ngl 99` | **N**umber **G**PU **L**ayers – sebenarnya **bukan** flag `llama.cpp`. Saya ragu: dengan `llama‑server` flag itu **tidak** ada `-ngl`. Kemungkinan Anda menuliskan **salah‑posisi** atau **syntax dari `llama.cpp`'s Python/CLI wrapper** (`-ngl 99`). Jika memang ada, berarti “keep *n* layers on GPU”. Namun **bukan** bagian resmi dari `llama-server`; secara umum flag yang valid adalah `--n-gpu-layers`. Jadi interpretasi: Anda probably meant `--n-gpu-layers 99` (offload 99 layers).  | Jika memang ada, ia berfungsi seperti `--n-gpu-layers`. Jika tidak – command akan gagal. |
| `--no-mmap --mlock` | `--no-mmap` mematikan **lazy‑mapping** (seperti `--no-map` di contoh lain). `--mlock` men‑**lock** memori virtual ke RAM (tidak bisa di‑swap). | `--no-mmap` memastikan semua data model langsung di‑read ke RAM; `--mlock` membuat memori itu **tidak dapat dipindahkan ke swap** (kritik pada Docker yang secara default membatasi memlock). |
| `--jinja` | Meng‑aktifkan **Jinja2** templating untuk men‑format output (biasanya menghasilkan JSON‑RPC yang lebih rapi, dan kadang men‑enable *pretty‑print*). | Produce output yang lebih mudah dibaca / memberi opsi formatting khusus; tidak mempengaruhi performa signifikan. |
| `-c 256000` | **Context size** – sebesar **256 000** token (karena flag `c` atau `--ctx-size`). | Lebih besar dari contoh sebelumnya (131 072). Ini memungkinkan *generation* atau *retrieval* dengan **256 k token** (≈ 500 KB * 256k ≈ 488 MB* hanya untuk token buffer; selain itu memakan memori cache). |

> **Catatan:** Flag `--no-mmap` dan `--mlock` berfungsi bersamaan. `--no-mmap` **meng‑load kernel‑file secara eager** (bukan lazy‑map). `--mlock` membuat bagian memori yang memegang **data model + cache** tetap di RAM dan tidak dapat dialihkan ke swap. Ini penting ketika Anda ingin menjamin *low‑latency* dan menghindari page‑fault yang dapat menunda request pertama.


```bash
./build/bin/llama-server \
  --model /root/models/Qwen_Qwen3.6-35B-A3B-Q4_K_M.gguf \
  --port 8080 --host 0.0.0.0 \
  --n-gpu-layers 99 \
  --n-cpu-moe 35 \
  --no-map \
  --ctx-size 131072 \
  --cache-type-k turbo4 \
  --cache-type-v turbo3 \
  --mlock \
  --jinja
```

| Flag | Penjelasan (ulang) | Penjelasan tambahan |
|------|-------------------|---------------------|
| `--n-gpu-layers 999` | Offload **seluruh** layer modelo ke GPU (999 > jumlah layer sebenarnya). | Pastikan **VRAM** (mis. 40 GB A100) cukup untuk semua layer + cache. |
| `--n-cpu-moe 35` | 35 thread CPU untuk MoE (jika ada). | Jika model tidak MoE, flag di‑abaikan. |
| `--no-map` | Equivalent dengan `--no-mmap` di contoh kedua – *disable mmap*. | Membuat *load eager* (seluruh model ke RAM). |
| `--ctx-size 131072` | Context window 131 072 token. | Meng‑ukuran kode token buffer; penting bila Anda ingin memproses konteks panjang. |
| `--cache-type-k turbo4` & `--cache-type-v turbo3` | Optimasi cache key/value (turbo‑4 / turbo‑3). | Reduksi memori cache, meningkatkan bandwidth. |
| `--mlock` | Lock seluruh memori virtual ke RAM (tidak swap). | Harus di‑pair dengan `--ulimit memlock=-1` atau `cap-add=IPC_LOCK` pada Docker; hasilnya memori tidak dipindahkan ke swap. |

> **Interaksi antarp semua flag ini** menghasilkan sebuah **pipeline** yang sangat agresif:
> - Model di‑offload **sepenuhnya** ke GPU,
> - Cache di‑optimalkan secara turbo,
> - Semua memori (model, cache, context) *ter‑mlocked*,
> - Context window sangat besar (131 k),
> - Tidak ada *lazy‑loading* yang memperlambat request pertama,
> - CPU hanya digunakan untuk MoE atau I/O (jika ada).

Powerful? **Ya** — tetapantara librasi memori gigantic; Resource‑intensive? **Sangat**. Jangan jalankan sebelum memastikan *resource budget* (RAM, VRAM, swap) memang cukup.
