
# Lecture 1 What is Measure

### Notation

$A^C = \Omega \backslash A$ 
$A \triangle B = A \bigcup B - A \bigcap B$  Symmetric difference, xor
$\mathcal{P}(\Omega)$ is the power set, set of all subsets of $\Omega$. 


### $\sigma$-field ( $\sigma$-algebra )

The question is what sets (or subsets of $\Omega$) am I allowed to measure?

Definition: For some set $\Omega$, a **$\sigma$-field** $\mathcal{F}$ is a collection of sets $A \subseteq \Omega$ , such that
1. $\emptyset,  \Omega \in \mathcal{F}$
2. If $A \in \mathcal{F}$, then $A^C \in \mathcal{F}$ 
3. For any countable collection of sets $\{A_i\}_{i=1}^{\infty}$, such that $A_i \in \mathcal{F}$ for $i \in \mathbb{N}$, we have $\bigcup_{i=1}^{\infty} A_i \in \mathcal{F}$

Consequence of 3: $\mathcal{F}$ also contains countable intersections.

A non-empty set $\Omega$ with a $\sigma$-algebra $\mathcal{F}$ over it, i.e. $(\Omega, \mathcal{F})$ forms a measurable space.

$\mathcal{F} \subset \mathcal{P}(\Omega)$ and $\mathcal{P}(\Omega)$ is often too big to be useful.

Verses field in abstract algebra: no additive inverse or multiplicative inverse in $\sigma$-algebra, countable infinite operations in algebraic field may not be well defined.


### Measure 

Let $(\Omega, \mathcal{F})$ be a measurable space

A measure $\mu : \mathcal{F} \rightarrow \mathbb{R}^+$ (Signed measure are not considered here) is a mapping such that
1. $\mu(\emptyset) = 0$
2. $\mu$ is countably additive - For a pairwise disjoint collection $\{A_i\}_{i=1}^{\infty}$ we have $\mu(\bigcup_{i=1}^{\infty} A_i) = \sum_{i=1}^{\infty} \mu(A_i)$  

Special cases: Let $(\Omega, \mathcal{F}, \mu)$ be a measure space
1. if $\mu(\Omega) = 1$ then we say that $(\Omega, \mathcal{F}, \mu)$ is a probability space and $\mu$ is a probability measure.
2. if $\mu(\Omega) < \infty$ then we say that $(\Omega, \mathcal{F}, \mu)$ is a finite measure.
3. if $\Omega = \bigcup_{i=1}^{\infty} A_i$ such that $\mu(A_i) < \infty, \forall i$, then $\mu$ is a $\sigma$-finite measure.

Example: $\Omega = \mathbb{R}$, $\mu([a,b]) = b-a$ then $\mathbb{R} = \bigcup_{i=1}^{\infty} [i-1,i] \cup [-i,-i+1]$ is a $\sigma$-field measure.  

Example: counting measure. $\Omega  = \{1,2,...,n\}$ then $\mathcal{P}(\Omega) = 2^{|\Omega|}$ is the set of all $2^n$ subsets of $\Omega$. $\mu(\{1,3,7\}) = 3, \mu(\Omega) = n$. We can define $\upsilon(A) = \frac{1}{n}\mu(A)$ then $\upsilon$ is a uniform distribution over $\{1,2,...,n\}$. In contrast, consider the binomial $(n,p)$ distribution, each point sets a measure of ${{n}\choose{i}}p^i (1-p)^{n-i}$ for $p\in (0,1)$. We have two measures over the same finite space, discrete cases is not so interesting.


# Lecture 2 Existence, Caratheodory Extension Theorem

Idea: if the measure of $(a,b]$ is $b-a$ for any $b > a$ then what else can we measure?

### Semi-Ring and Ring

The set of all such intervals $(a,b]$ is **NOT** a $\sigma$-field. But it is a semi-ring.

Definition (Semi-Ring): $\mathcal{A}$ is a collection of subsets of $\Omega$ is called a semi-ring when 
1. $\emptyset \in \mathcal{A}$
2. for any $A,B \in \mathcal{A}$, $A \bigcap B \in \mathcal{A}$
3. and $B \backslash A = \bigcup_{i=1}^{n} C_i$ for $C_i \in \mathcal{A}$ (finite! disjoint! union)

Definition (Ring): $\mathcal{A}$ is a collection of subsets of $\Omega$ is called a ring when 
1. $\emptyset \in \mathcal{A}$
2. for any $A,B \in \mathcal{A}$, $B \backslash A \in \mathcal{A}, A \bigcup B \in \mathcal{A}$ (finite unions not countable!)

Example: all finite unions of half open intervals.

Definition (Field): $\mathcal{A}$ is a field if it is a Ring and $\Omega \in \mathcal{A}$ 

Note: Field + countable unions = $\sigma$-field.

Definition (Set Function): $\mu: \mathcal{A} \rightarrow \mathbb{R}^+$ (not necessarily a measure yet) 
For $A,B \in \mathcal{A}$,  we say that
1. $\mu$ is increasing if $A \subset B \implies \mu(A) \le \mu(B)$ 
2. $\mu$ is additive if for $A,B$ disjoint, $\mu(A \bigcup B) = \mu(A) + \mu(B)$ 
3. $\mu$ is countably additive if for countable $\{A_i\}_{i=1}^{\infty}$ such that $A_i \bigcap A_j = \emptyset, \forall i \ne j$ (i.e. pairwise disjoint) and $\bigcup_{i=1}^{\infty} A_i \in \mathcal{A}$, then $\mu(\bigcup_{i=1}^{\infty} A_i) = \sum_{i=1}^{\infty} \mu(A_i)$ 
4. $\mu$ is countably sub-additive if for countable $\{A_i\}_{i=1}^{\infty}$ (i.e. not necessarily pairwise disjoint) and $\bigcup_{i=1}^{\infty} A_i \in \mathcal{A}$, then $\mu(\bigcup_{i=1}^{\infty} A_i) \le \sum_{i=1}^{\infty} \mu(A_i)$ 

Definition (Pre-measure): often, a set function $\mu$ on a Ring $\mathcal{A}$ such that $\mu(\emptyset) = 0$ and $\mu$ countably additive, then we say that $\mu$ is a pre-measure  


### Caratheodory Extension Theorem





