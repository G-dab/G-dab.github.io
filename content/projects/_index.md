---
title: "Projects"
type: "projects"
layout: "list"
---

`sumto()` function: \\(\sum_1^i i\\)

Can be done recursively: `sumto(5) = 5 + sumto(4)`

Recursive function rules:

1. have a base case (a case to stop the recursion)
2. the recursive function must progress toward the base case (otherwise it will recurse infinitely)
3. trust the induction
4. Make sure that it won't make too many recursive calls
5. Never run the same arguments of the recursive function twice. Caching may help