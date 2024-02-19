# LFX-Mentorship-Pre-test-3170
This repository is a test integration of Whisper.cpp as a new WASI-NN backend for the LFX Mentorship project[#3170](https://github.com/WasmEdge/WasmEdge/issues/3170)

## Table of Contents
- [System Information](#system-information)
- [Preparation](#preparation)
- [Running llama](#running-llama)
  - [Preparation](#preparation-1)
  - [Build](#build)
  - [Selecting the Model](#selecting-the-model)
  - [Talking with Llama](#talking-with-llama)
- [Running whisper](#running-whisper)
  - [Whisper preparation](#whisper-preparation)
  - [Transcribe audio file](#transcribe-audio-file)
  - [Real time transcription](#real-time-transcription)
  - [Multilingual Transcription](#multilingual-transcription)

## System Information
```
OS: MacOs Sonoma 14.3 23D56 arm64
Kernel: 23.3.0
Homebrew: 4.2.8
cmake version: 3.28.3
```

## Preparation

Create a clone using git.

```
git clone https://github.com/WasmEdge/WasmEdge.git && cd WasmEdge
git checkout hydai/0.13.5_ggml_lts
```

## Running llama

### Preparation
Installing dependencies

Cmake:`brew install cmake`

Ninja:`brew install ninja`

LLVM:`brew install llvm`


Follow the [guide](https://wasmedge.org/docs/contribute/source/plugin/wasi_nn/#build-wasmedge-with-wasi-nn-llamacpp-backend) to run llama.cpp.

## Build

Please run in the WasmEdge directory.
```
cmake -GNinja -Bbuild -DCMAKE_BUILD_TYPE=Release \
-DWASMEDGE_PLUGIN_WASI_NN_BACKEND="GGML" \
-DWASMEDGE_PLUGIN_WASI_NN_GGML_LLAMA_METAL=ON \
-DWASMEDGE_PLUGIN_WASI_NN_GGML_LLAMA_BLAS=OFF \
.
```

```
cmake --build build
cmake -install build
```

Download a cross-platform compatible portable Wasm file for the chat app. This application allows you to chat with the model via the command line.
```
curl -LO https://github.com/second-state/llama-utils/raw/main/chat/llama-chat.wasm
```

### Selecting the Model
Choose and download a model in ggml format. This time, llama-2-7b-chat.Q5_K_M.gguf was selected.Please download your preferred model from [here](https://huggingface.co/TheBloke/Llama-2-7B-Chat-GGUF/tree/main).

```
curl -LO https://huggingface.co/TheBloke/Llama-2-7b-Chat-GGUF/resolve/main/llama-2-7b-chat.Q5_K_M.gguf
```

### Talking with Llama
```
wasmedge --dir .:. \
  --nn-preload default:GGML:AUTO:llama-2-7b-chat.Q5_K_M.gguf \
    wasmedge-ggml-llama.wasm default
```
If it doesn't work as expected, please check [here](https://github.com/second-state/LlamaEdge/tree/main#requirements).

<img width="1280" alt="llama-chat-sample" src="https://github.com/cuticle999/LFX-Mentorship-Pre-test-3170/assets/26399136/d19f0bf9-f9cc-43cb-8b49-ca855b71e96c">

## Running whisper
Let's run [whisper.cpp](https://github.com/ggerganov/whisper.cpp) for transcription and real-time transcription!

### Whisper preparation
1. Clone the repository.
```
git clone https://github.com/ggerganov/whisper.cpp.git
cd whisper.cpp
```

2. Select a model
Download the whisper model converted to ggml format. This time, base.en was used.

```
bash ./models/download-ggml-model.sh base.en
```

Please refer here for selecting a model.
[model](https://github.com/ggerganov/whisper.cpp/blob/master/models/README.md)
Models are tiny, base, small, medium, large, with accuracy increasing from left to right, but also file size and memory usage.
>Models are multilingual unless the model name includes .en.

### Transcribe audio file.
Transcribe using a sample audio.

```
./main -f samples/jfk.wav
```

Success is achieved if transcription is completed as shown below.
>And so my fellow Americans, ask not what your country can do for you, ask what you can do for your country.
<img width="1279" alt="スクリーンショット 2024-02-18 15 03 29" src="https://github.com/cuticle999/LFX-test/assets/26399136/01a0fe06-6538-48ee-a05a-4e83280bc6e9">

### Real time transcription

1. Download dependencies.
We will download the dependencies and proceed with the execution.

```brew install sdl2```

2. Transcription in real time
When executed, it can transcribe from microphone input.

```
make stream
./stream -m ./models/ggml-base.en.bin -t 8 --step 500 --length 5000
```

The demo audio was created with [ElevenLabs](https://elevenlabs.io/).

https://github.com/cuticle999/LFX-Mentorship-Pre-test-3170/assets/26399136/6eb6ccbd-3748-4fc9-a831-f89037672845

### Multilingual Transcription
To use languages other than English, please specify the language using the -l option. Without this option, the system may default to English.

The example provided uses the 'base' model.The following command allows for real-time transcription in Japanese.

```
bash ./models/download-ggml-model.sh base
```

```
make stream
./stream -m ./models/ggml-base.bin -l ja -t 8 --step 500 --length 5000
```
