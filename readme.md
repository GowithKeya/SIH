# Offline Hindi → Santali Voice Translation System

**Phase 1 Baseline**

An offline pipeline that translates **Hindi** text (Devanagari) into **Santali** (Ol Chiki script) and synthesizes the result as spoken audio. Phase 1 covers text-to-speech output; Hindi speech input is planned for a later phase.

---

## Table of Contents

- [Architecture](#architecture)
- [Models](#models)
- [Project Structure](#project-structure)
- [Installation](#installation)
- [Usage](#usage)
- [Running Tests](#running-tests)
- [Roadmap](#roadmap)
- [Limitations](#limitations)
- [Acknowledgements](#acknowledgements)
- [License](#license)

---

## Architecture

```
Hindi Speech (future phase)
        ↓
Hindi Text (Devanagari, hin_Deva)
        ↓   ai4bharat/indictrans2-indic-indic-1B
Santali Text (Ol Chiki, sat_Olck)
        ↓   ai4bharat/indic-parler-tts
Santali Speech (.wav)
```

---

## Models

### Translation

| | |
|---|---|
| **Model** | [`ai4bharat/indictrans2-indic-indic-1B`](https://huggingface.co/ai4bharat/indictrans2-indic-indic-1B) |
| **Source language** | `hin_Deva` (Hindi, Devanagari) |
| **Target language** | `sat_Olck` (Santali, Ol Chiki) |
| **Pre/post-processing** | `IndicTransToolkit.IndicProcessor` |

### Text-to-Speech

| | |
|---|---|
| **Model** | [`ai4bharat/indic-parler-tts`](https://huggingface.co/ai4bharat/indic-parler-tts) |
| **Santali voices** | `Sumitra` (female), `Raju` (male) |
| **Tokenizers** | Dual setup: the model tokenizer encodes the text prompt, and the FLAN-T5 text-encoder tokenizer encodes the voice description prompt |

---

## Project Structure

```
hindi-santali-translator/
├── README.md
├── requirements.txt
├── .gitignore
├── configs/
│   └── config.yaml            # Model names, language codes, default speaker
├── models/                    # Local model cache
├── data/
│   ├── raw/
│   ├── processed/
│   └── samples/
├── src/
│   ├── __init__.py
│   ├── translation/
│   │   ├── __init__.py
│   │   └── indictrans.py      # Hindi → Santali translation wrapper
│   ├── tts/
│   │   ├── __init__.py
│   │   └── santali_tts.py     # Santali speech synthesis wrapper
│   └── pipeline.py            # End-to-end HindiToSantaliVoicePipeline
├── scripts/
│   ├── test_translation.py    # Translation test suite
│   └── test_tts.py            # End-to-end voice synthesis test
└── outputs/                   # Generated .wav files
```

---

## Installation

```bash
# Clone the repository
git clone https://github.com/GowithKeya/SIH.git
cd SIH

# (Recommended) create a virtual environment
python -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt
```

> **Note on offline use:** model weights are downloaded from Hugging Face on first run. Once they are cached locally, the pipeline runs without an internet connection.

---

## Usage

### Python API

```python
from src.pipeline import HindiToSantaliVoicePipeline

# Initialize the pipeline
pipeline = HindiToSantaliVoicePipeline(default_speaker="Sumitra")

# Translate Hindi to Santali (Ol Chiki) and synthesize audio
result = pipeline.process(
    hindi_text="नमस्ते, आप कैसे हैं?",
    output_wav_path="outputs/greeting.wav",
)

print("Santali Text:", result["santali_text"])
print("Audio Path  :", result["output_audio_path"])
```

To use the male voice, initialize with `default_speaker="Raju"`.

---

## Running Tests

### 1. Translation test suite

Validates translation across casual conversation, questions, instructions, numbers, and rural/agriculture vocabulary:

```bash
python scripts/test_translation.py
```

### 2. End-to-end voice synthesis test

Translates a sample Hindi sentence into Santali (Ol Chiki) and writes a `.wav` file to `outputs/`:

```bash
python scripts/test_tts.py
```

---

## Roadmap

- [x] **Phase 1:** Hindi text → Santali text → Santali speech (baseline)
- [ ] **Next:** Hindi speech input (ASR) to complete the speech-to-speech pipeline
- [ ] Quality evaluation on domain-specific vocabulary
- [ ] Performance tuning for low-resource, fully offline devices

---

## Limitations

- Translation and speech quality for Santali depend on the underlying pretrained models and may vary on rare or domain-specific vocabulary.
- The 1B translation model and the TTS model are large; a GPU is recommended for reasonable speed.
- Hindi speech input is not yet supported in this phase.

---

## Acknowledgements

- [AI4Bharat](https://ai4bharat.iitm.ac.in/) for [IndicTrans2](https://github.com/AI4Bharat/IndicTrans2) and [Indic Parler-TTS](https://huggingface.co/ai4bharat/indic-parler-tts)
- [IndicTransToolkit](https://github.com/VarunGumma/IndicTransToolkit) for pre/post-processing utilities

---

## License

Add your license here (e.g., MIT). Please also review the licenses of the models used above.
