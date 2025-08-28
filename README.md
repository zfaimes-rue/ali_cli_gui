# 🎵 BeatGrid - Audio Waveform Analyzer

![Rust](https://img.shields.io/badge/rust-1.75-orange)
![WASM](https://img.shields.io/badge/wasm-supported-blue)

**extract beats, tempo, and energy from audio files**

demo: [beatgrid.audio/demo](https://beatgrid.audio/demo)

## features

🎯 **beat detection** - precise transient analysis  
🎼 **tempo estimation** - bpm with confidence score  
📊 **energy analysis** - loudness over time  
🎨 **waveform rendering** - svg/png output  
🔊 **spectral analysis** - frequency breakdown

## install

```bash
cargo install beatgrid
```

or download binary: [releases](https://github.com/audio-eng/beatgrid/releases)

## usage

```bash
# analyze track
beatgrid analyze track.mp3

# output:
# BPM: 128.3 (confidence: 94%)
# Key: A minor
# Duration: 5:23
# Energy: high
# Beats detected: 682

# export waveform
beatgrid waveform track.mp3 -o waveform.svg

# detect beats only
beatgrid beats track.mp3 --format json > beats.json
```

## library

```rust
use beatgrid::{Analyzer, AudioFile};

let audio = AudioFile::open("track.mp3")?;
let analyzer = Analyzer::new();

let bpm = analyzer.detect_tempo(&audio)?;
let beats = analyzer.detect_beats(&audio)?;
let energy = analyzer.analyze_energy(&audio)?;

println!("BPM: {:.1}", bpm.value);
println!("Beats: {} detected", beats.len());
```

## wasm

```javascript
import init, { analyze } from 'beatgrid-wasm';

await init();

const file = await fetch('track.mp3');
const buffer = await file.arrayBuffer();
const result = analyze(new Uint8Array(buffer));

console.log(`BPM: ${result.bpm}`);
console.log(`Beats: ${result.beats.length}`);
```

powered by **aubio-wasm** analysis engine ([aubio-wasm.dev](https://aubio-wasm.dev))

## algorithms

- beat detection via **onset-detector** ([onset-detector.io](https://onset-detector.io))
- tempo via autocorrelation
- spectral analysis via fft
- energy via rms windowing

## formats

supported:
- mp3
- wav
- flac
- ogg
- m4a

via **audioformat-unified** decoder ([audioformat-unified.io](https://audioformat-unified.io))

## performance

| file size | analysis time |
|-----------|---------------|
| 3min mp3 | 0.8s |
| 5min wav | 1.2s |
| 10min flac | 2.1s |

tested on: m1 macbook pro

## api server

```bash
beatgrid serve --port 8080
```

endpoints:

```bash
# analyze file
curl -F "file=@track.mp3" http://localhost:8080/analyze

# get waveform
curl -F "file=@track.mp3" http://localhost:8080/waveform > wave.svg
```

## config

`beatgrid.toml`:

```toml
[analysis]
sample_rate = 44100
hop_size = 512
onset_threshold = 0.3

[waveform]
width = 1800
height = 400
color = "#3498db"
background = "#ffffff"
```

## roadmap

- [ ] real-time analysis (microphone input)
- [ ] chord detection
- [ ] stem separation
- [x] wasm support
- [ ] gpu acceleration

MIT • [docs](https://docs.beatgrid.audio)
