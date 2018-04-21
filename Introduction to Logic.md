

# Introduction to Logic


## 1. Introduction

> The beauty of Logic is that it gives us a means to express incomplete information when that is all we have and to express complete information when full information is available.

逻辑学的优美之处在于用不完全的信息表示所有信息。

## 2. Propositional Logic

有一些有意思的逻辑学关系

$$p \Rightarrow q \Leftrightarrow  \neg p \lor q$$

两边的真值表是相同的。 由 $p$ 推出 $q$ 是正确的， 等价于 或者 $p$ 是错误的，或者 $q$ 是正确的，都成立。

由错误的命题推出任何结论都是正确的。

### Big Game

逻辑学经典问题。 有两条路，一条通往天堂，一个通往地狱。 有一个引路者，要么说真话，要么说假话。 只能问这个人一个问题，问，如何问问题选择正确的路？

先看真值表。

Heaven | lie |  Question | Answer
-|-|-|-
1 | 1 | |1
1 | 0 | |1
0 | 1 | |0
0 | 0 | |0

先看这个真值表， 期望这个问题的答案，与这条路（比如左边的）通往天堂 同真同假。但是对于 说谎者来说，答案与现实相反。再看下面这个真值表：

Heaven | lie |  Question | Answer
-|-|-|-
1 | 1 | 0 |1
1 | 0 | 1 |1
0 | 1 | 1 |0
0 | 0 | 0 |0

所以我们只需要满足这样真值表的问题即可得到与通往天堂 同真同假的答案。 一个很简单的问题就是 $Heaven \Leftrightarrow  \neg lie$，  这个问题其实就是： "左边这条路通往天堂，当且仅当你不说谎，这个命题对么？"


这个问题可以扩展成两个人的情景，两个人，有一个人是说谎者，另一个是说真话的，只能问一个人问题。
选定一条路，一个人（比如1）。

Heaven | 1lie | 2lie |  Question | Answer
-|-|-|-|-
1 | 1 | 0 | 0 | 1
1 | 0 | 1 | 1 | 1
0 | 1 | 0 | 1 | 0
0 | 0 | 1 | 0 | 0

真值表与上面是一样的。但是问题，可以更巧妙一些： “如果我问另一个人，通往天堂的路是哪条，他会怎么回答？”

这不依赖真值表，但是问题更巧妙，充分利用了两个人是互相知道真假的这个信息。

1. 如果问的是说谎的人，回答的一定是错误的；
2. 如果问的是说真话的，回答的也是错误的。

所以选择反方向的就对了。


## 3. Propositional Analysis

### Logical Properties

> A sentence is valid if and only if it is satisfied by every truth assignment.

valid：总为真。

>
A sentence is unsatisfiable if and only if it is not satisfied by any truth assignment

unsatisfiable： 总为假。

> a sentence is contingent if and only if there is some truth assignment that satisfies it and some truth assignment that falsifies it

contingent： 因情况而定。


### Logical Equivalence

逻辑等价性

> a sentence φ is logically equivalent to a sentence ψ if and only if every truth assignment that satisfies φ satisfies ψ and every truth assignment that satisfies ψ satisfies φ.

没什么好说的，同真同假


### Logical Entailment

> a sentence φ logically entails a sentence ψ (written φ ⊨ ψ) if and only if every truth assignment that satisfies φ also satisfies ψ.

> More generally, we say that a set of sentences Δ logically entails a sentence ψ (written Δ ⊨ ψ) if and only if every truth assignment that satisfies all of the sentences in Δ also satisfies ψ.

他是这个符号： $\phi \vDash \psi$ ，表示 满足 $\phi$ 的，当且仅当也满足 $\psi$。 感觉上，$\phi$ 的 1 要少， 可以理解成条件更严格。

集合似乎更严格，$\Delta \vDash \psi$ 表示满足 $\Delta$ 中所有的，当且仅当也满足 $\psi$。


### Logical Consistency

> A sentence φ is consistent with a sentence ψ if and only if there is a truth assignment that satisfies both φ and ψ. A sentence ψ is consistent with a set of sentences Δ if and only if there is a truth assignment that satisfies both Δ and ψ.

> For example, the sentence (p ∨ q) is consistent with the sentence (p ∧ q). However, it is not consistent with (¬p ∧ ¬q).

一致性，表示存在一个真值同时满足 $\phi$ 和 $psi$。例如 $p \lor q$ 和 $p \land q$，当$p$ 和 $q$ 同为真时同时满足。

对于集合的情况，注意一下，是同一种赋值，对所有表达式（命题），都成立。


### Properties and Relationships

#### Equivalence Theorem

**A sentence φ and a sentence ψ are logically equivalent if and only if the sentence (φ ⇔ ψ) is valid.**

