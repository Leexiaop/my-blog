---
layout: notationalconventions
title: 规范公约
date: 2019-01-20 22:50:50
tags:
---
# 5.1 Syntactic and Lexical Grammars

## 5.1.1 Context-Free Grammars

A context-free grammar consists of a number of productions. Each production has an abstract symbol called a nonterminal as its left-hand side, and a sequence of zero or more nonterminal and terminal symbols as its right-hand side. For each grammar, the terminal symbols are drawn from a specified alphabet.

A chain production is a production that has exactly one nonterminal symbol on its right-hand side along with zero or more terminal symbols.

Starting from a sentence consisting of a single distinguished nonterminal, called the goal symbol, a given context-free grammar specifies a language, namely, the (perhaps infinite) set of possible sequences of terminal symbols that can result from repeatedly replacing any nonterminal in the sequence with a right-hand side of a production for which the nonterminal is the left-hand side.

## 5.1.2 The Lexical and RegExp Grammars

A lexical grammar for ECMAScript is given in clause 11. This grammar has as its terminal symbols Unicode code points that conform to the rules for SourceCharacter defined in 10.1. It defines a set of productions, starting from the goal symbol InputElementDiv, InputElementTemplateTail, or InputElementRegExp, or InputElementRegExpOrTemplateTail, that describe how sequences of such code points are translated into a sequence of input elements.

Input elements other than white space and comments form the terminal symbols for the syntactic grammar for ECMAScript and are called ECMAScript tokens. These tokens are the reserved words, identifiers, literals, and punctuators of the ECMAScript language. Moreover, line terminators, although not considered to be tokens, also become part of the stream of input elements and guide the process of automatic semicolon insertion (11.9). Simple white space and single-line comments are discarded and do not appear in the stream of input elements for the syntactic grammar. A MultiLineComment (that is, a comment of the form /*…*/ regardless of whether it spans more than one line) is likewise simply discarded if it contains no line terminator; but if a MultiLineComment contains one or more line terminators, then it is replaced by a single line terminator, which becomes part of the stream of input elements for the syntactic grammar.

A RegExp grammar for ECMAScript is given in 21.2.1. This grammar also has as its terminal symbols the code points as defined by SourceCharacter. It defines a set of productions, starting from the goal symbol Pattern, that describe how sequences of code points are translated into regular expression patterns.

Productions of the lexical and RegExp grammars are distinguished by having two colons “::” as separating punctuation. The lexical and RegExp grammars share some productions.

## 5.1.3 The Numeric String Grammar

Another grammar is used for translating Strings into numeric values. This grammar is similar to the part of the lexical grammar having to do with numeric literals and has as its terminal symbols SourceCharacter. This grammar appears in 7.1.3.1.

Productions of the numeric string grammar are distinguished by having three colons “:::” as punctuation.

## 5.1.4 The Syntactic Grammar

The syntactic grammar for ECMAScript is given in clauses 11, 12, 13, 14, and 15. This grammar has ECMAScript tokens defined by the lexical grammar as its terminal symbols (5.1.2). It defines a set of productions, starting from two alternative goal symbols Script and Module, that describe how sequences of tokens form syntactically correct independent components of ECMAScript programs.

When a stream of code points is to be parsed as an ECMAScript Script or Module, it is first converted to a stream of input elements by repeated application of the lexical grammar; this stream of input elements is then parsed by a single application of the syntactic grammar. The input stream is syntactically in error if the tokens in the stream of input elements cannot be parsed as a single instance of the goal nonterminal (Script or Module), with no tokens left over.

When a parse is successful, it constructs a parse tree, a rooted tree structure in which each node is a Parse Node. Each Parse Node is an instance of a symbol in the grammar; it represents a span of the source text that can be derived from that symbol. The root node of the parse tree, representing the whole of the source text, is an instance of the parse's goal symbol. When a Parse Node is an instance of a nonterminal, it is also an instance of some production that has that nonterminal as its left-hand side. Moreover, it has zero or more children, one for each symbol on the production's right-hand side: each child is a Parse Node that is an instance of the corresponding symbol.

