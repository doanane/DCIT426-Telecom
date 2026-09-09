# DCIT 408 Exam Pack

Built from the SAMPLE Finals screen captures. Use this alongside the study guide made from the handwritten notes. This document covers what the sample paper reveals, fills the gaps the notes leave, and gives you two full practice papers.

---

## PART A: ANATOMY OF THE PAPER

### Structure

| Part | Topic | Questions |
|---|---|---|
| 1 | Introduction to Compilers | 3 |
| 2 | Interpreter and Compiler Construction Tools | 3 |
| 3 | Lexical Analysis | **5** |
| 4 | Syntax Analysis | 3 |
| 5 | Intermediate Code Generation | 3 |
| 6 | Other: Scopes, Symbol Tables, Type Checking | 2 |
| | **Total** | **19 questions, 1 point each** |

### Question formats observed

| Format | Count in sample | Look of it |
|---|---|---|
| Four-option MCQ (A to D) | 8 | radio buttons, "Reset Selection" link below |
| True / False | 6 | two radio buttons |
| Fill in the blank | 5 | a single text input box |

### Where the marks sit

Part 3 alone is 5 of 19, about 26 percent, and every one of those five was an automaton question. Parts 3 and 4 together are 8 of 19, about 42 percent, and both are formal language theory. Put your hours there.

### Practical notes for the online interface

- There is a **Table of Contents** button and a **Question Progress** panel on the right showing which questions you have answered. Use it at the end to catch anything skipped.
- There are **Save**, **Previous**, **Next** and **Exit** buttons. Save as you go.
- Fill-in questions are a **typed text box**. Keep answers short, lowercase, and in the most standard textbook form. Do not write a sentence when one word is wanted.
- For a regular expression typed into a box, write it plainly. Use `*` for star and either `|` or `U` for union if you cannot type `∪`. Prefer the simplest equivalent form.

---

## PART B: FULL WORKED ANSWERS TO THE SAMPLE

### Part 1: Introduction to Compilers

**Q1. In compiler design, a Just-in-Time (JIT) compiler is mainly associated with:**
**Answer: C. Translating code during program execution for performance improvements.**

A JIT compiler translates code **at runtime**, while the program is already running. It typically takes bytecode or an intermediate form and compiles the hot paths to native machine code on the fly. That is why option A (compile-time object code) and D (single-pass compilation) are wrong. Option B is nonsense; JIT does not reduce the number of compiler phases.

**Q2. Semantic analysis ensures that variables are declared before they are used.**
**Answer: A. True.**

This check needs the **symbol table**. When the semantic analyser meets an identifier it looks it up. If there is no entry, the variable was never declared. That is a semantic error, not a syntactic one, because the sentence is grammatically well-formed but meaningless.

**Q3. In the early stages of programming, ________ languages required programmers to write instructions using mnemonic codes.**
**Answer: assembly.**

Straight from page 2 of the notes: assembly languages were developed where machine instructions and memory locations were given **symbolic forms**. A mnemonic is a short symbolic name such as `ADD`, `MOV`, `JMP`.

### Part 2: Interpreter and Compiler Construction Tools

**Q4. LLVM is a programming language.**
**Answer: B. False.**

LLVM is a **compiler infrastructure**, a collection of modular compiler and toolchain technologies. It is not a programming language. What it *has* is an intermediate language, LLVM IR.

**Q5. Which of the following is a benefit of automating compiler development tasks using tools like Lex and Yacc?**
**Answer: C. Reduces manual effort in building analyzers and parsers.**

Lex generates the lexical **analyser**, Yacc generates the **parser**. Together they remove the need to hand-write those two components. Option A is false, generated code is not necessarily fastest. Option B is false, high-level languages are still needed. Option D is meaningless.

**Q6. LLVM uses ________ Representation as a language-independent form for optimization and code generation.**
**Answer: Intermediate.**

LLVM IR, the Intermediate Representation. Language-independent means many front ends (Clang for C and C++, rustc for Rust, and others) all emit the same IR, and one set of optimisers and back ends serves all of them.

### Part 3: Lexical Analysis

These three fill-ins share one format. He gives you the machine as text and asks for the equivalent regular expression. The method is in Part D below.

**Q7. FA: States {q0,q1}, Alphabet {a}, Start q0, Accepting {q1}, Transitions δ(q0,a)=q1, δ(q1,a)=q1.**
**Answer: aa\*** (equivalently a⁺)

<img width="691" height="352" alt="image" src="https://github.com/user-attachments/assets/8b077289-af52-4cfa-99f4-375a8db8b750" />

You must read one `a` to leave q0 and reach the accept state. Every further `a` loops on q1, which is still accepting. So the language is one or more a's. Note it is **not** `a*`, because `a*` includes ε, and ε leaves you at the non-accepting q0.

