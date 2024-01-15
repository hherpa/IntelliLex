# IntelliLex

## Contents
1. [Autogen-conveyor](#Autogen_conveyor)
2. [ClI_Agent class of agents](#ClI_Agent)
3. [Two implementations of the CLI_Agent class](#ClI_Agent_2)
4. [Autogen agent typing](#Agent_type)
5. [Boost agent](#boost_agent)
6. [Exchange universal autogen agents](#trade_agent)
7. [Conveyer2Autogen](#Conveyer2Autogen)
8. [Conveyer2Autogen pipeline](#Conveyer2Autogen_pipeline)
9. [Conveyer2Autogen piplane disassembly](#R_Conveyer2Autogen)
10. [link model of agent communication](#link_model)
11. [Conveyer2Conveyer](#Conveyer2Conveyer)
12. [Work results](#work_result)

<a name="Autogen_conveyor"></a>
## Autogen-Conveyer
![Group 74](https://github.com/hherpa/IntelliLex-RU/assets/146547175/e64f6c03-6099-4b22-8234-a290cd17fb9c)

Autogen-Conveyer is a fully automated approach to create a multi-tiered system for performing complex tasks of a huge range of orientation, the components of which are departments of agents-LLM (autogen group chats) focused on specific tasks.

Also Autogen-Conveyer is not separable from the functional approach: "1 department - 1 function" and "1 agent - 1 sub-function". Rough formatting of information (up to templating the result of each sub-function) also contributes to the accuracy of this approach. This is what gives us the opportunity to use Autogen-Conveyer as multilevel neural network systems with high accuracy and a limitless range of tasks.

The access of sub-functions to the integrated tools is amazing. For example, with Autogen-Conveyer it is easy to implement a system where one department has access to LLAVA and with the help of sub-functions analyzes the input image/video, and then passes the obtained information to the OSINT processing department where it can search for all possible background information from simple metadata to the coordinates of the area depicted in the media and all this only on public sources (only for training/familiarization purposes). This is just an example with two departments, and there can be an infinite number of them.

We decided to use Autogen-Conveyer to automatically generate autogen (department) group chats with properly prescribed promts, tools and function calls and all this only on a simple user request. Our main problem in implementing Autogen-Conveyer is getting (parsing) the results of departments (group chats). We present an implementation to solve this problem as output_parser in the LCEL langchain. But we also have our own idea. We can use a special agent that will output a department result together with the @final tag if all chat participants agree that this is the final result. If there is more than one message with this tag, we can call this agent repeatedly requesting the final result. This solution can reduce parsing a huge group chat history to a primitive search for a message with the word "@final" in it

<a name="ClI_Agent"></a>
## New agent class ClI_Agent
CLI_Agent is a class of agents to continuously analyze autogen group chat and perform certain functions when conditions are triggered. These functions are divided into two types: `&command_name` and `!edit_memory`.
The memory of an agent of the CLI_Agent class may contain information that should be considered true for the chat in which it participates. An agent of this class can use a command in the format ```!edit_memory <old value><new value> @Agent_name``` to invoke the command handler to change information in the memory of a particular agent that is different from what is in its memory. In this way, the CLI_Agent can keep group chat information accurate and up to date.

![image](https://github.com/hherpa/Conveyer2Autogen-RU/assets/146547175/02d4e87b-cd06-475e-a3b4-46668c328320)

While the `!edit_memory` command is a pre-prepared command, `&command_name` are custom commands. For example, in Conveyer2Autogen there is a department for creating a list of roles. We tested this department and we always had to correct its work by writing on feedback: "display the list completely", "this role is redundant", "there are too few roles in the list - add more". This problem was solved by Regulatory agent of CLI_Agent class using custom commands:
* `&cleared_out` - indicates that the list is written incorrectly and does not concern the user's task
* `&add_more` - indicates that the list is too short and should be enlarged.
* `&only_list` - indicates that the agent has output something else besides the list that should have been corrected.

The user can also describe his triggering conditions to the CLI_Agent class agent and get the right results just as well!

<a name="ClI_Agent_2"></a>
## Two implementations of the CLI_Agent class
When using autogen, it is obvious that the correct approach is "one agent - one function", and therefore there are two implementations of the CLI_Agent class.

**1. The handler is initially located in the AssistantAgent:**

![9](https://github.com/hherpa/Conveyer2Autogen-RU/assets/146547175/af71986e-b2ce-4c0f-a3af-0566d2030ffe)

**2. The handler is a separate class of agents:**

![11](https://github.com/hherpa/Conveyer2Autogen-RU/assets/146547175/644bc01b-d94e-400f-8645-d9876cff31e3)

<a name="Agent_type"></a>
## Typing of autogen agents
Based on our experience with autogen - agents are of two types:

![AgentTypesRu](https://github.com/hherpa/Conveyer2Autogen-RU/assets/146547175/c1a509a0-1b17-42c9-87bd-ce2a809ab122)

<a name="boost_agent"></a>
## Accuracy Boost Agent
Studies on the effect of certain manipulations/motivations in promts on LLM response quality have been appearing more and more frequently. We decided to collect all the promts from these studies (from "My career depends on it..." to "Now May...") and create an agent that will adjust to each role in the chat and before she writes something improve her result with a similar "Frankenstein". We called this agent "Motivator" and we use it to this day in autogen. But this agent definitely requires improvement as we all understand that even one word in the promt can spoil the result, and therefore we need to thoroughly analyze the combinations of promts that we took from the research, as well as their usefulness, because for example the template "...I will pay you $200 for doing it right" had to be removed as it only worsens the result and the author of this template made a mistake in his work about what he wrote in social networks.

<a name="trade_agent"></a>
### Exchange autogen universal agents
Since even we managed to make a universal agent, which can increase the accuracy of autogen group chat, why don't autogen itself implement a platform where users can propose their own universal agents, and the best results will be posted officially either on the platform itself or on the github page.

<a name="Conveyer2Autogen"></a>
# Conveyer2Autogen
![image](https://github.com/hherpa/Conveyer2Autogen-RU/assets/146547175/6b8acf93-9bf4-42d0-97ed-051dd25d541a)

Conveyer2Autogen - allows the user to get a whole group chat autogen with properly prescribed and tested promts, integrated utilities into agents, and function calls. Also Conveyer2Autogen because of its simple "prompt --> answer and nothing extra" approach has its own TUI. We think this is a very important feature as it will allow more people to get familiar with autogen and start using it without writing code

<a name="Conveyer2Autogen_pipeline"></a>
## **Conveyer2Autogen pipeline**

<details>
<summary> <i>click here to view the full piplane
</i> </summary>
  
![ALL](https://github.com/hherpa/Conveyer2Autogen-RU/assets/146547175/2e1524cf-6a9d-4030-843f-f07a65990e1f)

</details>

* This pipeline is not accurate as it should be at least twice as large as we have to use the functional approach: "one agent - one function".

<a name="R_Conveyer2Autogen"></a>
### parsing the Conveyer2Autogen Pipeline ###
### 1. Create a list of roles/agents
![image](https://github.com/hherpa/Conveyer2Autogen-RU/assets/146547175/0b533c40-1b21-4ca5-9981-5493ba17add4)

Here everything is intuitive on the input of the user promt, and already on the output the list of roles. Here you can see how Regulatory Agent (of CLI_Agent class) and Motivator will be built into autogen group chats.

* **Example of implementation in TUI:**
  
![image](https://github.com/hherpa/Conveyer2Autogen-RU/assets/146547175/6d83f249-7e80-4597-8788-a2cee91573d7) ![image](https://github.com/hherpa/Conveyer2Autogen-RU/assets/146547175/9590f64e-1054-42cb-964b-ad41683be8b1)

### 2. Select a class for each agent in the list
![image](https://github.com/hherpa/Conveyer2Autogen-RU/assets/146547175/cd8f60a1-145a-4def-979a-ba6fc8d8659f)

Next, the generated list of agent names goes to the class selection department and although it may seem like a simple process, as almost all classes will be the same, it is not. We also consider adding RetrieveUserProxyAgent and RetrieveAssistantAgent to the selection list with the possibility of automatic filling of RAG using LLM_web_search, SerpAPI or other similar tools.
* **Example of implementation in TUI:**
  
![image](https://github.com/hherpa/Conveyer2Autogen-RU/assets/146547175/746c212c-925c-4744-b58e-462a2c6052c8)

### 2.1 Build autogen configuration

![image](https://github.com/hherpa/Conveyer2Autogen-RU/assets/146547175/206eb49a-e71e-4c2a-ad06-de1193d4d57f)

This item should be at the very beginning along with the promt input from the user, but due to technical issues it is here (obviously without access to llm item 1 and 2 is not possible...). In this point we don't use the autogen group chat, we just request the API key and insert it into llm_config. It is important to note that the API key does not have to be from ChatGPT, it can also be from textgen-webui, and therefore the user can use open-source models as well. Also a variant of building multi-model configurations has already been developed.

### 3. Generation of role-prompt for each agent
![image](https://github.com/hherpa/Conveyer2Autogen-RU/assets/146547175/d722cf75-9b04-4789-8a32-a883d57bd22e)
```
As you can see this department receives the data already in the format:
  ________________________________________ 
 | 1. AgentName | llm_config | Class name |
 | N. AgentName | llm_config | Class name |
 |________________________________________|
```

**Very important nuances that could not be displayed in the pipeline:**
1. Prompt engineer uses certain rules for writing prompts. Here are some of those rules:
<details>
<summary> <i>click here to view </i> </summary> </summary>
  
![image](https://github.com/hherpa/Conveyer2Autogen-RU/assets/146547175/9aecabf4-d0d4-44d6-adb2-6da2b8c0e826)

</details>

2. Prompt engineer fills in certain universal templates. For example:

```
Person designation: <insert agent's name here>, ``you are found in group chat by'' <write here the task of the group chat the agent is in``.
...etc.
```
  These templates can be huge and we usually call them: promt-structures and they improve accuracy quite a lot as they limit the LLM's response within strict bounds.

3- We want to do complex promt analysis using [promptfoo](https://github.com/promptfoo/promptfoo), [prompt-testing-framework](https://github.com/RafalWilinski/prompt-testing-framework), [prompttools](https://github.com/hegelai/prompttools), [promptimize](https://github.com/preset-io/promptimize) for a huge jump in accuracy.

### 4. Adding an insturment
![image](https://github.com/hherpa/Conveyer2Autogen-RU/assets/146547175/4c7fbf7a-6c77-47e4-8684-d105907221ea)
```
As you can see, this section receives the data already in format:
  _________________________________________________
 | 1. AgentName | llm_config | Class name | prompt |
 | N. AgentName | llm_config | Class name | prompt |
 |_________________________________________________|
```

**Very important nuances that could not be displayed in the pipeline:**
1. If the required tool was not found in RAG - user can give us its code in txt file and we will integrate it correctly
2. Tool search and integration is not done for every agent - we shouldn't go overboard with them

### 5. Adding function call

**This department consists of several sub-departments:**
1. analyzing whether the agent needs a function call
2. finding a function call and adding it
3. writing the function call and adding it.
```
The data to this sub-department is received in the format:
  ___________________________________________________________________
 | 1. AgentName | llm_config | Class name | prompt | tool (optional) |
 | N. AgentName | llm_config | Class name | prompt | tool (optional) |
 |___________________________________________________________________|
```

![image](https://github.com/hherpa/Conveyer2Autogen-RU/assets/146547175/93d4b6c3-1cf9-44ca-be4e-f3f99b3f3be1)

Everything is shown rather primitively and without half of the auxiliary agents as we think it is enough for simple familiarization.

![image](https://github.com/hherpa/Conveyer2Autogen-RU/assets/146547175/f2f18738-2c5e-48d8-a85c-88619fd48f34)

Just here we see that subdivisions can be used when certain conditions are triggered. Any department is not ready for any data, but if there are only two data types, why not make two sub-departments to work with each.

![image](https://github.com/hherpa/Conveyer2Autogen-RU/assets/146547175/640e09a1-8f43-4182-844c-e3e1a7dbc7cb)

### 6. Assembly
```
is the final stage of any pipeline, and this is where the data arrives in format:
  ______________________________________________________________________________________________
 | 1. AgentName | llm_config | Class name | prompt | tool (optional) | function call (optional) |
 | N. AgentName | llm_config | Class name | prompt | tool (optional) | function call (optional) |
 |______________________________________________________________________________________________|
```
![image](https://github.com/hherpa/Conveyer2Autogen-RU/assets/146547175/8cf367e9-c765-46ce-a0aa-643f5dc9e3e2)

This is where we start a chat room for the user where they can get their perfect answer

<a name="link_model"></a>
## Link model of agent communication

We were very much hampered by the fact that only one agent writes to one agent (while keeping a certain queue) - this slowed down the group chat process a lot, so in larger departments we added a "reference model" to each agent. What is a reference model? Roughly speaking it's an abstraction for an agent on how to address multiple people in the same message using @ to tag each person by nickname. Just this small addition has increased the productivity and efficiency of chat by leaps and bounds. You can attach a lot of screenshots here, but everything works in the same way:
```
Role_Name (to chat_manager):

@Role_name_2 ....text...
@Role_name_3 ....text...
@Role_name_4 ....text...
```
This is a good alternative to orextrator, but you need to roll back pyautogen to version 0.1.14 for it to work.

<a name="Conveyer2Conveyer"></a> 
# Conveyer2Conveyer
While Conveyer2Autogen allows us to get just one department (group chat), by design Conveyer2Conveyer allows us to get a huge number of departments and link them for data transfer up to assembly.

For example, the user request is to make a full-fledged python game. By full-fledged I mean not only python script, but also for example soundtrack, good visual design, elaborate plot, etc. Obviously, a regular autogen group chat (just one department) is not able to cope with this task. A semi-automated Conveyer2Conveyer tool should be able to provide the user with n-number of ready-made departments and a built chain of their interrelationships up to assembly. In the example of a complete python game, Conveyer2Conveyer should offer the user the departments of code development, music (with the [link1](https://github.com/rushidarge/Music-Generator-Ai) or [link2](https://github.com/salu133445/musegan) tool), interface ([link3](https://github.com/microsoft/autogen/blob/main/notebook/agentchat_dalle_and_gpt4v.ipynb)), story, etc. That's only 4 departments (not counting the assembly department) realizing that Conveyer2Conveyer can offer 20 or 30 departments for user's request is amazing.

<a name="work_result"></a>
# Performance results
The implementation of Conveyer2Autogen and Conveyer2Conveyer may be a big step for autogen, and especially for autobuild, but only a small step for IntelliLex. For us, these two tools are just ways to automatically create pipelines/departments in huge complex neural network systems. Yes, it's a huge process optimization that can reduce entire months of work to just one evening. It is also an attraction of new users for autogen and in general popularization of this technology since you don't need to write code, and to get the desired result you just need to write your query and insert the API key. But LLMs are rapidly developing every day and what's the point if we don't use their power to its full potential? I would even say that we almost don't use them at all, since a hundred separate systems can be built on one mistral-7b of textgen-webui! Based on the laws of Nathan Mirvol from microsoft's report "ACM97: The Next 50 Years of Computing" we can safely say that by analogy the development of LLM is not effective without the development of the operational part of its use. We are improving only one leg, although our task is running and it obviously involves both legs. We have many more grandiose projects on pipeline/department technology in our plans, so I can safely say that this is a big step for autogen and a small step for IntelliLex.
