# Hallucination Leaderboard

Public LLM leaderboard computed using Vectara's Hallucination Evaluation Model. This evaluates how often an LLM introduces hallucinations when summarizing a document. We plan to update this regularly as our model and the LLMs get updated over time.

Also, feel free to check out our [hallucination leaderboard](https://huggingface.co/spaces/vectara/leaderboard) in HuggingFace. 

Last updated on February 21st, 2024

|Model|Hallucination Rate|Factual Consistency Rate|Answer Rate|Average Summary Length (Words)|
|----|----:|----:|----:|----:|
|GPT 4|3.0 %|97.0 %|100.0 %|81.1|
|GPT 4 Turbo|3.0 %|97.0 %|100.0 %|94.3|
|Microsoft Orca-2-13b|3.2 %|96.8 %|100.0 %|66.2|
|GPT 3.5 Turbo|3.5 %|96.5 %|99.6 %|84.1|
|Google Gemini Pro|4.8 %|95.2 %|98.4 %|89.5|
|Llama 2 70B|5.1 %|94.9 %|99.9 %|84.9|
|Llama 2 7B|5.6 %|94.4 %|99.6 %|119.9|
|Llama 2 13B|5.9 %|94.1 %|99.8 %|82.1|
|Google Gemma-7b-it|7.5 %|92.5 %|100.0 %|113.0|
|Cohere-Chat|7.5 %|92.5 %|98.0 %|74.4|
|Cohere|8.5 %|91.5 %|99.8 %|59.8|
|Anthropic Claude 2|8.5 %|91.5 %|99.3 %|87.5|
|Microsoft Phi 2|8.5 %|91.5 %|91.5 %|80.8|
|Google Palm 2|8.6 %|91.4 %|99.8 %|86.6|
|Mixtral 8x7B|9.3 %|90.7 %|99.9 %|90.7|
|Amazon Titan Express|9.4 %|90.6 %|99.5 %|98.4|
|Mistral 7B|9.4 %|90.6 %|98.7 %|96.1|
|Google Palm 2 Chat|10.0 %|90.0 %|100.0 %|66.2|
|Google flan-t5-large|15.8 %|84.2 %|99.3 %|20.9|
|tiiuae falcon-7b-instruct|16.2 %|83.8 %|90.0 %|75.5|


**Note** on GPT4 Turbo. While the above figures show it to be comparable to GPT4, this is due to us filtering out some documents that some of the models refuse to summarize. When comparing to GPT 4 on all summaries (both GPT4 models summarize all documents) the turbo model is around 0.3% worse than GPT4, but still better than GPT 3.5 Turbo.

<table style="border-collapse: collapse;">
  <tr>
    <td style="text-align: center; vertical-align: middle; border: none;">
      <img src="img/candle.png" width="50" height="50">
    </td>
    <td style="text-align: left; vertical-align: middle; border: none;">
      In loving memory of <a href="https://www.ivinsfuneralhome.com/obituaries/Simon-Mark-Hughes?obId=30000023">Simon Mark Hughes</a>...
    </td>
  </tr>
</table>

## Model
You can find the model used to compute this leaderboard open sourced for commercial use on hugging face: https://huggingface.co/vectara/hallucination_evaluation_model along with instructions how to use the model.

## Data
See [link](https://drive.google.com/drive/folders/1TUlFlOIFfDjzobGz7WMx7hoUmIyV0x0x?usp=drive_link) for the generated summaries we used to evaluate the models with.

## Prior Research
Much prior work in this area has been done. For some of the top papers in this area (factual consistency in summarization) please see here:

* [SUMMAC: Re-Visiting NLI-based Models for Inconsistency Detection in Summarization](https://aclanthology.org/2022.tacl-1.10.pdf)
* [TRUE: Re-evaluating Factual Consistency Evaluation](https://arxiv.org/pdf/2204.04991.pdf)
* [TrueTeacher: Learning Factual Consistency Evaluation with Large Language Models](https://browse.arxiv.org/pdf/2305.11171v1.pdf)
* [ALIGNSCORE: Evaluating Factual Consistency with A Unified Alignment Function](https://arxiv.org/pdf/2305.16739.pdf)

For a very comprehensive list, please see here - https://github.com/EdinburghNLP/awesome-hallucination-detection. The methods described in the following section use protocols established in those papers, amongst many others.

## Methodology
For a detailed explanation of the work that went into this model please refer to our blog post on the release: [Cut the Bull…. Detecting Hallucinations in Large Language Models](https://vectara.com/blog/cut-the-bull-detecting-hallucinations-in-large-language-models/).

To determine this leaderboard, we trained a model to detect hallucinations in LLM outputs, using various open source datasets from the factual consistency research into summarization models. Using a model that is competitive with the best state of the art models, we then fed 1000 short documents to each of the LLMs above via their public APIs and asked them to summarize each short document, using only the facts presented in the document. Of these 1000 documents, only 831 document were summarized by every model, the remaining documents were rejected by at least one model due to content restrictions. Using these 831 documents, we then computed the overall factual consistency rate (no hallucinations) and hallucination rate (100 - accuracy) for each model. The rate at which each model refuses to respond to the prompt is detailed in the 'Answer Rate' column. None of the content sent to the models contained illicit or 'not safe for work' content but the present of trigger words was enough to trigger some of the content filters. The documents were taken primarily from the [CNN / Daily Mail Corpus](https://huggingface.co/datasets/cnn_dailymail/viewer/1.0.0/test). We used a **temperature of 0** when calling the LLMs.

We evaluate summarization factual consistency rate instead of overall factual accuracy because it allows us to compare the model's response to the provided information. In other words, is the summary provided 'factually consistent' with the source document. Determining hallucinations is impossible to do for any ad hoc question as it's not known precisely what data every LLM is trained on. In addition, having a model that can determine whether any response was hallucinated without a reference source requires solving the hallucination problem and presumably training a model as large or larger than these LLMs being evaluated. So we instead chose to look at the hallucination rate within the summarization task as this is a good analogue to determine how truthful the models are overall. In addition, LLMs are increasingly used in RAG (Retrieval Augmented Generation) pipelines to answer user queries, such as in Bing Chat and Google's chat integration. In a RAG system, the model is being deployed as a summarizer of the search results, so this leaderboard is also a good indicator for the accuracy of the models when used in RAG systems.

## Prompt Used
> You are a chat bot answering questions using data. You must stick to the answers provided solely by the text in the passage provided. You are asked the question 'Provide a concise summary of the following passage, covering the core pieces of information described.'  &lt;PASSAGE&gt;'

When calling the API, the &lt;PASSAGE&gt; token was then replaced with the source document (see the 'source' column in [leaderboard-summaries.csv](https://github.com/vectara/hallucination-leaderboard/blob/main/leaderboard_summaries.csv) ). 

## API Integration Details
Below is a detailed overview of the models integrated and their specific endpoints:

### OpenAI Models
- **GPT-3.5**: Accessed using the model name `gpt-3.5-turbo` through OpenAI's Python client library, specifically via the `chat.completions.create` endpoint.
- **GPT-4**: Integrated with the model identifier `gpt-4`.
- **GPT-4 Turbo**: Utilized under the model name `gpt-4-1106-preview`, in line with OpenAI's documentation.

### Llama Models via Anyscale
- **Llama 7B, 13B, and 70B**: These models of varying sizes are accessed through Anyscale hosted endpoints, tailored to each model's capacity.

### Cohere Models
- **Cohere Command**: Employed using the model `command` and the `/generate` endpoint.
- **Cohere-Chat**: Integrated through the `/chat` endpoint for enhanced conversational capabilities.

### Anthropic Model
- **Claude 2**: The largest model offered by Anthropic, `claude 2`, is integrated via their API. 

### Mistral AI Models on Hugging Face
- **Mistral 7B**: The [Mistral-7B-Instruct-v0.1](https://huggingface.co/mistralai/Mistral-7B-Instruct-v0.1) model is integrated using Hugging Face's API.
- **Mixtral 8x7B**: Similarly, the [Mixtral-8x7B-Instruct-v0.1](https://huggingface.co/mistralai/Mixtral-8x7B-Instruct-v0.1) model is accessed via Hugging Face's API.

### Google Palm Models via Vertex AI
- **Google Palm 2 and Google Palm 2 Chat**: Implemented using the `text-bison-001` and `chat-bison-001` models, respectively.
- **Google Palm 2 (Beta) and Google Palm 2-Chat (Beta)**: Utilized with the model identifiers `text-bison` and `chat-bison`.
- **Gemini Pro**: Google's `gemini-pro` model is incorporated for enhanced language processing, accessible on Vertex AI.

For an in-depth understanding of each model's version and lifecycle, especially those offered by Google, please refer to [Model Versions and Lifecycles](https://cloud.google.com/vertex-ai/docs/generative-ai/learn/model-versioning) on Vertex AI.

### Titan Models on Amazon Bedrock
- **Amazon Titan Express**: The [model](https://aws.amazon.com/bedrock/titan/) is accessed on Amazon Bedrock with model identifier of `amazon.titan-text-express-v1`.

### Microsoft Models on Hugging Face
- **Microsoft Phi-2**: The [phi-2](https://huggingface.co/microsoft/phi-2) model is accessed via Hugging Face's API.
- **Microsoft Orca-2-13b**: The [Orca-2-13b](https://huggingface.co/microsoft/Orca-2-13b) model is accessed via Hugging Face's API.

### Google Models on Hugging Face
- **Google flan-t5-large**: The [flan-t5-large](https://huggingface.co/google/flan-t5-large) model is accessed via Hugging Face's API.
- **Google gemma-7b-it**: The [gemma-7b-it](https://huggingface.co/google/gemma-7b-it) model is accessed via Hugging Face's API. 

### tiiuae Models on Hugging Face
- **tiiuae/falcon-7b-instruct**: The [falcon-7b-instruct](https://huggingface.co/tiiuae/falcon-7b-instruct) model is accessed via Hugging Face's API. 

## Frequently Asked Questions
* **Qu.** Why are you are using a model to evaluate a model?
* **Answer** There are several reasons we chose to do this over a human evaluation. While we could have crowdsourced a large human scale evaluation, that's a one time thing, it does not scale in a way that allows us to constantly update the leaderboard as new APIs come online or models get updated. We work in a fast moving field so any such process would be out of data as soon as it published. Secondly, we wanted a repeatable process that we can share with others so they can use it themselves as one of many LLM quality scores they use when evaluating their own models. This would not be possible with a human annotation process, where the only things that could be shared are the process and the human labels. It's also worth pointing out that building a model for detecting hallucinations is **much easier** than building a generative model that never produces hallucinations. So long as the hallucination evaluation model is highly correlated with human raters' judgements, it can stand in as a good proxy for human judges. As we are specifically targetting summarization and not general 'closed book' question answering, the LLM we trained does not need to have memorized a large proportion of human knowledge, it just needs to have a solid grasp and understanding of the languages it support (currently just english, but we plan to expand language coverage over time).

* **Qu.** What if the LLM refuses to summarize the document or provides a one or two word answer?
* **Answer** We explicitly filter these out. See out [blog post](https://vectara.com/blog/cut-the-bull-detecting-hallucinations-in-large-language-models/) for more information. You can see the 'Answer Rate' column on the leaderboard that indicates the percentage of documents summarized, and the 'Average Summary Length' column detailing the summary lengths, showing we didn't get very short answers for most documents.

* **Qu.** What version of model XYZ did you use?
* **Answer** Please see the API details section for specifics about the model versions used and how they were called, as well as the date the leaderboard was last updated. Please contact us (create an issue in the repo) if you need more clarity. 

* **Qu.** What about xAI's Grok LLM?
* **Answer** Currently (as of 11/14/2023) Grok is not publicly available and we do not have access. Those with early access I suspect are probably legally forbidden from doing this sort of evaluation on the model. Once the model is available via a public API we will look to add it, along with any other LLMs that are popular enough.

* **Qu.** Can't a model just score a 100% by providing either no answers or very short answers?
* **Answer** We explicitly filtered out such responses from every model, doing the final evaluation only on documents that all models provided a summary for. You can find out more technical details in our [blog post]([https://vectara.com/blog/cut-the-bull-detecting-hallucinations-in-large-language-models/) on the topic. See also the 'Answer Rate' and 'Average Summary Length' columns in the table above.

* **Qu.** Wouldn't an extractive summarizer model that just copies and pastes from the original summary score 100% (0 hallucination) on this task?
* **Answer** Absolutely as by definition such a model would have no hallucinations and provide a faithful summary. We do not claim to be evaluating summarization quality, that is a separate and **orthogonal** task, and should be evaluated independently. We are **not** evaluating the quality of the summaries, only the **factual consistency** of them, as we point out in the [blog post](https://vectara.com/cut-the-bull-detecting-hallucinations-in-large-language-models/).

* **Qu.** This seems a very hackable metric, as you could just copy the original text as the summary
* **Answer.** That's true but we are not evaluating arbitrary models on this approach, e.g. like in a Kaggle competition. Any model that does so would perform poorly at any other task you care about. So I would consider this as quality metric that you'd run alongside whatever other evaluations you have for your model, such as summarization quality, question answering accuracy, etc. But we do not recommend using this as a standalone metric. None of the models chosen were trained on our model's output. That may happen in future but as we plan to update the model and also the source documents so this is a living leaderboard, that will be an unlikely occurrence. That is however also an issue with any LLM benchmark. We should also point out this builds on a large body of work on factual consistency where many other academics invented and refined this protocol. See our references to the SummaC and True papers in this [blog post](https://vectara.com/blog/cut-the-bull-detecting-hallucinations-in-large-language-models/), as well as this excellent compilation of resources - https://github.com/EdinburghNLP/awesome-hallucination-detection to read more.

* **Qu.** This does not definitively measure all the ways a model can hallucinate
* **Answer.** Agreed. We do not claim to have solved the problem of hallucination detection, and plan to expand and enhance this process further. But we do believe it is a move in the right direction, and provides a much needed starting point that everyone can build on top of.

* **Qu.** Some models could hallucinate only while summarizing. Couldn't you just provide it a list of well known facts and check how well it can recall them?
* **Answer.** That would be a poor test in my opinion. For one thing, unless you trained the model you don't know the data it was trained on, so you can't be sure the model is grounding its response in real data it has seen on or whether it is guessing. Additionally, there is no clear definition of 'well known', and these types of data are typically easy for most models to accurately recall. Most hallucinations, in my admittedly subjective experience, come from the model fetching information that is very rarely known or discussed, or facts for which the model has seen conflicting information. Without knowing the source data the model was trained on, again it's impossible to validate these sort of hallucinations as you won't know which data fits this criterion. I also think its unlikely the model would only hallucinate while summarizing. We are asking the model to take information and transform it in a way that is still faithful to the source. This is analogous to a lot of generative tasks aside from summarization (e.g. write an email covering these points...), and if the model deviates from the prompt then that is a failure to follow instructions, indicating the model would struggle on other instruction following tasks also.

* **Qu.** This is a good start but far from definitive
* **Answer.** I totally agree. There's a lot more that needs to be done, and the problem is far from solved. But a 'good start' means that hopefully progress will start to be made in this area, and by open sourcing the model, we hope to involve the community into taking this to the next level.

## Coming Soon
* We will also be adding a leaderboard on citation accuracy. As a builder of RAG systems, we have noticed that LLMs tend to mis-attribute sources sometimes when answering a question based on supplied search results. We'd like to be able to measure this so we can help mitigate it within our platform.
* We will also look to expand the benchmark to cover other RAG tasks, such as multi-document summarization.
* We also plan to cover more languages than just english. Our current platform covers over 100 languages, and we want to develop hallucination detectors with comparable multi-lingual coverage.
