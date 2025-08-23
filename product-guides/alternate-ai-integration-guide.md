---
description: >-
  Information for using an alternate AI other than Ollama & OpenWebUI or OpenAI
  (ChatGPT)
---

# 🛑 Alternate AI Integration Guide

### Supported AI Models and Services

Reactive Resume is designed to work seamlessly with AI services that implement the standard OpenAI API interface. This includes both local AI deployments and cloud-based services that follow the OpenAI API specification.

### Compatibility Requirements

For optimal performance, your AI service must support:

* OpenAI-style API endpoints (`/v1/chat/completions`)
* Standard API key authentication format (`sk-****`)
* Identical request/response structure as OpenAI's API

### Recommended Setup Options

#### Option 1: Local AI Deployment (Recommended)

* **Ollama** with OpenWebUI interface

#### Option 2: Cloud Services

* **OpenAI ChatGPT** (direct integration)
* **Azure OpenAI Services**
* Any service providing full OpenAI API compatibility

### Important Notice: Unsupported Configurations

#### Incompatible Services

Services such as **Perplexity AI**, **Claude API**, and other providers that do not implement the exact OpenAI API specification will result in errors including:

* `Error 400: Bad Request`
* `Error 405: Method Not Allowed`
* Authentication failures
* Response parsing errors

#### Critical: Avoid "Thinking" Models

**We highly recommend against using "thinking" AI models** such as DeepSeek's thinking models or any AI that employs extended processing chains before responding. These models:

1. **Break expected response patterns** that Reactive Resume relies on
2. **Cause timeouts and processing errors** due to extended response times
3. **Provide unstructured responses** that cannot be properly parsed
4. **Interfere with the standard request-response flow** essential for resume generation

### Troubleshooting

If you encounter errors when using AI features:

1. **Verify your endpoint** matches the OpenAI API structure exactly
2. **Confirm API key format** uses the `sk-` prefix
3. **Test with standard OpenAI** first to establish baseline functionality
4. **Check that your local AI deployment** (Ollama, etc.) is properly configured with OpenWebUI or equivalent OpenAI-compatible interface

### Need Help?

For additional support with AI integration, please consult:

* OpenWebUI documentation for local setup
* Ollama's OpenAI compatibility guide
* Reactive Resume's AI integration documentation

Remember: Successful AI integration requires exact compliance with the OpenAI API specification. Alternative implementations will not function correctly with Reactive Resume.

**Note:** Reactive Resume is built around the OpenAI API standard. If you have experience integrating other AI services and can implement support without significant code changes, we welcome your contribution. Please feel free to submit a pull request to our [GitHub project](https://github.com/lazy-media/Reactive-Resume).
