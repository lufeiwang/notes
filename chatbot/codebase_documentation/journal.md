
The goal of this project was to get familiar with the flow of setting up a chatbot. The problem statement was: Given a local codebase, create
a chatbot that can answer questions about the codebase.

The settings were as follows:

- Locally hosted ollama model (mistral)
- Code base that I'm intimately familiar with

Steps:

- Install ollama cli and start localhost serving endpoint
- Given a code base path, traverse the code base to build a dependency graph
- Starting from the leaf nodes, load the entire file content, as well as the content of the dependency files, and send to local LLM to generate markdown documentation
- Save LLM response in local file
- Build embeddings from the generated documentation and store in local ChromaDB
- Have chatbot answer questions
- Build tests to assess performance

# 2025-08-07

First problem - running out of context window => Noticed this problem when manually looking at the output responses. Dependency modules' context was
passed in last in the prompt, and i noticed that the response of the prompt pretty much just looked like that verbatim. Immediately I came to the realization
that I had exceeded the context window, because early tokens get truncated when that happens.

-> Dependency files are too large, exceeding mistral's context window => instead of passing the code content of depdency modules, used the generated markdown
-> still too large => generated summary versions of the markdown and used that
-> had some large code files that were too large => chunked by class and file, and consolidated to about 16k characters chunks

Second problem - LLM could not distinguish the context i provided about dependency modules, and pretty much treated everything as from the code file.
I adjusted the prompt multiple times to emphasize not to include documentation about the dependency modules, but that was not respected, and actually got worse
from time to time.

This was where I gave up and forgone passing in the dependency modules, due to local hosted LLM constraint. If I had more time, these are what I would explore:

- put another LLM to police the output of the first documentor LLM, to strip out irrelevant documentation stemming from the dependency files
- try more advanced models like gemini/gpt




