# Google Prompting Essentials
## Start Writing Prompts like a Pro
Here's the basic prompting framework:
### Task
Present your task with clear language.Include:
1. A persona (role) for gen AI or the person who's going to read the output.
2. A format for gen AI's response. 
### Context
Declare the context by providing detailed information
### References
Provide one or more examples as references or refer to actual URLs, images, etc. 
### Evaluate
Evaluate gen AI's response
### Iterate
Iterate the entire process for improvement. To do so,
- Revise prompt
- Seperate prompt into smaller parts
- Re-phrase prompt
- Add constraints if necessary.
## Design Prompts for Everyday Work Tasks
### Tone and style
Within the Task specify the tone and style (friendly, academic, sarcastic, simple, etc) you want gen AI to use. Add constraints if needed. 
### Google App and AI
Gen AI can generate tables and saves them in spreadsheetσ based on the instructions and *constraints* we set. All Google app are integrated with Google Gemini which can be used directly in Google Docs and Sheets.
### Summarize documents
- Use Google AI Studio
- Break a long text up and summarize chunks of it. Then, summarize the summaries!
- Use Chain of density technique: start with long summaries and shorten them at each iteration. That way you check the information at every step and avoid hallucinations. 
## Speed up Data Analysis and Presentation Building
### Image generation
Image generation prompts shoulg specify:
- Size
- Color
- Position
- Aesthetic
### Presentations
- Use image generation prompt framework alongside with basic prompting framework.
- Ask gen AI to give you speaker notes appropriate to your audience. 
- Upload a recording to AI for inspection and evaluation. 
### AI Settings
- **Temperature**: The higher the more creative (ideal for artistic tasks or brainstorming). The lower the more predictable and consistent output (perfect for accuracy, reasoning, summarizing).
- **Top-k**: sets a fixed number of words the tool can choose from. For example, setting top-k to 10 instructs the tool to choose a word only from the top 10 most likely options on its list.
- **Top-p**: sets a dynamic number of words the tool can choose from. It instructs the tool to automatically shrink or grow its list of options based on how predictable the next word is. For example, setting a high top-p (e.g., $p=0.9$) acts as a quality filter, instructing the tool to consider only the most probable choices.

Temperature is a creativity dial, while top-k and top-p help you keep that creativity focus.

How to set? 

**Recipe 1: Focused Analysis**

- Task: Generate answers that are factual, consistent, and predictable.
- Use Cases: Summarizing reports, extracting data insights, writing technical documentation, or generating code.
- Settings:
    - Temperature: Low (e.g., 0.1 to 0.4)
    - Top-k or Top-p: Leave at default, as the low temperature already makes the output very focused.

**Recipe 2: Creative Brainstorming**

- Task: Generate surprising, diverse, or innovative output.
- Use Cases: Brainstorming new ideas, drafting marketing materials, or writing fictional stories.
- Settings:
    - Temperature: High (e.g., 0.8 to 1.0)
    - Top-k or Top-p: A high value (like 0.95 for top-p) can be helpful here to give the AI tool creative freedom while still filtering out irrelevant options.
## Use AI as a Creative or Expert Partner
### Prompt chaining
Prompt chaining refers to sequential prompts where a prompt depends on the output of a previous prompt. It's not just iteration. Instead it adds context and new tasks at every step. With this method, one starts from general aspects (like an outline) and then, move on to specific parts of it. 

Some types of prompt chaining are:

1. **Chain of thought**: Ask gen AI to explain its reasoning by saying "explain your reasoning", or "go step by step". That could be for the entire result, or for a part of it. 

2. **Tree of thought**: Ask gen AI to explore multiple paths at once. For example, ask gen AI to create three images of a theme (instead of one), and then choose one of them and explore it further. 
### Meta-prompting
Use gen AI to produce the most efficient prompt for a purpose. 

- **Prompt generation strategies**: use for example an image or PDF or text instructions as a reference and ask gen AI to create a prompt for you.
- **Prompt refinement strategies**: use already generated prompts as input and ask for improvement.
### AI agents
AI agents simulate conversations and are capable of roleplaying.

To create an AI agent, follow the prompting framework focusing on Persona and Context. Set some rules (constraints) and provide a stop phrase. Add references if needed. After finishing the conversation, ask the agent to provide takeaways. 

**Examples**
- Simulation agents are AI personalities with a specific role. They used to start a conversation with a user, collect information and stop when the user say a *magic* word.
- Expert agents are AI personalities designed to answer your questions in any area of expertise. Create them directly with a prompt specifying the role, the type of responses, the criticizing model and the stopping word. 

Agents can be embedded in Gems. 

### Prompt library
Keep a library of successful prompts for any future use. 




 