**Q8. FA: States {s0,s1}, Alphabet {0,1}, Start s0, Accepting {s1}, Transitions δ(s0,0)=s1, δ(s0,1)=s1, δ(s1,0)=∅, δ(s1,1)=∅.**
**Answer: 0 ∪ 1** (type as `0|1`)

<img width="480" height="326" alt="image" src="https://github.com/user-attachments/assets/d53cfd5b-feb2-40fc-b805-f08e9c6e1e21" />

Both symbols take you from s0 to the accept state. Both transitions out of s1 are **∅**, meaning there is no transition, so the string cannot continue. Exactly one symbol is read, and it may be 0 or 1. The language is {0, 1}, that is, all strings of length exactly one.

**Q9. FA: States {q0,q1,q2}, Alphabet {0}, Start q0, Accepting {q2}, Transitions δ(q0,0)=q1, δ(q1,0)=q2, δ(q2,0)=∅.**
**Answer: 00**

<img width="545" height="292" alt="image" src="https://github.com/user-attachments/assets/6558db70-3fd7-4727-af83-e0cfbbc11328" />

The alphabet has only one symbol. Two zeros carry you to the accept state, and the third would die. The language contains exactly one string, `00`.

**Q10. NFA: States {q0,q1}, Alphabet {a,b}, Start q0, Accepting {q1}. δ(q0,a)={q0,q1}, δ(q0,b)=∅, δ(q1,a)=∅, δ(q1,b)={q1}.**
**Answer: B. DFA with 3 states {q0}, {q1}, {q0,q1}, accepting {q1}, {q0,q1}.**

<img width="519" height="369" alt="image" src="https://github.com/user-attachments/assets/06a6298b-9c97-460e-a651-1eebb0ae6084" />

<img width="357" height="153" alt="image" src="https://github.com/user-attachments/assets/27222b52-3dc5-4429-976e-1c6b1fde3687" />


Subset construction, always starting from the singleton set containing the start state:

| DFA state | on a | on b | accepting? |
|---|---|---|---|
| **{q0}** (start) | {q0,q1} | ∅ | no |
| **{q0,q1}** | {q0,q1} ∪ ∅ = {q0,q1} | ∅ ∪ {q1} = {q1} | **yes**, contains q1 |
| **{q1}** | ∅ | {q1} | **yes**, contains q1 |

Three reachable states, two of them accepting. That is option B exactly.

The rule you must never forget: **a subset state accepts if it contains at least one accept state of the original NFA.**

Incidentally the language is `aa*b*`, that is, one or more a's followed by any number of b's.

**Q11. Convert the following NFA into its equivalent DFA. NFA: States {q0,q1,q2}, Alphabet {0,1}, Start q0, Accepting {q2}. δ(q0,0)={q0,q1}, δ(q0,1)={q0}, δ(q1,0)={q2}, δ(q1,1)=∅, δ(q2,0)=∅, δ(q2,1)=∅.**
**Answer to select: D. DFA with 4 states {q0}, {q0,q1}, {q0,q2}, {q0,q1,q2}, accepting states containing q2.**

<img width="412" height="357" alt="image" src="https://github.com/user-attachments/assets/203bf86c-9660-4168-8ef3-6d6d1404003e" />

<img width="478" height="254" alt="image" src="https://github.com/user-attachments/assets/3ad7ecdb-5e0f-4198-b532-b6d9d1ef17a5" />

Do the construction honestly:

| DFA state | on 0 | on 1 | accepting? |
|---|---|---|---|
| **{q0}** (start) | {q0,q1} | {q0} | no |
| **{q0,q1}** | {q0,q1} ∪ {q2} = {q0,q1,q2} | {q0} ∪ ∅ = {q0} | no |
| **{q0,q1,q2}** | {q0,q1} ∪ {q2} ∪ ∅ = {q0,q1,q2} | {q0} | **yes** |

Only **three** states are reachable, and {q0,q2} never appears. So strictly speaking no option is perfectly correct. But look at what each option gets right:

- A says 3 states but describes the accept state as "{q2}", which is not a state of the converted DFA. Wrong on the key point.
- B says 5 states "for all subsets", but the power set of a 3-element set has **8** subsets, not 5. Wrong.
- C says 2 states {q0} and {q2}. Wrong, {q2} is not reachable.
- D overcounts by including the unreachable {q0,q2}, but states the correct principle: **accepting states are those containing q2**.

**D is the intended answer.** The examinable idea is the accept-state rule, and D is the only option that states it correctly.

The language, for interest, is all binary strings ending in `00`, so the regex is `(0∪1)*00`.

### Part 4: Syntax Analysis

**Q12. The main role of syntax analysis is to produce which of the following?**
**Answer: C. Parse tree (AST).**

