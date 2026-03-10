# Ultravox AI Multilingual Speech Benchmarking

Comprehensive evaluation suite for testing **Ultravox AI's multilingual speech capabilities** with a focus on API-relevant metrics for mobile applications.

## 🎯 Purpose

This ticket evaluates Ultravox AI's multilingual support across key metrics that matter for API-based mobile applications:

### Accuracy & Quality
- **STT - WER/CER**: Word Error Rate and Character Error Rate to measure transcription accuracy
- **Multilingual Competence**: Ability to handle 10+ languages including non-English dialects

### Speed & Responsiveness  
- **TTFA (Time to First Audio)**: Critical for real-time conversational UX
  - ✅ Excellent: < 200ms (feels instant)
  - ✅ Good: < 500ms (acceptable)
  - ⚠️ Fair: < 1000ms (noticeable)
  - ❌ Poor: > 1000ms (frustrating)
- **Total Latency**: End-to-end response time

### Reliability & Cost
- **API Failure Rate**: Measures uptime and stability
- **Cost per query**: For scalability projection

## 🌍 Supported Languages

- 🇺🇸 English (en)
- 🇪🇸 Spanish (es)
- 🇮🇳 Hindi (hi)
- 🇧🇩 Bengali (bn)
- 🇫🇷 French (fr)
- 🇩🇪 German (de)
- 🇧🇷 Portuguese (pt)
- 🇸🇦 Arabic (ar)
- 🇨🇳 Mandarin Chinese (zh)
- 🇯🇵 Japanese (ja)

## 🚀 Quick Start

### 1. Installation

```bash
# Install dependencies
pip install -r requirements.txt

# Set up your API key
cp .env.example .env
# Edit .env and add your ULTRAVOX_API_KEY
```

### 2. Run Evaluations

```bash
# Check configuration
python run_ultravox.py check

# Evaluate Speech-to-Text (STT) accuracy
python run_ultravox.py stt --languages en,es,hi,bn

# Evaluate Speech-to-Speech (STS) responsiveness
python run_ultravox.py sts --languages en,es,hi --iterations 5

# Run full evaluation suite
python run_ultravox.py full --languages en,es,hi,bn,fr
```

## 📊 Evaluation Types

### STT Evaluation (Speech-to-Text)

Measures transcription accuracy and latency:

```bash
python run_ultravox.py stt --languages en,es,hi,bn
```

**Metrics Collected:**
- Word Error Rate (WER) per language
- Character Error Rate (CER) per language  
- Average transcription latency
- API failure rate

**Output Example:**
```
Language    WER        CER        Latency (ms)    Status
------------------------------------------------------------
en          0.0523     0.0234     342.1           0/3 failed
es          0.0687     0.0312     367.4           0/2 failed
hi          0.0891     0.0445     389.2           0/2 failed
bn          0.1024     0.0523     412.7           0/1 failed
```

### STS Evaluation (Speech-to-Speech)

Measures Time to First Audio (TTFA) and responsiveness:

```bash
python run_ultravox.py sts --languages en,es,hi --iterations 5
```

**Metrics Collected:**
- Average TTFA (Time to First Audio)
- P95 TTFA (95th percentile)
- Total response latency
- TTFA quality rating (excellent/good/fair/poor)

**Output Example:**
```
Language    Avg TTFA (ms)   P95 TTFA    Rating       Status
------------------------------------------------------------
en          187.3           245.2       excellent    0/3 failed
es          234.5           298.1       good         0/2 failed
hi          423.7           512.3       good         0/2 failed
```

## 📁 Project Structure

```
benchmarking_experiments/
├── run_ultravox.py                      # Main CLI interface
├── requirements.txt            # Python dependencies
├── .env.example               # API key template
├── dataset/
│   ├── banking_metadata.json  # Test samples & prompts
│   └── audio/                 # Audio files (when available)
├── providers/
│   └── ultravox.py            # Ultravox API implementation
├── metrics/
│   └── accuracy.py            # WER/CER calculation
├── runners/
│   └── evaluate.py            # STT evaluation orchestration
└── results/                   # Evaluation outputs (auto-generated)
    └── benchmark.json
```

## 🔧 Configuration

### Environment Variables

