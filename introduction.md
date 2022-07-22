### Introduction 
- ANTLR v4 is a powerful parser generator that you can use to read, process, execute, or translate structured text or binary files.  
- It’s widely used in academia and industry to build all sorts of languages, tools, and frameworks.
### Installlation
https://github.com/dohkoos/antlr4-short-course/blob/master/installing-antlr.md
### Refenrence 
The definitive ANTLR 4 refenrence, 2nd version
### Short cut Chinese Version
https://github.com/dohkoos/antlr4-short-course
### Book demo examples
https://github.com/yytshirley/Grammars/blob/master/book-examples
### Basic Concepts
**Language：**
A language is a set of valid sentences; sentences are composed of phrases, which are composed of subphrases, and so on.

**Grammar：**
A grammar formally defines the syntax rules of a language. Each rule in a grammar expresses the structure of a subphrase.

**Syntax tree or parse tree：** This represents the structure of the sentence where each subtree root gives an abstract name to the elements beneath it. The subtree roots correspond to grammar rule names. The leaves of the tree are symbols or tokens of the sentence.

![image](https://user-images.githubusercontent.com/108787042/180364329-57cbdd7f-0eaa-470b-a815-73ba6490d7e1.png)  

**Token：** A token is a vocabulary symbol in a language; these can represent a category of symbols such as “identifier” or can represent a single operator or keyword.

**Lexer or tokenizer：** This breaks up an input character stream into tokens and passes them to a parser that checks the syntax. A lexer performs lexical analysis.

**Parser：** A parser checks sentences for membership in a specific language by checking the sentence’s structure against the rules of a grammar. The best analogy for parsing is traversing a maze, comparing words of a sentence to words written along the floor to go from entrance to exit. ANTLR generates top-down parsers called ALL(*) that can use all remaining input symbols to make decisions. Top-down parsers are goal-oriented and start matching at the rule associated with the coarsest construct, such as program or inputFile.

**Recursive-descent parser：** This is a specific kind of top-down parser implemented with a function for each rule in the grammar.

**Lookahead：** Parsers use lookahead to make decisions by comparing the symbols that begin each alternative.

**Parse-tree Listeners：** PANTLR generates a parse-tree listener interface that responds to events triggered by the built-in tree walker. The listeners themselves are exactly like SAX document handler objects for XML parsers. SAX listeners receive notification of events like startDocument and endDocument. 

![image](https://user-images.githubusercontent.com/108787042/180364907-b2d6a5d9-cf6d-4732-af72-560e953700ff.png)

 
**Parse-tree Visitors：** When we want to control the walk itself, explicitly calling methods to visit children, option -visitor asks ANTLR to generate a visitor interface from a grammar with a visit method per rule. 

![image](https://user-images.githubusercontent.com/108787042/180365180-5626b54b-72b2-4091-b19b-de2838a6603c.png)

**Two key ANTLR components：** the ANTLR tool itself and the ANTLR runtime API. The runtime is a library of classes and methods needed by that generated code such as Parser, Lexer, and Token. First we run ANTLR on a grammar and then compile the generated code against the runtime classes in the jar. Ultimately, the compiled application runs in conjunction with the runtime classes.

![image](https://user-images.githubusercontent.com/108787042/180365799-2a2be1d3-b2b4-4b7c-ae94-7bda320d5e96.png)

### Book Examples
- Matching an Arithmetic Expression Language tour/Expr.g4   
1. Rules/parser lowercase letter, Tokens/lexer Uppercase letter
2. handle left-recursive rules
```
​  tour/t.expr 
​ 	193
​ 	a = 5
​ 	b = 6
​ 	a+b*2
​ 	(1+2)*3
```

```
grammar Expr;
/** The start rule; begin parsing here.
A program is a sequence of statements terminated by newlines.
A statement is either an expression, an assignment or a blank line. */
prog:   stat+ ; 

stat:   expr NEWLINE                
    |   ID '=' expr NEWLINE        
    |   NEWLINE                   
    ;

expr:   expr ('*'|'/') expr   
    |   expr ('+'|'-') expr   
    |   INT                    
    |   ID                    
    |   '(' expr ')'         
    ;

ID  :   [a-zA-Z]+ ;      // match identifiers <label id="code.tour.expr.3"/>
INT :   [0-9]+ ;         // match integers
NEWLINE:'\r'? '\n' ;     // return newlines to parser (is end-statement signal)
WS  :   [ \t]+ -> skip ; // toss out whitespace
```
Split a grammar into parser and lexer grammars and factor out lexical rules into a “module”.
```
lexer grammar CommonLexerRules; // note "lexer grammar"

ID  :   [a-zA-Z]+ ;      // match identifiers
INT :   [0-9]+ ;         // match integers
NEWLINE:'\r'? '\n' ;     // return newlines to parser (end-statement signal)
WS  :   [ \t]+ -> skip ; // toss out whitespace
```

- Building a Cacular Using a Visitor
1. label the alternatives of the rules
```
grammar LabeledExpr; // rename to distinguish from Expr.g4

prog:   stat+ ;

stat:   expr NEWLINE                # printExpr
    |   ID '=' expr NEWLINE         # assign
    |   NEWLINE                     # blank
    ;

expr:   expr op=('*'|'/') expr      # MulDiv
    |   expr op=('+'|'-') expr      # AddSub
    |   INT                         # int
    |   ID                          # id
    |   '(' expr ')'                # parens
    ;

MUL :   '*' ; // assigns token name to '*' used above in grammar
DIV :   '/' ;
ADD :   '+' ;
SUB :   '-' ;
ID  :   [a-zA-Z]+ ;      // match identifiers
INT :   [0-9]+ ;         // match integers
NEWLINE:'\r'? '\n' ;     // return newlines to parser (is end-statement signal)
WS  :   [ \t]+ -> skip ; // toss out whitespace
```
tour/EvalVisitor.java: https://github.com/yytshirley/Grammars/blob/master/book-examples/tour/EvalVisitor.java#L58

- Building a  Translator with a Listener

```
	​import​ java.util.List;
​ 	​import​ java.util.Map;
​ 	​public​ ​class​ Demo {
​ 	        ​void​ f(​int​ x, String y) { }
​ 	        ​int​[ ] g(​/*no args*/​) { ​return​ ​null​; }
​ 	        List<Map<String, Integer>>[] h() { ​return​ ​null​; }
​ 	}
```
```
​ 	​interface​ IDemo {
​ 	        ​void​ f(​int​ x, String y);
​ 	        ​int​[ ] g(​/*no args*/​);
​ 	        List<Map<String, Integer>>[] h();
​ 	}
```

