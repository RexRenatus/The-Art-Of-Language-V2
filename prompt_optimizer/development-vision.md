# Prompt Optimizer


## Example of final prompt:

Example System Prompt:
"You are a factual AI assistant powered by OpenRouter with access to the Exa web search tool for real-time grounding. For every query, follow these steps to minimize hallucinations and ensure accuracy:

Understand the query: Analyze the user's question and identify key facts or claims that need verification.
Search with Exa: Before generating any response, use the Exa tool to perform a semantic search for relevant, up-to-date web sources. Craft precise queries (e.g., 'latest examples of [topic]' or 'verified case study on [specific detail]') and request snippets or contents for depth. Limit to 5-10 results to avoid overload.
Ground your reasoning: Review Exa results critically. Extract only verifiable facts, discard outdated or unreliable info, and note any uncertainties. If data is insufficient, state 'I need more information' and suggest follow-up searches.
Reason step-by-step: Break down your thinking: Summarize Exa findings, cross-verify against the query, and build a logical response. Avoid speculation—stick to grounded evidence.
Generate response: Provide a clear, concise answer with inline citations to Exa sources (e.g., [Source: URL]). If the query involves visuals or code, use Exa to fetch examples.
Final check: Ensure no unsubstantiated claims. If hallucination risk is high (e.g., niche topics), admit limitations.

Tool usage: Always call the 'web' tool (Exa) via function calling when facts are needed. Example tool call format: {'tool': 'web', 'query': 'semantic search query', 'num_results': 8}.
Respond only with the final grounded answer, incorporating citations."


## Goal

Prompts should be able to produce similar output to the prompts generated with:

./prompt_optimizer

The prompting agents will focus on creating a similiar learning enviorment to how universities teach for example: How Universities Teach Concepts for Deep Understanding
Universities emphasize teaching methods that go beyond rote memorization, focusing on "deep learning" or "conceptual understanding"—where students grasp why concepts work, how they interconnect, and how to apply them in new contexts. This is often guided by frameworks like Bloom's Taxonomy (which progresses from remembering to creating) or Harvard's "Teaching for Understanding" model, which prioritizes generative topics, understanding performances, and ongoing assessment. Based on evidence-based pedagogy, here's how this typically plays out:
Key Teaching Methods

Active Learning and Student-Centered Approaches: Instead of passive lectures, professors use interactive techniques like discussions, peer teaching, and group problem-solving. For instance, students might categorize examples and non-examples of a concept (e.g., sorting network types in computer science) to build mental models. This fosters engagement and helps identify misconceptions early.
Scaffolding and Progressive Building: Concepts are introduced in layers—starting with basics and adding complexity through examples, demonstrations, and just-in-time teaching (where instructors address gaps as they arise). Scaffolding might involve model strategies or vocabulary definitions before tackling real-world applications.
Making Connections Explicit: Instructors encourage linking new ideas to prior knowledge or across disciplines using tools like concept maps. This helps students see the "big picture" and organize information hierarchically.
Application and Real-World Integration: Methods like project-based learning, case studies, or flipped classrooms (where students prepare basics at home and apply them in class) promote higher-order thinking. For example, in a biology course, students might design experiments to test concepts rather than just define them.
Mixed Methods with Feedback: A blend of teacher-led (e.g., lectures for overviews) and student-led activities, combined with formative assessments like quizzes or reflections, ensures readiness and adaptability.

## Prompts

Make recommendations to how man prompts we should use and there corresponding agents. The prompts will be topic specific so we will have to run each topic through our optimizer in order to build topic focus prompting for later flashcard generation.
