# Regex-and-Finite-Automata--Ruby
<body>

<h1 class="title">Finite Automata Interpreter </h1>

<p>

</center>

<p>
</p><h2>Introduction</h2>

<p>
You will need to implement code to build up NFAs from regular
expressions, convert them to DFAs, and test whether a string 
is accepted by a DFA.  You will do this by extending a
provided <tt>FiniteAutomaton</tt> class that operates as part of a
simple finite automata interpreter. 

</p>
To test your <tt>FiniteAutomaton</tt> implementation, you can execute 
it on input files from the command line by typing 
commands like <tt>ruby fa.rb &lt; public_size.in</tt>.  When you compare
your output to the output in files like <tt>public_size.out</tt>, be
aware that for full credit you only need to match lines that do not
begin with <tt>%</tt>; these lines are effectively comments to the
grading script.  Nevertheless, this output should help you debug your
implementation, to make sure you are constructing automata that have
the right number of states, transitions, etc. even if the state names
differ.
<p>
The <tt>fa.rb</tt> file you downloaded already implements a simple
interpreter for commands to build/transform finite automata, print out
various properties about them (such as the number of states they have,
or the strings they accept), and use them to accept/reject particular
strings.  The interpreter operates using a stack, where each object on
the stack is a finite automaton.  Commands pop zero, one, or two
finite automata from the stack and push zero or one finite automata on
stack as a result, along with potentially printing output.  Each
command is separated by a whitespace: there is nothing special about
line breaks.  The interpreter (implemented in top-level
function <tt>interpreter</tt>) works by creating
new <tt>FiniteAutomaton</tt> objects and invoking methods on them.
<p>
The interpreter will accept the following commands:

<ul>
<li> <i>symbol</i> where <i>symbol</i> is a lowercase letter or the
  uppercase <b>E</b>.  In the former case this command creates finite
  automaton that accepts just that letter, and in the latter case it
  creates an automaton that accepts just the empty string.  The new
  automaton is pushed onto the stack.
<li> <b>.</b> pops off top 2 finite automata on the stack and creates
a new NFA representing their concatenation, pushing this new NFA on to
  the stack
<li> <b>|</b> pops off top 2 finite automaton on stack, creates
a new NFA representing their union, pushing this new NFA on to
  the stack
<li> <b>*</b> pops off top finite automaton on stack, creates
a new NFA representing its closure, pushing this new NFA on to
  the stack
<li> <b>SIZE</b> prints # of states in the finite automaton at the top
  of the stack (without popping it off)
<li> <b>PRINT</b> prints the finite automaton at the top of the
  stack (without popping it off).  All output from this command is
  preceded by <tt>%</tt>, so it is just for documentation & debugging
<li> <b>STAT</b> prints some statistics about the finite automaton at 
the top of the stack (without popping it off)
<li> <b>DFA</b> converts finite automaton (DFA or NFA)
at the top of the stack to a DFA 
<li> <b>"str"</b> using finite automaton (must be a DFA) at the top 
of the stack, decide whether to accept or reject string <i>str</i>
<li> <b>GENSTR#</b> prints all strings accepted by finite automaton 
(must be a DFA) at the top of the stack of length # or less
<li> <b>COMPLEMENT</b> converts finite automaton (must be DFA)
on top of stack to a DFA that is its complement (i.e., rejects
strings accepted by the previous DFA, and accepts strings
rejected by the previous DFA)
<li> <b>DONE</b> interpreter exits
</ul>

<h3>Example</h3>

Here is an example session with the interpreter:
<pre>
a
b
.
PRINT
SIZE
c
|
*
DONE
</pre>
The first line creates an FA that accepts exactly the
string <tt>a</tt>, and pushes it on the stack.  The second line
creates an FA that accepts exactly the string <tt>b</tt>, and pushes
it on the stack.  The third line pops these two automata off of the
stack, and constructs a new automaton that accepts the concatenation
of strings accepted by the two, i.e., <tt>ab</tt>.  This new automaton
is pushed back on the stack, and is now the only automaton on the
stack.  
<p>
The next command prints out this automaton (leaving it on the stack),
producing output like the following:
<pre>
% Start 0
% Final { 3 }
% States { 0 1 2 3 }
% Alphabet { a b }
% Transitions {
%  (0 a 1)
%  (1  2)
%  (2 b 3)
% }
</pre>
Note that different implementations might produce different output in
this case, most notably because they could choose different names for
states.
<p>
The next command prints the number of states in the topmost automaton
(leaving it on the stack), in this case 4.  The next command constructs an
automaton that accepts the string <tt>c</tt> and pushes it on-stack.
The subsequent command pops off this automaton and combines with the
the <tt>ab</tt> automaton also on-stack to produce the automaton
implementing <tt>ab|c</tt>, pushing it on-stack.  The next command
creates a new automaton from this one, implementing <tt>(ab|c)*</tt>.
The final command terminates the session.
<p>
As mentioned, linebreaks are immaterial.  Exactly the same results
would be produced by the input
<pre>
a b . PRINT SIZE c | * DONE
</pre>

