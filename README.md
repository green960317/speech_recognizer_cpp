# Speech Recognizer

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Unreal Engine](https://img.shields.io/badge/Unreal%20Engine-4.26%2B%20%7C%205.0%2B-blue.svg)](https://www.unrealengine.com/)
[![Platform](https://img.shields.io/badge/Platform-Windows%20%7C%20Mac%20%7C%20Linux-lightgrey.svg)]()

**Speech Recognizer** is an open-source Unreal Engine plugin that enables **real-time, offline speech recognition** powered by OpenAI's Whisper technology. Transform spoken words into text directly within your Unreal Engine projects without requiring internet connectivity or external services.

## 🚀 Features

- **🔄 Real-time Recognition**: Process audio streams in real-time with minimal latency
- **📱 Offline Operation**: No internet connection required - everything runs locally
- **🌍 Multi-language Support**: Supports 99+ languages with automatic language detection
- **🎯 High Accuracy**: Powered by OpenAI's state-of-the-art Whisper models
- **⚡ Performance Optimized**: Multiple acceleration backends (CPU, OpenBLAS, Vulkan)
- **🎮 Blueprint & C++ Support**: Full integration with both Blueprint visual scripting and C++
- **📊 Multiple Model Sizes**: Choose from Tiny to Large models based on your accuracy/performance needs
- **🔧 Easy Integration**: Simple API for quick implementation in any Unreal project

## 📋 Requirements

### Minimum System Requirements
- **Unreal Engine**: 4.26+ or 5.0+
- **Platform**: Windows (x64), macOS, Linux
- **RAM**: 4GB+ (varies by model size)
- **CPU**: Modern x64 processor with AVX support recommended

### Supported Platforms
- ✅ Windows (x64)
- ✅ macOS (Intel & Apple Silicon)
- ✅ Linux (x64)

## 🛠️ Installation

### Method 1: Unreal Engine Marketplace
1. Download from the [Unreal Engine Marketplace](com.epicgames.launcher://ue/marketplace/product/2b33311abdb048819664a6cfe0bbd90d)
2. Install to your engine or project
3. Enable the plugin in your project settings

### Method 2: Manual Installation
1. Clone or download this repository
2. Copy the plugin folder to your project's `Plugins` directory
3. Regenerate project files
4. Build your project
5. Enable "Speech Recognizer" in the Plugin settings

### Method 3: Git Submodule
```bash
cd YourProject/Plugins
git submodule add https://github.com/green960317/speech_recognizer_cpp.git
```

## 🎯 Quick Start

### Blueprint Setup

1. **Create Speech Recognizer Instance**
   ```
   Create Speech Recognizer → Speech Recognizer Reference
   ```

2. **Configure Settings** (in Project Settings → Speech Recognizer)
   - Choose model size (Tiny, Base, Small, Medium, Large)
   - Select language model (English-only or Multilingual)

3. **Start Recognition**
   ```
   Start Speech Recognition → On Started (Success/Failure)
   ```

4. **Process Audio Data**
   ```
   Process Audio Data (PCM Data, Sample Rate, Channels, Is Last)
   ```

5. **Handle Results**
   ```
   On Recognized Text Segment → Recognized Text (String)
   ```

### C++ Setup

```cpp
#include "SpeechRecognizer.h"

// Create recognizer instance
USpeechRecognizer* SpeechRecognizer = USpeechRecognizer::CreateSpeechRecognizer();

// Bind delegates
SpeechRecognizer->OnRecognizedTextSegmentNative.AddUObject(this, &AMyActor::OnTextRecognized);
SpeechRecognizer->OnRecognitionErrorNative.AddUObject(this, &AMyActor::OnRecognitionError);

// Start recognition
SpeechRecognizer->StartSpeechRecognition(FOnSpeechRecognitionStartedStatic::CreateUObject(this, &AMyActor::OnRecognitionStarted));

// Process audio data
TArray<float> AudioData; // Your PCM audio data
SpeechRecognizer->ProcessAudioData(AudioData, 16000.0f, 1, false);
```

## 📚 Documentation

### Core Classes

#### `USpeechRecognizer`
Main class for speech recognition functionality.

**Key Methods:**
- `CreateSpeechRecognizer()` - Creates a new recognizer instance
- `StartSpeechRecognition()` - Begins speech recognition
- `StopSpeechRecognition()` - Stops recognition
- `ProcessAudioData()` - Feeds audio data for processing

**Key Delegates:**
- `OnRecognizedTextSegment` - Fired when text is recognized
- `OnRecognitionProgress` - Reports recognition progress (0-100%)
- `OnRecognitionError` - Fired when errors occur
- `OnRecognitionFinished` - Fired when recognition completes

#### `USpeechRecognizerModel`
Asset class for storing language model data in GGML format.

### Model Configuration

#### Model Sizes
| Model | Size | Speed | Accuracy | Use Case |
|-------|------|-------|----------|----------|
| Tiny | ~39MB | Fastest | Good | Real-time, mobile |
| Tiny Q5_1 | ~26MB | Fastest | Good | Quantized, mobile |
| Base | ~74MB | Fast | Better | Balanced performance |
| Small | ~244MB | Medium | Good | High accuracy |
| Medium | ~769MB | Slow | Very Good | Professional use |
| Large | ~1550MB | Slowest | Best | Maximum accuracy |

#### Supported Languages
Auto-detection plus 99+ languages including:
- English, Chinese, German, Spanish, Russian
- Korean, French, Japanese, Portuguese, Turkish
- Polish, Catalan, Dutch, Arabic, Swedish
- Italian, Indonesian, Hindi, Finnish, Vietnamese
- And many more...

## ⚙️ Configuration

### Project Settings
Navigate to **Edit → Project Settings → Speech Recognizer**:

- **Model Size**: Choose the model size (affects accuracy vs performance)
- **Model Language**: English-only or Multilingual
- **Model Download Base URL**: URL for automatic model downloads (Editor only)

### Performance Optimization

#### CPU Optimization
- Enable AVX/AVX2 instruction sets for better performance
- Adjust thread count based on your CPU cores
- Use quantized models (Q5_1, Q8_0) for reduced memory usage

#### GPU Acceleration (Experimental)
```cpp
// In RuntimeSpeechRecognizer.Build.cs
bool bUseOpenBLAS = true;  // Windows only
bool bUseVulkan = true;    // Experimental, Windows only
```

## 🎮 Demo Project

The plugin includes a complete demo showcasing:
- **Microphone Input**: Real-time speech recognition from microphone
- **File Input**: Process pre-recorded audio files
- **Settings UI**: Configure recognition parameters
- **Results Display**: Show recognized text with confidence scores

**Demo Assets:**
- `Content/Demo/RSR_Demo.umap` - Main demo level
- `Content/Demo/Widgets/` - UI widgets for different scenarios

## 🔧 Advanced Usage

### Custom Audio Processing
```cpp
// Process audio with custom parameters
void ProcessCustomAudio()
{
    // Your audio processing logic
    Audio::FAlignedFloatBuffer ProcessedAudio = ProcessAudioBuffer(RawAudio);

    // Feed to recognizer
    SpeechRecognizer->ProcessAudioData(
        MoveTemp(ProcessedAudio),
        SampleRate,
        NumChannels,
        bIsLastChunk
    );
}
```

### Error Handling
```cpp
void OnRecognitionError(const FString& ShortError, const FString& DetailedError)
{
    UE_LOG(LogTemp, Error, TEXT("Recognition Error: %s - %s"), *ShortError, *DetailedError);

    // Handle specific error cases
    if (ShortError.Contains("Model"))
    {
        // Handle model loading errors
    }
    else if (ShortError.Contains("Audio"))
    {
        // Handle audio processing errors
    }
}
```

### Progress Monitoring
```cpp
void OnRecognitionProgress(int32 Progress)
{
    // Update UI progress bar
    ProgressBar->SetPercent(Progress / 100.0f);

    UE_LOG(LogTemp, Log, TEXT("Recognition Progress: %d%%"), Progress);
}
```

## 🏗️ Building from Source

### Prerequisites
- Visual Studio 2019+ (Windows) or Xcode (macOS)
- Unreal Engine 4.26+ or 5.0+ source or binary
- Git with LFS support

### Build Steps
1. Clone the repository with submodules:
   ```bash
   git clone --recursive https://github.com/green960317/speech_recognizer_cpp.git
   ```

2. Generate project files:
   ```bash
   # Windows
   GenerateProjectFiles.bat

   # macOS/Linux
   ./GenerateProjectFiles.sh
   ```

3. Build the project:
   ```bash
   # Windows (Visual Studio)
   MSBuild YourProject.sln -p:Configuration=Development

   # macOS (Xcode)
   xcodebuild -project YourProject.xcodeproj -configuration Development
   ```

### Third-Party Dependencies
The plugin includes the following third-party libraries:
- **whisper.cpp** - OpenAI Whisper implementation in C++
- **ggml** - Machine learning tensor library
- **OpenBLAS** - Optimized BLAS library (optional)

## 🤝 Contributing

We welcome contributions! Please see our [Contributing Guidelines](CONTRIBUTING.md) for details.

### Development Setup
1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Add tests if applicable
5. Submit a pull request

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- **OpenAI** for the Whisper speech recognition model
- **ggerganov** for the whisper.cpp implementation
- **Unreal Engine Community** for feedback and contributions

## 🔄 Changelog

### Version 1.0
- Initial release
- Real-time speech recognition
- Multi-language support
- Blueprint and C++ integration
- Demo project included

---

**Made with ❤️ for the Unreal Engine community**