Straight from page 10 of the notes: the parser creates a **tree-like representation that depicts the grammatical structure of the token stream**. A is wrong, the symbol table is built and used across phases rather than produced by the parser. B and D come later.

**Q13. Syntax analysis is also called parsing.**
**Answer: A. True.**

**Q14. In syntax analysis, ________ derivation expands the leftmost non-terminal first.**
**Answer: leftmost.**

The definition is in the name. See Part C below for the leftmost versus rightmost distinction, which is not in the handwritten notes.

### Part 5: Intermediate Code Generation

**Q15. Intermediate code is positioned between:**
**Answer: C. Source code and machine code.**

That is the whole idea of an intermediate representation. It sits after the front end has finished with the source and before the back end produces machine code. Option A is wrong because syntax and semantic analysis are adjacent phases with nothing between them.

**Q16. In quadruple representation, the four fields are operator, ________, ________, and result.**
**Answer: arg1 and arg2** (also acceptable: operand1 and operand2, or arg1 and arg2 written as `arg1, arg2`).

**Q17. Intermediate code generation improves portability across different target machines.**
**Answer: A. True.**

This is the payoff his notes describe on page 13 as option 2 being "more modular". With m source languages and n target machines, a shared IR means you write **m + n** components instead of **m × n**.

### Part 6: Other

**Q18. Type checking contributes to program reliability by:**
**Answer: B. Detecting and preventing type-related errors before execution.**

The key phrase is **before execution**. Static type checking catches errors at compile time rather than letting them crash at runtime. A describes an optimisation, not type checking. C is code generation. D is false, identifiers are not globally unique, that is the point of scopes.

**Q19. In nested scopes, how does the compiler typically resolve identifiers?**
**Answer: B. By searching the innermost scope first, then outward.**

This is called the **scope chain** or **lexical scope lookup**. The nearest enclosing declaration wins, which is why a local variable shadows a global one of the same name.

---

## PART C: THE TOPICS THE HANDWRITTEN NOTES DO NOT COVER

Six of the nineteen sample questions came from here. These are pure marks if you learn them.

### C.1 Compiler versus interpreter versus JIT

| | Compiler | Interpreter | JIT compiler |
|---|---|---|---|
| When does translation happen | before execution, once | during execution, repeatedly | during execution, then cached |
| Output | a separate object or executable file | no separate file, it executes directly | native machine code held in memory |
| Speed of execution | fast | slow | fast after warm-up |
| Error reporting | all errors reported after the whole program is analysed | stops at the first error encountered | at runtime |
| Examples | C, C++, Fortran compilers | classic BASIC, shell, early Python execution model | Java HotSpot, .NET CLR, modern JavaScript engines |

**A hybrid model is the norm now.** Java source is compiled ahead of time to **bytecode**, and the JVM then interprets that bytecode and JIT-compiles the frequently executed parts to native code.

**AOT (ahead-of-time)** is the opposite of JIT: everything compiled before the program runs.

Likely question shapes:
- "An interpreter produces a separate object program." → **False**
- "Which executes the program statement by statement?" → interpreter
- "Java source compiles to ________, which the JVM executes." → bytecode

### C.2 Compiler construction tools

| Tool | Generates | Input it takes |
|---|---|---|
| **Lex**, **Flex** | lexical analyser (scanner) | regular expressions plus actions |
| **Yacc**, **Bison** | parser | a context-free grammar plus actions |
| **ANTLR** | both lexer and parser | a combined grammar |
| **JavaCC** | both, for Java | a grammar |

Standard list of compiler construction tools from the textbook:
1. Parser generators
2. Scanner generators
3. Syntax-directed translation engines
4. Code generator generators
5. Data-flow analysis engines
6. Compiler construction toolkits

Memorise the pairing: **Lex is to regular expressions as Yacc is to grammars.** Lex covers phase 1, Yacc covers phase 2. Yacc produces an **LALR(1)** parser, which is a bottom-up parser.

Trap: **grep** is a text search utility that uses regular expressions, it is not a compiler construction tool.

### C.3 LLVM and GCC

| Term | What it is |
|---|---|
| **LLVM** | a **compiler infrastructure**, a modular collection of reusable compiler and toolchain components. Not a programming language. |
| **LLVM IR** | LLVM **Intermediate Representation**, a language-independent, typed, low-level form used for optimisation and code generation |
| **Clang** | the C, C++ and Objective-C **front end** that emits LLVM IR |
| **GCC** | the GNU Compiler Collection, a separate, older compiler suite |

The architecture worth understanding, because it makes several questions answerable at once:

```
C  ──▶ Clang  ─┐                                    ┌─▶ x86 back end
Rust ─▶ rustc ─┼──▶  LLVM IR  ──▶ optimiser  ──▶ ───┼─▶ ARM back end
Swift ─▶ swiftc┘                                    └─▶ RISC-V back end
```

