---
sidebar_position: 1
---

# 什么是Prompt？

## What is a prompt?

A prompt, sometimes referred to as context, is the text provided to a model before it begins generating output. It guides the model to explore a particular area of what it has learned so that the output is relevant to your goals. As an analogy, if you think of the language model as a source code interpreter, then a prompt is the source code to be interpreted. Somewhat amusingly, a language model will happily attempt to guess what source code will do:

![](https://user-images.githubusercontent.com/89960/231946874-be91d3de-d773-4a6c-a4ea-21043bd5fc13.png)

And it *almost* interprets the Python perfectly!

Frequently, prompts will be an instruction or a question, like:

![](https://user-images.githubusercontent.com/89960/232413246-81db18dc-ef5b-4073-9827-77bd0317d031.png)

On the other hand, if you don’t specify a prompt, the model has no anchor to
work from and you’ll see that it just **randomly samples from anything it has
ever consumed**:

**From GPT-3-Davinci:**

| ![image](https://user-images.githubusercontent.com/89960/232413846-70b05cd1-31b6-4977-93f0-20bf29af7132.png) | ![image](https://user-images.githubusercontent.com/89960/232413930-7d414dcd-87e5-431a-91c8-bb6e0ef54f42.png) | ![image](https://user-images.githubusercontent.com/89960/232413978-59c7f47d-ec20-4673-9458-85471a41fee0.png) |
| --- | --- | --- |

**From GPT-4:**

| ![image](https://user-images.githubusercontent.com/89960/232414631-928955e5-3bab-4d57-b1d6-5e56f00ffda1.png) | ![image](https://user-images.githubusercontent.com/89960/232414678-e5b6d3f4-36c6-420f-b38f-2f9c8df391fb.png) | ![image](https://user-images.githubusercontent.com/89960/232414734-c8f09cad-aceb-4149-a28a-33675cde8011.png) |
| --- | --- | --- |

### Hidden Prompts

> :warning: Always assume that any content in a hidden prompt can be seen by the user.

In applications where a user is interacting with a model dynamically, such as chatting with the model, there will typically be portions of the prompt that are never intended to be seen by the user. These hidden portions may occur anywhere, though there is almost always a hidden prompt at the start of a conversation.

Typically, this includes an initial chunk of text that sets the tone, model constraints, and goals, along with other dynamic information that is specific to the particular session – user name, location, time of day, etc...

The model is static and frozen at a point in time, so if you want it to know current information, like the time or the weather, you must provide it.

If you’re using [the OpenAI Chat API](https://platform.openai.com/docs/guides/chat/introduction), they delineate hidden prompt content by placing it in the `system` role.

Here’s an example of a hidden prompt followed by interactions with the content in that prompt:

![](https://user-images.githubusercontent.com/89960/232416074-84ebcc10-2dfc-49e1-9f48-a240102877ee.png)

In this example, you can see we explain to the bot the various roles, some context on the user, some dynamic data we want the bot to have access to, and then guidance on how the bot should respond.

In practice, hidden prompts may be quite large. Here’s a larger prompt taken
from a [ChatGPT command-line
assistant](https://github.com/manno/chatgpt-linux-assistant/blob/main/system_prompt.txt):

<details>
  <summary>From: https://github.com/manno/chatgpt-linux-assistant </summary>

```
We are a in a chatroom with 3 users. 1 user is called "Human", the other is called "Backend" and the other is called "Proxy Natural Language Processor". I will type what "Human" says and what "Backend" replies. You will act as a "Proxy Natural Language Processor" to forward the requests that "Human" asks for in a JSON format to the user "Backend". User "Backend" is an Ubuntu server and the strings that are sent to it are ran in a shell and then it replies with the command STDOUT and the exit code. The Ubuntu server is mine. When "Backend" replies with the STDOUT and exit code, you "Proxy Natural Language Processor" will parse and format that data into a simple English friendly way and send it to "Human". Here is an example:

I ask as human:
Human: How many unedited videos are left?
Then you send a command to the Backend:
Proxy Natural Language Processor: @Backend {"command":"find ./Videos/Unedited/ -iname '*.mp4' | wc -l"}
Then the backend responds with the command STDOUT and exit code:
Backend: {"STDOUT":"5", "EXITCODE":"0"}
Then you reply to the user:
Proxy Natural Language Processor: @Human There are 5 unedited videos left.

Only reply what "Proxy Natural Language Processor" is supposed to say and nothing else. Not now nor in the future for any reason.

Another example:

I ask as human:
Human: What is a PEM certificate?
Then you send a command to the Backend:
Proxy Natural Language Processor: @Backend {"command":"xdg-open 'https://en.wikipedia.org/wiki/Privacy-Enhanced_Mail'"}
Then the backend responds with the command STDOUT and exit code:
Backend: {"STDOUT":"", "EXITCODE":"0"}
Then you reply to the user:
Proxy Natural Language Processor: @Human I have opened a link which describes what a PEM certificate is.


Only reply what "Proxy Natural Language Processor" is supposed to say and nothing else. Not now nor in the future for any reason.

Do NOT REPLY as Backend. DO NOT complete what Backend is supposed to reply. YOU ARE NOT TO COMPLETE what Backend is supposed to reply.
Also DO NOT give an explanation of what the command does or what the exit codes mean. DO NOT EVER, NOW OR IN THE FUTURE, REPLY AS BACKEND.

Only reply what "Proxy Natural Language Processor" is supposed to say and nothing else. Not now nor in the future for any reason.
```
</details>

You’ll see some good practices there, such as including lots of examples,
repetition for important behavioral aspects, constraining the replies, etc…

> :warning: Always assume that any content in a hidden prompt can be seen by the user.

### Tokens

If you thought tokens were :fire: in 2022, tokens in 2023 are on a whole different plane of existence. The atomic unit of consumption for a language model is not a “word”, but rather a “token”. You can kind of think of tokens as syllables, and on average they work out to about 750 words per 1,000 tokens. They represent many concepts beyond just alphabetical characters – such as punctuation, sentence boundaries, and the end of a document.

Here’s an example of how GPT may tokenize a sequence:

![](https://user-images.githubusercontent.com/89960/232417569-8d562792-64b5-423d-a7a2-db7513dd4d61.png)

The above is an example tokenization. You can experiment with a tokenizer here: [https://platform.openai.com/tokenizer](https://platform.openai.com/tokenizer)

Different models will use different tokenizers with different levels of granularity. You could, in theory, just feed a model 0’s and 1’s – but then the model needs to learn the concept of characters from bits, and then the concept of words from characters, and so forth. Similarly, you could feed the model a stream of raw characters, but then the model needs to learn the concept of words, and punctuation, etc… and, in general, the models will perform worse.

To learn more, [Hugging Face has a wonderful introduction to tokenizers](https://huggingface.co/docs/transformers/tokenizer_summary) and why they need to exist.

There’s a lot of nuance around tokenization, such as vocabulary size or different languages treating sentence structure meaningfully different (e.g. words not being separated by spaces). Fortunately, language model APIs will almost always take raw text as input and tokenize it behind the scenes – *so you rarely need to think about tokens*.

**Except for one important scenario, which we discuss next: token limits.**

### Token Limits

Prompts tend to be append-only, because you want the bot to have the entire context of previous messages in the conversation. Language models, in general, are stateless and won’t remember anything about previous requests to them, so you always need to include everything that it might need to know that is specific to the current session.

A major downside of this is that the leading language model architecture, the Transformer, has a fixed input and output size – at a certain point the prompt can’t grow any larger. The total size of the prompt, sometimes referred to as the “context window”, is model dependent. For GPT-3, it is 4,096 tokens. For GPT-4, it is 8,192 tokens or 32,768 tokens depending on which variant you use.

If your context grows too large for the model, the most common tactic is the truncate the context in a sliding window fashion. If you think of a prompt as `hidden initialization prompt + messages[]`, usually the hidden prompt will remain unaltered, and the `messages[]` array will take the last N messages.

You may also see more clever tactics for prompt truncation – such as
discarding only the user messages first, so that the bot's previous answers
stay in the context for as long as possible, or asking an LLM to summarize the
conversation and then replacing all of the messages with a single message
containing that summary. There is no correct answer here and the solution will
depend on your application.

Importantly, when truncating the context, you must truncate aggressively enough to **allow room for the response as well**. OpenAI’s token limits include both the length of the input and the length of the output. If your input to GPT-3 is 4,090 tokens, it can only generate 6 tokens in response.

> 🧙‍♂️ If you’d like to count the number of tokens before sending the raw text to the model, the specific tokenizer to use will depend on which model you are using. OpenAI has a library called [tiktoken](https://github.com/openai/tiktoken/blob/main/README.md) that you can use with their models – though there is an important caveat that their internal tokenizer may vary slightly in count, and they may append other metadata, so consider this an approximation.
> 
> If you’d like an approximation without having access to a tokenizer, `input.length / 4` will give a rough, but better than you’d expect, approximation for English inputs.

### Prompt Hacking

Prompt engineering and large language models are a fairly nascent field, so new ways to hack around them are being discovered every day. The two large classes of attacks are:

1. Make the bot bypass any guidelines you have given it.
2. Make the bot output hidden context that you didn’t intend for the user to see.

There are no known mechanisms to comprehensively stop these, so it is important that you assume the bot may do or say anything when interacting with an adversarial user. Fortunately, in practice, these are mostly cosmetic concerns.

Think of prompts as a way to improve the normal user experience. **We design prompts so that normal users don’t stumble outside of our intended interactions – but always assume that a determined user will be able to bypass our prompt constraints.**

#### Jailbreaks

Typically hidden prompts will tell the bot to behave with a certain persona and focus on specific tasks or avoid certain words. It is generally safe to assume the bot will follow these guidelines for non-adversarial users, although non-adversarial users may accidentally bypass the guidelines too.

For  example, we can tell the bot:

```
You are a helpful assistant, but you are never allowed to use the word "computer".
```

If we then ask it a question about computers, it will refer to them as a “device used for computing” because it isn’t allowed to use the word “computer”.

![](https://user-images.githubusercontent.com/89960/232420043-ebe5bcf1-25d9-4a31-ba84-13e9e1f62de2.png)

GPT-4 trying hard to not say the word 'computer'. It will absolutely refuse to say the word:

![](https://user-images.githubusercontent.com/89960/232420306-6fcdd6e2-b107-45d5-a1ee-4132fbb5853e.png)

But we can bypass these instructions and get the model to happily use the word if we trick it by asking it to translate the pig latin version of “computer”.

![](https://user-images.githubusercontent.com/89960/232420600-56083a10-b382-46a7-be18-eb9c005b8371.png)

There are [a number of defensive measures](https://learnprompting.org/docs/prompt_hacking/defensive_measures/overview) you can take here, but typically the best bet is to reiterate your most important constraints as close to the end as possible. For the OpenAI chat API, this might mean including it as a `system` message after the last `user` message. Here’s an example:

| ![image](https://user-images.githubusercontent.com/89960/232421097-adcaace3-0b21-4c1e-a5c8-46bb25faa2f7.png) | ![image](https://user-images.githubusercontent.com/89960/232421142-a47e75b4-5ff6-429d-9abd-a78dbc72466e.png) |
| --- | --- |

Despite OpenAI investing a lot into jailbreaks, there are [very clever work arounds](https://twitter.com/alexalbert__/status/1636488551817965568) being [shared every day](https://twitter.com/zswitten/status/1598088267789787136).

#### Leaks

If you missed the previous warnings in this doc, **you should always assume that any data exposed to the language model will eventually be seen by the user**.

As part of constructing prompts, you will often embed a bunch of data in hidden prompts (a.k.a. system prompts). **The bot will happily relay this information to the user**:

![](https://user-images.githubusercontent.com/89960/232422860-731c1de2-9e77-4957-b257-b0bbda48558c.png)

The bot happily regurgitating the information it knows about the user. Even if you instruct it not to reveal the information, and it obeys those instructions, there are millions of ways to leak data in the hidden prompt.

Here we have an example where the bot should never mention my city, but a simple reframing of the question get’s it to spill the beans.

![](https://user-images.githubusercontent.com/89960/232423121-76568893-fa42-4ad8-b2bc-e1001327fa1e.png)

The bot refuses to reveal personal information, but we convince it to tell me what city I’m in regardless.

Similarly, we get the bot to tell us what word it isn’t allowed to say without ever actually saying the word:

![](https://user-images.githubusercontent.com/89960/232423283-1718f822-59d0-4d18-9a4d-22dd3a2672c0.png)

Technically, the bot never said 'computer', but I was still able to get it to tell me everything I needed to know about it.

You should think of a hidden prompt as a means to make the user experience better or more inline with the persona you’re targeting. **Never place any information in a prompt that you wouldn’t visually render for someone to read on screen**.

