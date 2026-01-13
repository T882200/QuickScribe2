# QuickScribe - AI Audio Transcription Tool

QuickScribe is a powerful, browser-based transcription application that uses Groq's Whisper API to convert audio files into accurate text transcriptions with timestamps.

## 🌟 Features

- **Fast AI Transcription**: Powered by Groq's Whisper Large v3 Turbo model (216x real-time speed)
- **Multiple Input Methods**: Upload audio files or record directly in your browser
- **Large File Support**: Automatically chunks files larger than 25MB for processing
- **Multiple Output Formats**:
  - SRT (SubRip) format with precise timestamps
  - Plain text format
- **Multi-Language Support**: Transcribe audio in Hebrew, English, Arabic, Russian, French, Spanish, German, or auto-detect
- **AI Summarization**: Generate concise summaries using Groq's LLaMA model
- **Real-Time Progress**: Track transcription progress with detailed status updates
- **Offline Storage**: API key stored securely in browser localStorage
- **Audio Recording**: Built-in microphone recording capability
- **Customizable Subtitles**: Adjust words per subtitle (4-12 words)

## 🚀 Live Demo

Visit the live application: **[QuickScribe on GitHub Pages](https://t882200.github.io/QuickScribe2/)**

> **Note**: To enable GitHub Pages, go to repository Settings → Pages → Select source branch and save.

## 📋 Prerequisites

To use QuickScribe, you need:

1. A modern web browser (Chrome, Firefox, Safari, Edge)
2. A Groq API key (free tier available)

## 🔑 Getting a Groq API Key

1. Visit [console.groq.com](https://console.groq.com)
2. Sign up or log in to your account
3. Click on "Create API Key"
4. Copy the generated key
5. Paste it into QuickScribe's API Key input (accessible via the key icon 🔑)

**Pricing**: Groq offers competitive pricing at $0.04 per hour of audio transcribed with the Whisper Large v3 Turbo model.

## 🎯 How to Use

### Basic Usage

1. **Set Your API Key**
   - Click the key icon (🔑) in the control panel
   - Paste your Groq API key
   - Click "Save" (your key is stored locally in your browser)

2. **Choose Audio Source**
   - Click the microphone/upload icon
   - Select either:
     - **Upload File**: Choose an audio file from your device
     - **Record**: Use your microphone to record audio

3. **Configure Settings** (Optional)
   - Click the settings icon (⚙️)
   - Adjust words per subtitle (4-12)
   - Select transcription language or use auto-detect

4. **Start Transcription**
   - Click "התחל תמלול" (Start Transcription)
   - Wait for processing (progress bar shows status)
   - View results in SRT or Plain Text format

5. **Use Your Transcription**
   - **Copy**: Copy text to clipboard
   - **Download**: Save as SRT or TXT file
   - **Summarize**: Generate AI summary of the content

### Supported Audio Formats

- MP3 (.mp3)
- MP4 Audio (.mp4, .m4a)
- WAV (.wav)
- WebM (.webm)

### File Size Limits

- Maximum: 25MB per chunk (automatically handled)
- Large files are automatically split and processed sequentially
- No manual splitting required

## 🛠️ Technical Details

### Technology Stack

- **Frontend**: Vanilla JavaScript (ES6+), HTML5, CSS3
- **APIs Used**:
  - Groq Whisper API (transcription)
  - Groq LLaMA API (summarization)
  - MediaRecorder API (audio recording)
- **Storage**: Browser localStorage and IndexedDB
- **Styling**: Custom CSS with RTL (Right-to-Left) support for Hebrew

### API Specifications

**Transcription Endpoint:**
```
POST https://api.groq.com/openai/v1/audio/transcriptions
```

**Parameters:**
- `model`: whisper-large-v3-turbo
- `response_format`: verbose_json
- `language`: ISO-639-1 code (optional, auto-detect if not specified)
- `temperature`: 0 (for consistent results)

**Headers:**
```
Authorization: Bearer YOUR_API_KEY
```

## 🏗️ Development

### Local Setup

1. Clone the repository:
```bash
git clone https://github.com/T882200/QuickScribe2.git
cd QuickScribe2
```

2. Open `index.html` in your browser:
```bash
# Using Python's built-in server
python -m http.server 8000

# Or using Node's http-server
npx http-server
```

3. Visit `http://localhost:8000` in your browser

### Project Structure

```
QuickScribe2/
├── index.html              # Main application (all-in-one file)
├── README.md              # This file
└── FEATURE_SUGGESTIONS.md # Future feature ideas
```

### GitHub Pages Deployment

This repository is configured for GitHub Pages deployment:

1. Go to repository Settings
2. Navigate to "Pages" section
3. Select branch: `claude/groq-whisper-api-integration-ig7kH` (or `main`)
4. Select folder: `/` (root)
5. Click "Save"
6. Your site will be published at: `https://t882200.github.io/QuickScribe2/`

## 🔒 Privacy & Security

- **API Key Storage**: Your Groq API key is stored locally in your browser's localStorage and never sent to any server except Groq's API
- **No Backend**: QuickScribe runs entirely in your browser - no server-side processing
- **Audio Privacy**: Audio files are sent directly to Groq's API and are not stored or logged by QuickScribe
- **Local Processing**: All UI interactions and file handling occur locally in your browser

## 📝 Transcription Tips

For best results:

1. **Audio Quality**: Use clear, high-quality audio recordings
2. **Background Noise**: Minimize background noise for better accuracy
3. **Language Selection**: Specify the language if known (improves accuracy vs. auto-detect)
4. **File Format**: WAV and M4A typically provide best quality
5. **Speaking Pace**: Clear, well-paced speech transcribes more accurately

## 🌐 Language Support

QuickScribe supports transcription in multiple languages:

- 🇮🇱 Hebrew (עברית)
- 🇺🇸 English
- 🇸🇦 Arabic (العربية)
- 🇷🇺 Russian (Русский)
- 🇫🇷 French (Français)
- 🇪🇸 Spanish (Español)
- 🇩🇪 German (Deutsch)
- 🌍 Auto-detect (recommended if unsure)

## 🐛 Troubleshooting

### "Invalid API Key" Error
- Verify your API key is correct
- Check you have remaining API credits on Groq
- Try regenerating your API key

### Transcription Fails or Times Out
- Check your internet connection
- Ensure audio file is in a supported format
- Try with a shorter audio file first
- Check Groq API status

### No Audio Recorded
- Grant microphone permissions in your browser
- Check your microphone is working
- Try a different browser

### Progress Stuck
- Wait a few minutes (large files take time)
- Check browser console for errors (F12)
- Refresh page and try again

## 🤝 Contributing

Contributions are welcome! Check out the [FEATURE_SUGGESTIONS.md](FEATURE_SUGGESTIONS.md) file for ideas on potential improvements.

### Contribution Guidelines

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 📄 License

This project is open source and available under the MIT License.

## 👨‍💻 Author

**Shachar Golan**
- YouTube: [@sgolan20](https://www.youtube.com/@sgolan20)

## 🙏 Acknowledgments

- **Groq**: For providing fast and affordable Whisper API access
- **OpenAI**: For creating the Whisper model
- **Font Awesome**: For the icons
- **Google Fonts**: For the Heebo font family

## 📚 Resources

- [Groq Documentation](https://console.groq.com/docs)
- [Whisper Large v3 Turbo Model](https://console.groq.com/docs/model/whisper-large-v3-turbo)
- [Groq API Reference](https://console.groq.com/docs/api-reference)
- [SRT Format Specification](https://en.wikipedia.org/wiki/SubRip)

## 📊 Performance

- **Transcription Speed**: 216x real-time (1 hour of audio transcribed in ~17 seconds)
- **Accuracy**: Word Error Rate (WER) ~1% lower than Distil-Whisper
- **File Processing**: Sequential chunking for files > 25MB
- **Timeout**: 5-minute timeout per chunk

## 🆕 Recent Updates

### Latest Changes
- ✅ Updated to Whisper Large v3 Turbo model for faster transcription
- ✅ Improved error handling and user feedback
- ✅ Added comprehensive documentation
- ✅ GitHub Pages deployment ready

---

**Enjoy transcribing with QuickScribe! 🎙️✨**

For questions, issues, or feature requests, please open an issue on GitHub.