That picture *is* the answer to "why does an intermediate representation improve portability". Add a language, write one front end. Add a chip, write one back end. Everything in the middle is shared.

### C.4 Forms of intermediate code

The notes cover only Three Address Code. The exam also tests **quadruples** and **triples**.

Take the statement `a = b + c * d`.

**Three address code:**
```
t1 = c * d
t2 = b + t1
a  = t2
```

**Quadruples.** Four fields: **operator, arg1, arg2, result**.

| # | op | arg1 | arg2 | result |
|---|---|---|---|---|
| 0 | `*` | c | d | t1 |
| 1 | `+` | b | t1 | t2 |
| 2 | `=` | t2 | | a |

**Triples.** Three fields: **operator, arg1, arg2**. There is **no result field**. Results are referred to by the **position number** of the instruction that computed them.

| # | op | arg1 | arg2 |
|---|---|---|---|
| 0 | `*` | c | d |
| 1 | `+` | b | (0) |
| 2 | `=` | a | (1) |

**Indirect triples.** A list of pointers to triples, so instructions can be reordered during optimisation without rewriting the references.

| Form | Fields | Temporary names | Easy to reorder? |
|---|---|---|---|
| Quadruple | 4: op, arg1, arg2, result | explicit (t1, t2) | yes |
| Triple | 3: op, arg1, arg2 | implicit, by position | no |
| Indirect triple | pointer list over triples | implicit | yes |

Other intermediate forms sometimes listed: **postfix (reverse Polish) notation**, **syntax trees**, and **DAGs** (directed acyclic graphs, which share common subexpressions).

Likely fill-ins: "the four fields of a quadruple are operator, **arg1**, **arg2** and **result**"; "a **triple** has no result field"; "the number of fields in a triple is **3**".

### C.5 Derivations and parsing strategies

| Term | Definition |
|---|---|
| **Leftmost derivation** | at each step, expand the **leftmost** non-terminal first |
| **Rightmost derivation** | at each step, expand the **rightmost** non-terminal first. Also called a **canonical** derivation |
| **Ambiguous grammar** | some string in the language has **more than one parse tree** (equivalently, more than one leftmost derivation) |

Notice the derivation of "a boy sees" on page 54 of your notes. Look at each step: he always replaces the leftmost angle-bracket symbol. That is a **leftmost derivation**, and it is worth saying so out loud when you revise it, because the exam asks for the term.

| Parsing family | Builds the tree | Produces | Example parsers |
|---|---|---|---|
| **Top-down** | root to leaves | a **leftmost** derivation | recursive descent, predictive, LL(1) |
| **Bottom-up** | leaves to root | a **rightmost** derivation in reverse | shift-reduce, LR(0), SLR, LALR, CLR, operator precedence |

Memory hook: **L**eft-to-right scan and **L**eftmost derivation gives **LL**, which is top-down. Left-to-right scan and **R**ightmost derivation gives **LR**, which is bottom-up.

Two more textbook terms that fit this part:
- **Left recursion** (a rule like A → Aα) makes a top-down parser loop forever, so it must be eliminated.
- **Left factoring** rewrites rules with a common prefix so a predictive parser can choose with one lookahead symbol.

### C.6 Scopes

| Term | Meaning |
|---|---|
| **Scope** | the region of the program where a declaration of a name is visible |
| **Static (lexical) scoping** | the binding is determined by the **textual structure** of the program, at **compile time**. Used by C, Java, Python and almost everything modern |
| **Dynamic scoping** | the binding is determined by the **calling sequence** at **runtime** |
| **Shadowing** | an inner declaration hides an outer one with the same name |
| **Scope resolution rule** | search the **innermost** enclosing scope first, then work **outward** to the global scope |

Implementation: the symbol table is kept as a **stack of scopes**, or a chain of hash tables. Entering a block pushes a new table, leaving the block pops it. Lookup walks the stack from top to bottom, which is exactly "innermost first, then outward".

### C.7 Symbol table, beyond the notes

**Operations:** `insert` (add a name and its attributes), `lookup` (find a name), and for scoped tables, `enter scope` and `exit scope`.

**Implementations:** linear list (simple, slow), **hash table** (the usual choice, fast lookup), binary search tree.

The notes already give you the attributes stored. Add to that: the symbol table is **created and populated during lexical and syntax analysis**, and **used heavily by semantic analysis and code generation**.

### C.8 Type checking

| Term | Meaning |
|---|---|
| **Static type checking** | done at **compile time**, during semantic analysis |
| **Dynamic type checking** | done at **runtime** |
| **Strongly typed language** | type rules are enforced strictly, few implicit conversions |
| **Weakly typed language** | permissive, many implicit conversions |
| **Type coercion** | the compiler **implicitly** converts one type to another, for example int to float |
| **Type casting** | the programmer **explicitly** requests a conversion |
| **Type system** | the set of rules assigning types to expressions |

