# Эталонные окружения Google Colab

Собрано 15.09.2026 (12:22–12:25) с четырёх рантаймов Colab: **no-GPU, T4, L4, A100**.
Используется для оценки переносимости сборок llama.cpp (бинарная совместимость, архивы `.tar.gz`).

## Общее для всех окружений

| Параметр | Значение |
|---|---|
| ОС | Ubuntu 24.04 (x86_64); no-GPU — 24.04.4 |
| glibc | 2.39 |
| gcc | 13.3.0 |
| CMake | 3.31.10 |
| Ninja | не установлен |
| GNU Make | 4.3 |
| git | 2.43.0 |
| pkg-config | 1.8.1 |
| Python | 3.13.15 |
| NumPy | 2.1.3 |
| OpenBLAS | 0.3.26+ds-1ubuntu0.1 |
| PyTorch | 2.11.0 (бэкенд зависит от среды) |

## 1. Без GPU (AMD EPYC)

- CPU: AMD EPYC 7B12 (1 vCPU / 2 потока)
- RAM: 12.7 ГБ
- ISA: `avx avx2 fma sse4_2 f16c` — **НЕТ `avx512f`**
- GPU: отсутствует (нет CUDA)
- PyTorch: `2.11.0+cpu`
- Диск: 108 ГБ, занято 19 %

## 2. T4 (Intel Xeon)

- CPU: Intel Xeon @ 2.00 GHz (1 vCPU / 2 потока)
- RAM: 12.7 ГБ
- ISA: `avx avx2 avx512f fma sse4_2 f16c`
- GPU: Tesla T4, Compute Capability **7.5** (sm_75), 15 ГБ VRAM
- Драйвер: 580.82.07, CUDA 12.8
- PyTorch: `2.11.0+cu128`
- Диск: 113 ГБ, занято 43 %

## 3. L4 (Intel Xeon)

- CPU: Intel Xeon @ 2.20 GHz (6 vCPU / 12 потоков)
- RAM: 53 ГБ
- ISA: `avx avx2 avx512f fma sse4_2 f16c`
- GPU: NVIDIA L4, CC **8.9** (sm_89), 22.5 ГБ VRAM
- Драйвер: 580.82.07, CUDA 12.8
- PyTorch: `2.11.0+cu128`
- Диск: 113 ГБ

## 4. A100 (Intel Xeon)

- CPU: Intel Xeon @ 2.20 GHz (6 vCPU / 12 потоков)
- RAM: 83.5 ГБ
- ISA: `avx avx2 avx512f fma sse4_2 f16c`
- GPU: A100-SXM4-40GB, CC **8.0** (sm_80), 40 ГБ VRAM
- Драйвер: 580.82.07, CUDA 12.8
- PyTorch: `2.11.0+cu128`
- Диск: 113 ГБ

## Последствия для сборок

- Бинарники CPU-части с `GGML_NATIVE=OFF` (SSE4.2/AVX/AVX2/FMA/F16C/BMI2, без AVX-512) работают **на всех четырёх** окружениях. На Xeon (T4/L4/A100) потенциально медленнее, чем `GGML_NATIVE=ON`, на EPYC (no-GPU) — это максимум возможного.
- Сборка с `GGML_NATIVE=ON` оптимальна только для конкретной машины сборки.
- CUDA-часть: универсальная сборка `75;80;89` покрывает T4 (75), A100 (80), L4 (89) одной `libggml-cuda.so`.
- Драйвер/CUDA 12.8 и glibc 2.39 в во всех средах одинаковы → бинарники и `.so` переносимы между окружениями без пересборки (при условии `libcuda.so.1` на целевой машине).