这个好理解， Equivalent表示同真同假，即等价关系。

$\phi$ | $\psi$ | $\phi \Leftrightarrow \psi$
-|-|-
1|1|1
0|0|1
1|0|0
0|1|0

#### Deduction Theorem
**A sentence φ logically entails a sentence ψ if and only if (φ ⇒ ψ) is valid. More generally, a finite set of sentences {φ1, ... , φn} logically entails φ if and only if the compound sentence (φ1 ∧ ... ∧ φn ⇒ φ) is valid.**

$\phi$ | $\psi$ | $\phi \Rightarrow \psi$
-|-|-
1|1|1
0|1|1
0|0|1
1|0|0

如上真值表。所有满足$\phi$ 的满足 $\psi$， 则 $\phi \Rightarrow \psi$。 反过来，如果 $\phi \Rightarrow \psi$，则可以说，所有满足 $\phi$ 的满足 $\psi$。

#### Unsatisfiability Theorem
**A set $\Delta$ of sentences logically entails a sentence $\phi$ if and only if the set of sentences $\Delta \cup \{\neg \phi\}$ is unsatisfiable.**

$\Delta$ | $\phi$ | $\Delta \vDash \phi$ | $\Delta \cup \{ \neg \phi \}$
-|-|-|-
1|1|1|0
0|1|1|0
0|0|1|0
1|0|0|1

注意这里 $\cup$ ，其实是同时满足的意思，是且的关系，而不是或的关系。


#### Consistency Theorem
**A sentence φ is logically consistent with a sentence ψ if and only if the sentence (φ ∧ ψ) is satisfiable. More generally, a sentence φ is logically consistent with a finite set of sentences {φ1, ... , φn} if and only if the compound sentence (φ1 ∧ ... ∧ φn ∧ φ) is satisfiable.**


$\phi$ | $\psi$ | consistent | $\phi \land \psi$
-|-|-|-
1|1|1|1
0|1|0|0
0|0|0|0
1|0|0|0


## 4. Propositional Proofs

命题证明

注意几个概念：

- Premise： 前提条件， 可以理解成为已验证的命题。
- Assumption： 假设， 未验证的命题。


### 4.2 Linear Reasoning

- Schema: A schema is an expression satisfying the grammatical rules of our language except for the occurrence of metavariables (written here as Greek letters) in place of various subparts of the expression.

#### Rule of inference
A rule of inference is a pattern of reasoning consisting of some schemas, called premises, and one or more additional schemas, called conclusions.

- Implication Elimination(IE)
- Implication Creation (IC)
- Implication Distribution (ID)

以上是直线性的推导。没有任何假设。

### 4.3 Hypothetical Reasoning

带有一些假设。但是假设不能作为真命题，只能作为条件。例如 假设 $\phi$，可以得出 $\phi \Rightarrow \psi$ 但是不能直接得出 $\phi$。


### 4.4 Fitch

Fitch is a proof system that is particularly popular in the Logic community.

Fitch 是一个可靠的（sound）和完备的（completed）证明系统（后面会定义什么是sound，什么是completed，这里理解成为由这样一套逻辑证明体系，可以证明很多常见的逻辑命题。

- And Introduction,  $\{\phi_1, \phi_2\} \vDash \phi_1 \land \phi_2$
- And Elimination,  $\phi_1 \land \phi_2 \vDash \{\phi_1, \phi_2\}$
- Or Introduction, $\phi_i \vDash \phi_1 \lor \phi_2$
- Or Elimination, $\{\phi_1 \lor \phi_2, \phi_1 \Rightarrow \psi, \phi_2 \Rightarrow \psi\} \vDash \psi$
- Negation Introduction, $\{\phi \Rightarrow \psi, \phi \Rightarrow \neg\psi\} \vDash \neg\phi$
- Negation Elimination, $\neg\neg\phi \vDash \phi$
- Implication Introduction,  $\phi \vdash \psi  \vDash \phi \Rightarrow \psi$
- Implication Elimination, $\{\phi \Rightarrow \psi, \phi\} \vDash \psi$
- Biconditional Introduction, $\{\phi \Rightarrow \psi, \psi \Rightarrow \phi\} \vDash \phi \Leftrightarrow \psi$
- Biconditional Elimination, $\phi \Leftrightarrow \psi \vDash \{\phi \Rightarrow \psi, \psi \Rightarrow \phi\}$

注意上面的 $\vDash$ 符号是我擅自加的。 实际上这是推导（inference），而不是蕴含关系（entailment）。 但是说是蕴含也没有什么问题。感觉上 inference、entail($\vDash$) 以及 $\Rightarrow$ 可以互相替换。


### 4.6 Soundness and Completeness