<h2>Part 1: Extend the FiniteAutomaton class to support NFAs</h2>

The <tt>FiniteAutomaton</tt> class in <tt>fa.rb</tt> is based on the
DFA class used as an example during discussion section.  The original
DFA class is able to represent DFAs and run them to determine whether
input strings are accepted by the DFA.  You need to decide how to
change/extend this FiniteAutomaton class to represent both NFAs and
DFAs.  In particular, your modified class should support additional
features allowed by NFAs, such as epsilon-transitions and multiple
transitions with the same label.  You may choose to represent
epsilon-transitions as transitions labeled with the empty string ""
(as we do in our reference implementation used to produce the sample
public outputs) or some other label.
<p>
If an automaton is deterministic (i.e., it represents a DFA), it
should be able to run on input strings; i.e.,
the <b>accept?</b> method should return true if your FiniteAutomaton
object is a DFA and it accepts the string; the <b>accept?</b>
method returns false if the automaton does not accept the string.
We will not test the <b>accept?</b> method on NFAs.

<h2>Part 2: Building NFAs out of other NFAs</h2>

The <b>symbol!</b> method for creating a new NFA for an individual
symbol is provided.   You need to add the ability
to construct more complex NFAs from these single-symbol NFAs. 
Three of the commands of the interpreter require the ability to
create new NFA from existing NFA, using one of the following actions:
concatenate, union, and closure.  You need to implement the
same functionality for NFAs represented by the FiniteAutomaton
class in the methods <b>concat!</b> <b>union!</b> <b>closure!</b>.  
You must construct the new NFA using the algorithm discussed in
lecture.  In particular, given two NFA <b>a</b> and <b>b</b> as follows:

<br>
<p>
Note that though there are other algorithms for generating correct NFA
for these operations, using them will yield a different NFA that will not
pass the submit server tests.  
Your methods may (destructively) modify the current NFAs <b>a</b>
and <b>b</b>.  You can assume there is only one start state
for each NFA.

<h2>Part 3: Implement utility functions</h2>

You should ensure that the existing code provided for 
the SIZE, PRINT, and GENSTR# commands will work with 
the extensions you made to the FiniteAutomaton class 
to support NFAs. Make whatever code changes are necessary.

Implement code to support the STAT command. The STAT command 
should output the following statistics about the finite 
automaton: the number of states, the number of final states,
the number of transitions, and the number of states found with
n (outgoing) transitions (in ascending order by n), using
the following format:  
<pre>
FiniteAutomaton
  4 states
  1 final states
  3 transitions
    1 states with 0 transitions
    3 states with 1 transitions
</pre>
If no states are found with n transitions for some value n,
do not output the line "0 states found with n transitions".
<p>
Make sure all utility functions except 
the GENSTR# command work for both DFAs and NFAs. 
These commands are useful for debugging your code during 
development, and are also used on the submit server to 
check the correctness of your solution automata.

<h2>Part 4: Reducing NFA to DFA</h2>

Once you have a NFA, implement code that can reduce it
to a new finite automaton representing the DFA created by
the subset reduction method presented in class.  I.e., the
method should return a new FiniteAutomaton object that will 
accept only the strings accepted by the current FiniteAutomaton 
object, but which does not have epsilon-transitions or multiple
transitions from a state with the same label.  You should
probably not find it necessary to modify the existing
NFA while reducing it to a DFA.

<h2>Part 5: Build complement of a DFA </h2>

Finally given a DFA x, implement code that can
build a new DFA y which is its complement.
I.e., y rejects all strings accepted by x, and accepts all strings
rejected by x.  You must use the algorithm discussed in lecture.
First add an explicit dead state and make explicit all transitions 
to it. Second change all final states to non-final states, and all 
non-final states to final states.  Your methods may (destructively) 
modify the current DFA when building its complement DFA.

</body>
