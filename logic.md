

## Introduction to Logic


> The beauty of Logic is that it gives us a means to express incomplete information when that is all we have and to express complete information when full information is available.

逻辑学的优美之处在于用不完全的信息表示所有信息。


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


## Logical Properties

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