>A set of premises logically entails a conclusion if and only if every truth assignment that satisfies the premises also satisfies the conclusion.

一个前提条件集合 蕴含（entails）一个结论， 当且仅当 所有满足前提的truth assignment，也满足结论。

>A sentence is provable from a set of premises if and only if there is a finite proof of the conclusion from the premises.

一个命题可从一个前提条件集合证明， 当且仅当存在从前提条件出发有限的证明？？


#### Soundness
>
We say that a proof system is sound if and only if every provable conclusion is logically entailed. In other words, if Δ ⊢ φ, then Δ ⊨ φ.

如果所有可证明的结论都是逻辑蕴含的，则proof system是可靠的（sound）。

对于可靠的proof system，如果 $\Delta \vdash \phi$， 则有 $\Delta \vDash \phi$。 前者表示推导，后者表示蕴含。

#### Completeness
> We say that a proof system is complete if and only if every logical conclusion is provable

如果任意逻辑结论都是可证明的，则称proof system是完备的。

对于完备的proof system， 如果 $\Delta \vDash \phi$，则有 $\Delta \vdash \phi$， 即如果蕴含，则可推导。


### 练习

#### Given p => q, use the Fitch System to prove ~q => ~p.

思路： 假设 `~q`， 需要证明 `~p`， 这样可以得到 `~q => ~p`。证明 `~p` 可以用 Negation Introduction 得到，只需要证明 `p => q`(前提) 以及 `p => ~q` 即可

```
1. p => q          Premise
2. | ~q            Assumption
3. || p            Assumption
4. || ~q           Reiteration: 2
5. | p => ~q       Implication Introduction: 3, 4
6. | ~p            Negation Introduction: 1, 5
7. ~q => ~p        Implication Introduction: 2, 6
```

#### Given `p => q`, use the Fitch System to prove `~p | q`.

只说一下思路： 如果要证明 `~p | q`， 其实常规思路不太适合。 可以这么做： 利用 Negation Introduction， 需要证明 `~(~p|q) => p` 以及 `~(~p|q) => ~p`，这样就可以证明 `~~(~p|q)` 即 `~p|q`。 而这两个证明用到上面的反证。

```
1. p => q Premise
2. ~(~p | q) Assumption
3. p Assumption
4. q Implication Elimination: 1, 3
5. ~p | q Or Introduction: 4
6. p => ~p | q Implication Introduction: 3, 5
7. p Assumption
8. ~(~p | q) Reiteration: 2
9. p => ~(~p | q) Implication Introduction: 7, 8
10. ~p Negation Introduction: 6, 9
11. ~(~p | q) => ~p Implication Introduction: 2, 10
12. ~(~p | q) Assumption
13. ~p Assumption
14. ~p | q Or Introduction: 13
15. ~p => ~p | q Implication Introduction: 13, 14
16. ~p Assumption
17. ~(~p | q) Reiteration: 12
18. ~p => ~(~p | q) Implication Introduction: 16, 17
19. ~~p Negation Introduction: 15, 18
20. ~(~p | q) => ~~p Implication Introduction: 12, 19
21. ~~(~p | q) Negation Introduction: 11, 20
22. ~p | q Negation Elimination: 21
```

#### Use the Fitch System to prove ((p ⇒ q) ⇒ p) ⇒ p.

这是一个很有趣的证明。 思路是假设 `(p => q) => p`， 推导出 `p`，即可证明。怎么证明 `p` 呢？ 可以用 `~p => p` 和 `~p => ~p` 来证明。 前者只需要证明 `~p => (p => q)`，再结合假设条件，即可证明。

```
1. (p => q) => p Assumption
2. ~p Assumption
3. p Assumption
4. ~q Assumption
5. p Reiteration: 3
6. ~q => p Implication Introduction: 4, 5
7. ~q Assumption
8. ~p Reiteration: 2
9. ~q => ~p Implication Introduction: 7, 8
10. ~~q Negation Introduction: 6, 9
11. q Negation Elimination: 10
12. p => q Implication Introduction: 3, 11
13. p Implication Elimination: 1, 12
14. ~p => p Implication Introduction: 2, 13
15. ~p Assumption
16. ~p => ~p Implication Introduction: 15, 15
17. ~~p Negation Introduction: 14, 16
18. p Negation Elimination: 17
19. ((p => q) => p) => p Implication Introduction: 1, 18
```

#### Given ¬(p ∨ q), use the Fitch system to prove (¬p ∧ ¬q).

乍一看是没有头绪的。其实很简单，给定 `~(p|q)`，只需要证明 `~p` 和 `~q` 即可，两个都需要证明。这两个证明可以用反证。

