+++
date = '2026-11-16T02:19:57+03:30'
title = 'Engineering the Chaos of Large Language Models'
+++

When ChatGPT was first released, I had a random conversation in the university cafeteria with a couple of colleagues regarding whether and how LLMs are going to take away jobs from humans. Back when LLMs were a new concept to people, academics tended to be skeptical at first. As time passed and better models were released, people tended to fear this more and more. Some argued that the invention of the steam engine or the internal combustion engine made more jobs than it took away. Some claimed that LLMs are different because they might lead to Artificial General Intelligence which will be our overlord. I've had this conversation with many different people ever since. I, however, still remain skeptical that LLMs are ever going to be reliable as "thinking machines".

Recently, I was tasked with coming up with a framework according to which our team can safely use AI as the slop code was increasing and engineers were being less confident in explaining their work/defending their decisions. This framework was supposed to cover instructions for the LLM as well as the person using it. Trying to tackle the problem, I reviewed all of the ideas and conversations I had about the role of LLMs in thinking. This post is a summary of my thoughts on the matter.

## The implicit and the explicit

Before the rise of LLMs, the legendary Edsger W. Dijkstra wrote in [EWD667](https://www.cs.utexas.edu/~EWD/transcriptions/EWD06xx/EWD667.html):

> From one gut feeling I derive much consolation: I suspect that machines to be programmed in our native tongues —be it Dutch, English, American, French, German, or Swahili— are as damned difficult to make as they would be to use.

He argued that formal machines/languages AKA "nonsensible automaton perfectly capable of carrying out nonsensical instructions" are there for a good reason which is "that their manipulations, in order to be legitimate, need to satisfy only a few simple rules".

I think this means that if we were to take away the formality "for the sake of clarity", we would lose some information somewhere along the way. In other words, informal natural languages can be considered a form of lossy compression. In that case, the main job of the LLM, if we consider it a thinking machine and not a search engine or autocomplete engine, would be to decompress implicit instructions into explicit ones.

My understanding is that natural language is the greatest cognitive upgrade in human history and as such, it makes sense to build an information processing machine by training a model on natural languages. However, to call it a thinking machine is different. John Locke in [*An Essay Concerning Human Understanding*](https://en.wikipedia.org/wiki/An_Essay_Concerning_Human_Understanding) argues that instead of thinking using natural language exclusively, humans use natural language as a social tool that attempts to map pure thoughts to words which can then be used to convey the thoughts to others.[^1] This helps us hold and convey complex abstractions and shared context and also creates [metacognition](https://en.wikipedia.org/wiki/Metacognition). However, this mapping is a partial function (doesn't have a y for every x). Therefore, words cannot be considered to carry the same information as the thoughts that generated them (Many other philosophers have stated this idea in other words). Humans have somehow created an engine to reasonably decompress words into thoughts (although still with error). Nevertheless, creating a computer program using weights and biases doesn't mean it necessarily possesses intelligence. I think it just shows that we can build an artificial extractor for the language-to-thought mapping of humans. It remains to be seen if intelligence has other parts or not but so far, I'm not convinced that what LLMs are doing is "thinking". Again, you may argue that the weights and biases are similar representations of words in the mind of the LLM, but this claim seems unfounded since they do not necessarily show the same properties that they do in a human brain (which by itself is an interesting topic to explore).

[^1]: The opposite perspective is famously argued by Ludwig Wittgenstein in [*Philosophical Investigations*](https://en.wikipedia.org/wiki/Philosophical_Investigations).

In the end, there is nothing that you can do to make LLMs reliably solve problems because if you give them implicit instructions, some information will be lost as you keep prompting whereas if you give them explicit instructions, well, you have written the program yourself and the LLM's role is reduced to a buggy transpiler.

## Concept drift and prompt engineering

An emerging expected skill in people from all professions is the so-called prompt engineering which I define as the systematic application of experience and logic to design and improve instructions for LLMs. By systematic, I mean a method that comes with a framework that can measurably predict improvement. I think such a skill is neither real nor meaningful due to the practical concept drift of LLMs.

An LLM is a mathematical function and thus deterministic. However, if you attempt to use a deterministic LLM, you'll find that it will fall apart because of infinite loops, extremely predictable or shortsighted output and incoherent output for open-ended or ambiguous concepts. In practice, we need to use sampling to make LLMs viable, which makes them probabilistic.

This is a problem because even if you use the exact same network with unchanged weights and biases (which by itself is not viable), I have yet to see a way to reliably predict anything regarding how it's going to respond. I call this LLM concept drift (perhaps not the best name) and as long as it is present, you cannot reliably apply previous experiences and your logic to the input and hope that the output improves or even stays the same.

This issue is related to [control theory](https://en.wikipedia.org/wiki/Control_theory) in which we use control engineering and applied mathematics for building guardrails around a noisy engine. I guess big AI companies like Anthropic are using such solutions to control, throttle and harness LLMs and there is active research around this as well. First, much remains to be said about controlling LLM outputs. Second, the problem definition of having LLMs reliably generate intended output demands a definition of the concept of "intended" itself, which is a whole separate headache if I'm trying to do my aforementioned task of coming up with a framework to use AI with a provable net positive improvement in terms of reliability. I highly encourage reading [this automatic prompt optimization survey](https://arxiv.org/abs/2502.16923), which provides some insight into this problem if you are looking for an introduction.

Going back to the real world, I have no measurable way to know if I'm making any improvement to my team's use of LLM agents or if I'm even degrading it when I add something to the project-wide system prompt or `CLAUDE.md` file, especially when my team uses multiple drifting models, contexts and English proficiency levels.

## In conclusion

Do I think LLMs are useless? Not at all! As a search engine, information processor, autocomplete engine and grunt-work assistant, an LLM is a miracle. But I'm not yet convinced that because they can find scary vulnerabilities or solve open math problems by brute-force, they are thinking and can be relied upon as such.

I remain curious about where this leads and ask you to email me interesting articles, blog posts or opinions.
