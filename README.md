# VSCode Ollama Extension

> Visual Studio Code extension for seamless integration with local Ollama models and optional AI model connections.

![Version](https://img.shields.io/badge/version-1.0.0-blue.svg)
![License](https://img.shields.io/badge/license-MIT-green.svg)

## � Table of Contents

- [Features](#features)
- [Installation](#installation)
- [Configuration](#configuration)
- [Usage](#usage)
- [Terminal Commands](#terminal-commands)
- [HTTP Interface](#http-interface)
- [Test On Save](#test-on-save)
- [Troubleshooting](#troubleshooting)
- [Contributing](#contributing)

## ✨ Features

### Multi-Model Support
- Connect to multiple local Ollama models
- Switch models on the fly
- Support for:
  - Llama 2
  - CodeLlama
  - Mistral
  - Custom models

### Context-Aware Assistance
- Cursor position-based suggestions
- Selected code analysis
- File context awareness
- Language-specific responses

### External Integrations
- Connect to other AI services
- API key management
- Model fallback options

## � Installation

1. Install VSCode Extension:
   ```bash
   code --install-extension ollama-vscode
   ```

2. Install Ollama:
   ```bash
   # macOS/Linux
   curl https://ollama.ai/install.sh | sh

   # Windows
   # Download from https://ollama.ai/download
   ```

3. Pull Models:
   ```bash
   ollama pull codellama
   ollama pull llama2
   ```

## ⚙️ Configuration

```jsonc
{
  // Endpoints configuration
  "ollama.endpoints": [
    {
      "name": "Local",
      "url": "http://localhost:11434",
      "models": ["codellama", "llama2"]
    }
  ],

  // Model preferences
  "ollama.defaultModel": "codellama",
  "ollama.fileTypeModels": {
    "javascript": "codellama",
    "python": "codellama",
    "markdown": "llama2"
  },

  // Model parameters
  "ollama.temperature": 0.7,
  "ollama.maxTokens": 2000
}
```

## � Usage

### Commands
Press `Ctrl+Shift+P` (`Cmd+Shift+P` on macOS) and type:
- `Ollama: Ask Model`
- `Ollama: Switch Model`
- `Ollama: Explain Code`
- `Ollama: Generate Documentation`

### Keyboard Shortcuts
- Ask Ollama: `Ctrl+Alt+O`
- Explain Selection: `Ctrl+Alt+E`
- Generate Docs: `Ctrl+Alt+D`

## � Terminal Commands

### Model Management
```bash
# List models
ollama list

# Pull model
ollama pull codellama

# Remove model
ollama rm codellama

# Create custom model
ollama create mymodel -f Modelfile
```

### Server Control
```bash
# Start server
ollama serve

# Check status
curl http://localhost:11434/api/tags
```

## � HTTP Interface

### Generate Completions
```bash
curl http://localhost:11434/api/generate -d '{
  "model": "codellama",
  "prompt": "Write a function that...",
  "system": "You are an expert programmer.",
  "temperature": 0.7
}'
```

### Model Management
```bash
# List models
curl http://localhost:11434/api/tags

# Model info
curl http://localhost:11434/api/show -d '{
  "name": "codellama"
}'
```

## � Test On Save

### Configuration
```jsonc
{
  // Test Runner
  "ollama.testOnSave": {
    "enabled": true,
    "patterns": {
      "**/*.test.js": "npm test ${file}",
      "**/*.spec.py": "python -m pytest ${file}"
    }
  },

  // API Testing
  "ollama.apiTestOnSave": {
    "enabled": true,
    "endpoints": [
      {
        "pattern": "src/api/*.js",
        "method": "GET",
        "url": "http://localhost:3000/api/test"
      }
    ]
  }
}
```

### Custom Commands
```jsonc
{
  "ollama.onSave": {
    "commands": [
      {
        "pattern": "src/**/*.js",
        "command": "eslint ${file} --fix"
      }
    ]
  }
}
```

## ❗ Troubleshooting

### Common Issues

1. **Connection Failed**
   - Check if Ollama is running
   - Verify endpoint configuration
   - Check network connectivity

2. **Model Not Found**
   - Run `ollama pull <model>`
   - Check model name spelling
   - Verify compatibility

### Debug Mode
```jsonc
{
  "ollama.debug": true
}
```

## � Contributing

1. Fork repository
2. Create feature branch
3. Submit pull request

See [CONTRIBUTING.md](CONTRIBUTING.md)

## � License

MIT License - [LICENSE](LICENSE)

## � Support

- Issues: [GitHub Issues](https://github.com/yourusername/vscode-ollama/issues)
- Docs: [Wiki](https://github.com/yourusername/vscode-ollama/wiki)
- Community: [Discussions](https://github.com/yourusername/vscode-ollama/discussions)