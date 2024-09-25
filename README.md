# 👾 LM Studio Server Examples

This repository contains code examples demonstrating how to interact with the LM Studio server, showcasing various capabilities and use cases.

## Table of Contents

- [Introduction](#introduction)
- [Examples](#examples)
  - [Hello World (curl)](#hello-world-curl)
  - [Chat Completion (Python)](#chat-completion-python)
  - [Interactive AI Assistant (Python)](#interactive-ai-assistant-python)
  - [Vision Analysis (Python)](#vision-analysis-python)
  - [Text Embeddings (Python)](#text-embeddings-python)
- [Version Compatibility](#version-compatibility)
- [Additional Resources](#additional-resources)

## Introduction

LM Studio allows you to run large language models locally. These examples demonstrate how to leverage the LM Studio server API for various natural language processing tasks using both curl and Python.

## Examples

### Hello World (curl)

A basic chat completion request using curl:

```bash
curl http://localhost:1234/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{ 
    "model": "model-identifier",
    "messages": [ 
      { "role": "system", "content": "Always answer in rhymes." },
      { "role": "user", "content": "Introduce yourself." }
    ], 
    "temperature": 0.7, 
    "max_tokens": -1,
    "stream": true
}'
```

### Chat Completion (Python)

Simple chat completion using the OpenAI Python library:

```python
from openai import OpenAI

client = OpenAI(base_url="http://localhost:1234/v1", api_key="lm-studio")

completion = client.chat.completions.create(
  model="model-identifier",
  messages=[
    {"role": "system", "content": "Always answer in rhymes."},
    {"role": "user", "content": "Introduce yourself."}
  ],
  temperature=0.7,
)

print(completion.choices[0].message)
```

### Interactive AI Assistant (Python)

An interactive AI assistant for your terminal:

```python
from openai import OpenAI

client = OpenAI(base_url="http://localhost:1234/v1", api_key="lm-studio")

history = [
    {"role": "system", "content": "You are an intelligent assistant. You always provide well-reasoned answers that are both correct and helpful."},
    {"role": "user", "content": "Hello, introduce yourself to someone opening this program for the first time. Be concise."},
]

while True:
    completion = client.chat.completions.create(
        model="model-identifier",
        messages=history,
        temperature=0.7,
        stream=True,
    )
    new_message = {"role": "assistant", "content": ""}
    
    for chunk in completion:
        if chunk.choices[0].delta.content:
            print(chunk.choices[0].delta.content, end="", flush=True)
            new_message["content"] += chunk.choices[0].delta.content
    history.append(new_message)
    
    print()
    history.append({"role": "user", "content": input("> ")})
```

### Vision Analysis (Python)

Analyze an image using vision capabilities:

```python
from openai import OpenAI
import base64

client = OpenAI(base_url="http://localhost:1234/v1", api_key="lm-studio")

path = input("Enter a local filepath to an image: ")

try:
    with open(path, "rb") as image_file:
        base64_image = base64.b64encode(image_file.read()).decode("utf-8")
except IOError:
    print("Couldn't read the image. Make sure the path is correct and the file exists.")
    exit()

completion = client.chat.completions.create(
  model="model-identifier",
  messages=[
    {
      "role": "system",
      "content": "You are an AI assistant that analyzes images.",
    },
    {
      "role": "user",
      "content": [
        {"type": "text", "text": "What's in this image?"},
        {
          "type": "image_url",
          "image_url": {
            "url": f"data:image/jpeg;base64,{base64_image}"
          },
        },
      ],
    }
  ],
  max_tokens=1000,
  stream=True
)

for chunk in completion:
    if chunk.choices[0].delta.content:
        print(chunk.choices[0].delta.content, end="", flush=True)
```

### Text Embeddings (Python)

Generate embeddings for a given text:

```python
from openai import OpenAI

client = OpenAI(base_url="http://localhost:1234/v1", api_key="lm-studio")

def get_embedding(text, model="model-identifier"):
   text = text.replace("\n", " ")
   return client.embeddings.create(input=[text], model=model).data[0].embedding

print(get_embedding("Once upon a time, there was a cat."))
```

## Version Compatibility

These examples are based on LM Studio server version 0.2.31. They may require adjustments for version 0.3.x or later. Always refer to the official LM Studio documentation for the most up-to-date information.

## Additional Resources

- [LM Studio Official Website](https://lmstudio.ai)
- [OpenAI Python Library Documentation](https://github.com/openai/openai-python)

---

Note: Replace `"model-identifier"` in the examples with the appropriate model identifier for your LM Studio setup. Ensure your LM Studio server is running and accessible at the specified URL before running these examples.
