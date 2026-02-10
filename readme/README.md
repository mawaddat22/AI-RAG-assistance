# AI Knowledge Assistant

A powerful RAG (Retrieval-Augmented Generation) chatbot built with n8n that enables intelligent conversations with your documents. Upload PDFs and get accurate, context-aware responses powered by vector search and AI.

## Overview

This project implements a complete AI assistant workflow that combines document processing, vector embeddings, and conversational AI to create an intelligent question-answering system. Users can upload documents through a clean web interface and receive precise answers based solely on the uploaded content.

## Features

- **Document Processing**: Automatic PDF extraction and intelligent text chunking
- **Vector Search**: Semantic search using Pinecone vector database with Cohere embeddings
- **Conversational Memory**: Maintains context across 10 previous messages for natural dialogue
- **File Upload Support**: Easy document uploads through the chat interface
- **Source-Grounded Responses**: Answers are strictly based on uploaded documents, preventing hallucinations
- **Modern UI**: Clean, responsive interface with glassmorphic design

## Architecture

The workflow consists of two main pipelines:

### Document Ingestion Pipeline
1. PDF files are uploaded and extracted
2. Text is chunked into 500-character segments
3. Chunks are converted to embeddings using Cohere
4. Embeddings are stored in Pinecone vector database

### Chat Pipeline
1. User messages trigger the AI agent
2. Agent searches the vector store for relevant context
3. LLaMA 3 8B model generates responses using retrieved documents
4. Conversation history is maintained for context

## Technology Stack

- **Automation**: n8n workflow automation
- **LLM**: LLaMA 3 8B Instruct (via OpenRouter)
- **Embeddings**: Cohere embed-english-v3.0
- **Vector Database**: Pinecone
- **Frontend**: n8n Chat Widget with custom HTML/CSS
- **Memory**: Buffer window memory (10 messages)

## Prerequisites

Before you begin, ensure you have:

- n8n instance (self-hosted or cloud)
- API keys for:
  - Cohere (embeddings)
  - OpenRouter (LLM access)
  - Pinecone (vector storage)

## Setup Instructions

### 1. Create Pinecone Index

Create a new index in Pinecone with the following settings:
- **Name**: `llama-text-embed-v2-index`
- **Dimensions**: 1024 (for Cohere embed-english-v3.0)
- **Metric**: Cosine

### 2. Configure API Credentials

In your n8n instance, add credentials for:

**Cohere API**
- Create account at cohere.com
- Generate API key
- Add to n8n credentials

**OpenRouter API**
- Sign up at openrouter.ai
- Get API key
- Configure in n8n

**Pinecone API**
- Create account at pinecone.io
- Copy API key and environment
- Add to n8n credentials

### 3. Import the Workflow

1. Copy the contents of `workflow.json`
2. In n8n, go to Workflows → Import
3. Paste the JSON and import
4. Connect your credentials to each node

### 4. Deploy the Frontend

1. Update the webhook URL in `newn8n.html`:
   ```javascript
   webhookUrl: 'YOUR_N8N_WEBHOOK_URL'
   ```
2. Host the HTML file on your web server
3. Access the interface in your browser

## Usage

### Uploading Documents

1. Open the chat interface
2. Use the file upload feature to submit PDF documents
3. Wait for processing to complete
4. Documents are automatically chunked and indexed

### Asking Questions

Simply type your questions in the chat. The AI will:
- Search the vector database for relevant content
- Generate answers based only on your documents
- Indicate when information isn't found in the uploaded materials

### Example Interactions

```
User: What are the main topics covered in this document?
AI: [Searches vector store and provides summary based on content]

User: Can you explain the methodology section?
AI: [Retrieves relevant chunks and explains in context]

User: What does it say about future work?
AI: I could not find information about future work in the uploaded document.
```

## Configuration

### Adjusting Chunk Size

Modify the chunking logic in the "Code in JavaScript" node:

```javascript
const chunkSize = 500; // Adjust based on your needs
```

Smaller chunks: More precise retrieval, higher API costs
Larger chunks: More context per chunk, fewer API calls

### Memory Window

Change conversation history length in the "Simple Memory" node:

```javascript
contextWindowLength: 10 // Number of previous messages
```

### System Prompt

Customize the AI's behavior in the "AI Agent" node system message:

```
You are a helpful AI assistant.
You must always search the vector store tool to answer questions.
Only answer from the retrieved documents.
[Customize as needed]
```

## Cost Considerations

- **Cohere**: ~$0.0001 per 1k tokens for embeddings
- **OpenRouter**: LLaMA 3 8B pricing varies (~$0.10-0.20 per 1M tokens)
- **Pinecone**: Free tier available, paid plans start at $70/month

Monitor your usage and consider implementing rate limiting for production deployments.

## Troubleshooting

**Issue**: Chat widget not appearing
- Verify webhook URL is correct and publicly accessible
- Check browser console for errors
- Ensure n8n workflow is active

**Issue**: No results from vector search
- Confirm documents were successfully uploaded
- Check Pinecone index has vectors
- Verify embedding model matches between ingestion and retrieval

**Issue**: Responses from general knowledge instead of documents
- Review system prompt enforcement
- Check vector store tool is properly connected
- Verify retrieval is happening (check n8n execution logs)

## Security Notes

- Never expose API keys in frontend code
- Use environment variables for sensitive credentials
- Implement authentication if deploying publicly
- Consider rate limiting to prevent abuse
- Regularly rotate API keys

## Future Enhancements

- [ ] Support for multiple file formats (DOCX, TXT, MD)
- [ ] Citation of specific document chunks in responses
- [ ] Multi-document management and selection
- [ ] Advanced filtering and search options
- [ ] Usage analytics and logging
- [ ] User authentication and session management

## Contributing

Contributions are welcome! Please feel free to submit issues or pull requests.

## License

This project is provided as-is for educational and personal use.

## Acknowledgments

- Built with [n8n](https://n8n.io/)
- Powered by [Cohere](https://cohere.com/), [OpenRouter](https://openrouter.ai/), and [Pinecone](https://pinecone.io/)
- UI components from [@n8n/chat](https://www.npmjs.com/package/@n8n/chat)

---

**Need help?** Open an issue or reach out to the community for support.
