---
title: "Bionic Frog"
date: 2024-09-01
author: ["Chenhao Wu", "yuxuan Chen"]
longImage: "longpic.jpg"
showImage: "showpic.png"
---

## Abstract

ecursive functio

<center><img src=https://s2.loli.net/2024/09/06/S7TUYpGlAEXea9f.jpg width=50%></center>

## Design

ecursive functio

## System Architecture

ecursive functio

## Publications

`sumto()` function: \\(\sum_1^i i\\)

Can be done recursively: `sumto(5) = 5 + sumto(4)`

Recursive function rules:

1. have a base case (a case to stop the recursion)
2. the recursive function must progress toward the base case (otherwise it will recurse infinitely)
3. trust the induction
4. Make sure that it won't make too many recursive calls
5. Never run the same arguments of the recursive function twice. Caching may help