New Parse Nodes are instantiated for each invocation of the parser and never reused between parses even of identical source text. Parse Nodes are considered the same Parse Node if and only if they represent the same span of source text, are instances of the same grammar symbol, and resulted from the same parser invocation.

    NOTE 1
        Parsing the same String multiple times will lead to different Parse Nodes, e.g., as occurs in:eval(str); eval(str);

    NOTE 2
        Parse Nodes are specification artefacts, and implementations are not required to use an analogous data structure.

Productions of the syntactic grammar are distinguished by having just one colon “:” as punctuation.

The syntactic grammar as presented in clauses 12, 13, 14 and 15 is not a complete account of which token sequences are accepted as a correct ECMAScript Script or Module. Certain additional token sequences are also accepted, namely, those that would be described by the grammar if only semicolons were added to the sequence in certain places (such as before line terminator characters). Furthermore, certain token sequences that are described by the grammar are not considered acceptable if a line terminator character appears in certain “awkward” places.

In certain cases, in order to avoid ambiguities, the syntactic grammar uses generalized productions that permit token sequences that do not form a valid ECMAScript Script or Module. For example, this technique is used for object literals and object destructuring patterns. In such cases a more restrictive supplemental grammar is provided that further restricts the acceptable token sequences. Typically, an early error rule will then define an error condition if "P is not covering an N", where P is a Parse Node (an instance of the generalized production) and N is a nonterminal from the supplemental grammar. Here, the sequence of tokens originally matched by P is parsed again using N as the goal symbol. (If N takes grammatical parameters, then they are set to the same values used when P was originally parsed.) An error occurs if the sequence of tokens cannot be parsed as a single instance of N, with no tokens left over. Subsequently, algorithms access the result of the parse using a phrase of the form "the N that is covered by P". This will always be a Parse Node (an instance of N, unique for a given P), since any parsing failure would have been detected by an early error rule.

## 5.1.5 Grammar Notation

Terminal symbols of the lexical, RegExp, and numeric string grammars are shown in fixed width font, both in the productions of the grammars and throughout this specification whenever the text directly refers to such a terminal symbol. These are to appear in a script exactly as written. All terminal symbol code points specified in this way are to be understood as the appropriate Unicode code points from the Basic Latin range, as opposed to any similar-looking code points from other Unicode ranges.

Nonterminal symbols are shown in italic type. The definition of a nonterminal (also called a “production”) is introduced by the name of the nonterminal being defined followed by one or more colons. (The number of colons indicates to which grammar the production belongs.) One or more alternative right-hand sides for the nonterminal then follow on succeeding lines. For example, the syntactic definition:

    WhileStatement:
        while(Expression)Statement

states that the nonterminal WhileStatement represents the token while, followed by a left parenthesis token, followed by an Expression, followed by a right parenthesis token, followed by a Statement. The occurrences of Expression and Statement are themselves nonterminals. As another example, the syntactic definition:
    ArgumentList:
        AssignmentExpression
        ArgumentList,AssignmentExpression

states that an ArgumentList may represent either a single AssignmentExpression or an ArgumentList, followed by a comma, followed by an AssignmentExpression. This definition of ArgumentList is recursive, that is, it is defined in terms of itself. The result is that an ArgumentList may contain any positive number of arguments, separated by commas, where each argument expression is an AssignmentExpression. Such recursive definitions of nonterminals are common.

The subscripted suffix “opt”, which may appear after a terminal or nonterminal, indicates an optional symbol. The alternative containing the optional symbol actually specifies two right-hand sides, one that omits the optional element and one that includes it. This means that:

    VariableDeclaration:
        BindingIdentifierInitializeropt

is a convenient abbreviation for:

    VariableDeclaration:
        BindingIdentifier
        BindingIdentifierInitializer
        
and that:

    IterationStatement:
        for(LexicalDeclarationExpressionopt;Expressionopt)Statement

is a convenient abbreviation for:

    IterationStatement:
        for(LexicalDeclaration;Expressionopt)Statement
        for(LexicalDeclarationExpression;Expressionopt)Statement

which in turn is an abbreviation for:

    IterationStatement:
        for(LexicalDeclaration;)Statement
        for(LexicalDeclaration;Expression)Statement
        for(LexicalDeclarationExpression;)Statement
        for(LexicalDeclarationExpression;Expression)Statement

