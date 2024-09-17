# Google Gemini API Integration

This repository demonstrates the integration of Google Gemini's generative AI models into a Python environment. It showcases various functionalities such as content generation, image processing, chat interactions, and embedding generation using Google’s Gemini API. The code provided highlights how to leverage these models for different applications, including generating text based on prompts, creating content from images, engaging in conversational interactions, and generating vector embeddings for text data.

## Scope

The scope of this project encompasses several key areas of interaction with the Google Gemini API:

1. **Content Generation**: Create text based on given prompts using the generative AI models.
2. **Image Processing**: Generate textual content based on images, including creating descriptive blog posts.
3. **Chat Interactions**: Engage with the chat model to receive explanations and responses to user queries.
4. **Embedding Generation**: Obtain vector representations for text data, useful for tasks like information retrieval and similarity analysis.

## Setup and Configuration

### Prerequisites

Before using the provided code, ensure you have the following prerequisites:

- **Python 3.12 or newer**: The code is compatible with Python 3.12 or later versions.
- **Required Libraries**: The code depends on the `google-generativeai`, `kaggle_secrets`, and `PIL` libraries.

### Installation

1. **Install Dependencies**

   First, install the necessary Python libraries. Run the following command:

   ```bash
   pip install google-generativeai kaggle_secrets pillow
   ```

2. **Obtain API Key**

   To use the Google Gemini API, you need an API key. Follow the instructions on [Google’s AI tutorials setup page](https://ai.google.dev/tutorials/setup) to obtain your API key.

3. **Configure Secrets**

   Add your API key to your environment. Use the Kaggle secrets manager to create a secret named `GEMINI_API_KEY` and ensure it is available in your notebook or script.

### Configuration in Code

Configure the API key and initialize the Google Gemini client with the following code snippet:

```python
from kaggle_secrets import UserSecretsClient
import google.generativeai as genai

# Retrieve API Key
user_secrets = UserSecretsClient()
apiKey = user_secrets.get_secret("GEMINI_API_KEY")

# Configure API Key
genai.configure(api_key=apiKey)
```

## Content Generation

Google Gemini’s generative models can create text based on user prompts. This functionality is useful for generating creative content, answering questions, or elaborating on specific topics.

```python
model = genai.GenerativeModel('gemini-pro')
response = model.generate_content("What is the meaning of life?")
print(response.text)
```

**Explanation**: 
- **GenerativeModel**: Initializes the model with the specified ID ('gemini-pro').
- **generate_content**: Sends a prompt to the model and retrieves the generated content.

If the API call fails, use `response.prompt_feedback` to check for safety concerns or other issues.

## Image Processing

This functionality allows you to generate textual content based on images. This can be useful for tasks such as creating descriptive blog posts or generating captions for photos.

1. **Download and Display Image**

   ```python
   !curl -o image.jpg https://t0.gstatic.com/licensed-image?q=tbn:ANd9GcQ_Kevbk21QBRy-PgB4kQpS79brbmmEG7m3VOTShAn4PecDU5H5UxrJxE3Dw1JiaG17V88QIol19-3TM2wCHw
   from PIL import Image
   img = Image.open('image.jpg')
   img.show()
   ```

2. **Generate Content from Image**

   ```python
   model = genai.GenerativeModel('gemini-pro-vision')
   response = model.generate_content(img)
   print(response.text)
   ```

3. **Generate Blog Post from Image and Text**

   ```python
   response = model.generate_content(["Write a short, engaging blog post based on this picture. It should include a description of the meal in the photo and talk about my journey meal prepping.", img])
   print(response.text)
   ```

**Explanation**:
- **Download Image**: Retrieves an image from a URL.
- **Open and Display Image**: Uses PIL to open and display the image.
- **Generate Content from Image**: Sends the image to the model to generate related content.
- **Generate Blog Post**: Combines text and image to create a blog post.

## Chat Interactions

Engage with the chat model to receive explanations or answers to user queries. This feature is particularly useful for interactive applications where conversational AI is required.

1. **Start a Chat Session**

   ```python
   model = genai.GenerativeModel('gemini-pro')
   chat = model.start_chat(history=[])
   ```

2. **Send Messages and Get Responses**

   ```python
   response = chat.send_message("In one sentence, explain how a computer works to a young child.")
   print(response.text)
   
   response = chat.send_message("Okay, how about a more detailed explanation to a high schooler?")
   print(response.text)
   ```

3. **View Chat History**

   ```python
   for message in chat.history:
       print(f'**{message.role}**: {message.parts[0].text}')
   ```

**Explanation**:
- **Start a Chat Session**: Initializes a chat session with the model.
- **Send Messages**: Sends user messages and retrieves responses.
- **View Chat History**: Displays the history of messages exchanged during the chat.

## Embedding Generation

Generate embeddings (vector representations) for text. Embeddings are useful for tasks like information retrieval, similarity analysis, and more.

1. **Embedding for Single String**

   ```python
   result = genai.embed_content(
       model="models/embedding-001",
       content="What is the meaning of life?",
       task_type="retrieval_document",
       title="Embedding of single string"
   )
   print(str(result['embedding'])[:50], '... TRIMMED')
   ```

2. **Embedding for List of Strings**

   ```python
   result = genai.embed_content(
       model="models/embedding-001",
       content=[
         'What is the meaning of life?',
         'How much wood would a woodchuck chuck?',
         'How does the brain work?'
       ],
       task_type="retrieval_document",
       title="Embedding of list of strings"
   )
   for v in result['embedding']:
       print(str(v)[:50], '... TRIMMED ...')
   ```

3. **Embedding for Chat History**

   ```python
   result = genai.embed_content(
       model='models/embedding-001',
       content=chat.history
   )
   for i, v in enumerate(result['embedding']):
       print(str(v)[:50], '... TRIMMED...')
   ```

**Explanation**:
- **Embedding for Single String**: Generates an embedding for a single piece of text.
- **Embedding for List of Strings**: Generates embeddings for multiple pieces of text.
- **Embedding for Chat History**: Generates embeddings for the chat history.
