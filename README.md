# Santa-2021-The Merry Movie Montage

This project is for **OPERATIONS RESEARCH APPLICATIONS AND IMPLEMENTATION** in the first semester of 2021. 
We chose Kaggle's Christmas contest in 2021 as our topic. The contest URL is as follows：
https://www.kaggle.com/c/santa-2021/overview

## Table of Contents

- [Introduction](#Introduction)
    - [Background and Motivation](#Background-and-Motivation)
    - [Problem Definition](#Problem-Definition)
- [Prerequisite knowledge](#Prerequisite-knowledge)
    - [The Minimal Superpermutation Problem](#The-Minimal-Superpermutation-Problem)
    - [Ant Colony Optimization(ACO)](#Ant-Colony-Optimization(ACO))
    - [Lin-Kernighan(LKH)](#Lin-Kernighan(LKH))
- [How do DEA model work?](#how-do-dea-model-work)
    - [DEA model](#dea-model)
    - [CRS DEA model](#crs-dea-model)
    - [VRS DEA model](#vrs-dea-model)
- [How to evaluate the efficiency?](#how-to-evaluate-the-efficiency)
    - [Input-Oriented Model](#input-oriented-model)
    - [Output-Oriented Model](#output-oriented-model)
    - [Decomposing the Overall Efficiency](#Decomposing-the-Overall-Efficiency)
    - [Illustration of efficiency decomposion](#illustration-of-efficiency-decomposion)
- [Case study：Airline Efficiency](#case-studyairline-efficiency)
    - [Data preparation](#data-preparation)
    - [Environment](#environment)
    - [Execute](#execute)
    - [Result](#result)
- [Comments](#comments)
- [Reference](#reference)

## Introduction
### Background and Motivation
The Elves at the North Pole are starting to recognize this, and need to work as fast as possible to launch their latest holiday offering: SantaTV+! A 24/7 streaming television channel where it’s “Always Christmas, All the Time.” To debut their new station, they’ve decided to kick things off with a made-for-television Christmas movie marathon! They’re excited for the premiere of such movies as 🎅, 🤶, 🦌, 🧝, 🎄, 🎁, and 🎀!

But elves know that just as important as the movie themselves is the order they’ll be aired. So the elves have decided the best way to figure out which order is best is to watch all the movies in every possible combination to see which feels the most Christmas-y.

Your job is to help the elves by giving them the shortest viewing schedules that shows them every combination of movies so they can get SantaTV+ live as soon as possible!The elves have formed three movie-watching teams to lighten the load.


### Problem Definition
Your objective is to find a set of three strings containing every permutation of the seven symbols 🎅, 🤶, 🦌, 🧝, 🎄, 🎁, and 🎀 as substrings, subject to the following conditions:

* Every permutation must be in at least one string.
* Each permutation beginning with 🎅🤶 must be in all three strings.
* Each string may have up to two wildcards 🌟, which will match any symbol in a permutation. No string of length seven containing more than one wildcard will count as a permutation.


## Prerequisite knowledge
### The Minimal Superpermutation Problem
A  Minimal superpermutation of n symbols is a permutation containing n!n symbols as a contiguous substring where each symbol occurs at **least once**.The goal finding a shortest possible string on the symbols.[[reference link](https://arxiv.org/pdf/1408.5108.pdf)]
For example, if $n = 3$, there are six permutations from A, B, and C: , ABC, ACB, BAC, BCA, CAB and CBA.  
We can use graph theory to describe this topic, where each of permutation is a vertex, and there is a directed edge connecting every permutation to every other permutation. Associated with each edge is a weight, as shown in figure:

![](https://i.imgur.com/Cjskw9w.png)

With the previous graph, we can find a Hamiltonian path through the directed graph that minimizes the total weight of all traversed edges. To get the shortest the permutation. So this problem is a Asymmetric traveling salesman problem(ATSP).
![](https://i.imgur.com/6oL7dt7.png)

Finally, we get the minial superpermutation when $n = 3$ is $ABCABACBA$ (length = 9). When $n≤4$ is easy to find the optimal solution. But when $n$ increase, the problem will become the N-P hard problem. 

Therefore, in order to solve this problem, we can improve the quality of the initial solution through data preprocessing. The two architectures mentioned below are used in the superpermutation problem, and we can take their solutions as the initial permutation[1],[2]：

* **Robin Houston**
On 27 February 2019, Robin Houston replacing standard kernels with non-standard kernels to get  results in smaller permutations than standard kernels(5907-->5906)
* **Williams Construction**
All permutations of n! Can be covered by two rings. After cutting and splicing can form a path that visits each vertex once. Finally, the permutation is centered on "121" to form a symmetrical structure. (7! length = 5913)
    * $n=3$  ->123**121**321
    * $n=4$  ->123412314231243**121**342132413214321
## **Methodology for ATSP**
In this case, we choose Ant Colony Optimization and Lin-Kernighan to solve this problem.

### **Ant Colony Optimization(ACO)**
ACO is a bionic iterative algorithm. At each iteration, a number of artificial ants are considered. They builds a solution by walking from one vertex (city) to another on the graph, with one constraint being not to visit the vertices it has already visited during it walk. During walking (solving), the ant selects the following vertices to visit according to a random mechanism biased by pheromone, As shown below:
![](https://i.imgur.com/2EdAq9X.png)
when ant in the vertex $i$, the following vertex is selected stochastically among the previously unvisited ones. For example, if $j$ has not been visited before, it can be selected with a probability that is proportional to the pheromone associated with edge $(i, j)$. At the end of the iteration, depending on the quality of the solutions built by the ants, the pheromone values are modified so that in future iterations the ants build solutions that are similar to the previous best solution.
The Algorithm of ACO metaheuristic as below[3]:
![](https://i.imgur.com/s9DJbak.png)
### **Lin-Kernighan(LKH)**
The Lin-Kernighan-Helsgaun is the heuristic algorithm which usually use for the traveling salesman problem. The algorithm is specified in terms of exchanges (or moves) that can transform one tour into another, thereby shortening the path. 
The algorithm as follows[4]:
1. Generate the initial tours in some randomized way.
2. LKH use the $\lambda$-opt to optimizes tour. In each iteration, by removing the $\lambda$ links, the path can reverse one or more of them, shortening each step. 
3. Until algorithm cannot improve the solution then stop.

Take 2-opt as an example
![](https://i.imgur.com/vImavkw.png)
1. Start witha given tour.
2. Replace 2 links of original tour with 2 other links in such a way that the obtain the new shorter tour.


## **Implement**