Connect it back to the notes: the `inttofloat(60)` line in the TAC example on page 14 is a **coercion** that the semantic analyser inserted after type checking.

Benefit of type checking, in the exam's words: it **detects and prevents type-related errors before execution**, which improves reliability.

---

## PART D: METHOD FOR THE FILL-IN AUTOMATA QUESTIONS

Three of the five Part 3 questions used this exact template:

> FA: States: {...} · Alphabet: {...} · Start: ... · Accepting: {...} · Transitions: δ(...)=..., ... Equivalent regular expression: Fill in the blank: ______

### Six steps that always work

1. **Sketch it.** Draw circles for the states, an incoming arrow on the start state, a double circle on each accept state, and one labelled arrow per transition. Ten seconds of drawing prevents most mistakes.
2. **Mark the dead ends.** `δ(x, y) = ∅` means **no transition exists**. If the machine is in state x and reads y, the string is rejected immediately. A state whose transitions are all ∅ is a dead end, so nothing can follow it.
3. **List every path from start to an accept state.** The symbols along the path, in order, spell the core of the expression.
4. **Turn self-loops into stars.** A self-loop labelled `a` at a state becomes `a*` at that point in the expression. A self-loop labelled `a` and `b` becomes `(a∪b)*`.
5. **Turn alternative paths into unions.** Two different routes to the same place join with `∪`.
6. **Test three strings.** One that should be accepted, one that should be rejected, and **ε**. The ε test is where most errors show up.

### The ε test, spelled out

Ask: is the start state also an accept state?
- **Yes** → your expression must generate ε. It probably starts with something starred.
- **No** → your expression must **not** generate ε. `a*` would be wrong, `aa*` is right.

That single check is the difference between `a*` and `aa*`, which is exactly what Q7 was testing.

### Reference table of shapes

| Machine shape | Regular expression |
|---|---|
| start ──a──▶ ((accept)), accept has no outgoing transitions | `a` |
| start ──a──▶ ((accept)), accept loops on a | `aa*` |
| start loops on a, ──b──▶ ((accept)) | `a*b` |
| start loops on a, ──b──▶ ((accept)) which loops on a and b | `a*b(a∪b)*` |
| start is itself accepting and loops on a | `a*` |
| start ──a──▶ s1 ──a──▶ s2 ──a──▶ back to start, start accepting | `(aaa)*` |
| start ──0──▶ mid ──1──▶ ((accept)), no other transitions | `01` |
| start ──0 or 1──▶ ((accept)), no other transitions | `0∪1` |
| two states toggling on 0, looping on 1, start accepting | `1*(01*01*)*` (even number of 0s) |
| start loops on 0 and 1, ──1──▶ s2 ──0,1──▶ ((accept)) | `(0∪1)*1(0∪1)` |

### Subset construction, the four-step drill

For the "convert this NFA to a DFA" MCQs:

1. **Start** with the set containing only the NFA start state, `{q0}`. (If the NFA has ε-transitions, include everything reachable from q0 by ε alone. This is called the ε-closure.)
2. For each set you have, and each **input symbol**, compute the **union** of the NFA transitions from every state in the set. That union is a new DFA state.
3. **Repeat** until no new sets appear.
4. **Accept** any set that **contains at least one NFA accept state**.

Two facts that answer questions on their own:
- An n-state NFA gives a DFA with **at most 2ⁿ** states.
- Not all 2ⁿ subsets are usually **reachable**. Options that say "all subsets" are almost always the wrong answer.

---

## PART E: PRACTICE PAPER A

Same structure as the sample: 3, 3, 5, 3, 3, 2. Cover the answers.

### Part 1: Introduction to Compilers

**A1.** Which phase of the compiler groups characters into lexemes?
A. Syntax analysis  B. Lexical analysis  C. Semantic analysis  D. Code generation

**A2.** (True/False) A compiler translates the entire source program before execution, while an interpreter executes it statement by statement.

**A3.** (Fill) The ________ part of a compiler, also called the front end, breaks the source program into constituent pieces and imposes a grammatical structure on them.

### Part 2: Interpreter and Compiler Construction Tools

**A4.** Which of the following is a parser generator?
A. Lex  B. Flex  C. Yacc  D. grep

**A5.** (True/False) An interpreter produces a separate executable file as its output.

**A6.** (Fill) ________ is a compiler infrastructure whose C and C++ front end is called Clang.

### Part 3: Lexical Analysis

**A7.** (Fill) FA: States {q0,q1}; Alphabet {a,b}; Start q0; Accepting {q1}; Transitions δ(q0,a)=q0, δ(q0,b)=q1, δ(q1,a)=q1, δ(q1,b)=q1. Equivalent regular expression: ________

