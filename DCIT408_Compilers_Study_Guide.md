# DCIT 408 (Compilers): Complete Study Guide

Built page by page from Mr. Agyemfra's handwritten notes, pages 1 to 55.
Exam format expected: objectives (MCQ), true/false, fill-in-the-blanks. Heavy on DFA and NFA.
Likely task type: he describes a machine, you type or select the regular expression that recognises it, or you complete its formal definition.

---

## PART 0: DECODER FOR HIS SHORTHAND

You cannot answer a question you cannot read. These are every abbreviation and symbol in the notes.

### Handwriting shorthand

| What he writes | What it means |
|---|---|
| ff | "following" (as in "the ff conditions") |
| eg | for example |
| ie | that is |
| no. or nᵒ | number |
| NB | note well |
| LHS / RHS | left-hand side / right-hand side of a grammar rule |
| Cmplx | complex |
| Prep | preposition |

### Technical abbreviations

| Abbreviation | Full form | Where it appears |
|---|---|---|
| TAC | Three Address Code | intermediate code generation, p.14 |
| ISA | Instruction Set Architecture | p.17 |
| RISC | Reduced Instruction Set Computer | p.20 |
| CISC | Complex Instruction Set Computer | p.20 |
| FSM | Finite State Machine | p.36 |
| DFA | Deterministic Finite Automaton (plural: automata) | p.42 |
| NFA | Nondeterministic Finite Automaton | p.42 |
| CFG | Context-Free Grammar | p.51 |
| CFL | Context-Free Language | p.51 |
| PDA | Pushdown Automaton | p.51 |
| PMI | Principle of Mathematical Induction | p.28 |
| Lex / Flex | lexical analyser generator tools | p.30 |

### Mathematical symbols

| Symbol | Read it as | Meaning |
|---|---|---|
| Σ (sigma) | "the alphabet" | a finite non-empty set of symbols |
| ε (epsilon) | "epsilon" | the empty string, length 0 |
| δ (delta) | "delta" | the transition function |
| Q | | the set of states |
| q₀ | "q nought" | the start state |
| F | | the set of accept (final) states |
| \|w\| | "length of w" | number of symbols in string w |
| Σ* | "sigma star" | set of ALL strings over Σ, including ε |
| Σ⁺ | "sigma plus" | all strings over Σ except ε |
| Σⁿ | | all strings over Σ of length exactly n |
| Σ_ε | "sigma epsilon" | Σ ∪ {ε}, used only for NFAs |
| ∪ | union | "or" |
| ∘ | concatenation | glue two strings together |
| * | Kleene star | zero or more repetitions |
| ⊆ | subset of | every element of the left is in the right |
| ∈ | element of | membership |
| ∅ | empty set | the set with nothing in it |
| P(Q) or ρ(Q) | power set of Q | the set of ALL subsets of Q |
| ℕ, ℤ, ℝ, ℤ⁺ | naturals, integers, reals, positive integers | |
| ⟹ | derives (one step) | used in grammar derivations |
| → | rewrites to | used in grammar rules |
| \| | "or" in a grammar rule | A → 0A1 \| B means two rules |

**Trap to memorise now:** Σ* includes ε. Σ⁺ does not. The notes state it as Σ* = Σ⁺ ∪ {ε} on page 33.

---

## PART 1: WHAT A COMPILER IS (pages 1 to 3)

### The definition, word for word

A compiler is a computer program that translates computer code written in one programming language (the **source language**) into another programming language (the **target language**).

The name "compiler" is primarily used for programs that translate source code from a **high-level** programming language to a **lower-level** programming language (assembly language, object code, or machine code) to create an **executable program**.

Plain English: a compiler is a translator. You write in a language humans find easy. The machine only understands one language. The compiler bridges the two.

### Why study compilers (two reasons he gives)

1. The concepts used in designing and constructing compilers are useful in many other areas of computing, for example **Natural Language Processing**.
2. We need knowledge of compilation techniques to deal with the recognition and translation of many other types of languages: **CGI languages, Typesetting (TeX), Page Description Languages**.

Memorise the example he names for reason 1. "Natural Language Processing" is the exact phrase likely to appear as an MCQ option.

### History timeline (page 2)

