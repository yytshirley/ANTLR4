# ANTLR4-阅读笔记
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
- Designning Grammers

  1. ANTLR Core Notation: https://learning.oreilly.com/library/view/the-definitive-antlr/9781941222621/f_0036.xhtml#sec.common-lex-structures
  2. Common computer lanaguage patterns: https://learning.oreilly.com/library/view/the-definitive-antlr/9781941222621/f_0036.xhtml#sec.common-lex-structures
  3. Precedence
  > ANTLR resolves ambiguities in favor of the alternative given first, implicitly allowing us to specify operator precedence. ANTLR associates operators
  > left to right as we’d expect for * and +. Some operators like exponentiation group right to left, though, so we have to manually specify the associativity on the     > operator token using option assoc.
  > ANTLR lexers resolve ambiguities between lexical rules by favoring the rule specified first
  
  ```
  	expr ​:​ expr ​'^'​​<​assoc​=​right​>​ expr ​// ^ operator is right associative
	     ​|​ INT
 	     ​;
  ```
**Syntax tree or parse tree：** This represents the structure of the sentence where each subtree root gives an abstract name to the elements beneath it. The subtree roots correspond to grammar rule names. The leaves of the tree are symbols or tokens of the sentence.

![image](https://user-images.githubusercontent.com/108787042/180364329-57cbdd7f-0eaa-470b-a815-73ba6490d7e1.png)  

**Token：** A token is a vocabulary symbol in a language; these can represent a category of symbols such as “identifier” or can represent a single operator or keyword.

**Lexer or tokenizer：** This breaks up an input character stream into tokens and passes them to a parser that checks the syntax. A lexer performs lexical analysis.
- Lexer
  1. Precedence
  > ANTLR lexers resolve ambiguities between lexical rules by favoring the rule specified first. 
  2. By prefixing the rule with fragment, we let ANTLR know that the rule will be used only by other lexical rules.
  3. The dot wildcard operator matches any single character. Therefore, .* would be a loop that matches any sequence of zero or more characters.The loop in STRING now matches either an escape character sequence, by calling fragment rule ESC, or any single character via the dot wildcard. The *? subrule operator terminates the (ESC|.)*? loop upon seeing what follows, an unescaped double-quote character.
```
	​grammar​ KeywordTest​;​
​ 	enumDef ​:​ ​'enum'​ ​'{'​ ​...​ ​'}'​ ​;​
​ 	​...​
​ 	FOR ​:​ ​'for'​ ​;​
​ 	​...​
​ 	ID ​:​ ​[​a​-​zA​-​Z​]+​ ​;​ ​// does NOT match 'enum' or 'for'
```
Token Category	Description and Examples
Punctuation

The easiest way to handle operators and punctuation is to directly reference them in parser rules.
```
​ 	call ​:​ ID ​'('​ exprList ​')'​ ​;​
```
Some programmers prefer to define token labels such as LP (left parenthesis) instead.
```
​ 	call ​:​ ID LP exprList RP ​;​
​ 	LP ​:​ ​'('​ ​;​
​ 	RP ​:​ ​')'​ ​;​
```
Keywords

Keywords are reserved identifiers, and we can either reference them directly or define token types for them.
```
​ 	returnStat ​:​ ​'return'​ expr ​';'​
```
Identifiers

Identifiers look almost the same in every language, with some variation about what the first character can be and whether Unicode characters are allowed.
```
​ 	ID ​:​ ID_LETTER ​(​ID_LETTER ​|​ DIGIT​)*​ ​;​ ​// From C language​
​ 	​fragment​ ID_LETTER ​:​ ​'a'​​..​​'z'​​|​​'A'​​..​​'Z'​​|​​'_'​ ​;​
​ 	​fragment​ DIGIT ​:​ ​'0'​​..​​'9'​ ​;​
```
Numbers

These are definitions for integers and simple floating-point numbers.
```
​ 	INT ​:​ DIGIT​+​ ​;​
​ 	FLOAT
​ 	    ​:​ DIGIT​+​ ​'.'​ DIGIT​*​
​ 	    ​|​ ​'.'​ DIGIT​+​
​ 	    ​;​
```
Strings

Match double-quoted strings.
```
​ 	STRING ​:​  ​'"'​ ​(​ ESC ​|​ ​.​ ​)*?​ ​'"'​ ​;​
​ 	​fragment​ ESC ​:​ ​'\\'​ ​[​btnr​"\\]​ ​;​ ​// \b, \t, \n etc...​
```
Comments
  ```
  ​ 	assign ​:​ ID ​(​WS​|​COMMENT​)?​ ​'='​ ​(​WS​|​COMMENT​)?​ expr ​(​WS​|​COMMENT​)?​ ​;
  ```
Match and discard comments.
```
​ 	LINE_COMMENT ​:​ ​'//'​ ​.*?​ ​'\n'​ ​->​ skip ​;​
​ 	COMMENT      ​:​ ​'/*'​ ​.*?​ ​'*/'​ ​->​ skip ​;​
```
Whitespace

Match whitespace in the lexer and throw it out.
```
​ 	WS ​:​ ​[​ ​\​t​\​n​\​r​]+​ ​->​ skip ​;
```
**Parser：** A parser checks sentences for membership in a specific language by checking the sentence’s structure against the rules of a grammar. The best analogy for parsing is traversing a maze, comparing words of a sentence to words written along the floor to go from entrance to exit. ANTLR generates top-down parsers called ALL(*) that can use all remaining input symbols to make decisions. Top-down parsers are goal-oriented and start matching at the rule associated with the coarsest construct, such as program or inputFile.

**Recursive-descent parser：** This is a specific kind of top-down parser implemented with a function for each rule in the grammar.

**Lookahead：** Parsers use lookahead to make decisions by comparing the symbols that begin each alternative.

**Parse-tree Listeners：** PANTLR generates a parse-tree listener interface that responds to events triggered by the built-in tree walker. The listeners themselves are exactly like SAX document handler objects for XML parsers. SAX listeners receive notification of events like startDocument and endDocument. Listener event methods also can’t use method return values to pass data around. When we need to control the walk or want to return values with event-method return values, 

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
classDeclaration
    :   'class' Identifier typeParameters? ('extends' type)?
        ('implements' typeList)?
        classBody
    ;
```
```
​ 	​interface​ IDemo {
​ 	        ​void​ f(​int​ x, String y);
​ 	        ​int​[ ] g(​/*no args*/​);
​ 	        List<Map<String, Integer>>[] h();
​ 	}
```
```
methodDeclaration
    :   type Identifier formalParameters ('[' ']')* methodDeclarationRest
    |   'void' Identifier formalParameters methodDeclarationRest
    ;
```
  
  tour/ExtractInterfaceListener.java: https://github.com/yytshirley/Grammars/blob/master/book-examples/tour/ExtractInterfaceListener.java

- Embedding Arbitrary Actions in a Grammar
- Island Grammars: Dealing with Different Formats in the Same File
- Drawing the line between lexer and parser
  > 192.168.209.85 "GET /download/foo.html HTTP/1.0" 200
    1. Count how many lines there are in the file. The lexer doesn’t have to recognize much in the way of structure, and the parser matches a sequence of newline tokens.
    2. Collect a list of IP addresses from the log file. This means we need a rule to recognize the lexical structure of an IP address, and we might as well provide lexer rules for the other record elements.

```
 	file  ​:​ NL​+​ ​;​              ​// parser rule matching newline (NL) sequence​
​ 	STUFF ​:​ ​~​​'\n'​​+​ ​->​ skip ​;​   ​// match and discard anything but a '\n'​
​ 	NL    ​:​ ​'\n'​ ​;​             ​// return NL to parser or other invoking code

 	IP    ​:​ INT ​'.'​ INT ​'.'​ INT ​'.'​ INT ​;​ ​// 192.168.209.85​
​ 	INT   ​:​ ​[​0​-​9​]+​ ​;​        ​// match IP octet or HTTP result code​
​ 	STRING​:​ ​'"'​ ​.*?​ ​'"'​ ​;​   ​// matches the HTTP protocol command​
​ 	NL    ​:​ ​'\n'​ ​;​          ​// match log file record terminator​
​ 	WS    ​:​ ​' '​ ​->​ skip ​;​   ​// ignore spaces
``` 
```
​ 	file  ​:​ row​+​ ​;​                ​// parser rule matching rows of log file​
​ 	row   ​:​ ip STRING INT NL ​;​    ​// match log file record​
​ 	ip    ​:​ INT ​'.'​ INT ​'.'​ INT ​'.'​ INT ​;​ ​// match IPs in parser​
​ 	
​ 	INT   ​:​ ​[​0​-​9​]+​ ​;​        ​// match IP octet or HTTP result code​
​ 	STRING​:​ ​'"'​ ​.*?​ ​'"'​ ​;​   ​// matches the HTTP protocol command​
​ 	NL    ​:​ ​'\n'​ ​;​          ​// match log file record terminator​
​ 	WS    ​:​ ​' '​ ​->​ skip ​;​   ​// ignore spaces
```


- Parse CSV
```
​ 	Details,Month,Amount
​ 	Mid Bonus,June,"$2,000"
​ 	,January,"""zippo"""
​ 	Total Bonuses,"","$5,000"
```
  ```
  grammar CSV;

file : hdr row+ ;
hdr : row ;

row : field (',' field)* '\r'? '\n' ;

field
    :   TEXT
    |   STRING
    |
    ;

TEXT : ~[,\n\r"]+ ;
STRING : '"' ('""'|~'"')* '"' ; // quote-quote is an escaped quote
```

- Parse Json

```
​ 	{
​ 	        "antlr.org": {
​ 	                "owners" : [],
​ 	                "live" : ​true​,
​ 	                "speed" : 1​e​100,
​ 	                "menus" : [​"File"​, ​"Help\nMenu"​]
​ 	        }
​ 	}
```

```
// Derived from http://json.org

grammar JSON;

json:   object
    |   array
    ;

object
    :   '{' pair (',' pair)* '}'
    |   '{' '}' // empty object
    ;
pair:   STRING ':' value ;

array
    :   '[' value (',' value)* ']'
    |   '[' ']' // empty array
    ;

value
    :   STRING
    |   NUMBER
    |   object  // recursion
    |   array   // recursion
    |   'true'  // keywords
    |   'false'
    |   'null'
    ;

STRING :  '"' (ESC | ~["\\])* '"' ;

fragment ESC :   '\\' (["\\/bfnrt] | UNICODE) ;
fragment UNICODE : 'u' HEX HEX HEX HEX ;
fragment HEX : [0-9a-fA-F] ;

NUMBER
    :   '-'? INT '.' INT EXP?   // 1.35, 1.35E-9, 0.3, -4.5
    |   '-'? INT EXP            // 1e10 -3e4
    |   '-'? INT                // -3, 45
    ;
fragment INT :   '0' | [1-9] [0-9]* ; // no leading zeros
fragment EXP :   [Ee] [+\-]? INT ; // \- since - means "range" inside [...]

WS  :   [ \t\n\r]+ -> skip ;
```

-Parse  Cymbol

```
​ 	// Cymbol test
​ 	int g = 9;        // a global variable
​ 	int fact(int x) { // factorial function
​ 	    if x==0 then return 1;
​ 	    return x * fact(x-1);
​ 	}
```

```
/** Simple statically-typed programming language with functions and variables
 *  taken from "Language Implementation Patterns" book.
 */
grammar Cymbol;

file:   (functionDecl | varDecl)+ ;

varDecl
    :   type ID ('=' expr)? ';'
    ;
type:   'float' | 'int' | 'void' ; // user-defined types

functionDecl
    :   type ID '(' formalParameters? ')' block // "void f(int x) {...}"
    ;
formalParameters
    :   formalParameter (',' formalParameter)*
    ;
formalParameter
    :   type ID
    ;

block:  '{' stat* '}' ;   // possibly empty statement block
stat:   block
    |   varDecl
    |   'if' expr 'then' stat ('else' stat)?
    |   'return' expr? ';' 
    |   expr '=' expr ';' // assignment
    |   expr ';'          // func call
    ;

/* expr below becomes the following non-left recursive rule:
expr[int _p]
    :   ( '-' expr[6]
        | '!' expr[5]
        | ID
        | INT
        | '(' expr ')'
        )
        ( {8 >= $_p}? '*' expr[9]
        | {7 >= $_p}? ('+'|'-') expr[8]
        | {4 >= $_p}? '==' expr[5]
        | {10 >= $_p}? '[' expr ']'
        | {9 >= $_p}? '(' exprList? ')'
        )*
    ;
*/

expr:   ID '(' exprList? ')'    // func call like f(), f(x), f(1,2)
    |   expr '[' expr ']'       // array index like a[i], a[i][j]
    |   '-' expr                // unary minus
    |   '!' expr                // boolean not
    |   expr '*' expr
    |   expr ('+'|'-') expr
    |   expr '==' expr          // equality comparison (lowest priority op)
    |   ID                      // variable reference
    |   INT
    |   '(' expr ')'
    ;
exprList : expr (',' expr)* ;   // arg list

ID  :   LETTER (LETTER | [0-9])* ;
fragment
LETTER : [a-zA-Z] ;

INT :   [0-9]+ ;

WS  :   [ \t\n\r]+ -> skip ;

SL_COMMENT
    :   '//' .*? '\n' -> skip
    ;
```
- Implementating Applications With Parse-Tree Listeners  
  PropertyFileListener.java: https://github.com/yytshirley/Grammars/blob/master/book-examples/listeners/PropertyFileListener.java
  ![image](https://user-images.githubusercontent.com/108787042/180385066-3379b070-afe0-465d-8ad5-94a0970ab378.png)

- Implementating Applications With Vistors  
  When we use the -visitor option on the command line, ANTLR generates interface PropertyFileVisitor and class PropertyFileBaseVisitor  
  https://github.com/yytshirley/Grammars/blob/master/book-examples/listeners/TestPropertyFileVisitor.java
  ![image](https://user-images.githubusercontent.com/108787042/180384921-fd9c533e-fda6-4813-b45c-692ef15ce8d4.png)
 
 - A simple calculator with a listener 
 ```
 ​ 	​grammar​ Expr​;​
​ 	s ​:​ e ​;​
​ 	e ​:​ e op​=​MULT e    ​// MULT is '*'​
​ 	  ​|​ e op​=​ADD e     ​// ADD is '+'​
​ 	  ​|​ INT
​ 	  ​;
```
```
public class TestEvaluator {
    /** Sample "calculator" (special case of collector) */
    public static class Evaluator extends ExprBaseListener {
        Stack<Integer> stack = new Stack<Integer>();
        public void exitE(ExprParser.EContext ctx) {
            if ( ctx.getChildCount()==3 ) { // operations have 3 children
                int right = stack.pop();
                int left = stack.pop();
                if ( ctx.op.getType()==ExprParser.MULT ) {
                    stack.push( left * right );
                }
                else {
                    stack.push( left + right ); // must be add
                }
            }
        }

        public void visitTerminal(TerminalNode node) {
            Token symbol = node.getSymbol();
            if ( symbol.getType()==ExprParser.INT ) {
                stack.push( Integer.valueOf(symbol.getText()) );
            }
        }
    }
```
```
 	​public​ ​static​ ​class​ EContext ​extends​ ParserRuleContext {
​ 	    ​public​ Token op;                     ​// derived from label op​
​ 	    ​public​ List<EContext> e() { ... }    ​// get all e subtrees​
​ 	    ​public​ EContext e(​int​ i) { ... }     ​// get ith e subtree​
​ 	    ​public​ TerminalNode INT() { ... }    ​// get INT node if alt 3 of e​
​ 	    ...
​ 	}
```
- Sharing Information among Event Methods  
  visitor return https://github.com/yytshirley/Grammars/blob/master/book-examples/listeners/TestEvalVisitor.java
```
public class TestEvalVisitor {
    // a4 -visitor LExpr.g4
    /** Visitor "calculator" */
    public static class EvalVisitor extends ExprBaseVisitor<Integer> {
        public Integer visitE(ExprParser.EContext ctx) {
            if ( ctx.getChildCount()==3 ) { // operations have 3 children
                if ( ctx.op.getType()==ExprParser.MULT ) {
                    return visit(ctx.e(0)) * visit(ctx.e(1));
                }
                else {
                    return visit(ctx.e(0)) + visit(ctx.e(1)); // must be add
                }
            }
            return visitChildren(ctx);   // must be e above INT
        }

        @Override
        public Integer visitTerminal(TerminalNode node) {
            if ( node.getSymbol().getType()==ExprParser.INT ) {
                return Integer.valueOf(node.getText());
            }
            return 0;
        }
    }
```
    tree properties  https://github.com/yytshirley/Grammars/blob/master/book-examples/listeners/TestEvaluator.java
    
   
```
        /** Sample "calculator" using tree properties not stack */
    public static class EvaluatorWithProps extends ExprBaseListener {
        ParseTreeProperty<Integer> values = new ParseTreeProperty<Integer>();

        @Override
        public void exitS(ExprParser.SContext ctx) {
            values.put(ctx, values.get(ctx.getChild(0)));
        }

        public void exitE(ExprParser.EContext ctx) {
            if ( ctx.getChildCount()==3 ) { // operations have 3 children
                int left = values.get(ctx.e(0));
                int right = values.get(ctx.e(1));
                if ( ctx.op.getType()==ExprParser.MULT ) {
                    values.put(ctx, left * right);
                }
                else {
                    values.put(ctx, left + right);
                }
            }
            else {
                values.put(ctx, values.get(ctx.getChild(0))); // an INT
            }
        }

        public void visitTerminal(TerminalNode node) {
            Token symbol = node.getSymbol();
            if ( symbol.getType()==ExprParser.INT ) {
                values.put(node, Integer.valueOf(symbol.getText()));
            }
        }
    }
```



