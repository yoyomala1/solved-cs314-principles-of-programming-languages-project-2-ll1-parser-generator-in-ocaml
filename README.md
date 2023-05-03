Download Link: https://assignmentchef.com/product/solved-cs314-principles-of-programming-languages-project-2-ll1-parser-generator-in-ocaml
<br>
In this project, you will implement an LL(1) parser generator using OCaml. Specifically you need to implement functions that generate FIRST, FOLLOW, and PREDICT sets for random, correctly-written, LL(1) grammar. Based on these set functions, you will then implement a parser generator function that returns a parser for identifying correct/incorrect input. An EXTRA-CREDIT component is available for generating parse trees from any correct input string. This is NOT a group project. You must work on your own.

<h1>1          Data Structures for Grammars and Sets</h1>

In this project, a grammar is represented by a tuple consisting of the start symbol (a string) and a list of rules. Each rule is represented by a tuple consisting of the LHS (a string) and the RHS (a list of strings). Thus the type of a grammar is string * (string * string list) list. For example, consider the following simple grammar:

In this project, it is represented by (“S”,[(“S”,[“a”;”S”;”b”]);(“S”,[])])

We use the Set module from the standard library to represent sets of symbols. We define SymbolSet = Set.Make(String). Thus FIRST, FOLLOW, and PREDICT sets all have type SymbolSet.t.

If s has type SymbolSet, and x is a symbol, then (SymbolSet.mem x s) checks whether x is in s. See OCaml manual on how to work with Set: https://caml.inria.fr/pub/docs/manual-ocaml/libref/Set.html

There are two special symbols that may appear in FIRST and FOLLOW sets. We use the string “eps” to represent the <em> </em>symbol that occurs in FIRST sets. We use the string “eof” to represent the end-of-input marker that occurs in FOLLOW sets. It is guaranteed that these two strings never occur as terminal or non-terminal symbols in any input grammar test cases.

We use the Map module from the standard library to manipulate associative maps from non-terminal symbols to their FIRST and FOLLOW sets. Thus we define SMap = Map.Make(String). We define the type of the map using type symbolMap = SymbolSet.t SMap.t, such that each key in the associative map is a string, and each key is associated with a SymbolSet. If m is a symbol map, containing the FIRST sets of non-terminals, and x is a non-terminal symbol, then (SMap.find x m) returns the FIRST set of x in m. You can also use other functions in the Map module, such as SMap.add for inserting into the map and SMap.find for searching in the map. See OCaml manual on how to work with Map: https://caml.inria.fr/pub/docs/manual-ocaml/libref/Map.html

The PREDICT sets are organized as a list of tuples. Each tuple consists of a rule (LHS and RHS) and a symbol set (the PREDICT set). Thus the list of PREDICT sets has type ((string * string list) * SymbolSet.t) list.

<h1>2          Algorithms for Constructing FIRST, FOLLOW, and PREDICT Sets</h1>

<h2>2.1       FIRST Sets</h2>

FIRST sets are defined not only for every non-terminal symbol, but more generally for every sequence of symbols. If <em>Y </em>= <em>Y</em><sub>1</sub><em>Y</em><sub>2 </sub>···<em>Y<sub>k </sub></em>is a sequence of symbols, then FIRST(<em>Y </em>) can be computed in the following way:

, then FIRST(.

<ol start="2">

 <li>If <em>Y</em><sub>1 </sub>is a terminal, then FIRST(<em>Y </em>) = {<em>Y</em><sub>1</sub>}.</li>

 <li>If <em>Y</em><sub>1 </sub>is a non-terminal, then:</li>

</ol>

(a) If <em> </em>∈ FIRST(<em>Y</em><sub>1</sub>), then FIRST(<em>Y </em>) = (FIRST(FIRST(<em>Y</em><sub>2 </sub>···<em>Y<sub>k</sub></em>) (b) IfFIRST(<em>Y</em><sub>1</sub>), then FIRST(<em>Y </em>) = FIRST(<em>Y</em><sub>1</sub>).

Recall the algorithm to construct FIRST sets for a grammar:

<ol>

 <li>For each non-terminal <em>X</em>, initialize FIRST(<em>X</em>) = {}.</li>

 <li>Iterate until no more terminals or can be added to any FIRST(<em>X</em>), where <em>X </em>is a non-terminal:</li>

</ol>

For each rule of the form <em>X </em>::= <em>Y </em>where <em>Y </em>is a sequence of symbols, add FIRST(<em>Y </em>) to FIRST(<em>X</em>).

<h2>2.2       FOLLOW Sets</h2>

Recall the algorithm for constructing FOLLOW sets for a grammar:

<ol>

 <li>For each non-terminal <em>X</em>, initialize FOLLOW(<em>X</em>) = {}.</li>

 <li>Add the end-of-input marker EOF to FOLLOW(<em>S</em>) where <em>S </em>is the start symbol.</li>

 <li>Iterate until no more terminals can be added to any FOLLOW(<em>X</em>).

  <ul>

   <li>if a rule is of the form <em>A </em>::= <em>αBβ</em>:</li>

  </ul></li>

</ol>

<ol>

 <li>if ∈ FIRST(<em>β</em>):</li>

</ol>

FOLLOW(<em>B</em>) ← FOLLOW(<em>B</em>) ∪ (FOLLOW(<em>A</em>) ∪ (FIRST( ii. otherwise:

FOLLOW(<em>B</em>) ← FIRST(<em>β</em>) ∪ FOLLOW(<em>B</em>).

<ul>

 <li>if a rule is of the form <em>A </em>::= <em>αB</em>, then FOLLOW(<em>B</em>) ← FOLLOW(<em>B</em>) ∪ FOLLOW(<em>A</em>).</li>

</ul>

The pseudo code below demonstrates the idea of how to the follow sets, where P is the set of production rules and NT is the set of non-terminals. Note that the pseudocode is only for illustration purpose (it uses loops in imperative languages). You will need to implement this as recursive functions, not using loops.

<table width="398">

 <tbody>

  <tr>

   <td width="383">while (FOLLOW sets are still changing) do</td>

   <td width="15"> </td>

  </tr>

  <tr>

   <td width="383">for each p ∈ P, of the form A → B<sub>1</sub>B<sub>2</sub>…B<em><sub>k </sub></em>for i ← 1 to k – 1 if B<em><sub>i </sub></em>∈ NT then</td>

   <td width="15">do</td>

  </tr>

 </tbody>

</table>

if  FIRST(B<em><sub>i</sub></em><sub>+1</sub>…B<em><sub>k</sub></em>) then

FOLLOW(B<em><sub>i</sub></em>) ← FOLLOW(B<em><sub>i</sub></em>) ∪ (FIRST(B FOLLOW(A) else

FOLLOW(B<em><sub>i</sub></em>) ← FOLLOW(B<em><sub>i</sub></em>) ∪ FIRST(B<em><sub>i</sub></em><sub>+1</sub>…B<em><sub>k</sub></em>)

if B<em><sub>k </sub></em>∈ NT then

FOLLOW(B<em><sub>k</sub></em>) ← FOLLOW(B<em><sub>k</sub></em>) ∪ FOLLOW(A)

<h2>2.3        PREDICT Sets</h2>

Recall the algorithm for constructing a PREDICT for a rule <em>A </em>::= <em>δ</em>:

FIRST(<em>δ</em>):

(a) PREDICT(<em>A </em>::= <em>δ</em>) = (FIRST(FOLLOW(<em>A</em>)

<ol start="2">

 <li>otherwise:</li>

</ol>

(a) PREDICT(<em>A </em>::= <em>δ</em>) = FIRST(<em>δ</em>)

For more information on FIRST, FOLLOW, and PREDICT sets, refer to the notes of lectures 5–7 and recitations week 3–4, and <em>Programming Language Pragmatics </em>Section 2.3.

<h1>3        Project Specification</h1>

You are given the following files:

<ol>

 <li>proj2 types.ml – This file contains the type definitions for use in this project. All other files depends on this file. DO NOT modify this file.</li>

 <li>proj2 types.mli – This file contains the signature of proj2 types.ml. DO NOT modify this file.</li>

 <li>ml – This file contains stub functions which you must implement. This section will specify each of these functions.</li>

 <li>mli – This file contains the signature of each function in proj2.ml. DO NOT modify this file. Your implementation will be checked against this file, so you should not change the signature of any existing functions. However, you are allowed to add helper functions as you need.</li>

 <li>proj2 driver.ml – This file contains some functions that can help you debug your ml. See Section 4 of this document on how to use this file.</li>

 <li>Makefile – This file specifies the dependence between different files and commands for generating object files and testing/submitting your code. See Section 4 of this document on how to use this file.</li>

 <li>ml – This file provides example code on how to test the functions you generated. See section 4 of this document on how to use this file.</li>

</ol>

<h2>3.1     Conventions</h2>

We adopt the following conventions when specifying the functions that you will need to implement:

<ol>

 <li>We use simple grammar to represent a simple LL(1) grammar.</li>

</ol>

simple grammar = (“S”,[(“S”,[“a”;”S”;”b”]);(“S”,[])])

<ol start="2">

 <li>We use complex grammar to represent a slightly more complex grammar for the same language. complex grammar = (“S”,[(“S”,[“T”]);(“T”,[“a”;”S”;”b”]);(“T”,[])])</li>

 <li>We use {“a”,”b”,”c”} to represent a symbol set that contains “a”,”b”,”c”. This notation is NOT recognized by OCaml. Instead you must build sets using empty and SymbolSet.add.</li>

 <li>We use [a -&gt; x;b -&gt; y;c -&gt; z] to represent an associative map that maps a to x, b to y, and c to z. This notation is NOT recognized by OCaml. Instead you must build maps using empty and SMap.add.</li>

 <li>In ml, some functions are declared with the rec keyword and some are not. This is only a hint, not a rule about whether the function should be recursive. In other words you can always add or remove the rec keyword.

  <ul>

   <li><strong>Function getStartSymbol </strong>getStartSymbol (g : grammar) : string</li>

  </ul></li>

</ol>

This function returns the start symbol of g.

Example: getStartSymbol simple grammar → “S”

<ul>

 <li><strong>Function getNonterminals </strong>getNonterminals (g : grammar) : string list</li>

</ul>

This function returns a list containing all non-terminals symbols in g. It is guaranteed that every nonterminal symbol appears as the LHS of some rule. The returned list does not need to be sorted in any way. You do not need to remove duplicate entries.

Example: getNonterminals simple grammar → [“S”;”S”] or [“S”]

<ul>

 <li><strong>Function getInitFirstSets </strong>getInitFirstSets (g : grammar) : symbolMap</li>

</ul>

This function build an initial, empty map from non-terminals to FIRST sets. The returned symbol map should satisfy:

<ol>

 <li>The keys in the map are exactly the non-terminals of g.</li>

 <li>The value associated with each key is an empty set {}.</li>

</ol>

Example: getInitFirstSets simple grammar → [“S” -&gt; {}]

<strong>Note</strong>: this function should ONLY return first sets for non-terminals (not including that for terminals).

<strong>3.5      Function getInitFollowSets </strong>getInitFollowSets (g : grammar) : symbolMap

This function builds an initial, empty map from non-terminals to FOLLOW sets. The returned symbol map should satisfy:

<ol>

 <li>The keys in the map are exactly the non-terminals of g.</li>

 <li>The value associated with the start symbol is {“eof”}.</li>

 <li>The value associated with every other non-terminal is {}.</li>

</ol>

Example: getInitFollowSets simple grammar → [“S” -&gt; {“eof”}]

<ul>

 <li><strong>Function computeFirstSet </strong>computeFirstSet (first : symbolMap) (symbolSeq : string list) : SymbolSet.t</li>

</ul>

This function takes a (possibly incomplete) map of FIRST sets, first, and a sequence of symbols, symbolSeq, and returns the FIRST set of symbolSeq, according to the given FIRST sets. The sequence of symbols symbolSeq has type string list. For instance symbolSeq = [”A”;”B”] represents the sequence AB for first set calculation.

Example:

computeFirstSet [“A” -&gt; {“a”,”b”};”B” -&gt; {“c”,”eps”}] [“A”;”B”] → {“a”,”b”} computeFirstSet [“A” -&gt; {“a”,”eps”};”B” -&gt; {“b”,”eps”}] [“A”;”B”] → {“a”,”b”,”eps”}

<ul>

 <li><strong>Function recurseFirstSets </strong>recurseFirstSets (g : grammar) (first : symbolMap) firstFunc : symbolMap</li>

</ul>

The parameter firstFunc is a function that returns the first set for a given sequence of symbols. It has the same signature and functionality as computeFirstSet defined above.

This function takes a grammar g, a (possibly incomplete) map of FIRST sets – first, and the function for computing first set of a sequence firstFunc, executes <strong>one </strong>iteration of the FIRST set construction algorithm (see section 2.1 of this document). That is, it iterates over the rules of the grammar in the listed order. For each rule, it adds FIRST(RHS) to FIRST(LHS), and moves on to the next rule. It returns the updated map of FIRST sets after processing each rule once.

Example:

recurseFirstSets simple grammar (getInitFirstSets simple grammar) computeFirstSet

→ [“S” -&gt; {“a”,”eps”}]

recurseFirstSets complex grammar (getInitFirstSets complex grammar) computeFirstSet

→ [“S” -&gt; {};”T” -&gt; {“a”,”eps”}]

<strong>Note: </strong>The implementation should NOT call computeFirstSet directly, but use firstFunc to compute the FIRST set of the RHS of each rule. The reason is that, if your computeFirstSet implementation is incorrect, we might give you partial points by testing your recurseFirstSets function with our correct version of computeFirstSet.

<strong>3.8      Function getFirstSets </strong>getFirstSets (g : grammar) (first : symbolMap) firstFunc : symbolMap

This function takes a grammar, g, a (possibly incomplete) map of FIRST sets, first, and an implementation of computeFirstSet, firstFunc. It should call recurseFirstSets repeatedly to update the map, until it no longer changes. At this point the map becomes a complete map of FIRST sets, which the function returns.

Example: See provided testcases.

<h2>3.9      Function updateFollowSet</h2>

updateFollowSet (first : symbolMap) (follow : symbolMap) (nt : string) (symbolSeq : string

list) : symbolMap

This function takes a completed map of FIRST sets, first, a (possibly incomplete) map of FOLLOW sets, follow, a non-terminal symbol, nt, and a sequence of symbols, symbolSeq. Here nt and symbolSeq are the LHS and RHS of a production rule. This function executes line 3–8 of the pseudocode in section 2.2, and returns the updated FOLLOW map.

Example:

updateFollowSet (getFirstSets simple grammar (getInitFirstSets simple grammar) computeFirstSet) (getInitFollowSets simple grammar) “S” [“a”;”S”;”b”] → [“S” -&gt; {“b”,”eof”}]

<h2>3.10      Function recurseFollowSets</h2>

recurseFollowSets (g : grammar) (first : symbolMap) (follow : symbolMap)

followFunc : symbolMap followFunc is a function that has the same signature and functionality as updateFollowSet.

This function takes a grammar, g, a complete map of FIRST sets, first, a (possibly incomplete) map of FOLLOW sets, follow, and an implementation of updateFollowSet, and executes <strong>one </strong>iteration of the FOLLOW set construction algorithm (see section 2.2). That is, it iterates over the rules of the grammar in the listed order. For each rule, it invokes followFunc to update the FOLLOW map, before moving on to the next rule. It returns the updated FOLLOW map after processing each rule once.

Example:

recurseFollowSets simple grammar (getFirstSets simple grammar (getInitFirstSets simple grammar) computeFirstSet) (getInitFollowSets simple grammar) updateFollowSet

→ [“S” -&gt; {“b”,”eof”}]

recurseFollowSets complex grammar (getFirstSets complex grammar (getInitFirstSets complex grammar) computeFirstSet) (getInitFollowSets complex grammar) updateFollowSet

→ [“S” -&gt; {“b”,”eof”};”T” -&gt; {“eof”}]

<strong>Note: </strong>The implementation should NOT call updateFollowSet directly, but use followFunc to update the FOLLOW map. The reason is that, if your updateFollowSet implementation is incorrect, we might give you partial points by testing your recurseFollowSets function with our correct version of updateFollowSet.

<h2>3.11      Function getFollowSets</h2>

getFollowSets (g : grammar) (first : symbolMap) (follow : symbolMap)

followFunc : symbolMap

This function takes a grammar, g, a complete map of FIRST sets, first, a (possibly incomplete) map of FOLLOW sets, follow, and an implementation of updateFollowSet. It should call recurseFollowSets repeatedly to update the FOLLOW map until it no longer changes. At this point we have a complete FOLLOW map, which the function returns.

Example: See provided testcases.

<h2>3.12      Function getPredictSets</h2>

getPredictSets (g : grammar) (first : symbolMap) (follow : symbolMap) firstFunc : ((string * string list) * SymbolSet.t) list

This function takes a grammar, g and completed maps of FIRST and FOLLOW sets, first and follow, and constructs the PREDICT set for each production rule.

Example: See provided testcases.

<strong>3.13    Function tryDerive </strong>tryDerive (g : grammar) (input : string list) : bool

In this function you will put everything you wrote together, to implement an LL(1) parser. This function takes a grammar, g, and a list of input tokens, input. The function returns a Boolean value, indicating whether input can be derived in the given grammar.

If we call this function using “tryDerive g”, it will return a parser that can parse any correct input written using this LL(1) grammar. This is done by currying, which is by default in OCaml.

Example: See provided testcases.

<h2>3.14      Function tryDeriveTree</h2>

type parseTree =

| Terminal of string

| Nonterminal of string * parseTree list tryDeriveTree (g : grammar) (inputStr : string list) : parseTree

This function is for <strong>extra credit</strong>. This function takes the same input as tryDerive. If input can be derived from the start symbol, then it returns a parse tree for input. We will only test this function on inputs that can be derived.

The parse tree is represented by an object of type parseTree. This type has two constructors. Terminal sym represents a single terminal symbol sym. Nonterminal (sym, child list) represents a single non-terminal symbol sym, with child nodes listed in child list. If sym expands into , then child list is an empty list.

Example: See provided testcases.

<h1>4        Testing and Debugging</h1>

There are three ways to test your proj2.ml implementation.

<strong>Method 1: </strong>The first way is to work inside ocaml, the interactive OCaml interpreter. Since proj2.ml depends on proj2 types.ml, you will need to compile proj2 types.ml into an object file (which can be done by typing make proj2 types.cmo) first. You will need to type the following command in the interpreter to load it:

#load “proj2 types.cmo”

Next you can type #use “proj2.ml”. If no error occurs, you can then play with your functions and see their output. If you make any changes to your proj2.ml file you will have to type #use “proj2.ml” again. Alternatively, if you want to program in the interpreter environment, you do not have invoke #use “proj2.ml”. Later you can copy the implementation back to the proj2.ml after you have finished testing.

<strong>Method 2: </strong>The second way is to take advantage of the helper functions provided in the proj2 driver.ml file. Similarly, you will have to load the object files that proj2 driver.ml depends on. To do this, you need to type the following four lines in the shown order:

#load “str.cma”

#load “proj2 types.cmo”

#mod use “proj2.ml”

#use “proj2 driver.ml”

If you have modified proj2.ml, you will have type to #mod use “proj2.ml” again.

We provide a number of helper functions for you to use in proj2 driver.ml. The functions include but are not limited to

<ol>

 <li>printSymbolSet: This function takes a symbol set and prints out its content.</li>

 <li>printSymbolMap: This function takes a symbol map and prints out its content.</li>

 <li>printPredictSet: This function takes a list of predict sets (as returned by getPredictSets) and prints out their content.</li>

 <li>printTree: This function takes a parse tree (as returned by tryDeriveTree and prints the tree in a simple format.</li>

 <li>printTreeGraphviz: Similar to printTree. However, the tree will be printed as a Graphviz script. You can convert Graphviz scripts into vector images here: http://www.webgraphviz.com/</li>

</ol>

<strong>Method 3: </strong>The third way is to use the file codetest.ml. If you invoke make test in the terminal, it will automatically compile all the object files that codetest.ml depends on (if they are not compiled or outdated) and run the code in codtest.ml. The results will be displayed in the terminal. The codetest.ml file contains example commands on how to test your first, follow, predict sets, as well as the generated parser.

The codetest.ml file also contains code on how to use the provided test cases. There are five test cases provided in the testcases simple/ folder. If you look at line 10 in codetest.ml, it contains this statement #use “testcases simple/testcase1.ml”, which loads the testcase1.ml file for testing. You can change testcase1.ml to testcase$i$.ml, where $i$ is 1 to 5, representing one of the five test cases we provided. An initial run of the codetest.ml file (through make test) will yield something like “test failed”, since all the function implementations are empty.

The codetest.ml file is a demonstration of how to make use of the functions you implemented in proj2.ml. You can also write code similar to that in codetest.ml for your own testing. You will not need to submit codetest.ml. This is not the final grading script. In the backend, we will have a rigorous auto-grader that checks every function you have implemented. However, the grammars in the five test cases will be covered by the auto-grader.





