# rustel-talkit

A Talk It! style speech synthesizer in native Rust. Text goes in, samples
come out. No models, no network, no dependencies.

The crate keeps the parameter model of the old Talk It! program: twenty
voice presets, pitch, speed, quality and effort, in English and Spanish.
The engine is written from measurements of that program's output. It speaks
in the engine's 8-bit steps at 11.025 kHz, so it sounds as the original
does.

## The talkit command

The command speaks through the default output device:

```sh
cargo install --git https://github.com/tzfm/rustel-talkit
talkit hello world
```

Parameters come as key=value pairs, and bare words are the text:

```sh
talkit personality=little-robot quality=monotone "I am a robot"
talkit lang=spanish out=hola.wav "hola mundo"
```

| Key | Short | Values |
| --- | --- | --- |
| personality | v | a voice preset, such as little-robot |
| pitch | p | 50..500, the start pitch in Hz, default 100 |
| speed | s | 50..400, 150 is nominal |
| quality | q | natural, monotone, sung |
| effort | e | normal, breathy, whispered |
| lang | l | english, spanish |
| out | o | a WAV file to write instead of playing |

A personality applies its whole preset, so put overrides after it.
`talkit --help` lists the twenty voices.

## As a library

The library is pure DSP. The default `play` feature adds cpal so the
command can reach an output device. A host that renders audio itself turns
the feature off and links no audio stack:

```toml
[dependencies]
rustel-talkit = { git = "https://github.com/tzfm/rustel-talkit", tag = "v0.1.0", default-features = false }
```

```rust
use rustel_talkit::{params::Params, render, wav};

let samples = render::render("hello world", &Params::default());
wav::write_wav("hello.wav", &samples, rustel_talkit::RATE).unwrap();
```

`render::render_phonetic` speaks the engine's phonetic notation, and
`params::Params::apply` reads the same key=value pairs as the command.

## In rustel

Rustel speaks text in a score through this engine. A `talkit` hap renders
to samples, so speech plays as any other sound does. See the
[studio guide](https://github.com/tzfm/rustel/blob/main/docs/talkit.md).

## License

AGPL-3.0-or-later. See [LICENSE](LICENSE) and [NOTICE.md](NOTICE.md).

The original Talk It! program spoke through the closed SoftVoice engine.
This project is an independent engine rebuilt from measurements of that
program's output. It is not affiliated with Microsoft or SoftVoice.
