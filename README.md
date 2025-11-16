# ArrowsGoapSystemDocs
documentation for arrows goap system

goal oriented action planning aka GOAP, is an AI planning method known for it's adaptivity and modularity
---

the core of our implementation is Brain -> Planner -> CombatSystem(WIP: optional) -> PerceptionSystem 
all these for collaborate together to make a goap agent
with the Brain being the operator over all of them

we have two methods for planning: **Direct** and **Bitset**

## 1. Direct
uses A* algorithm to plan for goal, logical and do it's job but it can be very complicated and heavy

@nightfall write direct doc here

## 2. Bitset
a new inovative method for planning, seamlessly fast and much easier to work with and debug, but it relies heavily on memory, hence it uses some briliant caching techniques to speed up the planning process

the main concept of Bitset originated from **Bit Masking** which allow you to combine multiple conditions in only one variable, which makes it easy and fast to check for different conditions with this one var, the limitation with **Bit Making** is that the number of conditions you could have is limited to the number of bits in the number you are using, .e.g **char** can only hold 8 conditions since it's 8 bit integer, **Int128** aka **"long long"** can only hold 128 conditions at a time, which is very limited for a wide system as goap, hence the idea is that we would map every action we have to their effects and precondtions so that if we need an action that satisfies a certain precondition we only look it up by the effect id in the map, the thing is this requires us to map every tag we use as an effect or a precondition to a unique id as you guessed it a bit, and those can be a lot, depending on the npc, and here where the name **Bitset** comes from, it comes from the c++ type **std::bitset** which allows you to store a sequencial bits limited to a number that you predefine, simple a big number that you define how many bits it has, bitset solved the problem and allowed us to stores whatever number we want, and this is the core of Bitset planning, which transforms goap from O(V · A log V) { where V:VisitedActions, A:AvailibeActions } to O(L) { where L:LengthOfThePlan } which is a huge optimazation for the system.

Bitset changed alot of how planning is done, and introduced other concepts like **ActionContainers** which solved a solved a problem we call **"Layerd Planning"** as the name suggets, it happens when an action has more than one action that satisfies it's precondition, which causes the plan to branch, with **ActionContainers** this is not even a thing to worry about, and it happens automatically

so this the whole idea of **Bitset**, what follows is a detailed explanation for the implementation of the system.

---
## Bitset Implementation Reference:
### Initialization

all Bitset work is build on top of Initialization it uses to steps workflow, Init -> Run, so if the initalization step faild Bitset won't be able to run, the Initialization process depends on two Parts, universal one in the the **UGoapWorldModel** here:

<img width="418" height="353" alt="Screenshot 2025-11-15 224214" src="https://github.com/user-attachments/assets/0aea7f61-d594-439d-9516-16bdb6fd7974" />

in which we give every goap tag a unique id and store them in GoapIDs for global access for planners.
The second part of the initialization is per goap Brain, **UGoapBrainComponenet** here:

<img width="331" height="17" alt="Screenshot 2025-11-15 225534" src="https://github.com/user-attachments/assets/9bb2cedc-1153-4972-942f-7906a645f28b" />

which maps all the availible actions for the Brain based on their effects, starting by mapping the action based on it's individual effects (makes the lookup easier), then after filling the Effects_ID which is FGoapBitset we map the action based on it's full effects, here:

<img width="817" height="339" alt="Screenshot 2025-11-15 225924" src="https://github.com/user-attachments/assets/ee86c1a7-76b3-40ed-8bc0-c53f38e4fea7" />

we also fill the Preconditions_ID for fast lookup in planning, here:

<img width="510" height="262" alt="Screenshot 2025-11-15 230213" src="https://github.com/user-attachments/assets/6fb08087-93b4-4a25-b7b8-8d6a7febb3a3" />

These are the two steps for Bitset Initialization.
