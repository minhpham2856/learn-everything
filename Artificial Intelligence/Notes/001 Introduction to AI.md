# What is AI?

The idea of Artificial Intelligence (AI) goes back to the early days of computing. As early as the 1930s and 1940s, when the foundations of modern computers were being developed, people began asking whether machines could perform tasks that we normally associate with human intelligence.

At its core, **AI is the attempt to replicate or simulate aspects of human intelligence using machines**.

There are many ways to simulate intelligence, but one of the most powerful approaches is to use **mathematics, statistics, and probability** to allow machines to make decisions based on information.

## Intelligence as decision-making

Understanding human intelligence is difficult because intelligence involves many different abilities: learning, reasoning, recognising patterns, understanding language, solving problems, and making decisions.

One useful way to simplify intelligence is to think about its most fundamental function: **decision-making**.

We make decisions constantly throughout the day. Decisions are made based on information we receive from our environment.

- You see that it is raining, so you decide to bring an umbrella.
- You feel sick, so you decide to stay home.
- You see a red traffic light, so you decide to stop.

The absence of information can also provide information. For example, if you are waiting for someone and they do not arrive, their absence may influence your decision about what to do next.

Therefore, we can think of decision-making as a process that takes **information as input and produces a decision as output**.

## Functions: The basic idea behind AI

If we want a computer to simulate this process, we need a way to transform information into a result. This is where a **function** becomes useful, it is a process that takes some input and transforms it into an output.

Imagine a machine in a factory, plastic pieces  are dumped in a machine which produces a lastic cup. The plastic pieces are the **input**. The machine performs some transformation on that input. Therefore, the machine represents the **function**. The plastic cup is the **output**.

In mathematical notation, we could represent this as:

**f(plastic pieces) = plastic cup**

Here, `f` represents the function - the process that transforms the input into the output.

## Applying functions to decision-making

If we want a computer to make a decision based on information, we can think of the process as:

**f(input information) = output decision**

Suppose you need to decide whether you should go to school tomorrow. You are seriously ill, and when you are seriously ill, you should not go to school.

A human can use this information to make a decision:
**Input:** You are seriously ill  
**Decision:** Do not go to school

We could represent this decision-making process with a simple function:
**f(seriously ill) = "Don't go to school"**

And if you are not seriously ill:
**f(not seriously ill) = "Go to school"**

The computer could therefore be given a function that takes your condition as input and produces a decision as output. In simple terms: **Information -> Function -> Decision**

## The fundamental idea

At a basic level, we want a machine to:
1. Receive information.
2. Process that information.
3. Use the information to determine an appropriate output or decision.

The difficult part of AI is not the basic concept of a function. The difficult part is creating functions that can handle **complex, uncertain, and previously unseen information**.

For example, instead of explicitly programming: "If sick -> don't go to school.", we may want a system that can learn from millions of examples and determine for itself what information is relevant and what decision should be made.

This is where **machine learning <ML>, statistics, and probability** become important.