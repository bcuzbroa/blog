---
title: Zero Cost Game
author: bcuzbroa
pubDatetime: 2026-02-12
slug:
featured: true
draft: false
tags:
  - CTF
  - ZeroCostGame
  - Rust
description:
    This article will go through my project ZeroCostGame. A a Capture The Flag (CTF) style game designed to help you learn Rust by solving coding puzzles and winning flags. 
---

![ZeroCostGame](src/assets/images/ZeroCostGame.png)

*_Source code_* : [github.com/bcuzbroa/ZeroCostGame](github.com/bcuzbroa/ZeroCostGame)

## Motivation

Developing in **Rust** is often described as a "zero-cost abstraction" experience. But learning it can feel like a high-cost pain. To make this journey more interactive, I developed **ZeroCostGame**, a Capture The Flag (CTF) project designed to teach Rust through puzzles and automated validation. 

## 🏗️ What is ZeroCostGame?

**ZeroCostGame** is a collection of coding challenges where the player must implement specific logic to "win" a flag. Unlike traditional exercises, this uses a specialized **Oracle**—a compiled black-box program that wraps your code, tests it, and reveals secrets only if your implementation is perfect.

The project is structured as a Rust **Workspace**:
* `challenges/`: Your playground. This is where you write your solutions.
* `oracle/`: The "Black Box" containing encrypted flags and validation logic.

---


