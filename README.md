# Tacotron & FalafelTron 
This repository brings together two related text-to-speech (TTS) projects you can explore side-by-side:

- **Tacotron (baseline)** — a faithful, educational re-implementation of the 2017 Google model that turns text into mel-spectrograms and reconstructs audio.
- **FalafelTron (improved)** — a modern re-design that keeps Tacotron’s end-to-end spirit but replaces the sequential parts with Transformers and a higher-fidelity vocoder.

The goal is to show *how design choices shape speech quality, speed, and stability*—from classic sequence-to-sequence with attention to a streamlined, attention-first architecture.

---

## What’s Inside (High-Level)

### 1) Tacotron — The Classic Baseline
- **Idea:** Characters → mel-spectrograms via an encoder-decoder with **location-sensitive attention**.
- **Design:** Convolutional front-end + **CBHG** (Convolution Bank, Highway, BiGRU) + RNN decoder.
- **Vocoder:** **Griffin-Lim** (iterative phase reconstruction).
- **Why it matters:** A transparent, step-by-step pipeline that makes alignment learning visible (attention maps) and clarifies core TTS concepts.
- **Tradeoffs:** Solid learning signal and interpretability, but slower training and audio quality capped by Griffin-Lim.

### 2) FalafelTron — The Modern Re-Think
- **Idea:** Keep Tacotron’s mapping (text → mel) but swap recurrence for **self-attention** to parallelize and stabilize training.
- **Design:** **Transformer encoder/decoder** (multi-head attention, cross-attention for alignment) and a simplified stack (no CBHG).
- **Vocoder:** **WaveGlow** (flow-based, non-iterative) for cleaner, more natural audio.
- **Why it matters:** Faster convergence, better long-range dependencies, and higher perceived quality with similar data.
- **Tradeoffs:** More moving parts in the attention blocks, and quality still scales with training time and data.

---

## Big Picture: Two Design Philosophies

| Dimension | Tacotron (2017-style) | FalafelTron (Transformer) |
|---|---|---|
| **Computation** | Sequential (RNNs) | Parallel (self-attention) |
| **Alignment** | Location-sensitive attention | Multi-head cross-attention |
| **Feature Stack** | CBHG + decoder | Streamlined Transformer stack |
| **Vocoder** | Griffin-Lim (iterative) | WaveGlow (flow-based) |
| **Training Feel** | Slower, but very instructive | 2–4× faster in practice |
| **Audio Quality** | Understandable, often “robotic” | Cleaner, higher fidelity |

> **Takeaway:** Attention + parallelism + a learned vocoder largely determine today’s TTS jump in clarity and stability.

---

## How They Work (Conceptual)

**Tacotron pipeline**  
`text → embeddings → conv/CBHG encoder → attention → RNN decoder → mel spectrogram → Griffin-Lim → waveform`

**FalafelTron pipeline**  
`text → embeddings + positional encoding → Transformer encoder → cross-attending Transformer decoder → mel spectrogram → WaveGlow → waveform`

- **Attention** aligns text positions with acoustic frames.
- **Transformers** replace recurrence with global context, improving long sentences and tricky pronunciations.
- **Vocoder choice** (Griffin-Lim vs WaveGlow) sets an upper bound on naturalness.

---

## What We Achieved

- **Educational baseline:** Tacotron notebooks that reveal attention alignments, losses, and spectrograms—great for learning and debugging.
- **Modernization path:** A clean Transformer variant that reduces architectural complexity (no CBHG), scales better, and produces **noticeably cleaner audio**.
- **Practical comparison:** Same problem, two designs—highlighting the impact of parallelism and vocoder quality on real outputs.

---

## Listening Notes (Qualitative)

- **Tacotron + Griffin-Lim:** Intelligible, sometimes buzzy/metallic timbre, prosody can drift on long sentences.
- **FalafelTron + WaveGlow:** Smoother timbre, fewer artifacts, more stable alignment on longer inputs.

> Quality scales with data size, training duration, and vocoder choice. The architectures here are educational but already show clear differences in sound.

---

## Design Choices That Matter (and Why)

- **From RNNs to Self-Attention:** Removes sequential bottlenecks → faster training, better long-range modeling.
- **Simplifying the Stack:** Dropping CBHG in favor of a unified Transformer makes the system easier to reason about and extend.
- **Vocoder Upgrade:** Moving from iterative reconstruction (Griffin-Lim) to a learned flow (WaveGlow) lifts perceptual ceiling.

---

## When to Use Which

- **Studying fundamentals / teaching:** Start with **Tacotron**—see alignments form and understand each block.
- **Pursuing quality / speed:** Go with **FalafelTron**—cleaner audio and better scaling on longer text.
- **Extending research:** FalafelTron is a friendlier base for trying conformers, new losses, or alternative vocoders.

---

## Roadmap Ideas

- **Perceptual objectives:** Multi-resolution STFT / spectral losses for sharper harmonics.  
- **Hybrid encoders:** Conformer or attention-augmented convs for robustness.  
- **Vocoder experiments:** Compare **HiFi-GAN**, **WaveRNN**, and WaveGlow under identical conditions.  
- **Stability:** Better stop-token prediction and duration control for long paragraphs.

---

## Credits

- **Tacotron** — Wang et al., *Tacotron: Towards End-to-End Speech Synthesis* (2017).  
- **WaveGlow** — Prenger et al., *WaveGlow: A Flow-based Generative Network for Speech Synthesis* (ICASSP 2019).  
- **Dataset** — LJSpeech.

> This unified repo reflects contributions to both the baseline Tacotron and the Transformer-based FalafelTron, illustrating how architecture and vocoding co-determine TTS performance.
