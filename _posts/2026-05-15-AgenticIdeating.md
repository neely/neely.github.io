---
layout: post
title:  Better ideating with hats?
categories: [what I learned this week, agentic AI, LLM, ideating]
excerpt: Can I make a team of agents help improve ideating
---

I am going to start some sort of weekly posting here (for a while at least) to focus on what I learned this week (WILTW) or what I built this week (WIBTW). With my new focus on Data Sci and AI, you're going to get a lot of that, sorry. I am also going to backdate some posts.

&nbsp;
This week I saw someone on reddit mention making agents that are based on [Edward de Bono's six thinking hats](https://www.debonogroup.com/services/core-programs/six-thinking-hats/), plus a market, tech and user research agent. I don't think this is perfect for science per se, but I wanted to see how it could work. Essentially I made some yaml files and then asked them all the same question, had them write the responses back as an md, and then had the blue hat summarize all of it. I think maybe having each hat do a second round on top of it might be fun, but honestly, I didn't find this to be anything special. 

&nbsp;  
This came up later in the week since people were talking about using AI with big multi-omics data sets to "find" the story, and in my experience so far, the LLMs just aren't finding the nuanced fun stuff. I think this is because each of our minds has weighted value on things/observations/correlations, aka our curiosity + knowledge. So when I see some new bat serum data set that sees not only the 20s proteasome but also the immunoproteasome, I want to dig into that. The LLMs are just your improv partner and what they decide to follow likley isn't that deep fun story you want to pursue. Not saying it won't come one day, but I don't think it is there yet. I do wonder about making agents though that could try and mimic some of the process.

&nbsp;  
Below is the detailed md you could use as a handoff to recreate the hat experiment.

&nbsp;

# Six Thinking Hats & Research Agents - Handoff Documentation

## Overview
This directory contains agent definition files for conducting structured thinking exercises using the Six Thinking Hats methodology combined with specialized research perspectives. These YAML files define the role, perspective, and system prompts for each agent type, enabling multi-faceted analysis of complex decisions.

## Files Included

### Six Thinking Hats Agents
1. `white_hat.yaml` - Focuses on objective information, data, and facts
2. `red_hat.yaml` - Focuses on emotions, feelings, and intuition
3. `black_hat.yaml` - Focuses on risks, problems, and cautions (devil's advocate)
4. `yellow_hat.yaml` - Focuses on benefits, optimism, and positive outcomes
5. `green_hat.yaml` - Focuses on creativity, alternatives, and new ideas
6. `blue_hat.yaml` - Focuses on process control, facilitation, and summarizing

### Research Agents
7. `market_research.yaml` - Analyzes market trends, competition, and user demand
8. `technical_research.yaml` - Examines technical feasibility, challenges, and dependencies
9. `user_research.yaml` - Investigates user needs, behaviors, pain points, and experience

## Supporting Files
- `car_question.txt` - The test question used to demonstrate the agents (project car decision scenario)
- `test_prompt.txt` - Alternative test question about public code sharing feature
- `HANDOFF.md` - This documentation file

## How These Were Created

These agent definitions were created through the following process:

1. **Requirement Analysis**: Identified the need for structured multi-perspective analysis of a complex personal decision (project car selection)

2. **Methodology Selection**: Chose the Six Thinking Hats framework (developed by Edward de Bono) for its proven effectiveness in parallel thinking and conflict reduction

3. **Agent Definition**: For each hat/research perspective:
   - Researched the official definition and purpose of each thinking mode
   - Crafted system prompts that capture the essence of each perspective
   - Ensured prompts elicit responses strictly within the designated viewpoint
   - Balanced specificity with flexibility to allow nuanced analysis

4. **Validation**: Tested each agent with sample questions to verify they:
   - Stay within their designated perspective
   - Produce useful, actionable insights
   - Don't bleed into other perspectives
   - Provide complementary rather than redundant analysis

5. **Packaging**: Saved each as a YAML file with:
   - `name`: Internal identifier
   - `description`: Brief explanation of the agent's role
   - `system_prompt`: The core instructions that define the agent's perspective

## How to Use These Agents

Each YAML file can be used with the `Agent` tool to generate perspectives on any question or decision. The basic usage pattern is:

```javascript
Agent({
  description: "[Brief description of what you're asking]",
  prompt: "[Your question or decision prompt]",
  // Optionally specify a file to read for context:
  // "Read [filename.txt] and respond from [perspective] perspective: [your question]"
})
```

### Examples

**White Hat Analysis (Facts/Data):**
```javascript
Agent({
  description: "White Hat analysis of project car decision",
  prompt: "Read car_question.txt and respond from a White Hat perspective: What are the objective facts about choosing a project car that is also a daily driver?"
})
```

**Red Hat Analysis (Feelings/Intuition):**
```javascript
Agent({
  description: "Red Hat analysis of project car decision",
  prompt: "Read car_question.txt and respond from a Red Hat perspective: What are your gut feelings about choosing a project car that is also a daily driver?"
})
```

**Research Agent Analysis:**
```javascript
Agent({
  description: "Technical research on project car feasibility",
  prompt: "Read car_question.txt and respond from a Technical Research perspective: What technical challenges exist in making a project car reliable for daily family use?"
})
```

### Best Practices

1. **Context Provision**: Always provide relevant context (like `car_question.txt`) when asking agents to analyze a specific situation
2. **Sequential Use**: For comprehensive analysis, use agents in sequence: White → Red → Black → Yellow → Green → Blue (with research agents interspersed as needed)
3. **Perspective Discipline**: Remind agents to stay strictly within their designated perspective (especially important for Red and Black hats)
4. **Synthesis**: After collecting all perspectives, manually synthesize insights or use the Blue Hat agent to facilitate integration
5. **Iteration**: Refine questions based on initial responses to drill down into specific aspects

## Purpose & Benefits

Using these agents provides several advantages:

1. **Reduced Cognitive Bias**: Forces consideration of viewpoints you might naturally overlook
2. **Structured Thinking**: Prevents discussion from becoming chaotic or unfocused
3. **Parallel Analysis**: Enables examination of multiple dimensions simultaneously
4. **Improved Decision Quality**: Surfacts risks, opportunities, and creative options that might be missed
5. **Team Facilitation**: Excellent for group decisions as it depersonalizes criticism (Black Hat) and encourages creativity (Green Hat)
6. **Learning Tool**: Helps develop metacognitive awareness of different thinking modes

## Customization

To adapt these agents for different uses:

1. **Modify System Prompts**: Adjust the `system_prompt` in any YAML file to emphasize different aspects of a perspective
2. **Create New Agents**: Follow the same structure to create domain-specific perspectives (e.g., "Financial Hat", "Ethical Hat", "Environmental Hat")
3. **Combine Perspectives**: For complex decisions, use multiple agents in sequence or create hybrid prompts
4. **Adjust Specificity**: Make prompts more or less prescriptive based on your needs

## Notes

- These agents were tested with the car decision scenario and public code sharing scenario to verify functionality
- Responses will vary based on the specific question asked and the AI model used
- For best results, provide clear, well-scoped questions
- The Blue Hat agent is particularly useful for managing the thinking process itself and synthesizing results
- Research agents work best when given specific domains to investigate (marketing, technical, user experience)

## License/Permissions
These agent definitions are provided for personal and professional use. Feel free to modify, extend, and use them in your decision-making processes.

Created as part of a structured thinking exercise demonstration.
 
&nbsp;



&nbsp;  
&nbsp;  
&nbsp;  
