# Persian Audio Watermarking

Does the AudioSeal watermark survive on synthetic Persian speech?

This project tests the [AudioSeal](https://arxiv.org/abs/2401.17264) watermark (a fixed 16-bit message) on Persian speech synthesized with **Mana-Persian-Piper**, and measures how it holds up against 11 common attacks: MP3 compression at four bitrates, white noise at two levels, speed change, gain reduction, a low-pass filter, and resampling. Speech quality is measured with word error rate (WER) from Whisper small.

## Key results

| Condition | Mean detection prob. | Detected | Mean bit accuracy | Mean WER (Whisper small) |
|---|---:|---:|---:|---:|
| Clean watermarked | 1.0000 | 10/10 | 1.0000 | 0.8044 |
| MP3 256k / 128k / 64k / 32k | 1.0000 / 1.0000 / 0.9996 / 0.9947 | 10/10 | 1.0000 | 0.846–0.882 |
| White noise 20 dB | 0.3872 | 1/10 | 0.8499 | 0.8349 |
| White noise 10 dB | 0.1299 | 0/10 | 0.6124 | 1.0030 |
| Speed 0.9x / 1.1x | 0.1097 / 0.1019 | 0/10 | 0.9500 / 0.9688 | 0.8030 / 0.8710 |
| Gain 0.5 / Low-pass 4 kHz / Resample 8 kHz | 1.0000 | 10/10 | 1.0000 | 0.769–0.848 |

**In short:** the watermark is inaudible and robust against MP3 compression, gain, low-pass filtering, and resampling. White noise breaks both detection and intelligibility, and speed change breaks *detection* (0/10) while the message itself mostly survives (95–97% bit accuracy) — so detection probability and bit accuracy must be reported separately.

The base model stayed Mana-Persian-Piper: an earlier MMS-TTS base was unintelligible in Persian, and Tacotron2 + ParallelWaveGAN was no better (WER 79.64% vs 77.66%). F5-TTS cannot read Persian script at all.

## Repository contents

```
├── README.md
├── report/
│   ├── persian-watermark-final-report-en.pdf    # Final report, version 2 (25 pages)
│   └── persian-watermark-final-report-en-word.docx
├── notebooks/
│   ├── 01-persian-watermark-smoke-test.ipynb        # Stage 1: MMS-TTS + AudioSeal
│   ├── 01b-persian-watermark-mana-piper.ipynb       # Stage 1b: Mana-Persian-Piper + AudioSeal
│   ├── 02-persian-watermark-robustness.ipynb        # Stage 2: 11 attacks, Whisper small WER
│   └── 03-persian-wavmark-comparison.ipynb          # WavMark vs AudioSeal (same sentences & attacks)
└── results/
    └── summary_stage2.csv    # Stage-2 mean results (120 rows: 10 sentences × 12 conditions)
```

## How to run

The notebooks are made for **Google Colab** (a T4 GPU is recommended; Whisper runs on CPU because of a Colab GPU library issue — see notebook 02). Run them in order: 01 → 01b → 02 → 03.

- Fixed test data in every stage: the same 10 Persian sentences and the same 16-bit message `[1,0,1,1,0,1,0,0,1,1,0,1,0,1,1,0]`.
- Notebook 03 compares WavMark with AudioSeal on the same sentences and attacks. WavMark returns a 16-bit message or nothing, so its comparison metric is the **detection rate**, not a probability.

## Full report

The complete report (method, all stages, analysis, limitations, future work, and all 19 references with DOIs) is in [`report/`](report/).

## References

The report's reference list (19 sources, all with DOIs) covers the watermarking literature used here: Cox et al. (1997), Hartung & Kutter (1999), Chen & Wornell (2001), Hua et al. (2016), Bastanfard & Amirkhani (2020), ASVspoof 2021, AASIST, WavMark, AudioSeal, TraceableSpeech, SilentCipher, AudioMarkBench, XAttnMark, WAKE, and recent reviews and provenance work (Cao 2025; MelShield 2026; TriniMark 2025; Golaszewski et al. 2026; Dathathri et al. 2024, *Nature*).

## Author

Sadraddin Shahvand — Text-to-Speech course, supervised by Dr. Azam Bastanfard.

## License

MIT — see [LICENSE](LICENSE).
