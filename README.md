# IntelliLex
## The autogen conveyor and its departments
For example, your task is to make a complete python game using only autogen. By full-fledged I mean not only python script, but also e.g. soundtrack, good visuals, etc.
Obviously, a regular autogen will not be able to cope with this. After all, if we make an agent for each task, the accuracy will still be minimal. But if we split this huge group chat into departments that can send data to the assembly - we can get the desired result.
Example:
```
1. code development department
  | coder
  | planer
  | critic
  | and other support roles
2. music development department
  | https://github.com/rushidarge/Music-Generator-Ai or https://github.com/salu133445/musegan
  | and other supporting roles
  | ...
3. interface development department
  | https://github.com/microsoft/autogen/blob/main/notebook/agentchat_dalle_and_gpt4v.ipynb
  | and other support roles
  | ...
4. assembly department
```
This is somewhat similar to the langchain LCEL, but here the main problem is parsing the group chat to find the result. But we have an idea to make an agent that will wait until all chat participants report that the problem is solved and there is a result, and then it will output it with @final, which will make parsing a primitive search for a message with the word "@final".

## Autogen2autogen
We must realize that the user treats the LLM as a black box, i.e. he is not interested in the processes inside, he just wants to get the right answer to his question. With autogen things are worse in this respect, because now the user has to know how to: write code, write role prompts for each agent, and know additional utilities that can be integrated into it. The user will simply not want to do all this and will be afraid of the complexity, and the one who already knows how to work with autogen just gets tired of performing these actions from time to time, and even more so when creating a really large group chat. But just imagine if all the possibilities of autogen with the right prompts and the right integrations, memories, function calls, and all of this just for one small request, were opened to a wide audience. This is exactly where we can use the autogen conveyor technology:
```
1. department for processing a brief user request to expand it into a large and detailed prompt 
2. department for writing a list of roles (chat participants)
3. department for processing each role
  | 3.1 sub-department for selecting the type of agent and its memory
  | 3.2 sub-department for writing the exact role-prompt
  | 3.3 sub-department of agent utility selection and integration
  | 3.4 sub-department of writing function calls when needed
4. build department
```
These are just the departments and sub-departments of autogen2autogen, but we also have a few new features in the agents themselves, so let's break them down in more detail using department №2 as an example.
```
2. department for writing a list of roles (chat participants):
  | Role List Engineer
  | Regulatory agent
  | Motivator
```
## CLI_Agents class
CLI_Agents is a class of agents that analyze the chat to find the fulfillment of any condition and if found, output a certain command to the chat. For example, in our department of writing a list of roles "Regulatory agent" was class CLI_Agents. What was this done for? While testing the "Role List Engineer" agent, we noticed that it always needed to be corrected with the same commands: "display the list completely", "this role is redundant", "there are too few roles in the list - add more". We made it so that "Regulatory agent" only analyzes the chat and when a certain condition is triggered it outputs the following commands for "Role List Engineer": `&cleared_out`, `&add_more`, `&only_list`. And already in the memory of the "Role List Engineer" agent we added a handler for the commands:
* ```&cleared_out``` - indicates that the list is written incorrectly and does not concern the user's task
* ```&add_more``` - indicates that the list is too short and should be enlarged.
* ```&only_list``` - indicates that the agent outputs something else besides the list that should have been corrected.
  
Thus, we began to receive the correct results from the agent "role list engineer". The user can simply describe their trigger conditions to the agent of the CLI_Agent class and get the correct results in the same way!

Also CLI_Agents class partially solves the problem with hallucinations in group chat. "Regularory agent" can wait for the condition of facts not matching the ones it has and use the command to replace the agent's memory fragment.
`&memory_edit <fragment to be replaced> <fragment to be replaced>`.
## Continuing the theme of the CLI_Agents class...
We were very much hampered by the fact that only one agent writes to one agent (while respecting a certain queue) - this slows down the group chat process very much, so in larger departments we added a "reference model" to each agent. What is a reference model? Roughly speaking it's an abstraction for an agent on how to address multiple people in a single message using @ to tag each person by nickname. Just this small addition has increased the productivity and efficiency of chat by leaps and bounds. You can attach a lot of screenshots here, but everything works in the same way:
```
Role_Name (to chat_manager):

@Role_name_2 ....text...
@Role_name_3 ....text...
@Role_name_4 ....text...
```
Thereby replacing huge paragraphs of tooltips with: `@agent_name &agent_command`.
### Universal agent to improve group chat accuracy.
There are also universal agents in autogen such as critic and planer, their role-prompts don't need to be edited and so they swing from one chat to another, but we want to introduce an agent that can universally improve the accuracy of any autogen chat - Motivator.
More and more often we started to see studies about the impact of manipulation and motivations in prompts on LLM response quality. We decided to collect all prompts from these studies (from "my career depends on this..." to "now may...") and create an agent that will adjust to each role in the chat and before she writes something improve her result with the help of such "Frankenstein" of prompts. This agent is the Motivator in department №2
