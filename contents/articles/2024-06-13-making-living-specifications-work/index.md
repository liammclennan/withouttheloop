---
title: The Missing Piece that Makes Living Specifications Work
author: Liam McLennan
date: 2024-06-13 20:32
template: article.jade
---

It has bugged me for years that software projects are not defined anywhere - except in the code - and that is not a human readable specification. 

What if the solution that makes living specifications work is:

> 1. let specification writers write specifications how they want
> 2. let software developers write automated tests how they want
> 3. map the specificaton statements to the tests that verify them

This just adds some rigour to the idea of [readme driven development](https://tom.preston-werner.com/2010/08/23/readme-driven-development). As Tom Preston-Warner says, "a perfect implementation of the wrong specification is worthless... a beautifully crafted library with no documentation is also damn near worthless".

Here is an example. 

The Number Guessing Game
--------

The number guessing game is a classic early programming exercise to write a program that prompts a user to guess a number. It is not fun, but it is easy to understand. 

![Guessing game in terminal](guessinggame.png)

The product owner may have written the following specification:

--------------------

# Guessing Game Specification

## User Input

The computer chooses a number, within a range (`[1,10]`) and prompts the user to guess the number. 

### Rules:

* The user must enter a valid, whole number
* The user must enter a number that is greater than or equal to the minimum value (`1`) and less than or equal to the maximum value (`10`)

## Evaluation

The computer then evaluates the user's guess and provides feedback.

### Rules

* If the user's guess is equal to the answer then the feedback is `Correct!`
* If the user's guess is lower than the answer then the feedback is `Higher`
* If the user's guess is higher than the answer then the feedback is `Lower`
* Guessing `42` is always correct

--------------------

The developer then implements the program and produces the following tests:

```csharp

```