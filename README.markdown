SAT solver with Online Deletion
===========================================


 These solvers are built based on the winning solver of the
SAT competition 2018, [Maple LCM Dist ChronoBT](https://helda.helsinki.fi/items/3bd6f832-e0cf-4db9-bf9b-89764dea3a72).
These solvers provide an advanced clause deletion scheme to improve the performance.
The new deletion scheme called [Online Deletion(https://www.cs.sfu.ca/~mitchell/papers/sat19-deletion.pdf). In Online Deletion, each time the solver derives
a new conflict clause we choose a previously learnt clause to
replace with it, according to the following simple method.
Clauses of Local are maintained in a circular list L with
an index variable i that traverses the list in one direction. The
index indicates the current “deletion candidate” Li at each
time. For deletion, we maintain a clause quality measure Q,
and a threshold quality value q. When a new clause C is
learnt and needs to be stored in Local, we select the next
“low quality” clause in the list to be replaced with C. The
index i is showing the next candidate Li. While Q(Li) ≥ q,
we increment i to “save” clause Li for one more “round” in
the circular list; The first time Q(Li) < q, we replace Li with
C and delete the “old” clause Li. The size of Local, indicates how long a round is, and the clause quality measure threshold
is chosen in a way that there are always sufficiently enough
“low-quality” clauses in the list to be deleted.

The submitted solvers use a simple quality measure based
on counting how many times a clause has been used in conflict
analysis since the last time it was considered for deletion. Q
is calculated as follows:
Each clause has a quality measure RUL which is an indicator
of its recent usage and LBD. RUL is initialized with 0 when a
clause is first learnt. Every time a clause C is used in conflict
analysis, its RUL is increase by 12/LBD(C). In the solvers submitted to this competition, the clause Li

is saved if its RUL
is at least 2. (Q(Li) = RUL(Li) and q = 2). If a clause is
saved, its RUL resets to 0.


Compiling and installing
-----

The code is written in c++. You can use the Makefile to compile and install. 
After the code is compiled, the binary is available under `/simp/m_OnlineDel`.

Solver's input file and results
-----
The command-line interface takes a [cnf](http://en.wikipedia.org/wiki/Conjunctive_normal_form) as an
input in the [DIMACS](http://www.satcompetition.org/2009/format-benchmarks2009.html)
format which is broadly used in SAT competitions.


Let's take the file::
```
p cnf 2 3
1 0
-2 0
-1 2 3 0
```

The files has 3 clauses and 2 variables, this is reflected in the header
`p cnf 2 3`. Every clause is ended by '0'. The clauses say: 1 must be True, 2
must be False, and either 1 has to be False, 2 has to be True or 3 has to be
True. The only solution to this problem is::
```
$ cryptominisat --verb 0 file.cnf
s SATISFIABLE
v 1 -2 3 0
```

If the file had contained::
```
p cnf 2 4
1 0
-2 0
-3 0
-1 2 3 0
```

Then there is no solution and the solver returns `s UNSATISFIABLE`.

