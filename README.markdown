CDCL SAT solver with Online Deletion
===========================================


These solvers are built based on the winning solver of the
SAT competition 2018, [Maple LCM Dist ChronoBT](https://helda.helsinki.fi/items/3bd6f832-e0cf-4db9-bf9b-89764dea3a72).
They provide an advanced clause deletion scheme to improve the performance.
The new deletion scheme is called [Online Deletion](https://www.cs.sfu.ca/~mitchell/papers/sat19-deletion.pdf). 

In Online Deletion, each time the solver derives
a new conflict clause we choose a previously learnt clause to
replace with it, according to the following method.
Clauses of Local are maintained in a circular list L with
an index variable i that traverses the list in one direction. The
index indicates the current “deletion candidate” L_i at each
time. For deletion, we maintain a clause quality measure Q,
and a threshold quality value q. When a new clause C is
learnt and needs to be stored in Local, we select the next
“low quality” clause in the list to be replaced with C. The
index i is showing the next candidate L_i. While Q(L_i) ≥ q,
we increment i to “save” clause L_i for one more “round” in
the circular list; The first time Q(L_i) < q, we replace it with
C and delete the “old” clause L_i. The size of Local, indicates how long a round is, and the clause quality measure threshold
is chosen in a way that there are always sufficiently enough
“low-quality” clauses in the list to be deleted.

To calculate the “quality” of a clause, Q, solvers use a quality measure based
on a combination of it's LBD value and counting how many times a clause has been used in conflict
analysis since the last time it was considered for deletion. Read more [here](https://helda.helsinki.fi/items/f9b9f04c-7003-4c0f-9f3e-cadf7d12f33a).


Compiling and installing
------------------------

The code is written in c++. You can use the Makefile to compile and install. 
After the code is compiled, the binary is available under `/simp/m_OnlineDel`.


Solver's input file and results
-------------------------------
The command-line interface takes a [cnf](http://en.wikipedia.org/wiki/Conjunctive_normal_form) as an
input in the [DIMACS](http://www.satcompetition.org/2009/format-benchmarks2009.html)
format which is broadly used in SAT competitions.


Let's take the file:

p cnf 3 2

-1 2 0

1 3 0


The cnf files has 3 variables and 2 cuases, as shown in the header`p cnf 3 2`. 
In DIMACS format all clauses end by '0'. In this example the first clause says: either valiable 1 has to be False or 2 has to be True.
The goal is to assign boolean values to the variables in a way that all clauses are satisfied (made TRUE). A solution to this problem is to assign all variables to TRUE. 
Given that there is a truth assignment that satisfies all clauses, when we run the solver on this SAT instance the solver returns:

$ m_OnlineDel file.cnf

s SATISFIABLE

...

If we had the following cnf file instead:

p cnf 1 2

1 0

-1 0

...

Then there is no solution to satisfy all clauses and the solver returns:

s UNSATISFIABLE

...

