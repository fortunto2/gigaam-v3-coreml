# GigaAM v3 e2e RNN-T — Core ML, int8 encoder

Russian speech-to-text with punctuation and capitals, on-device (iOS 17+, macOS 14+).

- Model: [GigaAM v3](https://github.com/salute-developers/GigaAM) e2e RNN-T by Sber (MIT).
- Core ML conversion (fp16): [smkrv/gigaam-v3-e2e-rnnt-coreml](https://huggingface.co/smkrv/gigaam-v3-e2e-rnnt-coreml) (MIT).
- This release: the encoder weights linearly quantized to int8 per channel with
  `coremltools.optimize.coreml` (442 → 222 MB); decoder and joint unchanged (fp16).
  Token-exact against fp16 on a 6-clip Russian smoke set (CPU+GPU compute units).

Files are the three `.mlpackage` directories flattened, so an app can fetch them one by one
and rebuild the package before `MLModel.compileModel`:

```
<Name>.Manifest.json      -> <Name>.mlpackage/Manifest.json
<Name>.model.mlmodel      -> <Name>.mlpackage/Data/com.apple.CoreML/model.mlmodel
<Name>.weight.bin         -> <Name>.mlpackage/Data/com.apple.CoreML/weights/weight.bin
```

The encoder's `weight.bin` (222 MB) is published as five 50 MB pieces,
`GigaAMv3Encoder.weight.bin.part0` … `part4`: `cat part0 part1 part2 part3 part4 > weight.bin`.

I/O contract, mel front-end (16 kHz, 64 HTK mels, n_fft = win = 320, hop = 160, center = false,
log(clamp(1e-9, 1e9))), blank id 1024 and the greedy loop: see the smkrv model card.
Used by [Voxlog](https://github.com/fortunto2/voxlog) (`ios/Voxlog/Audio/GigaAMEngine.swift`).

License: MIT (see LICENSE, the upstream GigaAM license).
