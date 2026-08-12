# Qwen3 4B — Model Details

## 1. Overview

**Qwen3-4B** is a **Large Language Model (LLM)** from the Qwen3 model family.

Recommended classification for an AI Chatbot system:

```text
Name: Qwen3 4B
Model Type: Chat
Architecture: Dense LLM
Family: Qwen3
Parameters: 4B
Provider: Local / Qwen
```

`4B` means the model has approximately **4 billion parameters**.

---

## 2. What Type of AI Model Is Qwen3-4B?

Qwen3-4B should primarily be classified as:

```text
Model Type: Chat
```

It is a generative language model that can perform many tasks, including:

- Chat
- Question answering
- Reasoning
- Coding
- Text generation
- Summarization
- Translation
- Tool calling
- RAG-based answering
- Knowledge Base question answering

A model can have one main technical type while supporting many capabilities.

Example:

```text
Qwen3-4B
│
├── Type
│   └── Chat
│
└── Capabilities
    ├── Chat
    ├── Reasoning
    ├── Coding
    ├── Summarization
    ├── Translation
    ├── Tool Calling
    └── Streaming
```

---

## 3. What Does 4B Mean?

```text
Qwen3-4B
       ↑
       4 Billion Parameters
```

Examples of parameter sizes:

```text
0.6B = about 600 million parameters
1.7B = about 1.7 billion parameters
4B   = about 4 billion parameters
8B   = about 8 billion parameters
14B  = about 14 billion parameters
32B  = about 32 billion parameters
```

Parameters are learned numerical weights inside the neural network.

In general, larger models may have stronger capabilities but usually require more RAM, VRAM, storage, and compute.

---

## 4. Qwen3-4B Is Not an Embedding Model

Do not confuse:

```text
Qwen3-4B
```

with:

```text
Qwen3-Embedding-4B
```

They have different jobs.

| Model | Type | Main Job |
|---|---|---|
| Qwen3-4B | Chat / LLM | Generate answers |
| Qwen3-Embedding-4B | Embedding | Convert text into vectors |
| Qwen3-Reranker-4B | Reranker | Re-rank retrieved documents |

---

## 5. Qwen3-4B in a Knowledge Base System

For a DSA Knowledge Base, the recommended architecture is:

```text
DSA Documents
     │
     ▼
Text Chunking
     │
     ▼
Embedding Model
     │
     ▼
Vector Database
     │
     │
     └─────────────────────────────┐
                                   │
User Question                      │
     │                             │
     ▼                             │
Embedding Model                    │
     │                             │
     ▼                             │
Vector Search ◄────────────────────┘
     │
     ▼
Relevant Chunks
     │
     ▼
Optional Reranker
     │
     ▼
Qwen3-4B
     │
     ▼
Final Answer
```

### Responsibilities

```text
Embedding Model
    ↓
Find relevant knowledge

Reranker Model
    ↓
Improve retrieval ranking

Qwen3-4B
    ↓
Generate the final response
```

---

## 6. Qwen3-4B-Q4_K_M.gguf Explained

Example filename:

```text
Qwen3-4B-Q4_K_M.gguf
```

Breakdown:

```text
Qwen3
│
├── Qwen          = Model family
├── 3             = Generation
├── 4B            = About 4 billion parameters
├── Q4_K_M        = Quantization type
└── GGUF          = Model file format
```

Important:

```text
Q4_K_M is NOT the model type.
```

Recommended database fields:

```text
ModelType    = Chat
Format       = GGUF
Quantization = Q4_K_M
```

---

## 7. What Is Quantization?

Quantization reduces the precision of model weights so the model can use less memory and storage.

Concept:

```text
Full Precision Model
        ↓
   Quantization
        ↓
Q8 / Q6 / Q5 / Q4
```

For local AI:

```text
Qwen3-4B
   ↓
Q4_K_M
   ↓
Smaller model file
   ↓
Lower RAM / VRAM usage
   ↓
Easier local inference
```

Typical trade-off:

```text
Higher Precision
      ↓
More memory
Potentially higher fidelity

Lower Precision
      ↓
Less memory
Faster / easier local deployment
Possible small quality loss
```

---

## 8. What Is GGUF?

`GGUF` is a model file format commonly used by local inference tools such as:

- llama.cpp
- LM Studio
- Ollama and related tooling

Example local flow:

```text
Qwen Model
   ↓
GGUF
   ↓
Q4_K_M
   ↓
llama.cpp
   ↓
llama-server.exe
   ↓
HTTP API
   ↓
ASP.NET Core Backend
   ↓
Angular Frontend
```

---

## 9. Recommended Model Configuration

Example model configuration:

