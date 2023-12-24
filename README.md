# Hands on Google Gemini
<p><h4>This quickstart demonstrates how to use the Python SDK for the Gemini API, which gives you access to Google's Gemini large language models. In this quickstart, you will learn how to:
<ol>
<li>Set up your development environment and API access to use Gemini.</li>
<li>Generate text responses from text inputs.</li>
<li>Generate text responses from multimodal inputs (text and images).</li>
<li>Use Gemini for multi-turn conversations (chat).</li>
<li>Use embeddings for large language models.</li></ol></h4></p>
<br>

<h3>Install Google Generative AI</h3>

```
!pip install -q -U google-generativeai
```
<h3>Import Generative AI</h3>

```
import google.generativeai as genai
```
<h3>Pass the API Key</h3>

```
genai.configure(api_key=GOOGLE_API_KEY)
```
<h2>List models</h2>
<p><h4>Now you're ready to call the Gemini API. Use the available Gemini models:</h4></p>

+ gemini-pro: optimized for text-only prompts.
```
model = genai.GenerativeModel('gemini-pro')
```
+ gemini-pro-vision: optimized for text-and-images prompts.
```
model = genai.GenerativeModel('gemini-pro-vision')
```
