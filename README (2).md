# AiChatbot Workflow

Full-stack AI chatbot workflow using:

- Angular
- ASP.NET Core .NET 8
- SignalR
- MySQL
- llama.cpp
- Qwen3 4B GGUF
- Knowledge Base / RAG
- Model Training

---

## 1. Normal Chat Workflow

The normal chatbot flow is:

```text
User
  ↓
Angular Chat UI
  ↓
User types:
"Can I ask you a question?"
  ↓
ChatPage
  ↓
ChatHubService
  ↓
SignalR
  ↓
ASP.NET Core ChatHub
  ↓
Validate:
- User JWT
- Selected model
- Provider
- Conversation
  ↓
IChatModelService
  ↓
ChatModelService
  ↓
llama.cpp Server
http://127.0.0.1:8081
  ↓
Qwen3 4B GGUF
  ↓
AI generates readable text
  ↓
llama.cpp returns streaming tokens
  ↓
ChatModelService parses tokens
  ↓
ChatHub
  ↓
SignalR
  ↓
Angular
  ↓
Assistant message shown to user
```

Example:

```text
USER
↓
What is C#?

ANGULAR
↓
{
  provider: "local",
  model: "qwen3-4b",
  messages: [
    {
      role: "user",
      content: "What is C#?"
    }
  ]
}

ASP.NET
↓
ChatHub.Generate()

↓
ChatModelService.GenerateStreamAsync()

↓
llama.cpp

↓
Qwen3 4B

↓
"C# is a modern programming language..."

↓
SignalR streaming

↓
ANGULAR UI

ASSISTANT
C# is a modern programming language...
```

> Normal user chat is **inference / generation**, not model training.

---

## 2. Runtime Architecture

```text
AiChatbot.Web
Angular
│
├── ChatPage
│      ↓
├── ChatHubService
│      ↓
│   SignalR
│      ↓
AiChatbot.Api
│
├── ChatHub
│      ↓
│
AiChatbot.Application
│
├── IChatModelService
│      ↓
│
AiChatbot.Infrastructure
│
├── ChatModelService
│      ↓
│
llama.cpp
│
├── llama-server.exe
│      ↓
│
Qwen3-4B-Q4_K_M.gguf
│
└── AI text generation
```

Important backend file:

```text
D:\AI\projects\AiChatbot\
src\AiChatbot.Infrastructure\
Ai\ChatModelService.cs
```

SignalR hub:

```text
D:\AI\projects\AiChatbot\
src\AiChatbot.Api\
Hubs\ChatHub.cs
```

Frontend chat page:

```text
D:\AI\projects\ai-chatbot-ui\AiChatbot.Web\
src\app\features\chat\
pages\chat-page\
chat-page.ts
```

---

## 3. Model Selection Workflow

Users can choose from models stored in MySQL.

```text
MySQL
ai_models
│
├── Mock Model
├── Qwen3 4B
├── Future Model 1
└── Future Model 2
      ↓
GET /api/v1/models
      ↓
Angular
      ↓
Model dropdown
      ↓
User selects:
Qwen3 4B
      ↓
provider = Local
model = qwen3-4b
      ↓
ChatHub
      ↓
ChatModelService
```

The model dropdown is for **model management and selection**.

It does not train the model.

---

## 4. llama.cpp Workflow

The local model runtime flow:

```text
Qwen GGUF file
      ↓
llama-server.exe
      ↓
Load model into RAM / GPU
      ↓
Expose API
      ↓
http://127.0.0.1:8081
      ↓
POST /v1/chat/completions
```

Example PowerShell command:

```powershell
cd D:\AI\llama.cpp

.\llama-server.exe `
  -m "D:\AI\models\models--Qwen--Qwen3-4B-GGUF\snapshots\bc640142c66e1fdd12af0bd68f40445458f3869b\Qwen3-4B-Q4_K_M.gguf" `
  --alias qwen3-4b `
  --host 127.0.0.1 `
  --port 8081 `
  -c 4096
```

Result:

```text
localhost:8081
      ↓
Ready for prompts
```

---

## 5. Message Storage Workflow

For real conversations:

```text
User sends message
      ↓
ChatHub
      ↓
MySQL
messages table
      ↓
Save:
role = User
content = "What is C#?"
modelId = Qwen3
      ↓
Generate AI response
      ↓
AI response
      ↓
MySQL
messages table
      ↓
Save:
role = Assistant
content = "C# is..."
modelId = Qwen3
```

Conversation example:

```text
Conversation
│
├── User
│   "What is C#?"
│
├── Assistant
│   "C# is a programming language..."
│
├── User
│   "Who created it?"
│
└── Assistant
    "C# was originally developed..."
```

---

## 6. Conversation Context Workflow

Previous messages should be included so the model understands follow-up questions.

```text
Conversation History
      ↓