```text
Name              Qwen3 4B
Slug              qwen3-4b
Provider          Local
Type              Chat
Family            Qwen3
ParameterCount    4B
Format            GGUF
Quantization      Q4_K_M
ContextWindow     Configurable
SupportsThinking  true
SupportsTools     true
SupportsStreaming true
IsActive          true
```

---

## 10. llama.cpp Example

Example:

```powershell
.\llama-server.exe `
  -m "D:\AI\models\Qwen3-4B-Q4_K_M.gguf" `
  --host 127.0.0.1 `
  --port 8081 `
  -c 4096
```

Explanation:

```text
-m
```

Specifies the model file.

```text
--host 127.0.0.1
```

Runs the server locally.

```text
--port 8081
```

Runs the HTTP server on port `8081`.

```text
-c 4096
```

Sets the runtime context size to 4096 tokens.

---

## 11. Recommended C# Model Type Enum

```csharp
namespace AiChatbot.Domain.Modules.AiModels.Enums;

public enum AiModelType
{
    Chat = 1,
    Embedding = 2,
    Reranker = 3,
    Vision = 4,
    SpeechToText = 5,
    TextToSpeech = 6,
    ImageGeneration = 7,
    Moderation = 8
}
```

For Qwen3-4B:

```csharp
ModelType = AiModelType.Chat;
```

Do not use:

```csharp
ModelType = AiModelType.Code;
```

Coding should normally be treated as a capability rather than the main model type.

---

## 12. Recommended Capability Enum

```csharp
namespace AiChatbot.Domain.Modules.AiModels.Enums;

public enum AiModelCapability
{
    Chat = 1,
    Reasoning = 2,
    Coding = 3,
    Summarization = 4,
    Translation = 5,
    ToolCalling = 6,
    Streaming = 7
}
```

Example Qwen3-4B capabilities:

```text
Qwen3-4B
│
├── Chat
├── Reasoning
├── Coding
├── Summarization
├── Translation
├── Tool Calling
└── Streaming
```

---

## 13. Model Type vs Capability

These concepts should be stored separately.

### Model Type

Describes the primary technical role of the model.

Examples:

```text
Chat
Embedding
Reranker
Vision
SpeechToText
TextToSpeech
ImageGeneration
Moderation
```

### Model Capability

Describes what the model can do.

Examples:

```text
Chat
Reasoning
Coding
Summarization
Translation
Tool Calling
Streaming
```

Example:

```text
Model:
Qwen3-4B

Type:
Chat

Capabilities:
- Chat
- Reasoning
- Coding
- Summarization
- Translation
- Tool Calling
- Streaming
```

---

## 14. Recommended Database Design

### AiModels

```text
AiModels
-----------------------------------
Id
Name
Slug
Provider
ModelType
Family
ParameterCount
Format
Quantization
ContextWindow
MaxOutputTokens
SupportsThinking
SupportsTools
SupportsStreaming
IsActive
CreatedAtUtc
UpdatedAtUtc
```

### AiModelCapabilities

```text
AiModelCapabilities
-----------------------------------
Id
AiModelId
Capability
```

Relationship:

```text
AiModel
   │
   └── 1 : Many
          │
          ▼
AiModelCapabilities
```

Example:

```text
Qwen3-4B
│
├── Chat
├── Reasoning
├── Coding
├── Summarization
├── Translation
├── ToolCalling
└── Streaming
```

---

## 15. Recommended Setup for DSA Knowledge Base

A practical first version can use:

```text
1. Chat Model
   Qwen3-4B

2. Embedding Model
   Dedicated embedding model

3. Vector Database
   Stores document embeddings

4. Reranker
   Optional at first
```

Architecture:

```text
Angular
   ↓
ASP.NET Core API
   ↓
RAG Service
   ├── Embedding Model
   ├── Vector Search
   ├── Optional Reranker
   └── Qwen3-4B
           ↓
      llama.cpp
```

For the first version, start with:

```text
Chat + Embedding
```

Then add:

```text
Reranker
```

when the RAG pipeline is already working correctly.

---

## 16. Final Recommended Classification

For `Qwen3-4B-Q4_K_M.gguf`:

```text
Name: Qwen3 4B
Slug: qwen3-4b
Family: Qwen3
Model Type: Chat
Parameters: 4B
Format: GGUF
Quantization: Q4_K_M
Provider: Local
Runtime: llama.cpp
```

Capabilities:

```text
Chat
Reasoning
Coding
Summarization
Translation
Tool Calling
Streaming
```

Do not classify `Q4_K_M` as a model type.

Correct:

```text
ModelType = Chat
Quantization = Q4_K_M
Format = GGUF
```