```bash
# Required
ULTRAVOX_API_KEY=your_api_key_here

# Optional (for future providers)
HUME_API_KEY=
```

### Dataset Format

The `dataset/banking_metadata.json` file contains:
- Multilingual test samples with reference transcriptions
- Language-specific system prompts
- Audio file paths (or use simulated audio for testing)

Example sample:
```json
{
  "id": "en_001",
  "audio_path": "audio/en/balance.wav",
  "reference_text": "I would like to check my account balance please",
  "duration_sec": 3.2
}
```

## 📈 Understanding Results

### WER (Word Error Rate)

- **< 0.05**: Excellent (5% error rate)
- **< 0.10**: Good (10% error rate)
- **< 0.20**: Acceptable (20% error rate)
- **> 0.20**: Poor - needs improvement

### TTFA (Time to First Audio)

Critical for conversational UX on mobile:

- **< 200ms**: Excellent - feels instantaneous
- **< 500ms**: Good - acceptable for natural conversation
- **< 1000ms**: Fair - noticeable but usable
- **> 1000ms**: Poor - frustrating user experience

### API Reliability

- **< 1% failure rate**: Excellent
- **< 5% failure rate**: Acceptable
- **> 5% failure rate**: Concerning - investigate issues

## 💡 Usage Examples

### Basic STT Evaluation

```bash
# Evaluate English and Spanish only
python run_ultravox.py stt --languages en,es

# Save to custom location
python run_ultravox.py stt --languages en,es,hi -o results/my_stt_test.json
```

### STS Benchmarking

```bash
# Quick test (3 iterations per sample)
python run_ultravox.py sts --languages en,es

# Thorough test (10 iterations for statistical confidence)
python run_ultravox.py sts --languages en,es,hi --iterations 10
```

### Full Evaluation

```bash
# Test all major languages
python run_ultravox.py full --languages en,es,hi,bn,fr,de,pt

# Focus on South Asian languages
python run_ultravox.py full --languages hi,bn
```

## 🔬 API Integration Notes

### Ultravox API Endpoints

The provider calls these endpoints:

1. **Transcription** (`/v1/transcribe`)
   - Input: Audio data (hex encoded)
   - Output: Transcribed text, language, confidence

2. **Speech-to-Speech** (`/v1/speech-to-speech`)
   - Input: Audio data, system prompt, language
   - Output: Streaming audio response

### Rate Limiting

- Add delays between iterations if you hit rate limits
- Adjust `--iterations` parameter accordingly

## 📊 Results Format

Results are saved as JSON files with this structure:

```json
{
  "provider": "ultravox",
  "evaluation_type": "stt|sts",
  "timestamp": "2026-03-10T...",
  "languages": {
    "en": {
      "avg_wer": 0.0523,
      "avg_cer": 0.0234,
      "avg_latency_ms": 342.1,
      ...
    }
  },
  "aggregate": {
    "languages_evaluated": 4,
    "avg_wer_all_languages": 0.0781,
    ...
  },
  "reliability": {
    "api_calls": 12,
    "api_failures": 0,
    "failure_rate": 0.0
  }
}
```

## 🎓 Best Practices

1. **Run multiple iterations** for statistical significance (≥3)
2. **Test representative samples** from your actual use case
3. **Monitor TTFA carefully** for conversational applications
4. **Track WER per language** to identify weak points
5. **Check API reliability** over extended testing periods

## 🐛 Troubleshooting

### "ULTRAVOX_API_KEY not set"
- Create `.env` file with your API key
- Or export it: `export ULTRAVOX_API_KEY=your_key`

### "No samples in dataset"
- Check `dataset/banking_metadata.json` has samples for your language
- Add custom samples if needed

### High failure rates
- Check API key validity
- Verify network connectivity
- Check Ultravox API status

## 📝 License

This benchmarking suite is part of the Community AI project evaluation framework.

## 🤝 Contributing

To add support for additional languages:

1. Add samples to `dataset/banking_metadata.json`
2. Add language code to `UltravoxProvider.SUPPORTED_LANGUAGES`
3. Add system prompt in the language
4. Test with `python run_ultravox.py stt --languages your_lang`

---

**Note**: This evaluation focuses on API-relevant metrics. Memory footprint and battery consumption are not measured since Ultravox is cloud-based.

