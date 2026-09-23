# What is Machine Learning?

AI is a broad field, so it is useful to begin with one of the fundamental ideas that supports many modern AI systems: **Machine Learning (ML)**. It's a way of teaching machines to make reasonably complex decisions using **mathematics, statistics, and probability**.

Instead of explicitly programming every rule a machine should follow, we can give it data and allow it to learn useful patterns from that data.

There are three major types of machine learning:
1. **Supervised learning**
2. **Unsupervised learning**
3. **Reinforcement learning**

## Supervised learning

Imagine that you have **100 math tests** and you want to teach a student to perform well on similar tests. One way to do this is to give the student 70 of the tests to practise with. We call this **training**. Each test contains questions and answers. We can think of the questions as **features** and the correct answers as **labels**. The student completes each test and compares their answers with the answer key. They can then adjust their reasoning based on their mistakes. We hope that, after completing many tests, the student has discovered useful patterns and learned the underlying logic rather than simply memorising the answers.

After the student has trained on the first 70 tests, we give them the remaining 30 tests without showing them the answer keys. The goal is to see whether the student can **generalise** what they learned from the first 70 tests to new tests they have never seen before. In machine learning, we can think of this process as: **Training data -> Learn patterns -> New data -> Make predictions**

If the student performs very well on the first 70 tests but performs poorly on the final 30 tests, they may have memorised the training examples instead of learning the underlying patterns. This is called **overfitting**. Similarly, if the student performs poorly on both the first 70 tests and the final 30 tests, they have probably failed to learn enough of the underlying patterns.This is called **underfitting**.

### The model

In machine learning, we refer to the system that learns from the data as the **model**.

In supervised learning, the model is given a set of **labelled data**, where each example contains both the input data and the correct output. We call this collection of examples the **training set**. The model learns from the training set and then makes predictions on new data. For example, **input** is the information about a house, and **label** is the actual price of the house. The model learns the relationship between the input information and the label. After training, we can give the model information about a house it has never seen before and ask it to predict the price. The difference between the model's prediction and the actual target is related to its **error** or **loss**.

## Unsupervised learning

In **unsupervised learning**, we do not give the student the answer keys. Imagine giving the student 70 tests but never telling them whether their answers are correct. The student cannot directly learn which answers are right or wrong. Instead, their job is to examine the data and discover **patterns, structures, and relationships** within it. For example, the student might notice that certain types of questions contain similar words, symbols, structures, or patterns. They could then organise the questions into different groups based on their similarities.

The important idea is that the student is not told what the groups should be. They must discover useful structure from the data themselves. When we give the student a new set of 30 tests, they still cannot determine the correct answers because they were never given the answer keys. However, they may be able to organise the new tests into groups based on the patterns they discovered during training. 

In machine learning, this can be used for tasks such as **clustering**, where similar data points are grouped together. Simply put, **supervised learning means learning from known answers**, while **unsupervised learning means discovering patterns without known answers.**

## Reinforcement learning

**Reinforcement learning** works differently. Imagine that the student completes a test, but instead of giving them an answer key, we give them feedback based on their actions. For example, we could give the student a positive score for a correct answer and deduct some points for an incorrect answer. The positive scores are called **rewards**, while the deductions are called **penalties**. The student is not simply trying to get one question correct. Their goal is to learn a strategy that **maximises the total reward they receive over time**.

In reinforcement learning, the system that makes decisions is called the **agent**. The agent interacts with an **environment**, takes **actions**, and receives **rewards or penalties** based on the consequences of those actions. Unlike supervised learning, there does not need to be a correct label for every action. Instead, the agent learns from the consequences of its decisions.

### Teaching a robot to walk

Imagine that we want to teach a robot to walk. We do not necessarily tell the robot exactly which movement it should make at every moment but we allow the robot to try different movements. If the robot moves forward without falling, we give it a reward. If it falls, we give it a penalty. After many attempts, the robot can learn which sequences of movements tend to produce higher rewards.

In this example:
- **Agent:** The robot.
- **Environment:** The world the robot interacts with.
- **Actions:** The robot's movements.
- **Rewards:** Positive feedback for useful actions.
- **Penalties:** Negative feedback for undesirable outcomes.

The agent gradually learns a strategy for choosing actions that produce better long-term results.

## ML in a nutshell

- **Supervised learning:** The model learns from examples where the correct answer is provided. It learns a mapping from features to labels so that it can make predictions on new data.
- **Unsupervised learning:** The model learns from data where there are no provided labels. It tries to discover useful structures or patterns in the data, such as groups, relationships, or lower-dimensional representations.
- **Reinforcement learning:** The agent learns by interacting with an environment. It chooses actions and learns from rewards and penalties so that it can improve its future decisions.

## Training and testing

There is an important distinction between **training** and **testing**. During training, the model is allowed to use the training data to adjust its internal parameters. During testing, the model is given data that it did not train on, and we measure how well it generalises to that new data. In supervised learning, the test data normally has known labels, but those labels are hidden from the model. This allows us to compare the model's predictions against the actual answers. For example, a model predicts $300,000 but the actual price is $320,000. The difference between the prediction and the target can then be used to measure the model's **error**.

## Parameters

An ML model contains internal values called **parameters**. These parameters determine how the model transforms its inputs into predictions. During training, the model adjusts these parameters using the training data so that its predictions become closer to the desired outputs. For example, imagine that a model is trying to predict the price of a house. The model might learn how strongly different features, such as house size or number of bedrooms, are related to the predicted price. Those learned values are represented by parameters. The parameters are therefore part of what the model **learns from the data**.

## Loss function

We need a way to measure how wrong the model's predictions are. This is the purpose of a **loss function**. It takes the model's prediction and the desired target and produces a numerical value representing how wrong the prediction is according to a particular objective.

For example:
- **Prediction:** $300,000.  
- **Target:** $320,000.  
- **Loss:** The difference between them.

During training, the model attempts to **minimise the loss**. It repeatedly adjusts its parameters and evaluates the resulting loss. So the general idea is: **Make prediction -> Calculate loss -> Adjust parameters -> Make another prediction**. After many repetitions, we hope that the model's predictions become more accurate.

## Math matters

A machine learning model is essentially a **mathematical system with parameters that can be adjusted**. Different areas of mathematics help us understand and train these systems:
- **Statistics** helps us reason about data, variation, and uncertainty.
- **Probability** helps us model uncertain events and predictions.
- **Optimisation** gives us methods for finding parameter values that make the model perform better.
These ideas work together to allow a machine learning system to learn patterns from data.

## The basic cycle of supervised ML

**Data -> Model -> Prediction -> Compare with target -> Calculate loss -> Adjust parameters -> Repeat**

The process is repeated many times during training. The goal is not simply to make the model perform well on the examples it has already seen. The goal is for the model to learn useful patterns that allow it to perform well on **new examples**.

## Overfitting

**Overfitting** occurs when a model becomes too specialised to the training data. An overfitted model may achieve very low training error but perform poorly on unseen data.

Imagine a student who memorises the exact answers to every practice exam. They may achieve excellent results on the practice exams but perform poorly when given a new exam with different questions. The student has learned the training examples too specifically instead of learning the underlying concepts.

## Underfitting

**Underfitting** is almost the opposite. An underfitted model has not learned enough of the underlying patterns in the data. As a result, it performs poorly even on the training data.

Imagine a student who has not studied enough to understand the material. They perform poorly on both the practice tests and the new tests. The model has not captured enough of the useful structure in the training data.