so, in this example, the nonterminal IterationStatement actually has four alternative right-hand sides.

A production may be parameterized by a subscripted annotation of the form “[parameters]”, which may appear as a suffix to the nonterminal symbol defined by the production. “parameters” may be either a single name or a comma separated list of names. A parameterized production is shorthand for a set of productions defining all combinations of the parameter names, preceded by an underscore, appended to the parameterized nonterminal symbol. This means that:

    StatementList[Return]:
        ReturnStatement
        ExpressionStatement

is a convenient abbreviation for:

    StatementList:
        ReturnStatement
        ExpressionStatement

    StatementList_Return:
        ReturnStatement
        ExpressionStatement

and that:

    StatementList[Return, In]:
        ReturnStatement
        ExpressionStatement

is an abbreviation for:

    StatementList:
        ReturnStatement
        ExpressionStatement

    StatementList_Return:
        ReturnStatement
        ExpressionStatement

    StatementList_In:
        ReturnStatement
        ExpressionStatement

    StatementList_Return_In:
        ReturnStatement
        ExpressionStatement
Multiple parameters produce a combinatory number of productions, not all of which are necessarily referenced in a complete grammar.

References to nonterminals on the right-hand side of a production can also be parameterized. For example:

    StatementList:
        ReturnStatement
        ExpressionStatement[+In]

is equivalent to saying:

    StatementList:
        ReturnStatement
        ExpressionStatement_In

and:

    StatementList:
        ReturnStatement
        ExpressionStatement[~In]

is equivalent to:

    StatementList:
        ReturnStatement
        ExpressionStatement

A nonterminal reference may have both a parameter list and an “opt” suffix. For example:

    VariableDeclaration:
        BindingIdentifierInitializer[+In]opt

is an abbreviation for:

    VariableDeclaration:
        BindingIdentifier
        BindingIdentifierInitializer_In

Prefixing a parameter name with “?” on a right-hand side nonterminal reference makes that parameter value dependent upon the occurrence of the parameter name on the reference to the current production's left-hand side symbol. For example:

    VariableDeclaration[In]:
        BindingIdentifierInitializer[?In]

is an abbreviation for:

    VariableDeclaration:
        BindingIdentifierInitializer

    VariableDeclaration_In:
        BindingIdentifierInitializer_In

If a right-hand side alternative is prefixed with “[+parameter]” that alternative is only available if the named parameter was used in referencing the production's nonterminal symbol. If a right-hand side alternative is prefixed with “[~parameter]” that alternative is only available if the named parameter was not used in referencing the production's nonterminal symbol. This means that:

    StatementList[Return]:
        [+Return]ReturnStatement
        ExpressionStatement

is an abbreviation for:

    StatementList:
        ExpressionStatement
        StatementList_Return:
        ReturnStatement
        ExpressionStatement

and that:

    StatementList[Return]:
        [~Return]ReturnStatement
        ExpressionStatement

is an abbreviation for:

    StatementList:
        ReturnStatement
        ExpressionStatement

    StatementList_Return:
        ExpressionStatement

When the words “one of” follow the colon(s) in a grammar definition, they signify that each of the terminal symbols on the following line or lines is an alternative definition. For example, the lexical grammar for ECMAScript contains the production:

    NonZeroDigit::one of
        1 2 3 4 5 6 7 8 9
which is merely a convenient abbreviation for:

    NonZeroDigit::
        1
        2
        3
        4
        5
        6
        7
        8
        9
If the phrase “[empty]” appears as the right-hand side of a production, it indicates that the production's right-hand side contains no terminals or nonterminals.

If the phrase “[lookahead ∉ set]” appears in the right-hand side of a production, it indicates that the production may not be used if the immediately following input token sequence is a member of the given set. The set can be written as a comma separated list of one or two element terminal sequences enclosed in curly brackets. For convenience, the set can also be written as a nonterminal, in which case it represents the set of all terminals to which that nonterminal could expand. If the set consists of a single terminal the phrase “[lookahead ≠ terminal]” may be used.

For example, given the definitions:

    DecimalDigit::one of
        0 1 2 3 4 5 6 7 8 9

    DecimalDigits::
        DecimalDigit
        DecimalDigitsDecimalDigit
