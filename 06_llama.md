# llama

## non moe model

perlu fork [llama.cpp-tq3](https://github.com/turbo-tan/llama.cpp-tq3) atau [llama.cpp-hip-turboquant-tq3](https://github.com/flamme-demon/llama.cpp-hip-turboquant-tq3):

- [x] [models - YTan2000/Qwen3.6-27B-TQ3_4S - turboquant llama.cpp - 13gb](https://huggingface.co/YTan2000/Qwen3.6-27B-TQ3_4S)
- [ ] [models - Jackrong/Negentropy-claude-opus-4.7-9B-GGUF - 5.63gb](https://huggingface.co/Jackrong/Negentropy-claude-opus-4.7-9B-GGUF)

## moe model

perlu fork [llama-cpp-turboquant](https://github.com/TheTom/llama-cpp-turboquant):

- [x] [models - bartowski/Qwen_Qwen3.6-35B-A3B-GGUF - turboquant llama.cpp - 21.4gb](https://huggingface.co/bartowski/Qwen_Qwen3.6-35B-A3B-GGUF)
- [x] [models - Jackrong/Qwopus3.6-35B-A3B-v1-GGUF - reasoning-enhanced MoE - 21.2gb](https://huggingface.co/Jackrong/Qwopus3.6-35B-A3B-v1-GGUF)
- [ ] [models - lmstudio-community/Qwen3.6-35B-A3B-GGUF - 21.2gb](https://huggingface.co/lmstudio-community/Qwen3.6-35B-A3B-GGUF)
- [ ] [deepseek-ai/DeepSeek-Coder-V2-Lite-Instruct](https://huggingface.co/deepseek-ai/DeepSeek-Coder-V2-Lite-Instruct)
- [ ] [mradermacher/Mixtral_13Bx2_MOE_22B-i1-GGUF](https://huggingface.co/mradermacher/Mixtral_13Bx2_MOE_22B-i1-GGUF)
- [ ] [google/gemma-4-26B-A4B-it](https://huggingface.co/google/gemma-4-26B-A4B-it)

perlu fork [llama.cpp-tq3](https://github.com/turbo-tan/llama.cpp-tq3) atau [llama.cpp-hip-turboquant-tq3](https://github.com/flamme-demon/llama.cpp-hip-turboquant-tq3):

- [x] [YTan2000/Qwen3.6-35B-A3B-TQ3_4S](https://huggingface.co/YTan2000/Qwen3.6-35B-A3B-TQ3_4S)
- [ ] [YTan2000/Gemma4-26b-Super-Abliterated-TQ3_4S](https://huggingface.co/YTan2000/Gemma4-26b-Super-Abliterated-TQ3_4S)

## dflash model

- [x] [starskyzheng/Qwen3.6-35B-DFlash-GGUF](https://huggingface.co/starskyzheng/Qwen3.6-35B-DFlash-GGUF)

DFlash = “speed booster via speculative decoding” mengurangi kerja model utama dengan “tebakan awal” dari model kecil

Draft model (model kecil/lebih ringan) yang dipakai untuk “menebak” token berikutnya lebih cepat (misal 4–8 token).

```bash
./build/bin/llama-server \ 
    -m /path/to/Qwen3.6-35B-target.Q4_K_M.gguf \ 
    -md /path/to/dflash-draft-3.6-q8_0.gguf \ 
    --spec-type dflash \ 
    -ngl 99 -ngld 99 \ 
    -np 1 -c 6048 -cd 256 \ 
    -fa on -b 256 -ub 64 \ 
    --host 0.0.0.0 --port 8080 --jinja \ 
    --chat-template-kwargs '{"enable_thinking": false}'
```

## mlx model

- [ ] [models - mlx-community/Qwen3.5-27B-Claude-4.6-Opus-Distilled-MLX-4bit - 15.1gb](https://huggingface.co/mlx-community/Qwen3.5-27B-Claude-4.6-Opus-Distilled-MLX-4bit)


## Only Support NVIDIA

perlu fork [llama ik_llama.cpp](https://github.com/ikawrakow/ik_llama.cpp):

- [x] [models - abovespec/Qwen3.6-35B-A3B-IQ4_K_R4-GGUF - ik_llama.cpp - 19gb](https://huggingface.co/abovespec/Qwen3.6-35B-A3B-IQ4_K_R4-GGUF)

## Install ROCM untuk GPU AMD RX6600

```bash
sudo paru -S rocm-hip-sdk rocm-opencl-sdk
```

set to environment variables

```
export HSA_OVERRIDE_GFX_VERSION 10.3.0
export HIP_VISIBLE_DEVICES 0
export ROCM_PATH /opt/rocm
```

## Cara build llama.cpp original, fork llama-cpp-turboquant, fork llama.cpp-tq3

support ROCM:

```bash
HIPCXX="/opt/rocm/lib/llvm/bin/clang" HIP_PATH="/opt/rocm" \
    cmake -S . -B build -DGGML_HIP=ON -DGPU_TARGETS=gfx1030 -DCMAKE_BUILD_TYPE=Release \
    && cmake --build build --config Release -- -j 16
```

support macos:

```bash
cmake -B build \
  -DGGML_METAL=ON \
  -DGGML_METAL_EMBED_LIBRARY=ON \
  -DGGML_METAL_FLASH_ATTN=ON

cmake --build build -j$(sysctl -n hw.ncpu)
```

## Cara build llama.cpp fork ik_llama.cpp

support ROCM:

```bash
HIPCXX="/opt/rocm/lib/llvm/bin/clang" \
HIP_PATH="/opt/rocm" \
cmake -S . -B build \
  -DGGML_HIP=ON \
  -DGGML_HIPBLAS=ON \
  -DGGML_NATIVE=ON \
  -DGPU_TARGETS=gfx1030 \
  -DCMAKE_BUILD_TYPE=Release

cmake --build build --config Release -j$(nproc)
```

notes: ik_llama.cpp ga support untuk ROCM sehingga build nya failed

## Cara menjalankan

support: ROCM
model: Qwen3.5-9B-GGUF

```bash
./build/bin/llama-server \
    --model /home/labkita/models/Qwen3.5-9B-GGUF/Qwen3.5-9B-Q4_K_M.gguf \
    --port 8080 --host 0.0.0.0 \
    --n-gpu-layers 99 \
    --n-cpu-moe 29 \
    --no-mmap \
    --ctx-size 65536 \
    --mlock \
    --parallel 2 \
    --cache-type-k q4_0 --cache-type-v q4_0 \
    --batch-size 128 \
    --ubatch-size 64 \
    --jinja --reasoning off --reasoning-budget 0
```

performance di RX6600 8GB:
- model bartowski/Qwen_Qwen3.6-35B-A3B-GGUF
- prompt eval 140 tok/s
- generate 30 tok/s

support: ROCM
model: Qwen3.5-9B-GGUF/Qwen3.5-9B-Q4_K_M.gguf

```bash
./build/bin/llama-server \
    --model /home/labkita/models/models--bartowski--Qwen_Qwen3.6-35B-A3B-GGUF/blobs/6f5c72e2cde7fb0a1584cc009cdb4513f26733740369d3e2df0e7d7247112d05 \
    --port 8080 --host 0.0.0.0 \
    --n-gpu-layers 99 \
    --n-cpu-moe 34 \
    --no-mmap \
    --ctx-size 262144 \
    --cache-type-k turbo4 \
    --cache-type-v turbo3 \
    --mlock \
    --parallel 1 \
    --batch-size 1024 \
    --ubatch-size 256 \
    --jinja

 ./build/bin/llama-server \
    --model /home/labkita/models/models--bartowski--Qwen_Qwen3.6-35B-A3B-GGUF/blobs/6f5c72e2cde7fb0a1584cc009cdb4513f26733740369d3e2df0e7d7247112d05 \
    --port 8080 --host 0.0.0.0 \
    --n-gpu-layers 99 \
    --n-cpu-moe 29 \
    --no-mmap \
    --ctx-size 65536 \
    --cache-type-k turbo4 \
    --cache-type-v turbo3 \
    --mlock \
    --parallel 1 \
    --batch-size 512 \
    --ubatch-size 128 \
    --jinja

performance di RX6600 8GB:
- model bartowski/Qwen_Qwen3.6-35B-A3B-GGUF
- prompt eval 49.67 tok/s
- generate 20.49 tok/s
- vram usage 6238 MB, free 1540 MB
- ram usage 16144 MB
- context 262144

support: ROCM
model: Qwen3.6-35B-A3B-TQ3_4S

./build/bin/llama-server \
  --model /home/labkita/models/models--YTan2000--Qwen3.6-35B-A3B-TQ3_4S/blobs/6af1c2df5cdeac6975079a6e129bc2043f7bbc0f0ac72125a7572016b452216e \
  --host 0.0.0.0 --port 8080 \
  -ngl 99 -c 65536 -np 1 \
  --n-cpu-moe 26 \
  -ctk q4_0 -ctv tq3_0 \
  -fa on \
  --jinja \
  --chat-template qwen2 \
  --no-cache-prompt \
  --temp 0.2 \
  --top-p 0.9 \
  --reasoning on \
  --reasoning-budget 2048

./build/bin/llama-server \
  --model /home/labkita/models/models--YTan2000--Qwen3.6-35B-A3B-TQ3_4S/blobs/6af1c2df5cdeac6975079a6e129bc2043f7bbc0f0ac72125a7572016b452216e \
  --mmproj /home/labkita/models/models--YTan2000--Qwen3.6-35B-A3B-TQ3_4S/blobs/356dfaa3111376a4f7165e32e8749713378d1700b37cf52e0c50d9f23322334d \
  --port 8080 --host 0.0.0.0 \
  -ctk q4_0 -ctv tq3_0 -fa "on" \
  --cache-ram 8192 --no-context-shift --clear-idle --kv-unified \
  -ngl 99 -c 4096 -np 1 --n-cpu-moe 26 --no-mmproj-offload \
  --jinja --chat-template-file /home/labkita/templates/chat_template.jinja \
  --reasoning off \
  --reasoning-budget 0
  --reasoning-format deepseek
  
  # --jinja --mlock --no-warmup \
  # --reasoning on --reasoning-budget 2048
  # --temp 0.2 \
  # --top-p 0.9 \
  # --chat-template qwen \
  # --no-cache-prompt \
  # --reasoning-format deepseek
  curl http://192.168.18.120:8080/v1/chat/completions \
    -H "Content-Type: application/json" \
    -d '{
      "model": "qwen",
      "messages": [
        {
          "role": "system",
          "content": "You are a helpful assistant that writes clean Go code."
        },
        {
          "role": "user",
          "content": "Write Golang code using Echo framework"
        }
      ],
      "temperature": 0.2,
      "top_p": 0.9,
      "max_tokens": 1024
    }'

./build/bin/llama-server \
    --model /home/labkita/models/models--YTan2000--Qwen3.6-35B-A3B-TQ3_4S/blobs/6af1c2df5cdeac6975079a6e129bc2043f7bbc0f0ac72125a7572016b452216e \
    --port 8080 --host 0.0.0.0 \
    --n-gpu-layers 99 \
    --n-cpu-moe 26 \
    --no-mmap \
    --ctx-size 65536 \
    --cache-type-k q4_0 \
    --cache-type-v tq3_0 \
    --mlock \
    --parallel 1 \
    --batch-size 512 \
    --ubatch-size 128 \
    --no-warmup \
    --temp 0.2 \
    --top-p 0.9 \
    --chat-template qwen \
    --cache-prompt 0 \
    --clear-idle \
    --reasoning off --reasoning-budget 0 --reasoning-format deepseek
    # --flash-attn 'on' \
    # --jinja \

support: ROCM
model: Qwen3.6-27B-TQ3_4S

 # gpu offload sebagian dan model nya bukan moe
 # lemot
 ./build/bin/llama-server \
    --model /home/labkita/models/models--YTan2000--Qwen3.6-27B-TQ3_4S/blobs/dcdee579deeccb5a84a85dab1fba7e73792b94c010480cb3ee9214dbc74a4e6b \
    --port 8080 --host 0.0.0.0 \
    --n-gpu-layers 35 \
    --no-mmap \
    --ctx-size 32768 \
    --cache-type-k q8_0 \
    --cache-type-v q8_0 \
    --mlock \
    --parallel 1 \
    --batch-size 254 \
    --ubatch-size 64 \
    --flash-attn 'on' \
    --no-warmup \
    --jinja

 ./build/bin/llama-server \
    -hf mradermacher/Qwen3-Desert.Coder.MoE-8X0.6B-i1-GGUF:Q4_K_M \
    --port 8080 --host 0.0.0.0 \
    --n-gpu-layers 99 \
    --ctx-size 40290 \
    --cache-type-k f16 \
    --cache-type-v f16 \
    --flash-attn 1 \
    --parallel 1 \
    -b 512 -ub 254 \
    --chat-template-kwargs '{"preserve_thinking": false}'

./build/bin/llama-server \
    --model /Users/fajar/models/models--YTan2000--Qwen3.6-35B-A3B-TQ3_4S/blobs/6af1c2df5cdeac6975079a6e129bc2043f7bbc0f0ac72125a7572016b452216e \
    --port 8080 --host 0.0.0.0 \
    --n-gpu-layers 99 \
    --n-cpu-moe 20 \
    --no-mmap \
    --ctx-size 10240 \
    --cache-type-k q8_0 \
    --cache-type-v q8_0 \
    --mlock \
    --parallel 1 \
    --batch-size 128 \
    --ubatch-size 64 \
    --no-warmup \
    --jinja

```

## Create service

create file `/etc/systemd/system/llama.service`

```bash
[Unit]
Description=Llama.cpp Server (Qwen3.5-9B)
After=network.target

[Service]
Type=simple
User=labkita
Group=labkita

Environment="HSA_ENABLE_SDMA=0"
Environment="HSA_OVERRIDE_GFX_VERSION=10.3.0"
Environment="ROCR_VISIBLE_DEVICES=0"
Environment="HIP_VISIBLE_DEVICES=0"
Environment="LD_LIBRARY_PATH=/opt/rocm/lib:/opt/rocm/lib64"

# Command
ExecStart=/home/labkita/Downloads/llama.cpp/build/bin/llama-server \
    --model /home/labkita/models/Qwen3.5-9B-GGUF/Qwen3.5-9B-Q4_K_M.gguf \
    --port 8080 --host 0.0.0.0 \
    --n-gpu-layers 99 \
    --n-cpu-moe 29 \
    --no-mmap \
    --ctx-size 65536 \
    --mlock \
    --parallel 2 \
    --cache-type-k q4_0 --cache-type-v q4_0 \
    --batch-size 128 \
    --ubatch-size 64 \
    --jinja --reasoning off --reasoning-budget 0

# Restart Policy
Restart=on-failure
RestartSec=5s

# Resource Limits (Opsional tapi disarankan)
LimitMEMLOCK=infinity
LimitNOFILE=65535
TasksMax=infinity

[Install]
WantedBy=multi-user.target
```

## Contoh cara benchmark

```bash
./build/bin/llama-bench \
  -m /home/labkita/models/models--bartowski--Qwen_Qwen3.6-35B-A3B-GGUF/blobs/6f5c72e2cde7fb0a1584cc009cdb4513f26733740369d3e2df0e7d7247112d05 \
  -ngl 99 \
  -ncmoe 34 \
  -mmp 0 \
  -b 1024 \
  -ub 256 \
  -ctk turbo4 \
  -ctv turbo3 \
  -p 0 \
  -n 128 \
  -r 1 
  # -fitc 262144

./build/bin/llama-bench \
  -m /home/labkita/models/models--bartowski--Qwen_Qwen3.6-35B-A3B-GGUF/blobs/6f5c72e2cde7fb0a1584cc009cdb4513f26733740369d3e2df0e7d7247112d05 \
  -ngl 99 \
  -ncmoe 29 \
  -mmp 0 \
  -b 256 \
  -ub 64 \
  -ctk turbo4 \
  -ctv turbo3 \
  -p 0 \
  -n 128 \
  -r 1 
  # -fitc 65536
```

```
options:
  -h, --help
  --numa <distribute|isolate|numactl>         numa mode (default: disabled)
  -r, --repetitions <n>                       number of times to repeat each test (default: 5)
  --prio <-1|0|1|2|3>                         process/thread priority (default: 0)
  --delay <0...N> (seconds)                   delay between each test (default: 0)
  -o, --output <csv|json|jsonl|md|sql>        output format printed to stdout (default: md)
  -oe, --output-err <csv|json|jsonl|md|sql>   output format printed to stderr (default: none)
  --list-devices                              list available devices and exit
  -v, --verbose                               verbose output
  --progress                                  print test progress indicators
  --no-warmup                                 skip warmup runs before benchmarking
  -fitt, --fit-target <MiB>                   fit model to device memory with this margin per device in MiB (default: off)
  -fitc, --fit-ctx <n>                        minimum ctx size for --fit-target (default: 4096)

test parameters:
  -m, --model <filename>                      (default: models/7B/ggml-model-q4_0.gguf)
  -hf, -hfr, --hf-repo <user>/<model>[:quant] Hugging Face model repository; quant is optional, case-insensitive
                                              default to Q4_K_M, or falls back to the first file in the repo if Q4_K_M doesn't exist.
                                              example: ggml-org/GLM-4.7-Flash-GGUF:Q4_K_M
                                              (default: unused)
  -hff, --hf-file <file>                      Hugging Face model file. If specified, it will override the quant in --hf-repo
                                              (default: unused)
  -hft, --hf-token <token>                    Hugging Face access token
                                              (default: value from HF_TOKEN environment variable)
  -p, --n-prompt <n>                          (default: 512)
  -n, --n-gen <n>                             (default: 128)
  -pg <pp,tg>                                 (default: )
  -d, --n-depth <n>                           (default: 0)
  -b, --batch-size <n>                        (default: 2048)
  -ub, --ubatch-size <n>                      (default: 512)
  -ctk, --cache-type-k <t>                    (default: f16)
  -ctv, --cache-type-v <t>                    (default: f16)
  -t, --threads <n>                           (default: 4)
  -C, --cpu-mask <hex,hex>                    (default: 0x0)
  --cpu-strict <0|1>                          (default: 0)
  --poll <0...100>                            (default: 50)
  -ngl, --n-gpu-layers <n>                    (default: 99)
  -ncmoe, --n-cpu-moe <n>                     (default: 0)
  -sm, --split-mode <none|layer|row|tensor>   (default: layer)
  -mg, --main-gpu <i>                         (default: 0)
  -nkvo, --no-kv-offload <0|1>                (default: 0)
  -fa, --flash-attn <0|1>                     (default: 0)
  -dev, --device <dev0/dev1/...>              (default: auto)
  -mmp, --mmap <0|1>                          (default: 1)
  -dio, --direct-io <0|1>                     (default: 0)
  -embd, --embeddings <0|1>                   (default: 0)
  -ts, --tensor-split <ts0/ts1/..>            (default: 0)
  -ot --override-tensor <tensor name pattern>=<buffer type>;...
                                              (default: disabled)
  -nopo, --no-op-offload <0|1>                (default: 0)
  --no-host <0|1>                             (default: 0)
```

## Cara test endpoint

```bash
curl http://192.168.18.120:8080/v1/chat/completions \
    -H "Content-Type: application/json" \
    -d '{
            "model": "Qwen3.6-27B",
            "messages": [
                {
                "role": "user",
                "content": "Halo, kamu model apa?"
                }
            ],
            "max_tokens": 100,
            "temperature": 0.7
        }'
```

## Config di gowails

```json
    "llama-cpp": {
        "api_url": "http://192.168.18.120:8080/v1",
        "api_key": "sasa",
        "available_models": [
          {
            "name": "Qwen3.6-27B",
            "capabilities": {
              "tools": true,
              "images": false,
              "parallel_tool_calls": true,
              "prompt_cache_key": false,
              "chat_completions": true
            }
          }
        ]
      }
```

## Config di librechat

```yaml
    - name: "llamacpp"
    baseURL: "http://192.168.18.120:8080/v1"
    apiKey: "sk-TAegllbZgaGokvWCPn0RlDMsNIy2ohgqOODiYBycsm5qsIfbhWw6JS8QGVoqrdKt"
    models:
        default: [
        "Qwen3.6-27B"
        ]
        fetch: false
    titleConvo: true
    titleModel: "llamacpp"
    summarize: false
    summaryModel: "llamacpp"
    modelDisplayLabel: "llamacpp"
```

## Penjelasan flag

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

contoh:

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

## Contoh LM Studio service

create file di `/etc/systemd/system/lmstudio.service`

```
[Unit]
Description=LM Studio Server
After=network.target

[Service]
Type=oneshot
RemainAfterExit=yes
User=labkita
Environment="HOME=/home/labkita"

# =========================
# START
# =========================

# Start daemon
ExecStartPre=/home/labkita/.lmstudio/bin/lms daemon up

# Clean state sebelum load (penting biar tidak numpuk model lama)
ExecStartPre=/home/labkita/.lmstudio/bin/lms unload --all

# Load model utama (Qwen 9B)
# ExecStartPre=/home/labkita/.lmstudio/bin/lms load qwen/qwen2.5-coder-7b --yes --gpu 1 --parallel 3 -c 32768
# ExecStartPre=/home/labkita/.lmstudio/bin/lms load qwen/qwen3-4b-2507 --yes --gpu 1 --parallel 3 -c 262144
# ExecStartPre=/home/labkita/.lmstudio/bin/lms load qwen/qwen3.5-9b --yes --gpu 1 --parallel 3 -c 262144
# ExecStartPre=/home/labkita/.lmstudio/bin/lms load google/gemma-4-e4b --yes --gpu 1 --parallel 3 -c 131072
# ExecStartPre=/home/labkita/.lmstudio/bin/lms load deepseek/deepsek-r1-0528-qwen3-8b --yes --gpu 1 --parallel 3 -c 131072
ExecStartPre=/home/labkita/.lmstudio/bin/lms load codegemma-7b-it --yes --gpu 1 --parallel 4 -c 8192

# Start API server
ExecStart=/home/labkita/.lmstudio/bin/lms server start --bind "0.0.0.0" --cors

# =========================
# STOP (clean shutdown)
# =========================

# Unload semua model dari VRAM/RAM (ini yang penting banget)
ExecStopPre=/home/labkita/.lmstudio/bin/lms unload --all

# Delay 1 detik
# ExecStopPre=/bin/sleep 1

# Stop API server dulu
ExecStopPre=/home/labkita/.lmstudio/bin/lms server stop

# Matikan daemon terakhir
ExecStop=/home/labkita/.lmstudio/bin/lms daemon down

[Install]
WantedBy=multi-user.target
```

## Cara menjalankan lm studio service

```bash
sudo systemctl daemon-reload
sudo systemctl start lmstudio.service
sudo systemctl enable lmstudio.service
sudo systemctl status lmstudio.service
```

## Beberapa command lm studio cli

```bash
# cek model yg sedang di load
lms ps
lms ls
# download gguf dr hugging face
lms get https://huggingface.co/lmstudio-community/codegemma-7b-it-GGUF
```

## MacOS Support

### fork [llama.cpp-tq3](https://github.com/turbo-tan/llama.cpp-tq3):

edit file `ggml/src/ggml-metal/ggml-metal-device.m`

```cpp
case GGML_OP_MUL_MAT_ID:
    // return has_simdgroup_reduction && op->src[0]->type != GGML_TYPE_NVFP4;
    return has_simdgroup_reduction &&
        op->src[0]->type != GGML_TYPE_NVFP4 &&
        op->src[0]->type != GGML_TYPE_TQ1_0 &&
        op->src[0]->type != GGML_TYPE_TQ2_0 &&
        op->src[0]->type != GGML_TYPE_TQ3_0 &&
        op->src[0]->type != GGML_TYPE_TQ3_1S &&
        op->src[0]->type != GGML_TYPE_TQ3_4S;
```

edit file `ggml/src/ggml-metal/ggml-metal-context.m`

```cpp
//void ggml_metal_get_tensor_async(ggml_metal_t ctx, const struct ggml_tensor * tensor, void * data, size_t offset, size_t size) {
void ggml_metal_get_tensor_async(
        ggml_metal_t ctx,
        const struct ggml_tensor * tensor,
        void * data,
        size_t offset,
        size_t size) {
    if (!data || size == 0) {
        return;
    }

    @autoreleasepool {
        id<MTLDevice> device = ggml_metal_device_get_obj(ctx->dev);
        // id<MTLBuffer> buf_dst = [device newBufferWithBytesNoCopy:data
        //                                                     length:size
        //                                                     options:MTLResourceStorageModeShared
        //                                                 deallocator:nil];

        // GGML_ASSERT(buf_dst);

        // Source GPU buffer
        struct ggml_metal_buffer_id bid_src = ggml_metal_get_buffer_id(tensor);
        if (bid_src.metal == nil) {
            // GGML_ABORT("%s: failed to find buffer for tensor '%s'\n", __func__, tensor->name);
            GGML_ABORT("%s: failed to find buffer for tensor '%s'\n",
                    __func__, tensor->name);
        }

        bid_src.offs += offset;
    
        // queue the copy operation into the queue of the Metal context
        // this will be queued at the end, after any currently ongoing GPU operations
        // CPU-visible staging buffer
        id<MTLBuffer> staging = [device newBufferWithLength:size
                                                    options:MTLResourceStorageModeShared];
        GGML_ASSERT(staging);
    
        // Queue GPU → staging copy
        id<MTLCommandQueue> queue = ggml_metal_device_get_queue(ctx->dev);
        id<MTLCommandBuffer> cmd_buf = [queue commandBuffer];
        id<MTLBlitCommandEncoder> encoder = [cmd_buf blitCommandEncoder];
    
        [encoder copyFromBuffer:bid_src.metal
                    sourceOffset:bid_src.offs
                    // toBuffer:buf_dst
                    toBuffer:staging
                destinationOffset:0
                            size:size];

        [encoder endEncoding];
        // [cmd_buf commit];
        // [buf_dst release];
    
        // do not wait here for completion
        //[cmd_buf waitUntilCompleted];
        // Copy staging → user buffer when complete
        __block id<MTLBuffer> staging_ref = [staging retain];
        [cmd_buf addCompletedHandler:^(id<MTLCommandBuffer> cb) {
            if (cb.status == MTLCommandBufferStatusCompleted) {
                memcpy(data, staging_ref.contents, size);
            }
            [staging_ref release];
        }];

        [cmd_buf commit];
    
        // instead, remember a reference to the command buffer and wait for it later if needed
        // Track command buffer
        [ctx->cmd_bufs_ext addObject:cmd_buf];
        ctx->cmd_buf_last = cmd_buf;
    
        [cmd_buf retain];
    
        [staging release];
    }
}
```
