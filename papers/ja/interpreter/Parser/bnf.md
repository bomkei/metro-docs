# BNF
## expr
```
constkwd        = "none" | "true" | "false"

dict_item       = expr ":" expr
dict            = "{" dict_item ("," dict_item)* "}"

sym             = type ("::" type)*
variable        = sym
callfunc        = sym "(" expr? ("," expr)* ")"

factor          = "(" expr ")" | constkwd | dict | stmt | imm | variable | callfunc

vector          = "[" (expr ("," expr)*)? "]"
typed_dict      = "dict" "<" type "," type ">" dict
cast            = "cast" "<" type ">" "(" expr ")"
primary         = vector | typed_dict | cast | factor

unary           = ("+" | "-")? primary
indexref        = unary ("[" expr ("," expr)* "]")?
member_access   = indexref ("." indexref)*
mul             = member_access (("*" | "/" | "%") member_access)*
add             = mul (("+" | "-") mul)*
shift           = add (("<<" | ">>") add)*
compare         = shift (("==" | "!=" | ">=" | "<=" | ">" | "<") shift)*
bit_op          = compare (("&" | "^" | "|") compare)*
log_and_or      = bit_op (("&&" | "||") bit_op)*
range           = log_and_or (".." log_and_or)?
assign          = range ("=" assign)?

expr            = assign
```

## stmt
```
if              = "if" expr scope ("else" (if | scope))?
case            = "case" expr ":" scope
switch          = "switch" expr "{" case "}"
return          = "return" expr?
break           = "break"
continue        = "continue"
controls        = if | case | switch | return | break | continue

loop            = "loop" scope
for             = "for" expr "in" expr scope
while           = "while" expr scope
do_while        = "do" scope "while" expr
loops           = loop | for | while | do_while

let             = "let" ident (":" type)? ("=" expr)?

scope           = "{" (expr semi?)* "}"

stmt            = scope | controls | loops | let
```

## program
```
import          = "import" ident ("/" ident)*

function        = "fn" ident "(" (argument ("," argument)*)? ")"
                  "->" type scope

program         = (import | function | expr semi?)*
```

## other
```
type            = ident ("<" type ("," type)* ">")? "const"?
argument        = ident ":" type
```