the definition:

    LookaheadExample::
        n[lookahead ∉ { 1, 3, 5, 7, 9 }]DecimalDigits
        DecimalDigit[lookahead ∉ DecimalDigit]
matches either the letter n followed by one or more decimal digits the first of which is even, or a decimal digit not followed by another decimal digit.

Similarly, if the phrase “[lookahead ∈ set]” appears in the right-hand side of a production, it indicates that the production may only be used if the immediately following input token sequence is a member of the given set. If the set consists of a single terminal the phrase “[lookahead = terminal]” may be used.

If the phrase “[no LineTerminator here]” appears in the right-hand side of a production of the syntactic grammar, it indicates that the production is a restricted production: it may not be used if a LineTerminator occurs in the input stream at the indicated position. For example, the production:

    ThrowStatement:
        throw[no LineTerminator here]Expression;
indicates that the production may not be used if a LineTerminator occurs in the script between the throw token and the Expression.

Unless the presence of a LineTerminator is forbidden by a restricted production, any number of occurrences of LineTerminator may appear between any two consecutive tokens in the stream of input elements without affecting the syntactic acceptability of the script.

When an alternative in a production of the lexical grammar or the numeric string grammar appears to be a multi-code point token, it represents the sequence of code points that would make up such a token.

The right-hand side of a production may specify that certain expansions are not permitted by using the phrase “but not” and then indicating the expansions to be excluded. For example, the production:

    Identifier::
        IdentifierNamebut not ReservedWord
means that the nonterminal Identifier may be replaced by any sequence of code points that could replace IdentifierName provided that the same sequence of code points could not replace ReservedWord.

Finally, a few nonterminal symbols are described by a descriptive phrase in sans-serif type in cases where it would be impractical to list all the alternatives:

    SourceCharacter::
        any Unicode code point

# 5.2 Algorithm Conventions

The specification often uses a numbered list to specify steps in an algorithm. These algorithms are used to precisely specify the required semantics of ECMAScript language constructs. The algorithms are not intended to imply the use of any specific implementation technique. In practice, there may be more efficient algorithms available to implement a given feature.

Algorithms may be explicitly parameterized, in which case the names and usage of the parameters must be provided as part of the algorithm's definition.

Algorithm steps may be subdivided into sequential substeps. Substeps are indented and may themselves be further divided into indented substeps. Outline numbering conventions are used to identify substeps with the first level of substeps labelled with lower case alphabetic characters and the second level of substeps labelled with lower case roman numerals. If more than three levels are required these rules repeat with the fourth level using numeric labels. For example:

    1.Top-level step
        a.Substep.
        b.Substep.
            i.Subsubstep.
                1.Subsubsubstep
                    a.Subsubsubsubstep
                        i.Subsubsubsubsubstep
A step or substep may be written as an “if” predicate that conditions its substeps. In this case, the substeps are only applied if the predicate is true. If a step or substep begins with the word “else”, it is a predicate that is the negation of the preceding “if” predicate step at the same level.

A step may specify the iterative application of its substeps.

A step that begins with “Assert:” asserts an invariant condition of its algorithm. Such assertions are used to make explicit algorithmic invariants that would otherwise be implicit. Such assertions add no additional semantic requirements and hence need not be checked by an implementation. They are used simply to clarify algorithms.

Algorithm steps may declare named aliases for any value using the form “Let x be someValue”. These aliases are reference-like in that both x and someValue refer to the same underlying data and modifications to either are visible to both. Algorithm steps that want to avoid this reference-like behaviour should explicitly make a copy of the right-hand side: “Let x be a copy of someValue” creates a shallow copy of someValue.

Once declared, an alias may be referenced in any subsequent steps and must not be referenced from steps prior to the alias's declaration. Aliases may be modified using the form “Set x to someOtherValue”.

## 5.2.1 Abstract Operations

In order to facilitate their use in multiple parts of this specification, some algorithms, called abstract operations, are named and written in parameterized functional form so that they may be referenced by name from within other algorithms. Abstract operations are typically referenced using a functional application style such as OperationName(arg1, arg2). Some abstract operations are treated as polymorphically dispatched methods of class-like specification abstractions. Such method-like abstract operations are typically referenced using a method application style such as someValue.OperationName(arg1, arg2).

