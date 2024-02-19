# LFX-Mentorship-Pre-test-3170
This repository is a test integration of Whisper.cpp as a new WASI-NN backend for the LFX Mentorship project[#3170](https://github.com/WasmEdge/WasmEdge/issues/3170)

## System Information
```
OS: MacOs Sonoma 14.3 23D56 arm64
Kernel: 23.3.0
Homebrew: 4.2.8
cmake version: 3.28.3
```
  
## Preparation

To create a local version of the remote repository, make a clone.

```
git clone https://github.com/WasmEdge/WasmEdge.git && cd WasmEdge
git checkout hydai/0.13.5_ggml_lts
```

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

https://github.com/cuticle999/LFX-test/assets/26399136/e817d7c4-b559-4aa9-92c2-717560a7a8da

### Multilingual Transcription
To use languages other than English, please specify the language using the -l option. Without this option, the system may default to English.

The example provided uses the 'base' model.

```
bash ./models/download-ggml-model.sh base
```

```
make stream
./stream -m ./models/ggml-base.bin -l ja -t 8 --step 500 --length 5000
```

<img width="552" alt="whisper-ja" src="https://github.com/cuticle999/LFX-test/assets/26399136/10c7842a-8d03-4852-aa01-f8e8fc61142e">
