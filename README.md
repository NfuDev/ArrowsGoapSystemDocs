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