## 5.2.2 Syntax-Directed Operations

A syntax-directed operation is a named operation whose definition consists of algorithms, each of which is associated with one or more productions from one of the ECMAScript grammars. A production that has multiple alternative definitions will typically have a distinct algorithm for each alternative. When an algorithm is associated with a grammar production, it may reference the terminal and nonterminal symbols of the production alternative as if they were parameters of the algorithm. When used in this manner, nonterminal symbols refer to the actual alternative definition that is matched when parsing the source text.

When an algorithm is associated with a production alternative, the alternative is typically shown without any “[ ]” grammar annotations. Such annotations should only affect the syntactic recognition of the alternative and have no effect on the associated semantics for the alternative.

Syntax-directed operations are invoked with a parse node and, optionally, other parameters by using the conventions on steps 1, 3, and 4 in the following algorithm:

1.Let status be the result of performing SyntaxDirectedOperation of SomeNonTerminal.
2.Let someParseNode be the parse of some source text.
3.Perform SyntaxDirectedOperation of someParseNode.
4.Perform SyntaxDirectedOperation of someParseNode passing "value" as the argument.

Unless explicitly specified otherwise, all chain productions have an implicit definition for every operation that might be applied to that production's left-hand side nonterminal. The implicit definition simply reapplies the same operation with the same parameters, if any, to the chain production's sole right-hand side nonterminal and then returns the result. For example, assume that some algorithm has a step of the form: “Return the result of evaluating Block” and that there is a production:

    Block:
        {StatementList}
but the Evaluation operation does not associate an algorithm with that production. In that case, the Evaluation operation implicitly includes an association of the form:

##Runtime Semantics: Evaluation

    Block:{StatementList}
        Return the result of evaluating StatementList.

## 5.2.3 Runtime Semantics

Algorithms which specify semantics that must be called at runtime are called runtime semantics. Runtime semantics are defined by abstract operations or syntax-directed operations. Such algorithms always return a completion record.

### 5.2.3.1 Implicit Completion Values

The algorithms of this specification often implicitly return Completion Records whose [[Type]] is normal. Unless it is otherwise obvious from the context, an algorithm statement that returns a value that is not a Completion Record, such as:

    Return "Infinity".

means the same thing as:

    Return NormalCompletion("Infinity").

However, if the value expression of a “return” statement is a Completion Record construction literal, the resulting Completion Record is returned. If the value expression is a call to an abstract operation, the “return” statement simply returns the Completion Record produced by the abstract operation.

The abstract operation Completion(completionRecord) is used to emphasize that a previously computed Completion Record is being returned. The Completion abstract operation takes a single argument, completionRecord, and performs the following steps:

    1.Assert: completionRecord is a Completion Record.
    2.Return completionRecord as the Completion Record of this abstract operation.

A “return” statement without a value in an algorithm step means the same thing as:

    1.Return NormalCompletion(undefined).
Any reference to a Completion Record value that is in a context that does not explicitly require a complete Completion Record value is equivalent to an explicit reference to the [[Value]] field of the Completion Record value unless the Completion Record is an abrupt completion.

### 5.2.3.2 Throw an Exception

Algorithms steps that say to throw an exception, such as

    1.Throw a TypeError exception.
mean the same things as:

    1.Return ThrowCompletion(a newly created TypeError object).

### 5.2.3.3 ReturnIfAbrupt

Algorithms steps that say or are otherwise equivalent to:

    1.ReturnIfAbrupt(argument).
mean the same thing as:

    1.If argument is an abrupt completion, return argument.
    2.Else if argument is a Completion Record, set argument to argument.[[Value]].

Algorithms steps that say or are otherwise equivalent to:

    1.ReturnIfAbrupt(AbstractOperation()).
mean the same thing as:

    1.Let hygienicTemp be AbstractOperation().
    2.If hygienicTemp is an abrupt completion, return hygienicTemp.
    3.Else if hygienicTemp is a Completion Record, set hygienicTemp to hygienicTemp.[[Value]].
Where hygienicTemp is ephemeral and visible only in the steps pertaining to ReturnIfAbrupt.

Algorithms steps that say or are otherwise equivalent to:

    1.Let result be AbstractOperation(ReturnIfAbrupt(argument)).