**A8.** (Fill) FA: States {p0,p1}; Alphabet {0,1}; Start p0; Accepting {p0}; Transitions δ(p0,0)=p1, δ(p0,1)=p0, δ(p1,0)=p0, δ(p1,1)=p1. Equivalent regular expression describes strings with an ________ number of 0s.

**A9.** Convert the following NFA to a DFA. NFA: States {q0,q1}; Alphabet {0,1}; Start q0; Accepting {q1}; δ(q0,0)={q0,q1}, δ(q0,1)={q0}, δ(q1,0)=∅, δ(q1,1)=∅.
A. DFA with 2 reachable states {q0} and {q0,q1}, accepting {q0,q1}
B. DFA with 3 states, accepting {q1} only
C. DFA with 4 states covering all subsets, accepting {q0}
D. DFA with 1 state {q0}, both start and accepting

**A10.** (True/False) Every deterministic finite automaton is also a nondeterministic finite automaton.

**A11.** Which regular expression describes all binary strings of even length?
A. (0 ∪ 1)\*  B. ((0 ∪ 1)(0 ∪ 1))\*  C. (00 ∪ 11)\*  D. (0 ∪ 1)(0 ∪ 1)\*

### Part 4: Syntax Analysis

**A12.** A grammar is ambiguous if:
A. it contains left recursion
B. some string in its language has more than one parse tree
C. it has more than one non-terminal
D. it uses the vertical bar shorthand

**A13.** (True/False) A rightmost derivation expands the rightmost non-terminal first.

**A14.** (Fill) The class of machines that recognises context-free languages is called ________ automata.

### Part 5: Intermediate Code Generation

**A15.** In three address code, each assignment has at most how many operators on the right hand side?
A. 0  B. 1  C. 2  D. 3

**A16.** (Fill) A ________ representation of intermediate code has three fields, operator, arg1 and arg2, and refers to earlier results by position number.

**A17.** (True/False) Generating code for an abstract machine and then translating it for a specific machine is more modular than generating code for a specific machine directly.

### Part 6: Other

**A18.** Which set of attributes does the symbol table typically store for a variable?
A. its type, the amount of memory it occupies, and its scope
B. its parse tree
C. its machine opcode
D. its regular expression

**A19.** (True/False) Type coercion is the automatic conversion of one type to another performed by the compiler.

### Answer key, Paper A

| Q | Answer | One-line reason |
|---|---|---|
| A1 | **B** | Grouping characters into lexemes is the definition of lexical analysis |
| A2 | **True** | The defining difference between the two |
| A3 | **analysis** | Analysis part = front end, page 5 and 7 of the notes |
| A4 | **C** | Lex and Flex are scanner generators; grep is a search tool |
| A5 | **False** | An interpreter executes directly, producing no object program |
| A6 | **LLVM** | Clang is LLVM's C and C++ front end |
| A7 | **a\*b(a ∪ b)\*** | Loop on a at q0, one b to reach q1, then anything |
| A8 | **even** | p0 accepting, each 0 toggles state, so an even count returns to p0. Regex: `1*(01*01*)*` |
| A9 | **A** | {q0} on 0 gives {q0,q1}, on 1 gives {q0}; {q0,q1} on 0 gives {q0,q1}, on 1 gives {q0}. Only 2 reachable. Language is strings ending in 0 |
| A10 | **True** | Nondeterminism is a generalisation of determinism, page 42 |
| A11 | **B** | Each `(0 ∪ 1)(0 ∪ 1)` adds exactly two symbols. C only allows doubled pairs, which is a smaller language |
| A12 | **B** | Ambiguity means more than one parse tree for a single string |
| A13 | **True** | |
| A14 | **pushdown** | Page 51 of the notes |
| A15 | **B** | At most one operator, which fixes the order of operations |
| A16 | **triple** | Quadruples have four fields including result; triples have three |
| A17 | **True** | Option 2 on page 13 of the notes |
| A18 | **A** | Type, memory, scope, page 9 |
| A19 | **True** | Coercion is implicit; casting is explicit |

---

## PART F: PRACTICE PAPER B

### Part 1: Introduction to Compilers

**B1.** Which of the following is **not** one of the six phases of a compiler?
A. Lexical analysis  B. Linking  C. Code optimisation  D. Semantic analysis

**B2.** (True/False) The loader resolves references in each object file to external variables and procedures declared in other files.

**B3.** (Fill) The first Fortran compiler is reported to have taken ________ man-years of effort to write.

### Part 2: Interpreter and Compiler Construction Tools

**B4.** Which statement best describes a just-in-time compiler?
A. It compiles the program completely before distribution
B. It compiles code at runtime, during program execution
C. It interprets bytecode line by line and never compiles
D. It only removes comments and whitespace

