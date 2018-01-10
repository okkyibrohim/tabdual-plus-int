# TABDUAL+int : An Improvement of TABDUAL+ by Adding Interned Terms

## About TABDUAL+int
TABDUAL+int is an improvement of TABDUAL+ (see https://github.com/harventhy/tabdual-plus) by adding an advance feature tabling which is tabling with interned terms. TABDUAL+int is implemented in XSB Prolog, so you need to install XSB Prolog in your machine. For convenience, you might want to put the path to directory where the XSB command is found in your `$PATH` environment variable. Otherwise, you have to include the path to directory where you have this implementation into XSB default search paths using predicate `library_directory/1`.

## How to Specify Your Knowledge Base
You can define an input program, i.e. a logic program, as your own knowledge base inside `in` folder in a file called `{filename}.ab`. Your input program has to satisfy following properties:
* *Abducibles* is specified by predicate `abds/1` whose argument is a list of abducibles along with its arity. For example `abds([a/1, b/2, c/3])`.
* To define a rule (including an *integrity constraint*), please use `<-` instead of `:-` to denote *if* operator. For example, please use `H <- X, Y.` instead of using `H :- X, Y.`.
* Predicates comprising just fact are written separately between the `beginProlog.` and `endProlog.` identifiers. 
* Regular Prolog programs (those that will not be transformed) are also written separately between the `beginProlog.` and `endProlog.` identifiers. Please use `H :- X, Y.` instead of using `H <- X, Y.` when defining regular Prolog programs because these programs will not be transformed but will be rewritten as it is.

With regard to properties mentioned above, your input program must be written in following parts, ordered from top to bottom:
1. `beginProlog.` and `endProlog.` identifiers, and also facts and programs that need to be placed between them. This part is unnecessary when no such program in it.
2. Abducibles.
3. Rules.

## How TABDUAL+int Works
TABDUAL+int implementation consists of two phases:
1. **Program transformation.** 
In transformation phase, your input program will be transformed into its corresponding output program that supports contextual abduction. The output program will be used in the next stage. Before you transform your input program, you can choose what mode of transformation you want to use using predicate `switch_mode/1`. TABDUAL+int provides four transformation modes, which are no tabling, normal tabling, tabling with answer subsumption, and tabling with interned terms. Those program transformation respectively coded into n, t, s, and i. For example, if you want to using tabling with interned terms mode, you must switch mode using directive `switch_mode(i)`. Notice that in TABDUAL+int, the default mode is normal tabling mode. So, if you skip this step, you will automatically using normal tabling mode for transformation program. <br />
To transform your input program `{filename}.ab`, type `transform({filename}).` and then hit *Enter*. The output program `{filename}_{InitialMode}.pl` will be created in `out` folder. For example, if you want to transform an input program `goodgrades.ab` using tabling with interned terms mode, you have to type `transform(goodgrades)`, and consequently a file `goodgrades_int.pl` will be created in `out` folder. Remember that your input program `goodgrades.ab` must be placed inside `in` folder. To avoid errors in the next steps, please always perform this step even though the corresponding output program has been created.
2. **Abduction.** 
In abduction phase, you may perform the abduction itself output program that is produced in the previous stage. Practically, you have to load the output program first, only then you can perform abduction by asking queries to the loaded program.
 
## How to Use
1. Open a terminal in the directory where you have this implementation.

2. Invoke XSB by the command:
```
$ xsb
```
Notice that this command may vary depends on how XSB is configured in your machine. Make sure you have entered XSB prompt level (indentified by `| ?-` prompt) before you proceed to the next step.

3. Type `[tabdual].` and then hit *Enter* to load the main program.  

4. Type `load({filename}).` and then hit *Enter* to load your output program.  For example, you have to type `load(goodgrades)`  to load the output program `goodgrades_int.pl`. Make sure you have transformed the corresponding input program that you want to load, otherwise errors may occur.

5. Now you may ask a query using predicate `ask/1` whose argument is the query you want to ask. If you want to retrieve the solution gradually, please use `ask/2` whose first argument is the query you want to ask, while the second is the solution to the query, retrieved one by one. If you want to supply abductive contexts, use predicate `ask/3` instead, whose second argument is a list of abductive contexts you want to supply, the first and the last arguments remain the same as `ask/2`. Please do not forget to use parentheses if you are asking multiple goals in the query. Here are some example of usage predicate `ask/1`, `ask/2`, and `ask/3`. These examples will use `in/goodgrades.ab` as its input program.

    	% using ask/1
	`?- ask(getting_good_grades)).`<br />                       
    	`(1) [getting_lucky,healthy]`<br />
	`(2) [have_study_notes]`<br />
    	`yes`<br />
    
    	% using ask/1 with multiple goals
	`?- ask((getting_good_grades,studying_hard)).` <br />        
    	`(1) [getting_lucky,healthy,have_study_notes]` <br />
	`(2) [have_study_notes]` <br />
    	`yes` <br />
    
    	% using ask/2
	`?- ask(getting_good_grades, O).` <br />                    
    	`O = [getting_lucky,healthy];` 	% type ';' to retrieve next goals <br />                   
	`O = [have_study_notes];` <br />
    	`no` <br />

    	% using ask/3 (with conflicting abductive context)
	`?- ask(q(1), [not a(1)], O).` <br />        
    	`O = [not have_study_notes,getting_lucky,healthy];`<br />
    	`no`
`    
