<h1> How to implement and operate </h1>
<h3> MakeFile </h3>
By convention, the main target should be placed at the first. Therefore the *all* was brought up to the top and added new dependencies, *cminus* and *cminus_flex*. *cminus*   needs *main.o*, *util.o*, and *scan.o*. *cminus_flex* needs all of *cminus* but *scan.o* because it would be automatically generated by *flex*. To do so, as written in the hint, the following lines is appended.

```
OBJS_FLEX=main.o util.o lex.yy.o
#by flex
cminus_flex: $(OBJS_FLEX)
	$(CC) $(CFLAGS) main.o util.o lex.yy.o -o cminus_flex -lfl

lex.yy.o: cminus.l scan.h util.h globals.h
	flex cminus.l
	$(CC) $(CFLAGS) -c lex.yy.c -lfl
```

This needs [flex](https://www.gnu.org/software/flex/) installed.

<h3> scan.h </h3>
*MAXTOKENLEN* should be bigger than 40(default) to handle longer tokens, especially the comments depending on the input.

<h3> scan.c </h3>
DFA
INLT, INGT, INNE, etc. are not described in the DFA because they can be constructed in similar way of INEQ.


<h3> cminus.l </h3>
'/*' is treated nontrivally manner. *flag* would be 1 after closing '*' occurs. If the following character is '/', the loop breaks and consume all the characters so far. If not, '/' is consumed as a part of the comment and continue with *flag*=0.

```
"/*"             { char c;
					int flag = 0;
                  do
                  { c = input();
                    if (c == EOF) break;
                    else if (c == '\n') lineno++;
					else if (c == '*') flag = 1;
					else if (c == '/' && flag) break;
					else flag = 0;
                  } while (1);
                }
.               {return ERROR;}
```


<h1> How to compile and run </h1>

<h3> Requirements </h3>

[flex](https://www.gnu.org/software/flex/)

```
make
./cminus [input file name]
./cminus_flex [input file name]
```

<h2> Environment </h2>
Ubuntu 16.04.3 LTS  
g++ 5.4.0 20160609

<h1> Screenshot </h1>
<h3> sample-cminus.tny</h3>

```
/* A program to perform Euclid's
Algorithm to computer gcd */
int gcd (int u, int v)
{
if (v == 0) return u;
else return gcd(v,u-u/v*v);
/* u-u/v*v == u mod v */
}
void main(void)
{
int x; int y;
x = input(); y = input();
output(gcd(x,y));
}
```

<h3> Result of the scanner with C code </h3>
! [Alt Text] (screenshot_cminus.png)
<h3> Result of the scanner with flex </h3>
! [Alt Text] (screenshot_cminus_flex.png)