**B5.** (True/False) Lex and Flex are used to generate lexical analysers.

**B6.** (Fill) A ________ executes source code statement by statement without producing a separate object program.

### Part 3: Lexical Analysis

**B7.** (Fill) FA: States {q0,q1,q2}; Alphabet {a}; Start q0; Accepting {q0}; Transitions δ(q0,a)=q1, δ(q1,a)=q2, δ(q2,a)=q0. Equivalent regular expression: ________

**B8.** (Fill) FA: States {s0,s1,s2}; Alphabet {0,1}; Start s0; Accepting {s2}; Transitions δ(s0,1)=s1, δ(s1,0)=s2, all other transitions = ∅. Equivalent regular expression: ________

**B9.** An NFA with n states, converted by the subset construction, can produce a DFA with at most how many states?
A. n  B. 2n  C. n²  D. 2ⁿ

**B10.** (True/False) In an NFA, a state may have more than one transition arrow for the same input symbol.

**B11.** Which of the following languages is **not** regular?
A. binary strings ending in 01
B. binary strings of even length
C. { 0ⁿ1ⁿ : n ≥ 0 }
D. binary strings containing 101

### Part 4: Syntax Analysis

**B12.** The terms top-down and bottom-up refer to:
A. the order in which compiler phases run
B. the process of parse tree construction
C. the direction of code generation
D. the order of symbol table lookup

**B13.** (True/False) The parser uses the first component of each token, the token name, to build the tree.

**B14.** (Fill) In a context-free grammar written as the 4-tuple (V, Σ, R, S), the symbol S is called the ________ variable.

### Part 5: Intermediate Code Generation

**B15.** Which of the following is a common code optimisation?
A. removing unreachable sections of code
B. adding more registers to the processor
C. increasing the number of tokens produced
D. expanding macros

**B16.** (Fill) In quadruple representation the four fields are operator, arg1, arg2 and ________.

**B17.** (True/False) The output of the code generation phase is usually assembly language or relocatable machine code.

### Part 6: Other

**B18.** Under static scoping, the binding of an identifier is determined by:
A. the sequence of function calls at runtime
B. the textual structure of the program at compile time
C. the order in which symbol table entries were inserted
D. the size of the identifier's type

**B19.** (True/False) Type checking is carried out during semantic analysis.

### Answer key, Paper B

| Q | Answer | One-line reason |
|---|---|---|
| B1 | **B** | Linking happens after compilation; the linker is a separate tool, not a phase |
| B2 | **False** | That is the **linker**. The loader calculates absolute addresses |
| B3 | **18** | Page 2 of the notes |
| B4 | **B** | JIT means translation during execution |
| B5 | **True** | |
| B6 | **interpreter** | |
| B7 | **(aaa)\*** | Three a's return you to the accepting start state, and ε is accepted since q0 is the start and accept state |
| B8 | **10** | One path only, read 1 then 0, then everything is ∅ |
| B9 | **D** | Each DFA state is a subset of the n NFA states, and there are 2ⁿ subsets |
| B10 | **True** | That is precisely what nondeterminism means |
| B11 | **C** | 0ⁿ1ⁿ needs unbounded counting, which a finite automaton cannot do, page 48 |
| B12 | **B** | Page 50 of the notes, word for word |
| B13 | **True** | Page 10 of the notes |
| B14 | **start** | |
| B15 | **A** | One of the four listed on page 16. Macro expansion is preprocessing, not optimisation |
| B16 | **result** | |
| B17 | **True** | Page 16 of the notes |
| B18 | **B** | Static means lexical, determined by program text |
| B19 | **True** | |

---

## PART G: EXTRA QUESTION BANK BY PART

Quick-fire. Answer, then check the key at the end of this section.

### Part 1 style

1. Which phase reports an error for the statement `int x = "hello";` in a strongly typed language?
2. (T/F) The analysis part of a compiler is also called the back end.
3. (Fill) The compiler phase that reads the stream of characters and groups them into meaningful sequences is called ________ analysis.
4. Which of these is produced by the assembler? A. tokens B. parse tree C. relocatable machine code D. absolute machine code
5. (T/F) Each of the six phases of a compiler interacts with a symbol table and an error handler.

### Part 2 style

6. (Fill) The Unix command that searches text using regular expressions is ________.
7. (T/F) Yacc generates a bottom-up parser.
8. Which of these compiles Java source into a form executed by a virtual machine? A. Clang B. javac C. Flex D. Bison
9. (Fill) Java source code is compiled into ________, which the JVM then executes.
10. (T/F) A compiler construction toolkit removes the need to understand compiler theory.

### Part 3 style