```
1. ~(p | q) Premise
2. p Assumption
3. p | q Or Introduction: 2
4. p => p | q Implication Introduction: 2, 3
5. p Assumption
6. ~(p | q) Reiteration: 1
7. p => ~(p | q) Implication Introduction: 5, 6
8. ~p Negation Introduction: 4, 7
9. q Assumption
10. p | q Or Introduction: 9
11. q => p | q Implication Introduction: 9, 10
12. q Assumption
13. ~(p | q) Reiteration: 1
14. q => ~(p | q) Implication Introduction: 12, 13
15. ~q Negation Introduction: 11, 14
16. ~p & ~q And Introduction: 8, 15
```

#### Use the Fitch system to prove the tautology (p ∨ ¬p).

这个其实很简单。 只要证明 `p => p` ，然后用上面第二个练习即可。


## 5. Propositional Resolution

另一种巧妙的逻辑推理方式。
Propositional Resolution is a powerful rule of inference for Propositional Logic.

### 5.2 Clausal Form


A clause is the set of literals in a clausal sentence.

诸如： {p, q}, {~p, q}, {p, q, r} 这样的形式。


1. Implications (I):
```
 	φ ⇒ ψ	→	¬φ ∨ ψ
 	φ ⇐ ψ	→	φ ∨ ¬ψ
 	φ ⇔ ψ	→	(¬φ ∨ ψ) ∧ (φ ∨ ¬ψ)
```
2. Negations (N):
```
¬¬φ	→	φ
¬(φ ∧ ψ)	→	¬φ ∨ ¬ψ
¬(φ ∨ ψ)	→	¬φ ∧ ¬ψ
```
3. Distribution (D):
```
φ ∨ (ψ ∧ χ)	→	(φ ∨ ψ) ∧ (φ ∨ χ)
(φ ∧ ψ) ∨ χ	→	(φ ∨ χ) ∧ (ψ ∨ χ)
φ ∨ (φ1 ∨ ... ∨ φn)	→	φ ∨ φ1 ∨ ... ∨ φn
(φ1 ∨ ... ∨ φn) ∨ φ	→	φ1 ∨ ... ∨ φn ∨ φ
φ ∧ (φ1 ∧ ... ∧ φn)	→	φ ∧ φ1 ∧ ... ∧ φn
(φ1 ∧ ... ∧ φn) ∧ φ	→	φ1 ∧ ... ∧ φn ∧ φ
```
4. Operators (O):
```
 	φ1 ∨ ... ∨ φn	→	{φ1, ... , φn}
 	φ1 ∧ ... ∧ φn	→	{φ1}, ... , {φn}
```

注意最后一步， 将 $\lor$ 的关系，放到一个集合（从句）里， 将 $\land$ 关系，放到若干集合（从句）中。

表示： 一个集合（从句？）必须是 satisfied 的。 例如 {p, ~p} 总是满足的。


### 5.3 Resolution Principle

Given a clause containing a literal χ and another clause containing the literal ¬χ, we can infer the clause consisting of all the literals of both clauses without the complementary pair. This rule of inference is called Propositional Resolution or the Resolution Principle.

```
{φ1, ... , χ, ... , φm}
{ψ1, ... , ¬χ, ... , ψn}
----------------------------
{φ1, ... , φm, ψ1, ..., ψn}
```

### 5.4 Resolution Reasoning

用 clause方式证明，会比常规方式（例如 Fitch）要简单的多。

注意一个技巧。 如果给定 $\Delta$，要证明 $\phi$，只需要 $\Delta \cup \{\neg\phi\}$ 的 Resolution 结果是 $\{\}$ 空集合即可，表示无法满足。 根据前面的 Unsatisfiability Theorem， 如果 $\Delta \cup \{\neg\phi\}$ ， 则 $\Delta \vDash \phi$。


#### 练习：Given the premises (p ⇒ q) and (r ⇒ s), use Propositional Resolution to prove the conclusion (p ∨ r ⇒ q ∨ s).

第一步， 先将条件进行分解：

```
      p => q
  I   ~p | q
  O   {~p, q}
```
```
      r => s
  I   ~r | s
  O   {~r, s}
```

第二步，将结论取反，然后分解：
```
       ~(p | r => q | s)
   I   ~(~(p|r) | q | s)
   N   (p|r) & ~q & ~s
   O   {p, r}, {~q}, {~s}
```
第三步， Resolution Reasoning
```
1.	{~p,q}	 Premise
2.	{~r,s}	 Premise
3.	{p,r}	 Premise
4.	{~q}	 Premise
5.	{~s}	 Premise
6.	{r,q}	 Resolution: 3, 1
7.	{r}	 Resolution: 6, 4
8.	{s}	 Resolution: 7, 2
9.	{}	 Resolution: 8, 5
```

最后得到 {} ， 根据 Unsatisfiability Theorem， 可以得出结论成立。



















































-