mean the same thing as:

    1.If argument is an abrupt completion, return argument.
    2.If argument is a Completion Record, set argument to argument.[[Value]].
    3.Let result be AbstractOperation(argument).

###5.2.3.4 ReturnIfAbrupt Shorthands

Invocations of abstract operations and syntax-directed operations that are prefixed by ? indicate that ReturnIfAbrupt should be applied to the resulting Completion Record. For example, the step:

    1.? OperationName().
is equivalent to the following step:

    1.ReturnIfAbrupt(OperationName()).

Similarly, for method application style, the step:

    1.? someValue.OperationName().
is equivalent to:

    1.ReturnIfAbrupt(someValue.OperationName()).

Similarly, prefix ! is used to indicate that the following invocation of an abstract or syntax-directed operation will never return an abrupt completion and that the resulting Completion Record's [[Value]] field should be used in place of the return value of the operation. For example, the step:

    1.Let val be ! OperationName().

is equivalent to the following steps:

    1.Let val be OperationName().
    2.Assert: val is never an abrupt completion.
    3.If val is a Completion Record, set val to val.[[Value]].
Syntax-directed operations for runtime semantics make use of this shorthand by placing ! or ? before the invocation of the operation:

    1.Perform ! SyntaxDirectedOperation of NonTerminal.

## 5.2.4 Static Semantics

Context-free grammars are not sufficiently powerful to express all the rules that define whether a stream of input elements form a valid ECMAScript Script or Module that may be evaluated. In some situations additional rules are needed that may be expressed using either ECMAScript algorithm conventions or prose requirements. Such rules are always associated with a production of a grammar and are called the static semantics of the production.

Static Semantic Rules have names and typically are defined using an algorithm. Named Static Semantic Rules are associated with grammar productions and a production that has multiple alternative definitions will typically have for each alternative a distinct algorithm for each applicable named static semantic rule.

Unless otherwise specified every grammar production alternative in this specification implicitly has a definition for a static semantic rule named Contains which takes an argument named symbol whose value is a terminal or nonterminal of the grammar that includes the associated production. The default definition of Contains is:

    1.For each child node child of this Parse Node, do
        a.If child is an instance of symbol, return true.
        b.If child is an instance of a nonterminal, then
            i.Let contained be the result of child Contains symbol.
            ii.If contained is true, return true.
    2.Return false.

The above definition is explicitly over-ridden for specific productions.

A special kind of static semantic rule is an Early Error Rule. Early error rules define early error conditions (see clause 16) that are associated with specific grammar productions. Evaluation of most early error rules are not explicitly invoked within the algorithms of this specification. A conforming implementation must, prior to the first evaluation of a Script or Module, validate all of the early error rules of the productions used to parse that Script or Module. If any of the early error rules are violated the Script or Module is invalid and cannot be evaluated.

## 5.2.5 Mathematical Operations

Mathematical operations such as addition, subtraction, negation, multiplication, division, and the mathematical functions defined later in this clause should always be understood as computing exact mathematical results on mathematical real numbers, which unless otherwise noted do not include infinities and do not include a negative zero that is distinguished from positive zero. Algorithms in this standard that model floating-point arithmetic include explicit steps, where necessary, to handle infinities and signed zero and to perform rounding. If a mathematical operation or function is applied to a floating-point number, it should be understood as being applied to the exact mathematical value represented by that floating-point number; such a floating-point number must be finite, and if it is +0 or -0 then the corresponding mathematical value is simply 0.

The mathematical function abs(x) produces the absolute value of x, which is -x if x is negative (less than zero) and otherwise is x itself.

The mathematical function min(x1, x2, ..., xN) produces the mathematically smallest of x1 through xN. The mathematical function max(x1, x2, ..., xN) produces the mathematically largest of x1 through xN. The domain and range of these mathematical functions include +∞ and -∞.

The notation “x modulo y” (y must be finite and nonzero) computes a value k of the same sign as y (or zero) such that abs(k) < abs(y) and x - k = q × y for some integer q.

The mathematical function floor(x) produces the largest integer (closest to positive infinity) that is not larger than x.

    NOTE
        floor(x) = x - (x modulo 1).