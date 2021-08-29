# Comparative-analysis
Comparative analysis of approaches to organizing interaction in application

# Motivation
I am writing this in order to understand this.

# About
In this article, I will touch on several options for organizing the interaction of various parts of our application - these are callback functions (callbacks), generators (yeild) and the message exchange bus (eventemitter).

# How it works

I will try to compare these three methods of processing the chain of asynchronous events according to the following criteria and, based on these criteria, determine for each what field of application it has - that is, which tasks with its help are more convenient or more efficient, and which are not.

![](https://github.com/aleksandrtamrazov/Comparative-analysis/blob/master/img/mainTable.png)

Each answer will have a small sample JS code that demonstrates the specified behavior. The answer for each criterion can contain two options (callbacks can be used to organize both synchronous and asynchronous interaction), in which case both options should be considered.

# Contributing
The main purpose of this repository is to continue evolving me and you. You are welcome!
