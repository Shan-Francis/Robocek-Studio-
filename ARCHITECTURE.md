# Robocek Studio Architecture

## Overview
Robocek Studio is a cross-platform IDE for IoT and robotics development, supporting multiple microcontroller boards (ESP32, ESP8266, Arduino Uno, Arduino Mega, etc.) with an integrated AI assistant (Robi) for code generation, debugging, and project guidance.

## Core Architecture

### 1. Board Management System
```
src/
  core/
    boards/
      BoardManager.ts          # Central board registry & detection
      BaseBoard.ts             # Abstract board class
      ESP32Board.ts            # ESP32 implementation
      ESP8266Board.ts          # ESP8266 implementation
      ArduinoUnoBoard.ts        # Arduino Uno implementation
      ArduinoMegaBoard.ts       # Arduino Mega implementation
      BoardConfig.json          # Board specifications & toolchains
```

### 2. Compiler & Upload Pipeline
```
src/
  compiler/
    CompilerManager.ts         # Orchestrates build process
    sketches/
      SketchBuilder.ts          # Handles .ino compilation
      LibraryManager.ts         # Manage sketches & libraries
    toolchains/
      ESPToolchain.ts           # esp-idf / Arduino-esp32 toolchain
      ArduinoToolchain.ts       # Arduino CLI integration
      ToolchainConfig.json      # Compiler paths & settings
```

### 3. UI Components (Arduino IDE Style)
```
src/
  ui/
    components/
      Editor/
        CodeEditor.tsx          # Monaco Editor with syntax highlighting
        SerialMonitor.tsx       # Serial output viewer
      Toolbar/
        CompileButton.tsx       # Verify/Compile
        UploadButton.tsx        # Upload to board
        BoardSelector.tsx       # Multi-board dropdown
      Sidebar/
        LibraryExplorer.tsx      # Browse & install libraries
        BoardManager.tsx         # Configure board settings
      MenuBar/
        FileMenu.tsx            # New, Open, Save, Examples
        EditMenu.tsx            # Edit operations
        ToolsMenu.tsx           # Tools, Preferences, SerialMonitor
```

### 4. Robi AI Chatbot Integration
```
src/
  ai/
    RobiManager.ts             # Core AI orchestration
    LLMConnector.ts            # LLM API integration (Gemini 2.5 Flash)
    CodeGeneration.ts          # AI-powered code gen
    ContextBuilder.ts          # Build AI context from project
    PromptTemplates.ts         # Pre-built prompts for common tasks
    RobiUI/
      ChatPanel.tsx            # Chat interface
      CodeSuggestions.tsx       # Inline suggestions
      RobiCommands.ts          # Slash commands for Robi
```

### 5. Project Structure
```
src/
  services/
    ProjectService.ts          # Project CRUD & lifecycle
    FileService.ts             # File I/O operations
    ConfigService.ts           # User preferences & settings
  models/
    Project.ts                 # Project data model
    Board.ts                   # Board configuration
    Sketch.ts                  # Sketch/Code file
  utils/
    SerialPortManager.ts       # COM port detection
    USBDetection.ts            # Automatic board detection
```

## Key Features

### Multi-Board Support
- **Auto-detection**: USB detection for connected boards
- **Board Switching**: Quick switch between ESP32, Arduino, etc.
- **Custom Configurations**: Per-board compiler flags, libraries, bootloaders

### Arduino IDE Compatibility
- Sketch format (.ino) support
- Built-in examples for each board
- Arduino libraries & third-party library support
- One-click upload workflow

### Robi AI Assistant
- **Code Generation**: Create sketches from natural language
- **Debugging Help**: Analyze errors with AI
- **Project Navigation**: Guided tours through projects
- **Learning Mode**: Educational explanations for beginners
- **Code Refactoring**: Suggest improvements & optimizations
- **Real-time Assistance**: Context-aware suggestions while coding

## Data Flow

### Upload Flow
```
User clicks Upload
  ↓
BoardManager: Get selected board config
  ↓
SketchBuilder: Compile sketch for target board
  ↓
ArduinoToolchain/ESPToolchain: Run compiler
  ↓
USB Detection: Find board COM port
  ↓
Upload Tool: Push binary to device
  ↓
Serial Monitor: Show upload progress & output
```

### AI Code Generation Flow
```
User: "Create a blink LED sketch"
  ↓
Robi: Build context (board type, available pins)
  ↓
LLMConnector: Send prompt to Gemini 2.5 Flash
  ↓
CodeGeneration: Parse & validate response
  ↓
Editor: Display generated code + accept/edit buttons
  ↓
User: Review & apply changes
```

## Configuration Files

### BoardConfig.json
Stores board specifications, compiler paths, library paths, bootloader settings.

### SketchConfig.json
Per-sketch metadata: board type, libraries, build flags, upload settings.

### RobiConfig.json
API endpoint, API key (encrypted), model settings, context window size.

## Tech Stack
- **Frontend**: React/Vue, Monaco Editor, Electron (desktop)
- **Backend**: Node.js / Rust (performance-critical compilation)
- **AI**: Google Gemini 2.5 Flash API
- **Build Tools**: Arduino CLI, esp-idf
- **Serial Communication**: serialport package
