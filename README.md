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
<p>Now you're ready to call the Gemini API. Use the available Gemini models:</p>

+ gemini-pro: optimized for text-only prompts.
```
model = genai.GenerativeModel('gemini-pro')
```
+ gemini-pro-vision: optimized for text-and-images prompts.
```
model = genai.GenerativeModel('gemini-pro-vision')
```

<h2>Generate text from text inputs</h2>
<p>For text-only prompts, use the gemini-pro model.
According to Google, the gemini-pro has an input context length of 30k tokens and an output context length of 2k tokens.
</p>

```
model = genai.GenerativeModel('gemini-pro')
```
Pass the prompt to generate text, the ``response.text`` accessor is all you need. To display formatted Markdown text, use the Markdown function.

```
response = model.generate_content("Prompt")
Markdown(response.text)
```
If the API failed to return a result, use ``GenerateContentRespose.prompt_feedback`` to see if it was blocked due to saftey concerns regarding the prompt.

```
response.prompt_feedback
```
To see the number of output generated as per prompt

```
response.candidates
```
<h2>SAFETY OF THE MODEL</h2>
[Saftey Setting Gemini](https://ai.google.dev/docs/safety_setting_gemini)are part of the request you send to the text service. It can be adjusted for each request you make to the API. The following table lists the categories that you can set and describes the type of harm that each category encompasses.

|Categories|Decriptions|
|----|-----|
|Harassment|	Negative or harmful comments targeting identity and/or protected attributes.|
|Hate speech|	Content that is rude, disrespectful, or profane.|
|Sexually explicit|	Contains references to sexual acts or other lewd content.|
|Dangerous|	Promotes, facilitates, or encourages harmful acts.|

```
response = model.generate_content("Prompt",
                                  safety_settings=[
                                      {"category":'HARM_CATEGORY_HARASSMENT',
                                                    "threshold":'block_none'},
                                                     {"category":'HARM_CATEGORY_DANGEROUS_CONTENT',
                                                      "threshold":'block_none'},
                                                      {"category":'HARM_CATEGORY_HATE_SPEECH',
                                                       "threshold":'block_none'}])

```