11. (Fill) FA: States {q0,q1}; Alphabet {0,1}; Start q0; Accepting {q0}; δ(q0,0)=q0, δ(q0,1)=q1, δ(q1,0)=q1, δ(q1,1)=q1. Regular expression: ________
12. (Fill) FA: States {a0,a1}; Alphabet {x}; Start a0; Accepting {a0}; δ(a0,x)=a1, δ(a1,x)=a0. Regular expression: ________
13. Which regular expression describes binary strings containing at least two 1s?
A. (0∪1)\*11(0∪1)\*  B. (0∪1)\*1(0∪1)\*1(0∪1)\*  C. 1\*1\*  D. (0∪1)\*1(0∪1)
14. (T/F) A DFA may contain ε-transitions.
15. (Fill) In the 5-tuple definition of a finite automaton, the component F is the set of ________ states.
16. (T/F) DFAs and NFAs recognise exactly the same class of languages.
17. (Fill) The transition function of an NFA has the form δ : Q × ________ → ________.
18. Which of these strings is accepted by an automaton whose language is `(0∪1)*1(0∪1)(0∪1)`? A. 0110 B. 0011 C. 1000 D. 0001

### Part 4 style

19. (Fill) A derivation that expands the rightmost non-terminal first is also called a ________ derivation.
20. (T/F) Every regular language is also a context-free language.
21. Which parser family produces a leftmost derivation? A. LR B. LALR C. LL D. SLR
22. (Fill) In a CFG rule A → w, the symbol A must be a single ________.
23. (T/F) A grammar with left recursion causes problems for top-down parsers.

### Part 5 style

24. (Fill) The most widely used intermediate language mentioned in the notes is ________ Address Code.
25. Convert `x = a - b * c` to three address code. How many temporaries are needed at minimum?
26. (T/F) A triple has an explicit result field.
27. (Fill) A directed acyclic graph is a useful intermediate form because it exposes common ________.
28. Which of these is considered during code generation? A. tokenisation B. register management C. macro expansion D. lexeme matching

### Part 6 style

29. (T/F) An inner declaration of a name hides an outer declaration of the same name. This is called shadowing.
30. (Fill) A symbol table is most commonly implemented using a ________ table for fast lookup.
31. (T/F) Dynamic type checking happens at compile time.
32. Which is checked by the semantic analyser? A. a missing semicolon B. an undeclared variable C. an unclosed string literal D. an illegal character

### Key to the extra bank

| # | Answer |
|---|---|
| 1 | semantic analysis |
| 2 | **False**, it is the front end |
| 3 | lexical |
| 4 | **C**, relocatable machine code |
| 5 | **True** |
| 6 | grep |
| 7 | **True**, Yacc produces an LALR(1) parser, which is bottom-up |
| 8 | **B**, javac |
| 9 | bytecode |
| 10 | **False** |
| 11 | `0*`, because reaching q1 is fatal (q1 is a non-accepting sink) so only strings of zeros are accepted |
| 12 | `(xx)*`, an even number of x's including zero |
| 13 | **B** |
| 14 | **False** |
| 15 | accept (or final) |
| 16 | **True** |
| 17 | Σ_ε → P(Q) |
| 18 | **A**, 0110 has a 1 in the third position from the right |
| 19 | canonical |
| 20 | **True** |
| 21 | **C**, LL |
| 22 | variable (non-terminal) |
| 23 | **True** |
| 24 | Three |
| 25 | Two: `t1 = b * c`, `t2 = a - t1`, `x = t2` |
| 26 | **False**, that is a quadruple |
| 27 | subexpressions |
| 28 | **B**, register management |
| 29 | **True** |
| 30 | hash |
| 31 | **False**, at runtime |
| 32 | **B**, an undeclared variable |

---

## PART H: EXAM DAY TACTICS

### Before you start

Open the Table of Contents and note that Part 3 has 5 questions. Those are worth the most and take the longest. Do not spend so long on Part 1 that you rush them.

### For a fill-in box

- Answer in **one word or a very short phrase**. The grader is likely matching text.
- Use lowercase unless it is a proper noun such as LLVM or Lex.
- For a regular expression, type the **simplest standard form**. Use `*` for star. Use `|` or `U` if you cannot type `∪`. Do not add spaces or commentary.
- Do not write "the answer is". Just the answer.

### For a true/false

The words **every, always, only, never** are where the traps live. Test the statement against one counterexample before answering. Statements swapping two related things (linker/loader, NFA/DFA, coercion/casting, static/dynamic) are the second most common trap.

### For a machine-to-regex question

Draw it. Do the ε test. Then answer. Thirty seconds of drawing beats two minutes of staring at the text.

### For a "convert the NFA" MCQ

Build the subset table for the reachable states only. Then check the options against **two** things: the number of reachable states, and the description of which states accept. If no option matches both, pick the one that gets the **accept-state rule** right, because that is what is being examined.

### Final sweep

Use the Question Progress panel on the right. Every question should show as answered before you exit. Save before you leave.