| Decade | What happened |
|---|---|
| 1940s | Early stored-program computers were programmed in **machine language**. Later, **assembly languages** were developed where machine instructions and memory locations were given **symbolic forms**. |
| 1950s | Early high-level languages developed, for example **Fortran**. More problem-oriented than assembly, but early Fortran still had many **machine-dependent features**. Compilation was not well understood. The first Fortran compiler took **18 man-years** of effort to write. |
| 1960s onwards | The study of the **parsing problem for context-free languages** during the 60s and 70s led to efficient algorithms for recognising context-free languages. These algorithms and tools are central to compiler construction today. The theory of **finite state machines and regular expressions** (which corresponds to **Chomsky's regular languages**) proved useful for describing the **lexical structure** of programming languages. |
| From Algol-60 | High-level languages became more problem-oriented and machine independent. Theory and tools available today make compiler construction a manageable task even for complex languages. |

**High-value numbers and names:** 18 man-years, Fortran, Algol-60, Chomsky.

**High-value pairing to memorise:**
- Finite state machines and regular expressions describe the **LEXICAL** structure.
- Context-free languages and parsing describe the **SYNTACTIC** structure.

That pairing is the single most quotable line in the history section and maps directly onto phase 1 and phase 2 of the compiler.

---

## PART 2: THE CONTEXT OF A COMPILER (pages 3 to 5)

The compiler does not work alone. Four other programs surround it.

```
skeletal          source            assembly        relocatable       absolute
source   ──▶ [Preprocessor] ──▶ [Compiler] ──▶ [Assembler] ──▶ [Link/Load] ──▶ machine
program           program           program      machine code       Editor        code
```

The precise chain from page 3:

`skeletal source program → Preprocessor → source program → Compiler → assembly program → Assembler → relocatable machine code → Link/Load Editor → absolute machine code`

### The five tools and what each one does

| Tool | Job | Input | Output |
|---|---|---|---|
| **Preprocessor** | Performs usually simple operations on source files **prior to** compilation | skeletal source program | source program |
| **Compiler** | Translates source to assembly | source program | assembly program |
| **Assembler** | Translates assembly to machine code | assembly program | relocatable machine code |
| **Linker** | Combines object code from many source programs plus standard library functions and OS resources; **resolves references** to external variables and procedures declared in other files | many object files | one combined program |
| **Loader** | Calculates **absolute addresses** for memory locations and amends the code to use them | relocatable machine code | absolute machine code |

### Preprocessor operations (exactly two given)

**(a) Expanding macros.** A macro is a shorthand notation for a longer construct. C example from the notes:
```c
#define foo(x,y) (3*x + y*(2+x))
```
When `foo` is used later in the program it is expanded by the preprocessor.

**(b) Inserting named files.** C example:
```c
#include "header.h"
#include <stdio.h>
```
These lines are replaced with the contents of those files.

### The one distinction he will test

Both the linker and the loader deal with addresses, so they are the perfect true/false trap.

- **Linker: resolves references.** It answers "where is the function `printf` that this file calls?"
- **Loader: calculates absolute addresses.** It answers "the operating system just gave me memory starting at address 5000, so recompute every address relative to 5000."

Why relocatable code exists in the first place (page 5): compilers, assemblers and linkers usually produce code whose memory references are made **relative to an undetermined starting location** that can be anywhere in memory. That is what "relocatable machine code" means.

**Mnemonic for the chain: P C A L L.** Preprocessor, Compiler, Assembler, Linker, Loader.

---

## PART 3: THE PHASES OF A COMPILER (pages 5 to 7)

### Two parts, or three

Up to this point the compiler was treated as a **single black box**. Opening it up gives **2 main parts**:

- The **analysis** part, also known as the **front end** of the compiler.
- The **synthesis** part, also known as the **back end** of the compiler.

Some books split it into **3 parts**, referred to as the **multi-pass compiler**. This comes equipped with a middle part, the **middle end**.

### What each part does (page 7)

**Analysis (front end):** breaks up the source program into constituent pieces and imposes a **grammatical structure** on them. It uses this structure to create an **intermediate representation** of the source program. If it detects that the source program is either syntactically ill-formed or semantically unsound, informative messages are provided to assist the programmer in taking corrective actions.

**Synthesis (back end):** constructs the desired **target program** from the **intermediate representation** and the **information stored in the symbol table**.

### Six phases

The compiler has **6 main phases** grouped into the 2 or 3 passes described. **Each of these 6 phases interacts with a symbol table and an error handler.**

| # | Phase | Also called | Part |
|---|---|---|---|
| 1 | Lexical Analyser | scanning | analysis / front end |
| 2 | Syntax Analyser | parsing | analysis / front end |
| 3 | Semantic Analyser | | analysis / front end |
| 4 | Intermediate Code Generator | | synthesis (middle) |
| 5 | Code Optimiser | | synthesis (middle) |
| 6 | Code Generator | | synthesis / back end |

Note from page 6: there are various variants to this model but **the essential elements remain the same**.

**Mnemonic: Lazy Students Sometimes Ignore Ordinary Compilers.**
Lexical, Syntax, Semantic, Intermediate, Optimiser, Code generator.

**Two numbers to have automatic: 6 phases, 2 (or 3) parts.**

---

## PART 4: EACH PHASE IN DETAIL

### 4.1 Lexical Analysis (pages 7, 8, 30 to 32)

The **first** phase, known as lexical analysis or **scanning**. The lexical analyser reads the **stream of characters** that make up the source program and groups the characters into meaningful sequences called **lexemes**.

Three terms that get confused constantly. This is a guaranteed exam item.

| Term | Definition from the notes | Think of it as |
|---|---|---|
| **Token** | A pair consisting of a **token name** and an **optional attribute value**. The token name is an abstract symbol representing a kind of lexical unit, for example a particular keyword or a sequence of input characters denoting an identifier. **The token names are the input symbols the parser processes.** | the category, plus a tag |
| **Pattern** | A **description of the form** that the lexemes of a token may take. Also: a **rule that specifies when** a sequence of characters from the input constitutes a token. | the rule |
| **Lexeme** | A **sequence of characters** in the source program that **matches the pattern** for a token and is identified by the lexical analyser as an **instance of a token**. | the actual text |

Worked example (not in the notes, but this is how they will test it). For the line `total = count + 42;`

| Lexeme (actual text) | Pattern it matched | Token produced |
|---|---|---|
| `total` | letter followed by letters or digits | ⟨id, pointer to symbol table entry⟩ |
| `=` | the character `=` | ⟨assign⟩ |
| `count` | letter followed by letters or digits | ⟨id, pointer to symbol table entry⟩ |
| `+` | the character `+` | ⟨plus⟩ |
| `42` | one or more digits | ⟨number, 42⟩ |
| `;` | the character `;` | ⟨semicolon⟩ |

Key sentence to memorise: **for each lexeme, the lexical analyser produces as output a token that is passed on to the subsequent phase for syntax analysis.**

For a keyword as a token, the pattern is **just the sequence of characters that form the keyword**. For identifiers and some other tokens, the pattern is a **more complex structure matched by many strings**. (p.32)

#### The role of the lexical analyser (page 30)

Its main task is to **read input characters of the source program, group them into lexemes, and produce as output a sequence of tokens** for each lexeme in the source program. The stream of tokens is then sent to the parser for syntax analysis.

#### How it talks to the parser (page 31)

Commonly the interaction is implemented by having the **parser call the lexical analyser**. The call is suggested by the command **`getNextToken()`**. That call causes the lexical analyser to read characters from its input until it can identify the next lexeme and produce for it the next token, which it **returns to the parser**.

```
Source                          token
Program ──▶ [Lexical Analyser] ─────────▶ [Parser] ──▶ to semantic analysis
                    ▲                        │
                    └──── getNextToken() ────┘
                    │                        │
                    └────▶ [Symbol Table] ◀──┘
```

**Direction matters.** The parser is the caller. The lexical analyser is the callee. The lexical analyser does not push tokens at the parser; the parser pulls them one at a time. Expect a true/false on this.

#### The cascade of two processes (page 31)

Sometimes lexical analysers are divided into a cascade of **2** processes:

(a) **Scanning**, consisting of the simple processes **excluding tokenization**, such as deletion of comments and compaction of consecutive whitespaces into 1.
(b) The more complex **tokenization** process, which produces tokens from the output of the scanner.

#### Tools

There are compiler construction tools. A **lexical-analyser generator** is one of them, used to generate a lexical analyser. **See Lex and Flex.** (p.30)

Names to hold: **Lex** and **Flex** generate lexical analysers. (If a textbook MCQ offers Yacc or Bison, those generate **parsers**, not lexical analysers.)

### 4.2 Symbol Table Management (page 9)

A **symbol table** is a **data structure** containing all the **identifiers** (that is, names of variables, functions and so on) of the source program together with **all the attributes of each identifier**.

**Attributes for variables:**
1. its **type**
2. the **amount of memory** it occupies
3. its **scope**

**Attributes for functions and procedures:**
1. the **number and type of each argument** (if any)
2. the **method of passing each argument**
3. the **type of value returned** (if any)

**Main purpose:** to provide **quick and uniform access** to identifier attributes throughout the compilation process.

**When is it filled?** Information is usually put into the symbol table during the **lexical analysis and syntax analysis** phases.

Two fill-in-the-blank magnets: "quick and uniform access", and "lexical analysis and syntax analysis".

Mnemonic for variable attributes: **T M S**. Type, Memory, Scope.

### 4.3 Syntax Analysis (pages 10, 11)

The **second** phase, syntax analysis or **parsing**.

The parser uses the **first component of the tokens** produced by the lexical analyser to create a **tree-like representation** that depicts the **grammatical structure** of the token stream.

Note carefully: the parser uses the **first component** of the token. The token is a pair (name, optional attribute). The first component is the **token name**. The parser does not care about the attribute value. That is a precise, testable detail.

A typical representation is a **syntax tree** in which each **interior node represents an operation** and the **children of the node represent the arguments of the operation**.

Another name for the syntax tree is the **parse tree**. Technically a **syntax tree is a compact form of the parse tree**.

The sequence of tokens from the lexical analyser is grouped into **phrases**, each with an associated **phrase type**. A **phrase** is simply a **logical unit with respect to the "rules" of the source language**. All languages have rules used to determine whether or not sentences are well-formed.

The structure of a phrase is best thought of as a parse or syntax tree, which illustrates the **grouping of tokens into phrases**.

### 4.4 Semantic Analysis (page 11)

The **third** phase.

The semantic analyser uses the **syntax tree** and the **information stored in the symbol table** to check the source program for **semantic consistency with the language definition**.

It **gathers type information** and saves it in either the **syntax tree** or the **symbol table**.

In practice, semantic analysers are mainly concerned with **type checking** and **type coercion** based on the **type rules** of the language.

Example given: many programming language definitions require an **array index to be an integer**. The compiler must report an error if it finds a type different from integer.

**Type checking** = "are these types allowed together?"
**Type coercion** = "silently convert this int into a float so the operation works." That is exactly what `inttofloat(60)` is doing in the TAC example on page 14. The two topics link.

### 4.5 Error Handling (pages 12, 13)

Each of the **6 phases** (but **mainly the analysis phase**) can encounter errors. On detecting the error, the compiler must:

1. **Report** the error in a helpful way
2. **Correct** the error (if possible)
3. **Continue** processing (if possible) after the error, in a bid to uncover new errors

Mnemonic: **R C C.** Report, Correct, Continue. Note that steps 2 and 3 are both qualified with "if possible". Step 1 is not.

**Two main types of errors:**

```
                    ERRORS
                   /      \
            Syntactic    Semantic
             /     \
        Lexical  Grammatical
```

**Syntactic errors** are errors in the source program **text** that may be either **lexical or grammatical**.

- **(a) A lexical error** is a mistake in the **lexeme**. Example: typing `tehn` instead of `then`.
- **(b) A grammatical error** is one that **violates the (grammatical) rules of the language**. Example from the notes: `if (x==7) System.out.print("Good");` with a missing `)`.

**Semantic errors** are mistakes concerning the **meaning** of a program construct. They may be **type errors, logical errors or runtime errors**. Semantic errors are **much harder and sometimes impossible to detect**.

Memorise the difficulty ordering: syntactic errors are caught reliably, semantic errors are much harder and sometimes impossible. Any option claiming the reverse is false.

### 4.6 Intermediate Code Generation (pages 13 to 15)

After the analysis phase is complete, the source program has been decomposed into a **symbol table** and a **parse tree**, both of which may have been modified by the semantic analyser.

From this information we generate object code by one of **2 approaches**:

1. Generate code for a **specific machine**, or
2. Generate code for a **general or abstract machine**, then use further translators to turn the abstract code into code for specific machines.

**Option (2) is more modular and efficient**, provided the abstract machine language is simple enough to produce and analyse and easily translate into the required language.

One of the most widely used intermediate languages is **Three Address Code (TAC)**.

#### The TAC example from page 14

```
t1 = inttofloat(60)
t2 = id3 * t1
t3 = id2 + t2
id1 = t3
```

#### Three points worth noting about TAC instructions

1. **Each TAC assignment has at most one (1) operator on the right hand side.** This therefore **fixes the order in which operations are to be done**.
2. The compiler **must generate a temporary name** to hold the computed value by the TAC.
3. **Some three address codes have fewer than 3 operands.** See lines 1 and 4 of the example.

Check line 4: `id1 = t3` has only two names, so it has fewer than three operands. Line 1 likewise. This is a classic "which lines have fewer than 3 operands" MCQ, and the notes literally point at lines 1 and 4.

There are also TAC instructions for **addresses, pointers, arrays, procedure calls** and so on. (p.15)

#### Syntax-Directed Code Generation (page 15)

Code is generated by **recursively walking through a parse (syntax) tree**, hence the name.

Example: `z = x * y + x`, syntax tree:

```
        =
       / \
      z   +
         / \
        *   x
       / \
      x   y
```

Translated into TAC as:
```
Temp3 = x
Temp4 = y
Temp1 = Temp3 * Temp4
Temp2 = x
z = Temp1 + Temp2
```

Notice how the walk works: to compute `+`, you must first compute its left child `*`, and to compute `*` you must first fetch `x` and `y`. Each internal node becomes one TAC line with exactly one operator, which satisfies point 1 above.

### 4.7 Code Optimisation (page 16)

An **optimiser attempts to improve the time and space requirements of a program**. There are many ways to optimise code, but most are **expensive, space and time-wise implementation**.

**Common optimisations include:**
1. Removing **redundant identifiers**
2. Removing **unreachable sections** of code
3. Identifying **common subexpressions**
4. **Unfolding loops** and **eliminating procedures**

Mnemonic: **R U C U.** Redundant, Unreachable, Common subexpressions, Unfolding loops.

Small worked illustrations so the four terms stick:

- Redundant identifier: `x = 5; x = 7;` The first assignment is dead.
- Unreachable code: statements after a `return` in the same block.
- Common subexpression: `a = b*c + d; e = b*c + f;` compute `b*c` once.
- Unfolding (unrolling) a loop: replace a loop that runs 3 times with the 3 statements written out, removing loop overhead.

### 4.8 Code Generation (page 16)

The **final** stage or phase of the compiler is to **generate code for the target machine**.

In this phase we consider:
- **memory management**
- **register management**
- **machine specific optimisation**

**The output of this phase is usually assembly language or relocatable machine code.**

That last line is a prime fill-in-the-blank. Note it says assembly **or relocatable machine code**, not "absolute machine code". Absolute machine code only appears after the loader.

Example given on page 17: the TACs discussed could typically result in an **ARM assembly** program.

---

## PART 5: INSTRUCTION SET ARCHITECTURE SIDEBAR (pages 17 to 20)

He labels this "Some Useful Knowledge that Helps Put Things in Perspective". It is background from Computer Organisation and Architecture, but he wrote it out fully, so it is fair game for objectives.

### What is an ISA?

Two definitions, both worth knowing:

1. The instruction set architecture of a processor is the **part of the processor that is visible to the programmer or (compiler) writer** and serves as a **boundary between hardware and software**.
2. The ISA is the **structure of the computer that a machine language programmer (or compiler) must understand to write a correct (timing independent) program** for that machine.

The phrase "boundary between hardware and software" is the one to have ready.

### An instruction set specifies a processor's functionality

1. **What operations** it supports
2. **What storage mechanisms** it has and how they are accessed
3. **How the programmer or compiler communicates programs to the CPU**

### Aspects of instruction sets (four headings, A to D)

| Aspect | Contents |
|---|---|
| **A. Format** | Length, encoding |
| **B. Operations** | Data types, number and kind of operands |
| **C. Storage** | Internal: accumulator, stack, general purpose registers. Memory: address size, address modes, alignments |
| **D. Control** | Branch conditions, special support for procedures, etc. |

Mnemonic: **F O S C.** Format, Operations, Storage, Control.

### Categories of operations (page 19)

| Category | Instructions |
|---|---|
| Arithmetic and Logical | ADD, MULT, AND, OR, XOR, NOT |
| Data Transfer | MOVE, LOAD, STORE |
| Control | conditional branch, JUMP, CALL, RETURN |
| System | syscall, traps |

### Data types and sizes

**Fixed point (integer) data:**

| Size | Name |
|---|---|
| 8-bit | byte |
| 16-bit | half |
| 32-bit | word |
| 64-bit | doubleword |

**Floating point data:**
- 32 / 64 bit: IEEE754 single / double precision
- 80-bit: Intel proprietary

**Address size (also known as machine size):** a 32-bit machine means addresses are 32-bit. The key issue is **virtual memory size**: 32-bit gives **4 GB**, which is **not enough anymore**.

Have "IEEE754" and "4 GB" ready as fill-ins.

### Two main classes of ISA (page 20)

| Class | Full name | Examples |
|---|---|---|
| **RISC** | Reduced Instruction Set Computers | IBM PowerPC, Sun Sparc, MIPS, Alpha, **ARM** (formerly Advanced RISC Machines) |
| **CISC** | Complex Instruction Set Computers | **Intel x86**, PDP-11, VAX |

The classic trap: **ARM is RISC, x86 is CISC.** Never the other way round.

### The x86 instruction table (page 20)

He used the original 8086/8088 instruction set.

| Instruction | Meaning | OpCode |
|---|---|---|
| AAA | ASCII adjust AL after Addition | 0x37 |
| AAD | ASCII adjust AX after Division | 0xD5 |
| CALL | Call Procedure | 0x9A, 0xE8 |
| CBW | Convert Byte to Word | 0x98 |
| CLI | Clear Interrupt Flag | 0xFA |
| CLC | Clear Carry Flag | 0xF8 |
| CMP | Compare Operands | 0x38, ... |
| IMUL | Signed Multiply | 0x69, ... |

He wrote "the list goes on, further reading". Do not memorise hex opcodes. Do memorise the meanings, especially the pair that looks alike: **CLI clears the Interrupt flag, CLC clears the Carry flag.** If a matching question appears, that pair is the one designed to catch you.

---

## PART 6: MATHEMATICAL PRELIMINARIES (pages 21 to 25)

He assumes you already know these, which means he will test the definitions rather than teach them.

### Sets

A **set** is a **collection of well defined objects**.

| Set | Definition |
|---|---|
| ℕ (naturals) | {1, 2, 3, ...} |
| ℤ (integers) | {..., -3, -2, -1, 0, 1, 2, 3, ...} |
| ℝ | the set of real numbers |

**Subset:** A is a subset of B, written A ⊆ B, if **every element in A is also an element of B**.

Consequences he draws out:
- Every set is a subset of itself: A ⊆ A.
- Two sets A and B are **equal** if A ⊆ B **and** B ⊆ A. (Both directions. This proof pattern reappears for languages on page 34.)
- The **empty set is a subset of every set**: ∅ ⊆ A.

**Power set:** if B is a set, the power set of B, denoted P(B), is **the set of all subsets of B**:
```
P(B) = { A : A ⊆ B }
```

Worked example so this is concrete. If B = {0, 1}, then P(B) = { ∅, {0}, {1}, {0,1} }. Four elements. In general **|P(B)| = 2^|B|**. That doubling fact is why converting an NFA with n states can give a DFA with up to 2ⁿ states.

**One correction to carry into the exam.** The notes write "∅ ⊆ P(B) and B ⊆ P(B)". The first is true (the empty set is a subset of everything). The second, B ⊆ P(B), is generally **false**; the correct statement is **B ∈ P(B)** (B is an *element* of its own power set, being one of its own subsets). Check with B = {1}: P(B) = {∅, {1}}. Is 1 an element of P(B)? No. So B ⊄ P(B). If the exam offers "B ∈ P(B)", that is the correct one.

### Common set operations

| Operation | Definition |
|---|---|
| Union | A ∪ B = { x : x ∈ A **or** x ∈ B } |
| Intersection | A ∩ B = { x : x ∈ A **and** x ∈ B } |
| Difference | A − B = A \ B = { x : x ∈ A **and** x ∉ B } |
| Cartesian product | A × B = { (x, y) : x ∈ A **and** y ∈ B } |
| Complement | Aᶜ = { x : x ∉ A } |

### Relations and functions

A **binary relation** on 2 sets A and B is a **subset of A × B**.

A **function** f from A to B, denoted f : A → B, is a **binary relation having the property that for each element a ∈ A there is exactly one ordered pair whose first component is a**.

We say f(a) = b, or f maps a to b, or the image of a under f is b.
- **A is the domain** of f.
- **{ b ∈ B : there is an a ∈ A with f(a) = b } is the range** of f.

| Property | Definition |
|---|---|
| **One-to-one (injective)** | for any 2 distinct elements a, a′ ∈ A we have f(a) ≠ f(a′). Equivalently f(a) = f(a′) if and only if a = a′ |
| **Onto (surjective)** | for each element b ∈ B there exists an element a ∈ A such that f(a) = b |
| **Bijective** | both injective and surjective |

Why this is here and not decoration: **δ is a function.** For a DFA, δ : Q × Σ → Q. "For each element there is exactly one ordered pair" is precisely why a DFA has **exactly one** transition arrow per state per input symbol. When you get to page 43 and read "for every state of a DFA there's exactly one transition arrow for each input symbol", that is the function definition being cashed in.

He even writes the example on page 23: **δ : Q × Σ → Q, δ(q₀, 1) = q₁.**

### Graphs

A **graph** G = (V, E) is a **pair consisting of a set V whose elements are called vertices and a set E whose elements are called edges**.

| Term | Definition |
|---|---|
| **Degree of a vertex v**, deg(v) | the number of edges **incident on** v |
| **Path** | a sequence of vertices connected by edges |
| **Cycle** | a path that **begins and ends with the same vertex** |
| **Simple path** | a path **without repeated vertices** |
| **Connected graph** | there is a path between **every pair** of vertices |

Why this is here: a state diagram **is a graph**. He says it explicitly on page 36, that states are "represented as circles or better still nodes or vertices of a graph". Transitions are directed edges.

### Alphabets and strings (page 25, repeated on page 33)

| Term | Definition |
|---|---|
| **Alphabet** | a **finite** set whose elements are called **symbols**. Page 33 adds **non-empty** |
| Examples | Σ = {0, 1}, the **binary alphabet**. Σ = {a, b, c, ..., z}, the **English alphabet** |
| **String (word) over Σ** | a **finite sequence of symbols**, where each symbol is an element of Σ. Page 33: a **finite concatenation (or juxtaposition)** of symbols from Σ |
| **Length \|w\|** | the number of symbols contained in w |
| **Empty string ε** | the string having length zero, \|ε\| = 0 |
| **Language** | a **set of strings** over an alphabet Σ |

His example: if Σ = {0,1}, then 01, 1000, 101 and ε are strings over Σ having lengths **2, 4, 3, 0** respectively.

---

## PART 7: PROOF TECHNIQUES (pages 26 to 29)

He assigned several proofs as coursework, so definitions here can appear as objectives even if you are not asked to write a full proof.

**Theorem:** a statement that is true (and has been proved).
**Proof:** a **sequence of mathematical statements that form an argument to show that the theorem is true**.

The statements in a proof include:
- **axioms** (assumptions about the underlying mathematical structure)
- **hypotheses** of the theorem to be proved
- **previously proven theorems**

**Central question:** how do we go about proofs of theorems?
**His answer:** there is **no specified way** of coming up with a proof, but there are some **generic strategies**.

**Useful tips he gives:**
- Read and completely understand the statement of the theorem to be proved. **This is often the hardest part.**
- Try to work out a few simple cases of the theorem to get a good grip on it.
- Be patient, finding proofs takes time, express and write clearly and try to be as precise as you can.

### A. Direct proof

Approach the theorem directly.

**Theorem 1:** If n is an even positive integer, then n² is even as well.
**Proof:** Any even positive integer n can be written as n = 2k where k ∈ ℤ⁺.
n = 2k ⟹ n² = (2k)² = 4k²
4k² = 2(2k²) = 2m, where m = 2k² ∈ ℤ⁺
⟹ n² is even since n² is of the form 2m. ∎

### B. Proof by contradiction

Given a theorem with statement S, we **assume that S is false**. Then we **derive a contradiction**, which implies that the earlier assumption that the statement is false is inaccurate, hence the statement ought to be true.

**Theorem:** Let n be a positive integer. If n² is even, then n is even.
**Proof:** By contradiction, assume n² is even but n is odd. If n is odd, then n² is odd, which is a contradiction. ∎

Note the pairing. Theorem 1 and this theorem are converses of each other, and he deliberately proves one directly and one by contradiction to show the two techniques on nearly identical material.

### The Pigeon Hole Principle

"**If n+1 or more objects are placed into n boxes, then there is at least one box containing 2 or more objects.**"

Restated in set language: if A and B are 2 sets such that **|A| > |B|**, then there is **no injective (one-to-one) function from A to B**.

### C. Proof by the Principle of Mathematical Induction (PMI)

For each positive integer n, let P(n) be a mathematical statement that depends on n. To prove P(n) is true for all positive integers n:

1. **Basic step:** prove that **P(1)** is true.
2. **Induction step:** prove that **for all n ≥ 1, if P(n) is true, then P(n+1) is also true.**

Note the exact wording of step 2. You assume P(n) and prove P(n+1). The assumption is called the induction hypothesis.

**Reading assignment given:** read Sipser's book, pages 31 to 82, before the next class. That page range is Sipser's chapter on **Regular Languages**, which is everything in Part 9 to Part 13 below. Since the TA says questions may come from the textbook, that page range is the highest-value external reading.

---

## PART 8: LANGUAGES AND DECIDABILITY (pages 33 to 35)

### The formal string machinery

| Notation | Meaning |
|---|---|
| Σ | a finite **non-empty** set of symbols |
| \|w\| | length of string w |
| ε | the empty or null string, the **unique** string satisfying \|ε\| = 0 |
| Σ* | the set of **all** strings over Σ |
| Σⁿ | { w ∈ Σ* : \|w\| = n }, all strings of length exactly n |
| Σ⁺ | ∪ (n ≥ 1) Σⁿ, so **Σ\* = Σ⁺ ∪ {ε}** |
| xⁿ | x concatenated with itself n times, with the convention **x⁰ = ε** |

Concrete check with Σ = {0,1}:
- Σ⁰ = {ε}, one string
- Σ¹ = {0, 1}, two strings
- Σ² = {00, 01, 10, 11}, four strings
- In general |Σⁿ| = 2ⁿ for a binary alphabet, and |Σⁿ| = |Σ|ⁿ in general.

**A language over Σ is a set L ⊆ Σ*.**

Two languages L₁ and L₂ over a common alphabet Σ are **equal if they are equal sets**. Thus **L₁ = L₂ if and only if L₁ ⊆ L₂ and L₂ ⊆ L₁**. (Same double-inclusion pattern as sets on page 21.)

### Decidability

Given a language L over some alphabet Σ, a basic question is: **for each possible word w ∈ Σ*, can we effectively decide if w is a member of L or not?** We call this the **decision problem of L**.

The word "**effectively**" here implies that the mechanism by which we decide membership or non-membership must be **finitistic, deterministic and mechanical**, capable of being carried out by some computing agent or computational model.

Memorise those three adjectives as a set: **finitistic, deterministic, mechanical**.

**Formal definition.** A language L ⊆ Σ* is said to be **decidable** if there exists an algorithm such that for every w ∈ Σ*:
- (a) the algorithm **terminates with output "Yes"** when w ∈ L, and
- (b) the algorithm **terminates with output "No"** when w ∉ L.

If **no such algorithm exists** then L is said to be **undecidable**.

The load-bearing word is **terminates**. An algorithm that runs forever on some input does not decide the language.

His example: Let Σ = {0,1}. Let L be the (infinite) language L = { w ∈ Σ* | w = 0ⁿ1, n ∈ ℤ⁺ }.
So L = {01, 001, 0001, 00001, ...}: any number of zeros followed by a single 1.

### Four basic facts (memorise all four verbatim)

1. **Every finite language is decidable.** (Hence **every undecidable language is infinite**.)
2. **Not every infinite language is undecidable.**
3. **Programming languages are (usually) infinite but (always) decidable.**

Fact 1's parenthetical is the **contrapositive** of fact 1. Fact 2 blocks the naive converse. Together they form a perfect true/false cluster:

| Statement | Truth |
|---|---|
| Every finite language is decidable | TRUE |
| Every undecidable language is infinite | TRUE |
| Every infinite language is undecidable | FALSE |
| Programming languages are decidable | TRUE |

### Application to compilation

Languages may be **classified by the means by which they are defined**. Of interest to us are **regular languages** and **context-free languages**.

---

## PART 9: FINITE AUTOMATA AND REGULAR LANGUAGES (pages 35 to 39)

We introduce the class of languages known as **regular languages**. Informally these languages can be "**processed**" by **computational models with limited memory**. We begin with the simplest model, called the **finite state machine (FSM)** or **finite automata**.

"Limited memory" is the whole story of this topic. A finite automaton has only its current state as memory. That is why it cannot count arbitrarily high, which is exactly the argument used against 0ⁿ1ⁿ on page 48.

### The machine M₁ (page 36)

<img width="1536" height="532" alt="image" src="https://github.com/user-attachments/assets/b97e6134-b67f-44ac-8bf1-59d733f329a8" />


Reading the picture, in his own words:

- M₁ has **3 states**.
- The states are labelled q₁, q₂, q₃, represented as **circles**, or better still **nodes or vertices of a graph**.
- **q₁ is the start state**, indicated by the **arrow pointing at it from nowhere**.
- **q₂ is the accept state**, indicated by a **double circle**.
- The arrows going from one state to another are called **transitions**.

### How a finite automaton computes

M₁ receives the symbols of the input string **one by one from left to right**. After reading each symbol, M₁ moves from one state to the other **along the transition that has that symbol as its label**. When M₁ reads its **last** symbol it produces an output.

**The output is "Accept" if M₁ is in an accept state after reading the last symbol, otherwise "Reject".**

### The full trace of 1101 (page 37, memorise this shape)

| Step | Action | State after |
|---|---|---|
| 1 | Start | q₁ |
| 2 | Read **1**, follow transition from q₁ to q₂ | q₂ |
| 3 | Read **1**, follow transition from q₂ to q₂ | q₂ |
| 4 | Read **0**, follow transition from q₂ to q₃ | q₃ |
| 5 | Read **1**, follow transition from q₃ to q₂ | q₂ |
| 6 | **Accept**, because M₁ is in the accept state q₂ at the end of the input | |

### What M₁ recognises

Experimenting reveals it accepts **1, 01, 01010101**, and also **100, 0100, 110000**.

His stated description: **M₁ accepts all strings of the binary alphabet Σ = {0,1} that end with 1, or with an even number of zeros after a 1.**

How to see it yourself, which is what you need if he changes the machine slightly:
- You cannot leave q₁ without reading a 1. So the string must **contain at least one 1**.
- After the **last** 1 you are in q₂. Count the trailing zeros: 0 zeros keeps you at q₂ (accept), 1 zero puts you at q₃ (reject), 2 zeros returns you to q₂ (accept), 3 zeros back to q₃ (reject).
- So: **at least one 1, and an even number of zeros after the last 1.**

**Regular expression for M₁: (0 ∪ 1)\*1(00)\***
Equivalently Σ\*1(00)\*. Test it: `1` ✓, `100` ✓, `110000` ✓, `01010101` ✓, `10` ✗ (correct, since one trailing zero is odd), `000` ✗ (correct, no 1 at all).

### Formal mathematical definition (page 38) — memorise word for word

> A **finite automaton is a 5-tuple (Q, Σ, δ, q₀, F)** where
> 1. **Q** is a **finite set of states**, Q = { qᵢ : 1 ≤ i ≤ n }
> 2. **Σ** is a **finite set of symbols called the alphabet**
> 3. **δ : Q × Σ → Q** is the **transition function**
> 4. **q₀ ∈ Q** is the **start state**, and
> 5. **F ⊆ Q** is the **set of accept states**

**Accept states are sometimes referred to as final states.**

Mnemonic for the order: **Quiet Snakes Don't Quit Fighting.** Q, Σ, δ, q₀, F.

Watch the membership symbols, because he will blank them out:
- q₀ **∈** Q (the start state is one single state, so it is an element)
- F **⊆** Q (the accept states are a set of states, so it is a subset)

### M₁ described formally

M₁ = (Q, Σ, δ, q₁, F) where

1. Q = {q₁, q₂, q₃}
2. Σ = {0, 1}
3. δ is described by the table:

| δ | 0 | 1 |
|---|---|---|
| **q₁** | q₁ | q₂ |
| **q₂** | q₃ | q₂ |
| **q₃** | q₂ | q₂ |

4. q₁ ∈ Q is the start state
5. F = {q₂} ⊆ Q

**How to read the table:** rows are current states, columns are input symbols, the cell is the next state. δ(q₂, 0) = q₃.

**Sanity check for any DFA table: every cell must be filled with exactly one state.** No blanks, no sets. If a cell is blank or holds two states, it is not a DFA.

### Language of a machine

If A is the set of all strings that machine M accepts, we say **A is the "language of machine M"** and write **L(M) = A**. We say **M recognises A**.

Special case: **when a machine accepts no strings, we say it recognises the empty language.**

Careful with wording here. A machine always recognises **exactly one** language. If it accepts nothing, that language is ∅, not "no language".

### Formal definition of computation (page 39) — high-probability fill-in

> Let M = (Q, Σ, δ, q₀, F) be a finite state machine and let w = w₁w₂...wₙ be a string where each wᵢ is a symbol in Σ.
> **M accepts w if a sequence of states r₀, r₁, ..., rₙ in Q exists with the 3 conditions:**
> 1. **r₀ = q₀**
> 2. **δ(rᵢ, wᵢ₊₁) = rᵢ₊₁, for i = 0, 1, ..., n−1**
> 3. **rₙ ∈ F**

Read in English: (1) start at the start state, (2) each move must follow the transition function, (3) end in an accept state.

Note the sequence has **n+1 states** for an input of **n symbols**, because r₀ is the state before any symbol is read.

### The definition everything builds to

> **A language is called a regular language if there exists some finite automaton that recognises it.**

---

## PART 10: THE REGULAR OPERATIONS (pages 40, 41)

We define **three** operations on languages called the **regular operations** and use them to study the properties of regular languages. Let A and B be languages over the same alphabet.

| # | Operation | Definition |
|---|---|---|
| 1 | **Union** | A ∪ B = { x \| x ∈ A **or** x ∈ B } |
| 2 | **Concatenation** | A ∘ B = AB = { xy \| x ∈ A **and** y ∈ B } |
| 3 | **Star** | A\* = { x₁x₂...x_k \| **k ≥ 0** and each xᵢ ∈ A } |

**A\* is obtained by taking any finite number of strings in A and concatenating them. When k = 0 we have the element ε. This means ε ∈ A\*.**

That last sentence is a guaranteed true/false item. **ε is in A\* for every language A, even for A = ∅.**

Mnemonic: **U C S.** Union, Concatenation, Star. Note that star is **unary** (one language), the other two are **binary** (two languages).

### His worked example (page 40)

Let Σ = {a, b, c, ..., z}, A = {red, yellow}, B = {kofi, ama}.

- **A ∪ B = { red, yellow, kofi, ama }**
- **A ∘ B = AB = { redkofi, redama, yellowkofi, yellowama }**

Note the size: |A| = 2, |B| = 2, and |A ∘ B| = 4 = 2 × 2. Concatenation pairs every string of A with every string of B, in that order. **Order matters:** B ∘ A would be {kofired, kofiyellow, amared, amayellow}, a completely different set.

- **A\* = { ε, red, yellow, redyellow, yellowred, redred, yellowyellow, redredyellow, redyellowred, redyellowyellow, ... }**

An **infinite set of strings whose length is finite**, and the strings in A\* are formed by a **finite concatenation of elements from A**.

Another example: if Σ = {0,1}, then Σ\* is the set of **all binary strings including ε**.

### Alternate definition of star (page 41)

Define **A⁰ = {ε}** and for **k ≥ 1**, **Aᵏ = A · Aᵏ⁻¹** (the concatenation of the two languages A and Aᵏ⁻¹). Then

```
A* = ∪ (k = 0 to ∞) Aᵏ  =  A⁰ ∪ A ∪ A² ∪ A³ ∪ A⁴ ∪ ...
```

### Closure theorems

> **Theorem 2: The set of regular languages is closed under the union operation.** That is, if A and B are regular languages over the alphabet Σ, then A ∪ B over Σ is also a regular language.

Assignment 1 questions 1, 2 and 3 asked for proofs of closure under **union, concatenation and star** respectively.

"**Closed under**" means: perform the operation on members of the class, and the result is still in the class. Regular languages are closed under all three regular operations.

---

## PART 11: NONDETERMINISTIC FINITE AUTOMATA (pages 42 to 44)

### The idea

We call a finite state machine **non-deterministic when there exist several choices for the next state at any point**.

In the machines dealt with so far, when the machine is in a given state and reads the next input symbol, **we know what the next state would be, it is determined (deterministic)**.

> **Nondeterminism is a generalisation of determinism, so every deterministic finite automaton is automatically a nondeterministic finite automaton.**

That sentence gives you two true/false answers at once:
- Every DFA is an NFA: **TRUE**
- Every NFA is a DFA: **FALSE**

### The three differences that get tested

| Feature | DFA | NFA |
|---|---|---|
| Transitions per state per symbol | **exactly one** | zero, one, or many |
| ε-transitions (move without reading input) | **not allowed** | **allowed** |
| Transition function | **δ : Q × Σ → Q** (returns a single state) | **δ : Q × Σ_ε → P(Q)** (returns a **set** of states) |

His own words on page 43: "**Notice that for every state of a DFA, there's exactly one transition arrow for each input symbol of the alphabet. That is obviously not the case with NFAs.**"

### The first NFA (page 43)

<img width="605" height="391" alt="image" src="https://github.com/user-attachments/assets/f0dcbe30-787a-4f68-9f61-ed926ef58e4a" />



His reading of it: when one is in state q₁ and reads symbol 1, **it has 3 possibilities**: either it stays on q₁, moves to q₂, or moves to q₃, because when it moves to q₂ the ε moves it to q₃ automatically.

**This NFA accepts all binary strings that contain 101 or 11 as a substring.**

Why both:
- The path q₁ →1→ q₂ →0→ q₃ →1→ q₄ spells **101**.
- The path q₁ →1→ q₂ →ε→ q₃ →1→ q₄ spells **11**, because ε consumes no input.
- The self-loops on q₁ and q₄ soak up anything before and after.

**Regular expression: (0 ∪ 1)\*(101 ∪ 11)(0 ∪ 1)\*** or equivalently Σ\*(101 ∪ 11)Σ\*.

**The ε-transition is the whole trick.** ε means "move state without consuming an input symbol". Any question that asks how many strings of a given length the machine can be reading, or "which state(s) is the machine in after reading X", hinges on remembering to follow every ε arrow for free.

### The second NFA (page 43)

Let **A = { w ∈ {0,1}\* : w has a 1 in the 3rd position from the right }**.

```
<img width="293" height="179" alt="image" src="https://github.com/user-attachments/assets/93e48f88-3edb-485c-aab1-85a2b2660bef" />

```
<img width="313" height="324" alt="image" src="https://github.com/user-attachments/assets/2f08ced9-d89d-421c-bcc6-28cde2e9ed83" />

The state diagram accepts all strings that are in A and rejects all strings that are not in A.

**Regular expression: (0 ∪ 1)\*1(0 ∪ 1)(0 ∪ 1)** or Σ\*1ΣΣ.

How to read it: the self-loop guesses where the important 1 is. After taking the 1 to q₂, exactly two more symbols must follow (any symbols), landing you at the accept state at the end of the string. So the 1 sits three from the right.

**Why NFAs are the point of this section:** the equivalent DFA for this language needs **8 states** (it has to remember the last three symbols, and 2³ = 8). The NFA does it in 4. That is exactly the "much easier to describe" claim on page 44.

### Formal definition of an NFA (page 44) — memorise

First two pieces of notation:
- For any alphabet Σ, define **Σ_ε = Σ ∪ {ε}**.
- For any set Q, the **power set of Q**, denoted P(Q), is the **set of all subsets of Q**: P(Q) = { R : R ⊆ Q }.

> A **nondeterministic finite state machine (NFA) is a 5-tuple M = (Q, Σ, δ, q₀, F)** where
> 1. **Q** is a **finite set of states**
> 2. **Σ** is a **finite alphabet**
> 3. **δ : Q × Σ_ε → P(Q)** is the **transition function**
> 4. **q₀ ∈ Q** is the **start state**, and
> 5. **F ⊆ Q** is the **set of accept states**

**Only item 3 differs from the DFA definition.** Items 1, 2, 4 and 5 are word for word identical. If a fill-in asks you to complete an NFA definition, the two things you must get right are **Σ_ε** on the input side and **P(Q)** on the output side.

### Equivalence of NFAs and DFAs (page 44)

> You may have the impression that NFAs are more powerful than DFAs, **but DFAs and NFAs recognise the same class of languages**. Such equivalence is both surprising and useful. The usefulness stems from the fact that **describing an NFA for a given language is much easier than describing a DFA for the same language**.

**Assignment 2 theorem (page 45):** Let N = (Q, Σ, δ, q, F) be a non-deterministic finite automaton. Then there exists a deterministic finite automaton M such that **L(M) = L(N)**.

**Simply put: every non-deterministic finite automaton has an equivalent deterministic finite automaton.**

The construction (called the subset construction, worth knowing for a textbook MCQ): each state of the DFA is a **subset** of the NFA's states, so an NFA with n states gives a DFA with **at most 2ⁿ** states.

---

## PART 12: REGULAR EXPRESSIONS (pages 45 to 48)

### The motivating analogy

In arithmetic we use operations like × and + to build expressions such as **(5+3) × 4**. Similarly we use the **regular operations** to build expressions describing languages, called **regular expressions**. Example: **(0 ∪ 1)0\***.

**The value of the arithmetic expression is 32. The value of a regular expression is a language.** In this case, the language of all binary strings starting with 0 or 1 and then followed by any number of zeros.

That contrast, "arithmetic expression evaluates to a number, regular expression evaluates to a language", is quotable and very likely to appear.

### Where they show up

Regular expressions have an important role in computer science applications. In applications involving text, users may want to search for strings that **match or satisfy certain patterns**. Regular expressions provide a **powerful method for describing such patterns**.

Examples he names: the **grep** Unix command, modern programming languages **Java, C#** and so on, and **text editors**.

### Formal definition (page 46) — the six clauses

> Say that **R is a regular expression** if R is:
> 1. **a** for some a in the alphabet Σ, that is a ∈ Σ
> 2. **ε**
> 3. **∅**
> 4. **(R₁ ∪ R₂)**, where R₁ and R₂ are regular expressions
> 5. **(R₁ ∘ R₂)**, where R₁ and R₂ are regular expressions, or
> 6. **R₁\***, where R₁ is a regular expression

In items (1) and (2) the regular expressions **a** and **ε** refer to the languages **{a}** and **{ε}** respectively. In item (3) the regular expression **∅** represents the **empty language**. Items (4), (5) and (6) represent the languages obtained by taking the **union**, or **concatenation** of R₁ and R₂, or the **star** of R₁, respectively.

**Structure to notice:** clauses 1, 2, 3 are the **base cases** (three of them). Clauses 4, 5, 6 are the **inductive cases**, one for each regular operation. That is a recursive definition. If asked "how many base cases does the definition of a regular expression have", the answer is **3**.

**The distinction that ruins people: ε versus ∅.**

| | Language | Number of strings in it |
|---|---|---|
| **ε** | {ε} | **one** string (the empty one) |
| **∅** | { } | **zero** strings |

### The twelve examples (page 47) — learn every one

Assume Σ = {0, 1} throughout.

| # | Expression | Language it describes |
|---|---|---|
| 1 | **0\*10\*** | { w : w contains a **single 1** } |
| 2 | **Σ\*1Σ\*** | { w : w contains **at least one 1** } |
| 3 | **Σ\*001Σ\*** | { w : w contains the **substring 001** } |
| 4 | **1\*(01⁺)\*** | { w : **every 0 in w is followed by at least one 1** } |
| 5 | **(ΣΣ)\*** | { w : w is a string of **even length** } |
| 6 | **(ΣΣΣ)\*** | { w : the length of w is a **multiple of 3** } |
| 7 | **01 ∪ 10** | { 01, 10 } |
| 8 | **0Σ\*0 ∪ 1Σ\*1 ∪ 0 ∪ 1** | { w : w **starts and ends with the same symbol** } |
| 9 | **(0 ∪ ε)1\*** | **01\* ∪ 1\*** |
| 10 | **(0 ∪ ε)(1 ∪ ε)** | { ε, 0, 1, 01 } |
| 11 | **1\*∅** | **∅** |
| 12 | **∅\*** | **{ε}** |

His explanations for the last four:

- **9:** The expression 0 ∪ ε describes the language {0, ε}, so the concatenation operation adds either the string 0 or ε before the 1\*.
- **11:** **Concatenating the empty set to any set yields the empty set.**
- **12:** The star operation puts together **any number** of strings from the language to get a string in the result. **If the language is empty, the star operation can put together 0 strings, giving only {ε}.**

**Notes on the trickier ones:**

- Example 1 vs 2. In 0\*10\*, the single `1` is not starred, so exactly one 1 appears. In Σ\*1Σ\*, the Σ\* on both sides can contain more 1s, so it means at least one.
- Example 8. Why the extra ∪ 0 ∪ 1? Because 0Σ\*0 needs at least two symbols. A one-character string like `0` starts and ends with the same symbol trivially, so it must be added separately. Note ε is **not** included in this language as written.
- Examples 11 and 12 are the pair he will use. **1\*∅ = ∅** but **∅\* = {ε}**. Concatenating with ∅ destroys everything; starring ∅ gives you exactly one string.

### Equivalence of regular expressions and finite automata (page 48)

Regular expressions and finite automata are **equivalent in their descriptive power**. In fact **any regular expression can be converted to a finite automaton that recognises the language it describes, and vice versa**.

Recall: a **regular language is one that is recognised by some finite automaton**.

> **Theorem: A language is regular if and only if some regular expression describes it.**

Note "if and only if". Both directions hold. This theorem is what lets the exam question "give a regular expression for this machine" make sense at all.

**The three-way equivalence to hold in your head:**

```
   DFA  ⟺  NFA  ⟺  Regular expression   ⟺   Regular language
```

All four describe exactly the same class.

---

## PART 13: NON-REGULAR LANGUAGES AND THE PUMPING LEMMA (pages 48, 49)

### Non-regular languages

**Certain languages cannot be recognised by any finite automata.** Example: **B = { 0ⁿ1ⁿ | n ≥ 0 }**.

In an attempt to find a finite automaton that recognises B, **we discover that the machine seems to need to remember how many zeros have been seen so far as it reads the input. Because the number of 0s isn't limited, the machine would have to track an unlimited number of possibilities.**

This is where "limited memory" from page 35 comes back. A finite automaton has finitely many states, so it can only remember finitely many things. Counting to an unbounded n needs unbounded memory.

### The Pumping Lemma

The technique for **proving nonregularity** stems from a theorem stating that **every regular language has the property that all strings in the language can be "pumped" if they are at least as long as a certain special value called the pumping length**. This means each such string **contains a section that can be repeated any number of times with the resulting string remaining in the language**.

This theorem is traditionally referred to as the **Pumping Lemma**.

**If we are able to show that a language does not have this property, we are guaranteed that it is not regular.**

> **Pumping Lemma.** If A is a regular language, then there is a number **p**, called the **pumping length**, where if s is any string in A of **length at least p**, then s may be divided into **3 pieces, s = xyz**, satisfying the following conditions:
> 1. **for each i ≥ 0, xyⁱz ∈ A**
> 2. **|y| > 0**
> 3. **|xy| ≤ p**

**What each condition is for, in plain English:**

| Condition | Says | Why it is there |
|---|---|---|
| xyⁱz ∈ A for all i ≥ 0 | you can repeat y any number of times, including **zero** times, and stay in the language | this is the "pumping" itself |
| \|y\| > 0 | y is **not empty** | otherwise pumping would do nothing and the lemma would be useless |
| \|xy\| ≤ p | y occurs within the **first p characters** | this is what forces y into a specific region of the string when you build a contradiction |

**The direction of the lemma.** It says regular ⟹ pumpable. So it is used as a **contrapositive**: not pumpable ⟹ not regular. **It can never be used to prove a language IS regular.** That is the most common exam trap on this topic.

**Note i ≥ 0 includes i = 0**, which means xz (with y deleted entirely) must also be in A.

---

## PART 14: SYNTAX ANALYSIS AND CONTEXT-FREE GRAMMARS (pages 50 to 55)

### Setting the scene

This is the **2nd phase of compilation**, referred to as **Syntax Analysis or Parsing**. **Unlike lexical analysis, there are many approaches to parsing.**

Parsing algorithms for context-free languages fall into **two main categories: Top-Down and Bottom-Up**. **The names refer to the process of parse-tree construction.** There are also different types of top-down and bottom-up parsing algorithms.

Top-down builds the parse tree from the root down to the leaves. Bottom-up builds it from the leaves up to the root.

### Why regular languages are not enough

**Regular languages are inadequate for specifying all but the simplest aspect of programming language syntax.** To specify more complex languages such as:

1. **L = { w ∈ {a,b}\* : w = aⁿbⁿ for some n }**
2. **L = { w ∈ {(,)}⁺ : w is a well balanced string of parenthesis }**
3. **The syntax of most programming languages**

we use **context-free languages**.

All three examples share the same problem: matching pairs, arbitrarily nested. A finite automaton cannot count nesting depth. That is why every programming language with nested braces or brackets needs a context-free grammar.

### The hierarchy

The collection of languages associated with context-free grammars are called **context-free languages**. **They include all regular languages and many additional languages.** The class of machines for recognising context-free languages are called **Pushdown Automata**.

| Language class | Machine that recognises it | Description tool |
|---|---|---|
| Regular | Finite Automaton (DFA / NFA) | regular expression |
| Context-free | **Pushdown Automaton** | context-free grammar |

**Regular ⊂ Context-free.** Every regular language is context-free. Not every context-free language is regular (0ⁿ1ⁿ is the standard counterexample).

A pushdown automaton is a finite automaton plus a **stack**. The stack is the unbounded memory that lets it count nesting.

### Grammar G₁ (page 51)

```
A → 0A1
A → B
B → #
```

**How to generate a string from a grammar (three steps, memorise them):**

1. **Write down the start variable.** Usually at the top of the rules, unless otherwise specified, it is the variable on the **LHS** (of the first rule).
2. **Find a variable that is written down and a rule that starts with that variable. Replace the written down variable with the right-hand side (RHS) of that rule.**
3. **Repeat step 2 until no variables remain.**

### Derivation (page 52)

**The sequence of substitutions used to obtain a string is called a derivation.**

Grammar G₁ generates the string **000#111**. A derivation:

```
A ⟹ 0A1 ⟹ 00A11 ⟹ 000A111 ⟹ 000B111 ⟹ 000#111
```

The corresponding **parse tree**:

```
        A
      / | \
     0  A  1
      / | \
     0  A  1
      / | \
     0  A  1
        |
        B
        |
        #
```

**Parse tree for 000#111 in Grammar G₁.**

Observe that L(G₁) = { 0ⁿ#1ⁿ : n ≥ 0 }, the same matching-pairs shape that finite automata cannot handle. That is the point of the example.

### Abbreviation convention

For convenience when presenting a CFG, we can **abbreviate several rules with the same left-hand side**. From the current example: **A → 0A1 | B**.

The `|` symbol means "or". It is shorthand for two separate rules, not a symbol of the language.

### Grammar G₂: a fragment of English (page 53)

| Label | Rule |
|---|---|
| A | ⟨SENTENCE⟩ → ⟨NOUN-PHRASE⟩⟨VERB-PHRASE⟩ |
| B | ⟨NOUN-PHRASE⟩ → ⟨CMPLX-NOUN⟩ \| ⟨CMPLX-NOUN⟩⟨PREP-PHRASE⟩ |
| C | ⟨VERB-PHRASE⟩ → ⟨CMPLX-VERB⟩ \| ⟨CMPLX-VERB⟩⟨PREP-PHRASE⟩ |
| D | ⟨PREP-PHRASE⟩ → ⟨PREP⟩⟨CMPLX-NOUN⟩ |
| E | ⟨CMPLX-NOUN⟩ → ⟨ARTICLE⟩⟨NOUN⟩ |
| F | ⟨CMPLX-VERB⟩ → ⟨VERB⟩ \| ⟨VERB⟩⟨NOUN-PHRASE⟩ |
| G | ⟨ARTICLE⟩ → a \| the |
| H | ⟨NOUN⟩ → boy \| girl \| flower |
| I | ⟨VERB⟩ → touches \| likes \| sees |
| J | ⟨PREP⟩ → with |

**The three counts he gives, and they are perfect MCQ material:**

- Grammar G₂ has **10 variables** (also called **non-terminals**)
- **27 terminals**, that is the standard English alphabet **including the space character** (26 letters + 1 space = 27)
- **18 rules**

Count the 10 variables: SENTENCE, NOUN-PHRASE, VERB-PHRASE, PREP-PHRASE, CMPLX-NOUN, CMPLX-VERB, ARTICLE, NOUN, VERB, PREP.

Count the 18 rules by expanding the `|` shorthand: A=1, B=2, C=2, D=1, E=1, F=2, G=2, H=3, I=3, J=1. Total = 18. That is exactly why the count is 18 rather than 10.

**Strings in L(G₂) include:**
1. a boy sees
2. the boy sees a flower
3. a girl with a flower likes the boy

### Derivation of "a boy sees" (page 54)

| Step | Sentential form | Rule used |
|---|---|---|
| | ⟨SENTENCE⟩ | |
| ⟹ | ⟨NOUN-PHRASE⟩⟨VERB-PHRASE⟩ | from A |
| ⟹ | ⟨CMPLX-NOUN⟩⟨VERB-PHRASE⟩ | from B |
| ⟹ | ⟨ARTICLE⟩⟨NOUN⟩⟨VERB-PHRASE⟩ | from E |
| ⟹ | a ⟨NOUN⟩⟨VERB-PHRASE⟩ | from G |
| ⟹ | a boy ⟨VERB-PHRASE⟩ | from H |
| ⟹ | a boy ⟨CMPLX-VERB⟩ | from C |
| ⟹ | a boy ⟨VERB⟩ | from F |
| ⟹ | a boy sees | from I |

Know the rule labels A to J. He may show a derivation with one step blanked and ask which rule was applied.

### Formal definition of a CFG (page 54) — memorise

> A **context-free grammar is a 4-tuple (V, Σ, R, S)** where
> 1. **V** is a **finite, non-empty set called variables** (or non-terminals)
> 2. **Σ** is a **finite set, disjoint from V, called terminals**, that is **V ∩ Σ = ∅**
> 3. **R** is a **finite set of rules**, where each rule has the form **A → w** where **A ∈ V** and **w ∈ (V ∪ Σ)\***
> 4. **S ∈ V**, called the **start variable**

Mnemonic: **Very Silly Rabbits Sing.** V, Σ, R, S.

**Two precise details he will blank out:**
- **V ∩ Σ = ∅.** Variables and terminals must be **disjoint**. A symbol cannot be both.
- **A ∈ V and w ∈ (V ∪ Σ)\*.** The left side of a rule is exactly **one variable**. The right side is **any string of variables and terminals, including ε** (because of the star).

The fact that the LHS is a single variable is precisely what makes the grammar **context-free**: you may replace A regardless of what surrounds it, that is, regardless of context.

### G₁ described formally

For G₁: **V = {A, B}**, **Σ = {0, 1, #}**, **R = { A → 0A1, A → B, B → # }** and **S = A ∈ V**.

### DFA 5-tuple vs CFG 4-tuple side by side

| | Finite automaton | Context-free grammar |
|---|---|---|
| Size | **5-tuple** | **4-tuple** |
| Components | (Q, Σ, δ, q₀, F) | (V, Σ, R, S) |
| States / variables | Q, finite set of states | V, finite set of variables |
| Alphabet | Σ, the alphabet | Σ, the terminals |
| Engine | δ, transition function | R, set of rules |
| Starting point | q₀ ∈ Q, start state | S ∈ V, start variable |
| Finishing | F ⊆ Q, accept states | (none; you finish when no variables remain) |

**5 for machines, 4 for grammars.** The grammar has no accept-state component, which is exactly why it is one shorter.

---

## PART 15: THE CRAM SHEET

If you only revise one page the night before, revise this one.

### Numbers

| Number | What it counts |
|---|---|
| **2** | main parts of a compiler (analysis / synthesis, front end / back end) |
| **3** | parts in the multi-pass split (adds the middle end) |
| **6** | phases of a compiler |
| **2** | side components touching all 6 phases (symbol table, error handler) |
| **3** | regular operations (union, concatenation, star) |
| **5** | components of a finite automaton (DFA or NFA) |
| **4** | components of a context-free grammar |
| **3** | conditions in the formal definition of computation |
| **3** | conditions in the pumping lemma |
| **3** | pieces a pumped string splits into (s = xyz) |
| **6** | clauses in the definition of a regular expression (3 base + 3 inductive) |
| **3** | steps for generating a string from a grammar |
| **3** | things the compiler must do on detecting an error |
| **18** | man-years for the first Fortran compiler |
| **10 / 27 / 18** | variables / terminals / rules in grammar G₂ |
| **3** | states in M₁ |
| **2** | main classes of ISA (RISC, CISC) |
| **2** | main categories of parsing (top-down, bottom-up) |

### The definitions you must be able to write cold

```
Finite automaton:      (Q, Σ, δ, q₀, F)      δ : Q × Σ   → Q
NFA:                   (Q, Σ, δ, q₀, F)      δ : Q × Σ_ε → P(Q)
Context-free grammar:  (V, Σ, R, S)          A → w, A ∈ V, w ∈ (V ∪ Σ)*
```

### Mnemonics collected

| Mnemonic | Unlocks |
|---|---|
| **P C A L L** | Preprocessor, Compiler, Assembler, Linker, Loader |
| **Lazy Students Sometimes Ignore Ordinary Compilers** | Lexical, Syntax, Semantic, Intermediate, Optimiser, Code generator |
| **Quiet Snakes Don't Quit Fighting** | Q, Σ, δ, q₀, F |
| **Very Silly Rabbits Sing** | V, Σ, R, S |
| **U C S** | Union, Concatenation, Star |
| **R C C** | Report, Correct, Continue (error handling) |
| **T M S** | Type, Memory, Scope (symbol table, variables) |
| **R U C U** | Redundant, Unreachable, Common subexpressions, Unfolding loops |
| **F O S C** | Format, Operations, Storage, Control (ISA aspects) |

### The five sentences most likely to be a fill-in-the-blank

1. "A language is called a **regular** language if there exists some **finite automaton** that recognises it."
2. "A language is regular **if and only if** some **regular expression** describes it."
3. "Every **non-deterministic** finite automaton has an equivalent **deterministic** finite automaton."
4. "Each of the **6** phases interacts with a **symbol table** and an **error handler**."
5. "The main purpose of the symbol table is to provide **quick and uniform access** to identifier attributes throughout the compilation process."

---

## PART 16: TRUE / FALSE TRAP BANK

Cover the answers. Say the answer out loud before revealing it.

| # | Statement | Answer |
|---|---|---|
| 1 | A compiler translates from a high-level language to a lower-level language | TRUE |
| 2 | The lexical analyser produces a parse tree | FALSE, it produces tokens |
| 3 | The syntax analyser produces a parse tree | TRUE |
| 4 | The symbol table is used by only one phase of the compiler | FALSE, all 6 |
| 5 | Semantic errors are easier to detect than syntactic errors | FALSE, much harder, sometimes impossible |
| 6 | Typing "tehn" instead of "then" is a lexical error | TRUE |
| 7 | A missing closing parenthesis is a grammatical error | TRUE |
| 8 | Lexical errors and grammatical errors are both kinds of syntactic error | TRUE |
| 9 | The linker calculates absolute addresses | FALSE, the loader does |
| 10 | The loader resolves references to external variables | FALSE, the linker does |
| 11 | Macro expansion is performed by the preprocessor | TRUE |
| 12 | Intermediate code generation is part of the analysis phase | FALSE, it is synthesis |
| 13 | Lexical, syntax and semantic analysis form the front end | TRUE |
| 14 | Information is put into the symbol table mainly during lexical and syntax analysis | TRUE |
| 15 | A syntax tree is a compact form of the parse tree | TRUE |
| 16 | Each TAC assignment has at most one operator on the right hand side | TRUE |
| 17 | Every TAC instruction has exactly three operands | FALSE, some have fewer |
| 18 | The output of the code generator is usually assembly or relocatable machine code | TRUE |
| 19 | The parser calls the lexical analyser via getNextToken() | TRUE |
| 20 | The lexical analyser calls the parser | FALSE |
| 21 | Lex and Flex are parser generators | FALSE, lexical analyser generators |
| 22 | ARM is a CISC architecture | FALSE, RISC |
| 23 | Intel x86 is a CISC architecture | TRUE |
| 24 | \|ε\| = 0 | TRUE |
| 25 | Σ* includes the empty string | TRUE |
| 26 | Σ⁺ includes the empty string | FALSE |
| 27 | ε ∈ A* for every language A | TRUE |
| 28 | ∅* = ∅ | FALSE, ∅* = {ε} |
| 29 | 1*∅ = ∅ | TRUE |
| 30 | ∅ and {ε} are the same language | FALSE, one has zero strings, the other has one |
| 31 | Every DFA is an NFA | TRUE |
| 32 | Every NFA is a DFA | FALSE |
| 33 | NFAs recognise a larger class of languages than DFAs | FALSE, same class |
| 34 | Every NFA has an equivalent DFA | TRUE |
| 35 | A DFA may have ε-transitions | FALSE |
| 36 | For a DFA, δ : Q × Σ → Q | TRUE |
| 37 | For an NFA, δ : Q × Σ_ε → P(Q) | TRUE |
| 38 | In a DFA, a state may have two arrows for the same input symbol | FALSE, exactly one |
| 39 | The start state is indicated by a double circle | FALSE, that is the accept state |
| 40 | Accept states are sometimes called final states | TRUE |
| 41 | Regular languages are closed under union, concatenation and star | TRUE |
| 42 | { 0ⁿ1ⁿ : n ≥ 0 } is a regular language | FALSE |
| 43 | The pumping lemma can be used to prove a language is regular | FALSE, only that it is not |
| 44 | In the pumping lemma, \|y\| > 0 | TRUE |
| 45 | In the pumping lemma, \|xy\| ≤ p | TRUE |
| 46 | In the pumping lemma, i must be at least 1 | FALSE, i ≥ 0 |
| 47 | Context-free languages include all regular languages | TRUE |
| 48 | Pushdown automata recognise context-free languages | TRUE |
| 49 | In a CFG, V and Σ must be disjoint | TRUE |
| 50 | In a CFG rule A → w, w may contain both variables and terminals | TRUE |
| 51 | Every finite language is decidable | TRUE |
| 52 | Every infinite language is undecidable | FALSE |
| 53 | Every undecidable language is infinite | TRUE |
| 54 | Programming languages are always decidable | TRUE |
| 55 | Top-down and bottom-up refer to the process of parse-tree construction | TRUE |
| 56 | Regular expressions and finite automata have equal descriptive power | TRUE |
| 57 | A machine that accepts no strings recognises the empty language | TRUE |
| 58 | Finite state machines and regular expressions describe the lexical structure of programming languages | TRUE |
| 59 | The parser uses the attribute value of each token to build the tree | FALSE, it uses the first component, the token name |
| 60 | B ⊆ P(B) for every set B | FALSE, the correct statement is B ∈ P(B) |

---

## PART 17: FILL-IN-THE-BLANK DRILLS

Write your answers, then check against Part 15 and the relevant part above.

1. A finite automaton is a 5-tuple ( ___ , ___ , ___ , ___ , ___ ).
2. For a DFA the transition function has the form δ : ___ × ___ → ___ .
3. For an NFA the transition function has the form δ : ___ × ___ → ___ .
4. Σ_ε = ___ .
5. P(Q) = { R : ___ }.
6. A context-free grammar is a 4-tuple ( ___ , ___ , ___ , ___ ).
7. In a CFG, each rule has the form A → w where A ∈ ___ and w ∈ ___ .
8. A language is called a ___ language if there exists some ___ that recognises it.
9. A language is regular ___ some regular expression describes it.
10. The three regular operations are ___ , ___ and ___ .
11. A* = { x₁x₂...x_k | k ≥ ___ and each xᵢ ∈ A }.
12. ∅* = ___ .
13. 1*∅ = ___ .
14. Σ* = Σ⁺ ∪ ___ .
15. The first phase of a compiler is ___ , also known as ___ .
16. The second phase of a compiler is ___ , also known as ___ .
17. The analysis part of a compiler is also known as the ___ .
18. The synthesis part of a compiler is also known as the ___ .
19. Each of the 6 phases interacts with a ___ and an ___ .
20. A ___ is a sequence of characters in the source program that matches the pattern for a token.
21. A ___ is a description of the form that the lexemes of a token may take.
22. A ___ is a pair consisting of a token name and an optional attribute value.
23. The main purpose of the symbol table is to provide ___ and ___ access to identifier attributes.
24. The two types of syntactic errors are ___ and ___ .
25. On detecting an error, the compiler must ___ , ___ and ___ .
26. One of the most widely used intermediate languages is ___ .
27. The output of the code generation phase is usually ___ or ___ .
28. The three things considered during code generation are ___ , ___ and ___ .
29. The two main classes of ISA are ___ and ___ .
30. The class of machines for recognising context-free languages are called ___ .
31. Parsing algorithms for context-free languages fall into ___ and ___ .
32. In the pumping lemma, s = ___ , with conditions ___ , ___ and ___ .
33. The tool that resolves references to external variables in object files is the ___ .
34. The tool that calculates absolute addresses is the ___ .
35. Grammar G₂ has ___ variables, ___ terminals and ___ rules.

**Answers**

1. Q, Σ, δ, q₀, F
2. Q × Σ → Q
3. Q × Σ_ε → P(Q)
4. Σ ∪ {ε}
5. R ⊆ Q
6. V, Σ, R, S
7. A ∈ V, w ∈ (V ∪ Σ)*
8. regular, finite automaton
9. if and only if
10. union, concatenation, star
11. k ≥ 0
12. {ε}
13. ∅
14. {ε}
15. lexical analysis, scanning
16. syntax analysis, parsing
17. front end
18. back end
19. symbol table, error handler
20. lexeme
21. pattern
22. token
23. quick, uniform
24. lexical, grammatical
25. report the error, correct it if possible, continue processing if possible
26. Three Address Code (TAC)
27. assembly language, relocatable machine code
28. memory management, register management, machine specific optimisation
29. RISC (Reduced Instruction Set Computers), CISC (Complex Instruction Set Computers)
30. Pushdown Automata
31. top-down, bottom-up
32. xyz; xyⁱz ∈ A for each i ≥ 0; |y| > 0; |xy| ≤ p
33. linker
34. loader
35. 10, 27, 18

---

## PART 18: MACHINE TO REGULAR EXPRESSION DRILLS

This is the format the TA named specifically. Below, Σ = {0,1} and Σ is shorthand for (0 ∪ 1).

### 18.1 The three machines from the notes

| Machine | Language | Regular expression |
|---|---|---|
| M₁ (page 36) | at least one 1, then an even number of 0s after the last 1 | **Σ\*1(00)\*** |
| NFA 1 (page 43) | contains 101 or 11 as a substring | **Σ\*(101 ∪ 11)Σ\*** |
| NFA 2 (page 43) | a 1 in the 3rd position from the right | **Σ\*1ΣΣ** |

### 18.2 Standard patterns to have memorised

| Description of the language | Regular expression |
|---|---|
| all strings | Σ\* |
| the empty language | ∅ |
| only the empty string | ∅\* or ε |
| starts with 1 | 1Σ\* |
| ends with 1 | Σ\*1 |
| starts and ends with 1 | 1Σ\*1 ∪ 1 |
| starts and ends with the same symbol | 0Σ\*0 ∪ 1Σ\*1 ∪ 0 ∪ 1 |
| contains at least one 1 | Σ\*1Σ\* |
| contains exactly one 1 | 0\*10\* |
| contains the substring 001 | Σ\*001Σ\* |
| contains no 1s at all | 0\* |
| even length | (ΣΣ)\* |
| odd length | Σ(ΣΣ)\* |
| length is a multiple of 3 | (ΣΣΣ)\* |
| length exactly 3 | ΣΣΣ |
| length at least 3 | ΣΣΣΣ\* |
| an even number of 0s | 1\*(01\*01\*)\* |
| every 0 is followed by at least one 1 | 1\*(01⁺)\* |
| a 1 in the 2nd position from the right | Σ\*1Σ |
| a 1 in the 3rd position from the right | Σ\*1ΣΣ |
| a 1 in the kth position from the right | Σ\*1Σ^(k−1) |
| the 2nd symbol from the left is 0 | Σ0Σ\* |
| any number of 0s followed by a single 1 (his page 35 example) | 0\*1 |
| 0ⁿ1 with n ≥ 1 (his exact wording, n ∈ ℤ⁺) | 00\*1 or 0⁺1 |

### 18.3 How to attack a "give the regex" question in the exam

1. **Find the accept state(s).** Trace every path from start to an accept state. That path spells out the core of the expression.
2. **Self-loops become stars.** A self-loop on symbol a at a state becomes a\* at that point in the expression.
3. **A loop on Σ (both 0 and 1) at the start state usually means Σ\* at the front.** Same at the end means Σ\* at the back.
4. **Two paths to the same place become a union.** Join with ∪.
5. **Test with three strings.** One short string that should be accepted, one that should be rejected, and ε. If your expression gets ε wrong, you have a star in the wrong place.
6. **Count states as a sanity check.** A machine with a chain of k states after the branch point usually means k−1 fixed symbols in the expression.

### 18.4 Machine definition completion drills

Given the DFA below:

```
                 1                    0
                ┌──┐                 ┌──┐
                ▼  │                 ▼  │
       ───▶( q0 )──┘──── 0 ────▶(( q1 ))──┘
                    ◀─── 1 ─────
```

Complete: M = ( ___ , ___ , δ, ___ , ___ ) where

- Q = { q₀, q₁ }
- Σ = { 0, 1 }
- q₀ is the start state
- F = { q₁ }
- δ table:

| δ | 0 | 1 |
|---|---|---|
| q₀ | q₁ | q₀ |
| q₁ | q₁ | q₀ |

Language: **strings that end in 0.** Regular expression: **Σ\*0**, or equivalently **(0 ∪ 1)\*0**.

Now try these yourself and check with the patterns in 18.2.

**Drill A.** A 2-state DFA over {0,1} with start state p (accepting) and state r. δ(p,0)=p, δ(p,1)=r, δ(r,0)=r, δ(r,1)=p. F = {p}. What language?
*Answer: strings with an even number of 1s. Regular expression: 0\*(10\*10\*)\*.*

**Drill B.** A 3-state DFA counting length modulo 3, start state s₀, F = {s₀}, every symbol advances to the next state cyclically. What language?
*Answer: strings whose length is a multiple of 3. Regular expression: (ΣΣΣ)\*.*

**Drill C.** An NFA with start state a, a self-loop on 0 and 1 at a, an arrow labelled 0 from a to b, an arrow labelled 0 from b to c, and c is the accept state with no outgoing arrows. What language?
*Answer: strings ending in 00. Regular expression: Σ\*00.*

**Drill D.** Write the regular expression for "contains at least two 1s".
*Answer: Σ\*1Σ\*1Σ\*.*

**Drill E.** Write the regular expression for "does not contain the substring 11".
*Answer: (0 ∪ 10)\*(1 ∪ ε).*

---

## PART 19: LIKELY TEXTBOOK ITEMS

The TA said questions may come from the compilers textbook. The two books in play are Aho, Lam, Sethi and Ullman (*Compilers: Principles, Techniques and Tools*, the "Dragon book") for the compiler half, and Sipser (*Introduction to the Theory of Computation*) pages 31 to 82 for the automata half, since that is the reading he assigned.

Standard textbook facts that fit his notes and are not spelled out fully in them:

| Question | Answer |
|---|---|
| Tool that generates lexical analysers | **Lex / Flex** |
| Tool that generates parsers | **Yacc / Bison** |
| Another name for the front end | analysis phase |
| Another name for the back end | synthesis phase |
| A one-pass compiler | passes over the source once |
| Which phase removes comments and whitespace | lexical analysis (the scanning sub-process) |
| Which phase detects an undeclared variable | semantic analysis, using the symbol table |
| Which phase detects a missing semicolon | syntax analysis |
| Which phase detects an illegal character | lexical analysis |
| Which phase detects a type mismatch | semantic analysis |
| Maximum states in a DFA converted from an n-state NFA | **2ⁿ** |
| Machine for regular languages | finite automaton |
| Machine for context-free languages | pushdown automaton |
| Number of states needed for "1 in kth position from the right" as a DFA | 2ᵏ |
| Which is easier to design for a given language | the NFA |
| Which is easier to implement in code | the DFA |

Watch this one carefully: **NFAs are easier to design, DFAs are easier to implement.** Options often swap the two.

---

## PART 20: THREE-DAY REVISION PLAN

**Day 1: the automata half.** Parts 8 to 13. Redraw M₁ and both NFAs from memory, with the transition table. Write out the DFA and NFA 5-tuples ten times until you never confuse Σ with Σ_ε or Q with P(Q). Do all of Part 18.

**Day 2: the compiler half.** Parts 1 to 5 and Part 14. Draw the six-phase pipeline from memory, with the symbol table and error handler. Write out the P C A L L chain. Learn the token / pattern / lexeme triple and the error taxonomy.

**Day 3: drilling.** Part 16 (true/false) twice, Part 17 (fill-ins) twice, Part 15 (cram sheet). Then teach the whole thing to someone else out loud, because explaining the difference between a lexeme and a token to another person is the fastest way to find the gaps in your own understanding.

### If you get stuck in the exam

- Asked for a regex from a machine: follow the six steps in 18.3, and always test ε.
- Asked to complete a 5-tuple: write "Quiet Snakes Don't Quit Fighting" in the margin first.
- Asked which phase does something: ask "is this about characters, structure, or meaning?" Characters → lexical. Structure → syntax. Meaning → semantic.
- Asked a true/false with the word "every", "always" or "only": those absolutes are usually where the trap is. Check it against a single counterexample.