User:
What is C#?

Assistant:
C# is a programming language...

User:
Who created it?

      ↓
Send relevant messages to Qwen
      ↓
Qwen understands:
"it" = C#
      ↓
Answer
```

Example request:

```json
{
  "model": "qwen3-4b",
  "messages": [
    {
      "role": "user",
      "content": "What is C#?"
    },
    {
      "role": "assistant",
      "content": "C# is a programming language..."
    },
    {
      "role": "user",
      "content": "Who created it?"
    }
  ]
}
```

---

## 7. Streaming Workflow

The response should appear gradually like a modern AI chat UI.

```text
Qwen
↓
"C#"
↓
" is"
↓
" a"
↓
" modern"
↓
" programming"
↓
" language"
```

Backend streaming flow:

```text
llama.cpp SSE
      ↓
ChatModelService
      ↓
ChatStreamChunk
      ↓
ChatHub
      ↓
MessageChunkReceived
      ↓
SignalR
      ↓
Angular
```

UI rendering:

```text
C#
C# is
C# is a
C# is a modern
C# is a modern programming
C# is a modern programming language
```

---

## 8. Model Training Workflow

Training is separate from normal chat.

```text
Admin
  ↓
Upload training dataset
  ↓
TrainingDataset
  ↓
Validate dataset
  ↓
TrainingExamples
  ↓
Create TrainingJob
  ↓
Worker / Training Runtime
  ↓
Fine-tune model
  ↓
Create new ModelVersion
  ↓
Evaluate
  ↓
Activate new model version
  ↓
Users can select it
```

Example training record:

```json
{
  "instruction": "Explain C# in Khmer",
  "input": "",
  "expectedOutput": "C# គឺជាភាសាសរសេរកម្មវិធី..."
}
```

Do not train the model every time a user asks a question.

Correct normal flow:

```text
User question
↓
Inference
↓
AI response
```

---

## 9. Knowledge Base / RAG Workflow

If the AI needs to answer from custom documents, use a Knowledge Base / RAG workflow.

```text
Admin/User uploads PDF/document
      ↓
Extract text
      ↓
Split into chunks
      ↓
Create embeddings
      ↓
Store in Knowledge Base
      ↓

User asks question
      ↓
Search relevant chunks
      ↓
Relevant knowledge
+
User question
      ↓
Qwen3
      ↓
Answer
```

Example:

```text
Company document:
"Our working hours are 8 AM - 5 PM"

User:
What time does the company close?

      ↓
RAG finds:
"Our working hours are 8 AM - 5 PM"

      ↓
Qwen3

      ↓
"The company closes at 5 PM."
```

RAG is usually better than retraining the model whenever information changes.

---

## 10. Full Final Workflow

```text
                     ┌──────────────────┐
                     │      USER        │
                     └────────┬─────────┘
                              ↓
                     ┌──────────────────┐
                     │ Angular Chat UI  │
                     └────────┬─────────┘
                              ↓
                     ┌──────────────────┐
                     │ SignalR Client   │
                     └────────┬─────────┘
                              ↓
                     ┌──────────────────┐
                     │    ChatHub       │
                     └────────┬─────────┘
                              ↓
                  ┌───────────┴───────────┐
                  ↓                       ↓
           Conversation              Knowledge Base
              History                    RAG
                  ↓                       ↓
                  └───────────┬───────────┘
                              ↓
                     ┌──────────────────┐
                     │ IChatModelService│
                     └────────┬─────────┘
                              ↓
                     ┌──────────────────┐
                     │ChatModelService  │
                     └────────┬─────────┘
                              ↓
                     ┌──────────────────┐
                     │   llama.cpp      │
                     │   :8081          │
                     └────────┬─────────┘
                              ↓
                     ┌──────────────────┐
                     │    Qwen3 4B      │
                     └────────┬─────────┘
                              ↓
                       Generated Text
                              ↓
                         Streaming
                              ↓
                          SignalR
                              ↓
                          Angular
                              ↓
                     ┌──────────────────┐
                     │ Assistant Answer │
                     └──────────────────┘
```

---

## 11. Recommended Implementation Order

```text
1. Qwen produces readable text
        ↓
2. Backend receives text
        ↓
3. SignalR streaming works
        ↓
4. Angular displays text
        ↓
5. Save conversations/messages
        ↓
6. Add multiple models
        ↓
7. Knowledge Base / RAG
        ↓
8. Model training
        ↓
9. Testing / Docker / Nginx / Deployment
```

---

## 12. Current Priority

The first priority is:

```text
User types text
      ↓
Angular
      ↓
ChatHub
      ↓
ChatModelService
      ↓
Qwen3 4B
      ↓
Readable text response
      ↓
SignalR
      ↓
Angular displays the answer
```

Training should come later.

