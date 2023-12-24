# Hands on Google Gemini
<p>This quickstart demonstrates how to use the Python SDK for the Gemini API, which gives you access to Google's Gemini large language models. In this quickstart, you will learn how to:
<ol>
<li>Set up your development environment and API access to use Gemini.</li>
<li>Generate text responses from text inputs.</li>
<li>Generate text responses from multimodal inputs (text and images).</li>
<li>Use Gemini for multi-turn conversations (chat).</li>
<li>Use embeddings for large language models.</li></ol></p>
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

Get your [APIkey](https://makersuite.google.com/app/apikey)

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

<h2>Generate text from image and text inputs</h2>
Gemini provides a multimodal model (`gemini-pro-vision`) that accepts both text and images and inputs. The `GenerativeModel.generate_content` API is designed to handle multimodal prompts and returns a text output.

```
import PIL.Image

img = PIL.Image.open('leaf2dull.JPG')
img
```
![Dull Leaf](https://github.com/iamrajharshit/OnGemini/blob/main/img%20data/leaf2%20dull.JPG)

Use the `gemini-pro-vision` model and pass the image to the model with `generate_content`.

```
model = genai.GenerativeModel('gemini-pro-vision')
```
```
response = model.generate_content(img)
Markdown(response.text)
```
To provide both text and images in a prompt, pass a list containing the strings and images:
```
response = model.generate_content(["Describe the health of the leaf.", img], stream=True)
response.resolve()
Markdown(response.text)
```

<h2>Chat conversations</h2>

Gemini enables you to have freeform conversations across multiple turns. The `ChatSession` class simplifies the process by managing the state of the conversation, so unlike with `generate_content`, you do not have to store the conversation history as a list.

Initialize the chat:
```
model = genai.GenerativeModel('gemini-pro')
chat = model.start_chat(history=[])
chat
```
Note: The vision model `gemini-pro-vision` is not optimized for multi-turn chat.

The `ChatSession.send_message` method returns the same `GenerateContentResponse` type as `GenerativeModel.generate_content`. It also appends your message and the response to the chat history:
```
response = chat.send_message("What are the usecases of LLMs?")
Markdown(response.text)
```
Check history
```
chat.history
```
```
response = chat.send_message("Okay. Can you explain more about Information Retrieval using llms?")
Markdown(response.text)
```
Know about [glm](https://medium.com/@sarka.pribylova/generalized-linear-model-f607ac7f0ef5) <br>
`glm.Content` objects contain a list of `glm.Part` objects that each contain either a text (string) or inline_data (`glm.Blob`), where a blob contains binary data and a `mime_type`. The chat history is available as a list of `glm.Content` objects in `ChatSession.history`:
```
for message in chat.history:
  display(Markdown(f'**{message.role}**: {message.parts[0].text}'))
```
<h2>Use Embeddings</h2>

[Embedding](https://developers.google.com/machine-learning/glossary#embedding-vector) is a technique used to represent information as a list of floating point numbers in an array. With Gemini, you can represent text (words, sentences, and blocks of text) in a vectorized form, making it easier to compare and contrast embeddings. For example, two texts that share a similar subject matter or sentiment should have similar embeddings, which can be identified through mathematical comparison techniques such as cosine similarity. For more on how and why you should use embeddings, refer to the [Embeddings guide](https://ai.google.dev/docs/embeddings_guide).

Use the `embed_content` method to generate embeddings. The method handles embedding for the following tasks (`task_type`):

|Task Type|	Description|
|----|----|
|RETRIEVAL_QUERY|	Specifies the given text is a query in a search/retrieval setting.
|RETRIEVAL_DOCUMENT|	Specifies the given text is a document in a search/retrieval setting. Using this task type requires a title.
|SEMANTIC_SIMILARITY|	Specifies the given text will be used for Semantic Textual Similarity (STS).
|CLASSIFICATION|	Specifies that the embeddings will be used for classification.
|CLUSTERING|	Specifies that the embeddings will be used for clustering.

The following generates an embedding for a single string for document retrieval:
```
result = genai.embed_content(
    model="models/embedding-001",
    content="What is the meaning of life?",
    task_type="retrieval_document",
    title="Embedding of single string")

# 1 input > 1 vector output
print(str(result['embedding'])[:50], '... TRIMMED]')
```
Note: The `retrieval_document` task type is the only task that accepts a title.

To handle batches of strings, pass a list of strings in `content`:

```
result = genai.embed_content(
    model="models/embedding-001",
    content=[
      'What is the meaning of life?',
      'How much wood would a woodchuck chuck?',
      'How does the brain work?'],
    task_type="retrieval_document",
    title="Embedding of list of strings")

# A list of inputs > A list of vectors output
for v in result['embedding']:
  print(str(v)[:50], '... TRIMMED ...')
```
While the `genai.embed_content` function accepts simple strings or lists of strings, it is actually built around the `glm.Content` type (like `GenerativeModel.generate_content`). `glm.Content` objects are the primary units of conversation in the API.

While the `glm.Content` object is multimodal, the `embed_content` method only supports text embeddings. This design gives the API the possibility to expand to multimodal embeddings.

```
response.candidates[0].content
```
```
result = genai.embed_content(
    model = 'models/embedding-001',
    content = response.candidates[0].content)

# 1 input > 1 vector output
print(str(result['embedding'])[:50], '... TRIMMED ...')
```
Similarly, the chat history contains a list of `glm.Content` objects, which you can pass directly to the `embed_content` function:

```
chat.history
```

```
result = genai.embed_content(
    model = 'models/embedding-001',
    content = chat.history)

# 1 input > 1 vector output
for i,v in enumerate(result['embedding']):
  print(str(v)[:50], '... TRIMMED...')
```


