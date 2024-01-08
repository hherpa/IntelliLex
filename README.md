# IntelliLex
## The autogen pipeline and its departments
For example, your task is to make a full-fledged python game using only autogen. By full-fledged, I mean not only a python script, but also, for example, sound, good visual design, etc.
Obviously, a regular autogen can't handle this. After all, by making an agent for each task, the accuracy will also be minimal. But if we split this huge group chat into departments that can transfer data to the assembly, we can get the desired result.
Example:
```
1. Code Development Department
  | coder
  | planer
  | critic
  | and other supporting roles
2. Musical accompaniment development department
  | https://github.com/rushidarge/Music-Generator-Ai or https://github.com/salu133445/musegan
  | and other supporting roles
  | ...
4. Interface Development Department
  | https://github.com/microsoft/autogen/blob/main/notebook/agentchat_dalle_and_gpt4v.ipynb
  | and other supporting roles
  | ...
5. Assembly Department
```
This is somewhat similar to the langchain LCEL, but here the main problem is parsing the group chat in search of a result. But we have an idea to make an agent who will wait for the moment when all the chat participants will inform that the task has been solved and there is a result, and after that he will display it marked @final, which will make parsing a primitive search for a message with the word "@final".

## Autogen2autogen
We must understand that the user treats LLM as a black box, that is, he is not interested in the processes inside, he just wants to get the right answer to his question. In autogen, everything is only worse in this regard, because now the user must be able to: write code, write a role prompt for each agent, and also know additional utilities that can be integrated into it. The user simply does not want to do all this and will be afraid of the complexity, and those who already know how to work with autogen simply get tired of performing these actions from time to time, and even more so when creating a really large group chat. But just imagine if all the possibilities of autogen are opened to a wide audience with the right prompts and correctly selected integrations, memories, function call, and all this only for one small request. This is where we can use the autogen pipeline technology:
```
1. The department handles a short user request for its deployment in a large and detailed prompt 
2. Department for writing a list of roles (chat participants)
3. Department for processing each role
| 3.1 sub-department for selecting the type of agent and its memory
  | 3.2 the sub-department of writing the exact role-prompt
  | 3.3 the sub-department of selecting the utility for the agent and its integration
| 3.4 the sub-department of writing a function call if necessary
4. Assembly Department
```
Only the departments and sub-departments of autogen2autogen are presented here, but we also have several innovations in the agents themselves, and therefore we will analyze them in more detail using the example of department №2.

2. Department for writing a list of roles (chat participants):
```
  | Role List Engineer
  | Regulatory agent
  | Motivator
```

## CLI_Agents class
CLI_Agents is a class of agents that analyze the chat to find the fulfillment of a condition and, when found, output a specific command to the chat. For example, in our department of writing a list of roles, the "Regulatory agent" was of the CLI_Agents class. What was it done for? When testing the "Role List Engineer" agent, we noticed that it always needs to be adjusted with the same commands: "display the list in full", "this role is superfluous", "there are too few roles in the list - add more". We made the "Regulatory agent" only analyze the chat and, when a certain condition is triggered, output the following commands for the "Role List Engineer": `&cleared_out`, `&add_more`, `&only_list'. And already in the memory of the agent "Role List Engineer" we added a command handler:
* ``&cleared_out`` - indicates that the list is written incorrectly and does not concern the user's task
* ``&add_more`` - indicates that the list is too short and needs to be enlarged
* ``&only_list`` - indicates that the agent has displayed something else besides the list that should have been corrected

The CLI_Agents class also partially solves the problem with hallucinations in group chat. The "regulatory agent" can wait for the condition of a discrepancy between the facts and those that it has and use the command to replace a fragment of the agent's memory.
`&memory_edit <fragment to replace> <fragment to replace>`
## Continuing the theme of the CLI_Agents class...
We were very much hindered by the fact that only one agent writes to one (while observing a certain queue) - this slows down the group chat process very much, so in larger departments we added a "reference model" to each agent. What is a reference model? Roughly speaking, this is an abstraction for an agent on how to address several people in one message at once using @ to mark each by nickname. Only this small addition has significantly increased the productivity and efficiency of the chat. Of course, you can attach a huge number of screenshots here, but everything works according to about the same template:
```
Role_Name (to chat_manager):

@Role_name_2 ....text...
@Role_name_3 ....text...
@Role_name_4 ....text...
```
Thus, I replaced huge paragraphs of hints with: `@agent_name &agent_command`
### Universal agent for improving the accuracy of group chat
Autogen also has universal agents such as critic and planer, their role-prompt does not need to be edited and therefore they swing from one chat to another, but we want to introduce an agent that can universally improve the accuracy of any autogen -Motivator chat.
More and more often, studies have begun to appear on the impact of manipulations and motivations in prompt on the quality of LLM response. We decided to collect all the prompts from these studies (starting with "my career depends on it..."before "it's May...") and create an agent that will adapt to each role in the chat and before she writes something, improve her result with the help of a similar "Frankenstein" from prompts. This agent is the Motivator in Department №2
