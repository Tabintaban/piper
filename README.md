![Piper logo](etc/logo.png)

A fast, local neural text to speech system that sounds great and is optimized for the Raspberry Pi 4.
Piper is used in a [variety of projects](#people-using-piper).

``` sh
echo 'Welcome to the world of speech synthesis!' | \
  ./piper --model en_US-lessac-medium.onnx --output_file welcome.wav
```

[Listen to voice samples](https://rhasspy.github.io/piper-samples) and check out a [video tutorial by Thorsten Müller](https://youtu.be/rjq5eZoWWSo)

Voices are trained with [VITS](https://github.com/jaywalnut310/vits/) and exported to the [onnxruntime](https://onnxruntime.ai/).

This is a project of the [Open Home Foundation](https://www.openhomefoundation.org/).

## Voices

Our goal is to support Home Assistant and the [Year of Voice](https://www.home-assistant.io/blog/2022/12/20/year-of-voice/).

[Download voices](VOICES.md) for the supported languages:

* Arabic (ar_JO)
* Catalan (ca_ES)
* Czech (cs_CZ)
* Danish (da_DK)
* German (de_DE)
* Greek (el_GR)
* English (en_GB, en_US)
* Spanish (es_ES, es_MX)
* Finnish (fi_FI)
* French (fr_FR)
* Hungarian (hu_HU)
* Icelandic (is_IS)
* Italian (it_IT)
* Georgian (ka_GE)
* Kazakh (kk_KZ)
* Luxembourgish (lb_LU)
* Nepali (ne_NP)
* Dutch (nl_BE, nl_NL)
* Norwegian (no_NO)
* Polish (pl_PL)
* Portuguese (pt_BR, pt_PT)
* Romanian (ro_RO)
* Russian (ru_RU)
* Serbian (sr_RS)
* Swedish (sv_SE)
* Swahili (sw_CD)
* Turkish (tr_TR)
* Ukrainian (uk_UA)
* Vietnamese (vi_VN)
* Chinese (zh_CN)

You will need two files per voice:

1. A `.onnx` model file, such as [`en_US-lessac-medium.onnx`](https://huggingface.co/rhasspy/piper-voices/resolve/v1.0.0/en/en_US/lessac/medium/en_US-lessac-medium.onnx)
2. A `.onnx.json` config file, such as [`en_US-lessac-medium.onnx.json`](https://huggingface.co/rhasspy/piper-voices/resolve/v1.0.0/en/en_US/lessac/medium/en_US-lessac-medium.onnx.json)

The `MODEL_CARD` file for each voice contains important licensing information. Piper is intended for text to speech research, and does not impose any additional restrictions on voice models. Some voices may have restrictive licenses, however, so please review them carefully!


## Installation

You can [run Piper with Python](#running-in-python) or download a binary release:

* [amd64](https://github.com/rhasspy/piper/releases/download/v1.2.0/piper_amd64.tar.gz) (64-bit desktop Linux)
* [arm64](https://github.com/rhasspy/piper/releases/download/v1.2.0/piper_arm64.tar.gz) (64-bit Raspberry Pi 4)
* [armv7](https://github.com/rhasspy/piper/releases/download/v1.2.0/piper_armv7.tar.gz) (32-bit Raspberry Pi 3/4)

If you want to build from source, see the [Makefile](Makefile) and [C++ source](src/cpp).
You must download and extract [piper-phonemize](https://github.com/rhasspy/piper-phonemize) to `lib/Linux-$(uname -m)/piper_phonemize` before building.
For example, `lib/Linux-x86_64/piper_phonemize/lib/libpiper_phonemize.so` should exist for AMD/Intel machines (as well as everything else from `libpiper_phonemize-amd64.tar.gz`).


## Usage

1. [Download a voice](#voices) and extract the `.onnx` and `.onnx.json` files
2. Run the `piper` binary with text on standard input, `--model /path/to/your-voice.onnx`, and `--output_file output.wav`

For example:

``` sh
echo 'Welcome to the world of speech synthesis!' | \
  ./piper --model en_US-lessac-medium.onnx --output_file welcome.wav
```

For multi-speaker models, use `--speaker <number>` to change speakers (default: 0).

See `piper --help` for more options.

### Streaming Audio

Piper can stream raw audio to stdout as its produced:

``` sh
echo 'This sentence is spoken first. This sentence is synthesized while the first sentence is spoken.' | \
  ./piper --model en_US-lessac-medium.onnx --output-raw | \
  aplay -r 22050 -f S16_LE -t raw -
```
Stream raw audio for Windows 10 x64
``` sh
echo 'This sentence is spoken first. This sentence is synthesized while the first sentence is spoken.' | \
  ./piper.exe --model en_US-lessac-medium.onnx --output-raw | \
  ffplay -nodisp - -f s16le -ar 22050 -autoexit
```

This is **raw** audio and not a WAV file, so make sure your audio player is set to play 16-bit mono PCM samples at the correct sample rate for the voice.

### JSON Input

The `piper` executable can accept JSON input when using the `--json-input` flag. Each line of input must be a JSON object with `text` field. For example:

``` json
{ "text": "First sentence to speak." }
{ "text": "Second sentence to speak." }
```

Optional fields include:

* `speaker` - string
    * Name of the speaker to use from `speaker_id_map` in config (multi-speaker voices only)
* `speaker_id` - number
    * Id of speaker to use from 0 to number of speakers - 1 (multi-speaker voices only, overrides "speaker")
* `output_file` - string
    * Path to output WAV file
    
The following example writes two sentences with different speakers to different files:

``` json
{ "text": "First speaker.", "speaker_id": 0, "output_file": "/tmp/speaker_0.wav" }
{ "text": "Second speaker.", "speaker_id": 1, "output_file": "/tmp/speaker_1.wav" }
```


## People using Piper

Piper has been used in the following projects/papers:

* [Home Assistant](https://github.com/home-assistant/addons/blob/master/piper/README.md)
* [Rhasspy 3](https://github.com/rhasspy/rhasspy3/)
* [NVDA - NonVisual Desktop Access](https://www.nvaccess.org/post/in-process-8th-may-2023/#voices)
* [Image Captioning for the Visually Impaired and Blind: A Recipe for Low-Resource Languages](https://www.techrxiv.org/articles/preprint/Image_Captioning_for_the_Visually_Impaired_and_Blind_A_Recipe_for_Low-Resource_Languages/22133894)
* [Open Voice Operating System](https://github.com/OpenVoiceOS/ovos-tts-plugin-piper)
* [JetsonGPT](https://github.com/shahizat/jetsonGPT)
* [LocalAI](https://github.com/go-skynet/LocalAI)
* [Lernstick EDU / EXAM: reading clipboard content aloud with language detection](https://lernstick.ch/)
* [Natural Speech - A plugin for Runelite, an OSRS Client](https://github.com/phyce/rl-natural-speech)
* [mintPiper](https://github.com/evuraan/mintPiper)

## Training

See the [training guide](TRAINING.md) and the [source code](src/python).

Pretrained checkpoints are available on [Hugging Face](https://huggingface.co/datasets/rhasspy/piper-checkpoints/tree/main)


## Running in Python

See [src/python_run](src/python_run)

Install with `pip`:

``` sh
pip install piper-tts
```

and then run:

``` sh
echo 'Welcome to the world of speech synthesis!' | piper \
  --model en_US-lessac-medium \
  --output_file welcome.wav
```

This will automatically download [voice files](https://huggingface.co/rhasspy/piper-voices/tree/v1.0.0) the first time they're used. Use `--data-dir` and `--download-dir` to adjust where voices are found/downloaded.

If you'd like to use a GPU, install the `onnxruntime-gpu` package:


``` sh



.venv/bin/pip3 install onnxruntime-gpu
```

and then run `piper` with the `--cuda` argument. You will need to have a functioning CUDA environment, such as what's available in [NVIDIA's PyTorch containers](https://catalog.ngc.nvidia.com/orgs/nvidia/containers/pytorch).

## Звуковой поток напрямую на колонки через плеер ffplay
```sh
echo "This is the test audio." | .\piper.exe --model en_US-lessac-low.onnx --output-raw | ffplay -nodisp - -f s16le -ar 22050 -autoexit
```
```sh
echo "Привет" | .\piper.exe --model ru_RU-ruslan-medium.onnx --output-raw | ffplay -nodisp - -f s16le -ar 22050 -autoexit
```
```sh
cat demo.txt | .\piper.exe --model ru_RU-ruslan-medium.onnx --output-raw | ffplay -nodisp - -f s16le -ar 22050 -autoexit
```
cat demo.txt - берет буквы из файла demo.txt
.\piper.exe - используя модель опреобразует буквы в речь отправляет его
командой --output-raw на проигрыватель
ffplay - приняв сырой звуковой поток (raw) форматирует его в s16le и
проигрывает его через колонки

-nodisp -без окна проигрывателя
-ar 22050 -скорость воспроизведения
-autoexit -закрытие окна проигрывателя
-f s16le -формат файла, использовать только его, все остальные шипят.

Необработанный звук в FFmpeg может принимать несколько разных «форм», то есть форматов сэмплов. Например:
s -означает «подписанный» (для целочисленных представлений), u будет означать «беззнаковый»
16 -означает 16 бит на выборку.
le -означает кодирование с прямым порядком байтов для образцов

Вы можете увидеть список поддерживаемых форматов примеров, проверив вывод ffmpeg -formats:
https://trac.ffmpeg.org/wiki/audio%20types
$ ffmpeg-форматы | grep ПКМ
DE alaw PCM A-закон
DE f32be PCM 32-битные числа с плавающей запятой, обратный порядок байтов
DE f32le PCM 32-битная с плавающей запятой, прямой порядок байтов
DE f64be PCM 64-битная с плавающей запятой, обратный порядок байтов
DE f64le PCM 64-битная с плавающей запятой, прямой порядок байтов
DE Law PCM Law-Law
DE s16be PCM подписанный 16-битный порядок байтов с прямым порядком байтов
DE s16le PCM подписан 16-битным прямым порядком байтов
DE s24be PCM подписанный 24-битный порядок байтов с прямым порядком байтов
DE s24le PCM подписанный 24-битный прямой порядок байтов
DE s32be PCM подписанный 32-битный формат с прямым порядком байтов
DE s32le PCM подписанный 32-битный формат с прямым порядком байтов
DE s8 PCM подписанный 8-битный
DE u16be PCM беззнаковый 16-битный с прямым порядком байтов
DE u16le PCM беззнаковый 16-битный порядок байтов с прямым порядком байтов
DE u24be PCM беззнаковый 24-битный с прямым порядком байтов
DE u24le PCM беззнаковый 24-битный с прямым порядком байтов
DE u32be PCM беззнаковый 32-битный с прямым порядком байтов
DE u32le PCM 32-битный без знака с прямым порядком байтов
DE u8 PCM беззнаковый 8-битный
