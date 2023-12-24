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

[Saftey Setting Gemini](https://ai.google.dev/docs/safety_setting_gemini) are part of the request you send to the text service. It can be adjusted for each request you make to the API. The following table lists the categories that you can set and describes the type of harm that each category encompasses.

|Categories|Decriptions|
|----|-----|
|Harassment|	Negative or harmful comments targeting identity and/or protected attributes.|
|Hate speech|	Content that is rude, disrespectful, or profane.|
|Sexually explicit|	Contains references to sexual acts or other lewd content.|
|Dangerous|	Promotes, facilitates, or encourages harmful acts.|

The following is a python code snippet showing how to set safety settings in your ``GenerateContent`` call. This sets the harm categories Harassment and Hate speech to ``BLOCK_LOW_AND_ABOVE`` which blocks any content that has a low or higher probability of being ``Harassment`` or ``Hate speech`` and ``BLOCK_NONE`` for ``Dangerous`` category.

```
response = model.generate_content("Prompt",
                   safety_settings=[
                            {"category":'HARM_CATEGORY_HARASSMENT',
                             "threshold":'BLOCK_LOW_AND_ABOVE'},
                            {"category":'HARM_CATEGORY_DANGEROUS_CONTENT',
                             "threshold":'BLOCK_NONE'},
                            {"category":'HARM_CATEGORY_HATE_SPEECH',
                             "threshold":'BLOCK_LOW_AND_ABOVE'}])

```

<h2>Configuring Hyperparameters with GenerationConfig</h2>
To generate content based on the prompt "Explain Quantum Mechanics to a five year old?" The `model.generate_content()` function likely uses a machine learning model to generate text based on the input prompt and certain generation configuration parameters.

```
response = model.generate_content("Explain Quantum Mechanics to a five year old?",
                                  generation_config=genai.types.GenerationConfig(
                                  candidate_count=1,
                                  stop_sequences=['.'],
                                  max_output_tokens=20,
                                  top_p = 0.7,
                                  top_k = 4,
                                  temperature=0.7)
                                  )
Markdown(response.text)
```
`candidate_count`:
This parameter specifies the number of candidate responses that the model will consider before making a final prediction. In this case, it's set to 1, so the model generates a single candidate response.

`stop_sequences`:
It's a list of strings that, when encountered in the generated text, signals the model to stop generating further content. In this case, the model will stop generating when it encounters a period ('.'). So, the generated output will be up to the first occurrence of a period.

`max_output_tokens`:
It determines the maximum number of tokens (words or subword units) that the model will output in the generated text. The generation will stop once this limit is reached. Here, it's set to 20 tokens.

`max_output_tokens`:
It determines the maximum number of tokens (words or subword units) that the model will output in the generated text. The generation will stop once this limit is reached. Here, it's set to 20 tokens.


`top_k` (Top-k sampling):
This parameter restricts the model to consider only the top k most probable tokens at each step of generation. It helps in controlling the diversity of generated text. Lower values of top_k make the model more conservative, while higher values allow more diversity. Here, it's set to 4

`temperature`
It controls the randomness in the generation process. A higher temperature leads to more randomness and diversity in the generated text, while a lower temperature produces more deterministic outputs. Here, it's set to 0.7, indicating a moderate level of randomness.

Response:`Imagine you have a very special box`

