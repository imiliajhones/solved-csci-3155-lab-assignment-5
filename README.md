Download Link: https://assignmentchef.com/product/solved-csci-3155-lab-assignment-5
<br>
The primary purpose of this lab to explore mutation or imperative updates in programming languages. With mutation, we explore two related language considerations: parameter passing modes and casting. Concretely, we extend JAVASCRIPTY with mutable variables and objects, parameter passing modes, (recursive) type declarations, type casting. At this point, we have many of the key features of JavaScript/TypeScript, except dynamic dispatch.

Parameters are always passed by value in JavaScript/TypeScript, so the parameter passing modes in JAVASCRIPTY is an extension beyond JavaScript/TypeScript. In particular, we consider parameter passing modes primarily to illustrate a language design decision and how the design decision manifests in the operational semantics. Call-by-value with addresses and callby-reference are often confused, but with the operational semantics, we can see clearly the distinction.

We will update our type checker and small-step interpreter from Lab 4 and see that mutation forces a global refactoring of our interpreter. To minimize the impact of this refactoring, we will be explore the functional programming idea of encapsulating computation in a data structure (known as a <em>monad</em>). We will also consider the idea of transforming code to a “lowered” form to make it easier to implement interpretation.

<strong>PL Ideas </strong>Imperative programming (memory, addresses, aliasing). Language design choices (via parameter passing modes as a case study).

<strong>FP Skills </strong>Encapsulating computation as a data structure.

<strong>Instructions. </strong>From your team of 8-10 persons in your lab section, find a new partner for this lab assignment (different from your Lab 1 partner). You will work on this assignment closely with your partner. However, note that <strong>each student needs to submit </strong>and are individually responsible for completing the assignment.

You are welcome to talk about these questions in larger groups. However, we ask that you write up your answers in pairs. Also, be sure to acknowledge those with which you discussed, including your partner and those outside of your pair.

Recall the evaluation guideline from the course syllabus.

<em>Both your ideas and also the clarity with which they are expressed matter—both in your English prose and your code!</em>

<em>We will consider the following criteria in our grading:</em>

1

<ul>

 <li>How well does your submission answer the questions? <em>For example, a common mistake is to give an example when a question asks for an explanation. An example may be useful in your explanation, but it should not take the place of the explanation.</em></li>

 <li>How clear is your submission? <em>If we cannot understand what you are trying to say, then we cannot give you points for it. Try reading your answer aloud to yourself or a friend; this technique is often a great way to identify holes in your reasoning. For code, not every program that “works” deserves full credit. We must be able to read and understand your intent. Make sure you state any preconditions or invariants for your functions (either in comments, as assertions, or as </em><em>require clauses as appropriate).</em></li>

</ul>

Try to make your code as concise and clear as possible. Challenge yourself to find the most crisp, concise way of expressing the intended computation. This may mean using ways of expression computation currently unfamilar to you.

Finally, make sure that your file compiles and runs on COG. A program that does not compile will <em>not </em>be graded.

<strong>Submission Instructions.              </strong>Upload to the moodle exactly four files named as follows: • Lab5_<em>YourIdentiKey</em>.scala with your answers to the coding exercises

<ul>

 <li>Lab5Spec-<em>YourIdentiKey</em>.scala with any updates to your unit tests.</li>

 <li>Lab5-<em>YourIdentiKey</em>.jsy with a challenging test case for your JAVASCRIPTY</li>

 <li>Lab5_<em>YourIdentiKey</em>.pdf with your answers to the written exercises (only written exercise is for extra credit).</li>

</ul>

Replace <em>YourIdentiKey </em>with your IdentiKey (e.g., I would submit Lab5-bec.pdf and so forth). Don’t use your student identification number. To help with managing the submissions, we ask that you rename your uploaded files in this manner.

Submit your Lab5.scala file to COG for auto-testing. We ask that you submit both to COG and to moodle in case of any issues.

Sign-up for an interview slot for an evaluator. To fairly accommodate everyone, the interview times are strict and <strong>will not be rescheduled</strong>. Missing an interview slot means missing the interview evaluation component of your lab grade. Please take advantage of your interview time to maximize the feedback that you are able receive. Arrive at your interview ready to show your implementation and your written responses. Implementations that do not compile and run will not be evaluated.

<strong>Getting Started. </strong>Clone the code from the Github repository with the following command: git clone -b lab5 https://github.com/bechang/pppl-labs.git lab5

A suggested way to get familiar with Scala is to do some small lessons with Scala Koans <a href="http://www.scalakoans.org/">(</a><a href="http://www.scalakoans.org/">http://www.scalakoans.org/</a><a href="http://www.scalakoans.org/">)</a>.

<strong>sealed class </strong>DoWith[W,R] <strong>private </strong>(doer: W <strong>=&gt; </strong>(W,R)) {

<strong>def </strong>apply(w: W) = doer(w)

<strong>def </strong>map[B](f: R <strong>=&gt; </strong>B): DoWith[W,B] = <strong>new </strong>DoWith[W,B]({

(w: W) <strong>=&gt; </strong>{ <strong>val </strong>(wp, r) = doer(w)

(wp, f(r))

} })

<strong>def </strong>flatMap[B](f: R <strong>=&gt; </strong>DoWith[W,B]): DoWith[W,B] = <strong>new </strong>DoWith[W,B]({

(w: W) <strong>=&gt; </strong>{ <strong>val </strong>(wp, r) = doer(w)

f(r)(wp) <em>// same as f(r).apply(wp)</em>

}

})

}

<strong>def </strong>doget[W]: DoWith[W,W] = <strong>new </strong>DoWith[W,W]({ w <strong>=&gt; </strong>(w, w) }) <strong>def </strong>doput[W](w: W): DoWith[W, Unit] = <strong>new </strong>DoWith[W, Unit]({ _ <strong>=&gt; </strong>(w, ()) }) <strong>def </strong>doreturn[W,R](r: R): DoWith[W,R] = <strong>new </strong>DoWith[W,R]({ w <strong>=&gt; </strong>(w,r) }) <strong>def </strong>domodify[W](f: W <strong>=&gt; </strong>W): DoWith[W,Unit] = <strong>new </strong>DoWith[W,Unit]({ w <strong>=&gt; </strong>(f(w),()) })

Figure 1: The DoWith type.

<ol>

 <li><strong>Feedback</strong>. Complete the survey on the linked from the moodle after completing this assignment. Any non-empty answer will receive full credit.</li>

 <li><strong>Warm-Up: Encapsulating Computation</strong>. To implement our interpreter for JAVASCRIPTY with memory, we introduce the idea of encapsulating computation with the DoWith[W,R] type. This idea builds on the concepts of abstract data types, collections, and higher-order functions introduced in Lab 4.</li>

</ol>

The DoWith type constructor is defined for you in the jsy.lab5 package and shown in Figure 1. The essence of the DoWith[W,R] type is that it encapsulates a function of type W<strong>=&gt;</strong>(W,R), which is a computation that returns a value of type R with an input-output state of type W. The doer field holds precisely a function of the type W<strong>=&gt;</strong>(W,R).

We should view DoWith[W,R] as a “collection” somewhat like List[A]. A value of type List[A] encapsulates a sequence of elements of type A and has methods to process and transform those elements. Similarly, a value of type DoWith[W,R] encapsulates a computation <em>with </em>a input-output state W for a result R and has methods to process and transform that computation.

Consider the map method shown in Figure 1.             Let us focus on the signature of the map method:

<strong>class </strong>DoWith[W,R] { <strong>def </strong>map[B](f: R <strong>=&gt; </strong>B): DoWith[W,B] }

From the signature, we see that the map method transforms a DoWith holding a computation <em>with </em>a W for a R to one for a B using the callback f. Intuitively, the input computation (bound to <strong>this</strong>) that will yield a result <em>r</em>:R and map transforms it to computation that will yield the result f(<em>r</em>):B. The flatMap method

<strong>class </strong>DoWith[W,R] { <strong>def </strong>flatMap[B](f: R <strong>=&gt; </strong>DoWith[W,B]): DoWith[W,B] }

is quite similar to map, but it allows the callback f to return a DoWith[W,B] computation. Intuitively, flatMap sequences the input computation (bound to <strong>this</strong>) that will yield a result <em>r</em>:R with the computation obtained from f(<em>r</em>).

We have four functions doget, doput, doreturn, and domodify for constructing DoWith objects (and disallow the direct construction of DoWith objects). The doget method creates a computation whose result is the current state w. The doput[W](w : W) method creates a computation that sets the state to w (and whose result is just unit ()). The doreturn[W,R](r : R) method creates a computation that leaves the state untouched whose result is r. Finally, the domodify[W](f : W<em>=&gt;</em>W) method creates a computation that modifies the state according to f. The doreturn and domodify functions are strictly needed, as they can be defined in terms of doget, doput, map, and flatMap, but we provide them because they are commonly-needed operations.

In this question, we practice using the DoWith[W,R] type.

<ul>

 <li>Implement a function <strong>def </strong>rename(env: Map[String, String], e: Expr): DoWith[Int,Expr]</li>

</ul>

that yields a computation to yield a resulting expression that is a version of the input expression e with bound variables renamed. The environment env maps names for free variables in e to what they should be renamed to in the result. You should use the provided helper function <strong>def </strong>fresh: DoWith[Int,String] for creating fresh variable names.

For the purposes of this exercise, we will only consider the subset of the expression language, consisting of the following:

<em>e </em>::<em>= x | n | e</em><sub>1 </sub><em>+ e</em><sub>2 </sub><em>|</em><strong>const </strong><em>x = e</em><sub>1</sub>; <em>e</em><sub>2</sub>

The strategy that we will use for renaming will be to globally renaming all variables uniquely using an integer counter. For example, we will rename

<strong>const </strong>a = (<strong>const </strong>a = 1; a ); (<strong>const </strong>a = 2; a )

to

<strong>const </strong>x0 = (<strong>const </strong>x1 = 1; x1); (<strong>const </strong>x2 = 2; x2) .

Note that we will completely ignore the names given in the input, so the following expression will also be renamed to the syntactically same expression as above:

<strong>const </strong>a = (<strong>const </strong>b = 1; b ); (<strong>const </strong>c = 2; c ) .

This policy for new variable names is captured in given helper function fresh, and we will rename expressions from left-to-right.

Seeing the DoWith[Int,Expr] type as an encapsulated Int<strong>=&gt;</strong>(Int,Expr), we see that the signature our rename is conceptually <strong>def </strong>rename(env: Map[String, String], e: Expr): (Int <strong>=&gt; </strong>(Int, Expr))

The rename function is thus conceptually a curried function that takes as input first env and e, which returns a function that takes an integer <em>i </em>to return a integer-expression pair (<em>i</em><em>0</em>,<em>e</em><em>0</em>). The integer state captures the next available variable number.

<strong>Hint</strong>: The only functions or methods for manipulating DoWith objects in this exercise are doreturn, map, and flatMap. The doget and doput functions are used in fresh, but you will not need to call them directly.

<h1>3.    JavaScripty Implementation</h1>

At this point, we are used to extending our interpreter implementation by updating our type checker typeInfer and our small-step interpreter step. The syntax with extensions highlighted is shown in Figure 2 and the new AST nodes are given in Figure 3.

<strong>Mutation.           </strong>In this lab, we add mutable variables declared as follows:

<strong>var </strong><em>x = e</em><sub>1</sub>; <em>e</em><sub>2</sub>

and then include an assignment expression:

<em>e</em>1 <em>= e</em>2

that writes the value of <em>e</em><sub>2 </sub>to a location named by expression <em>e</em><sub>1</sub>. Expressions may be mutable variables <em>x </em>or fields of objects <em>e</em><sub>1</sub>.<em>f </em>. We make all fields of objects mutable as is the default in JavaScript.

We remove the AST node ConstDecl and replace it with the node Decl with an additional parameter mut : Mutability that specifies the mutability of the variable, that is, whether the variable is <strong>const </strong>or <strong>var</strong>.

<strong>Aliasing. </strong>In JavaScript and in this lab, objects are dynamically allocated on the heap and then referenced with an extra level of indirection through a heap address. This indirection means two program variables can reference the same object, which is called <em>aliasing</em>. With mutation, aliasing is now observable as demonstrated by the following example:

<strong>const </strong>x = { f : 1 } <strong>const </strong>y = x x.f = 2

<h1>console.log(y.f)</h1>

<table width="604">

 <tbody>

  <tr>

   <td width="150">expressions valueslocation expressions location values unary operators binary operators</td>

   <td width="454"><em>e </em>::<em>= x | n | b |</em><strong>undefined</strong><em>| uope</em><sub>1 </sub><em>| e</em><sub>1 </sub><em>bop e</em><sub>2 </sub><em>| e</em><sub>1 </sub>? <em>e</em><sub>2 </sub>: <em>e</em><sub>3</sub><em>| </em><em>mut x = e</em><sub>1</sub>; <em>e</em><sub>2 </sub><em>|</em><strong>console</strong>.<strong>log</strong>(<em>e</em><sub>1</sub>)<em>| str |</em><strong>function </strong><em>p</em>(<em>params</em>)<em>tann e</em><sub>1 </sub><em>| e</em><sub>0</sub>(<em>e</em>)<em>| </em>{ <em>f </em>: <em>e </em>} <em>| e</em><sub>1</sub>.<em>f </em><em>| e</em><sub>1 </sub><em>= e</em><sub>2 </sub><em>| a |</em><strong>null</strong><em>| </em><strong>interface </strong><em>T </em>{ <em>f </em>: <em>τ</em>} ; <em>e</em><sub>1 </sub><em>v </em>::<em>= n | b |</em><strong>undefined</strong><em>| str |</em><strong>function </strong><em>p</em>(<em>params</em>)<em>tann e</em><sub>1</sub><em>| </em><em>a |</em><strong>null </strong><em>le </em>::<em>= x | e</em><sub>1</sub>.<em>f</em><em>lv </em>::<em>= ∗a | a</em>.<em>f</em><em>uop </em>::<em>= </em>–<em>|</em>!<em>|∗|〈τ〉</em><em>bop </em>::<em>= </em>,<em>|</em>+<em>|</em>–<em>|</em>*<em>|</em>/<em>|</em>===<em>|</em>!==<em>|</em>&lt;<em>|</em>&lt;=<em>|</em>&gt;<em>|</em>&gt;=<em>|</em>&amp;&amp;<em>|||</em></td>

  </tr>

  <tr>

   <td width="150">types</td>

   <td width="454"><em>τ </em>::<em>= </em><strong>number</strong><em>|</em><strong>bool</strong><em>|</em><strong>string</strong><em>|</em><strong>Undefined</strong><em>| </em>(<em>params</em>) <em>⇒τ<sup>0 </sup>| </em>{ <em>f </em>: <em>τ</em>}<em>| </em><strong>Null</strong><em>| T |</em><strong>Interface </strong><em>T τ</em></td>

  </tr>

  <tr>

   <td width="150">variables</td>

   <td width="454"><em>x</em></td>

  </tr>

  <tr>

   <td width="150">numbers (doubles)</td>

   <td width="454"><em>n</em></td>

  </tr>

  <tr>

   <td width="150">booleans strings</td>

   <td width="454"><em>b </em>::<em>= </em><strong>true</strong><em>|</em><strong>false</strong><em>str</em></td>

  </tr>

  <tr>

   <td width="150">function names function parameters field names</td>

   <td width="454"><em>p </em>::<em>= x |ε</em><em>params </em>::<em>= </em><em>x </em>: <em>τ</em><em>| mode x </em>: <em>τ</em><em>f</em></td>

  </tr>

  <tr>

   <td width="150">type annotations mutability passing mode addresses</td>

   <td width="454"><em>tann </em>::<em>= </em>: <em>τ|ε</em><em>mut </em>::<em>= </em><strong>const</strong><em>|</em><strong>var</strong><em>mode </em>::<em>= </em><strong>name</strong><em>|</em><strong>var</strong><em>|</em><strong>ref </strong><em>a</em></td>

  </tr>

  <tr>

   <td width="150">type variables</td>

   <td width="454"><em>T</em></td>

  </tr>

  <tr>

   <td width="150">type environments</td>

   <td width="454">Γ ::<em>= ·|</em>Γ[<em>mut x 7→τ</em>]</td>

  </tr>

  <tr>

   <td width="150">memories contents</td>

   <td width="454"><em>M </em>::<em>= ·| M</em>[<em>a 7→ k</em>] <em>k </em>::<em>= v | </em>{ <em>f </em>: <em>v </em>}</td>

  </tr>

 </tbody>

</table>

Figure 2: Abstract Syntax of JAVASCRIPTY

<em>/* Declarations */</em>

<strong>case class </strong>Decl(mut: Mutability, x: String, e1: Expr, e2: Expr) <strong>extends </strong>Expr

Decl(<em>mut</em>, <em>x</em>, <em>e</em><sub>1</sub>, <em>e</em><sub>2</sub>) <em>mut x =e</em><sub>1</sub>; <em>e</em><sub>2 </sub><strong>case class </strong>InterfaceDecl(tvar: String, tobj: Typ, e: Expr) <strong>extends </strong>Expr InterfaceDecl(<em>T</em>, <em>τ</em>, <em>e</em>) <strong>interface</strong><em>T τ </em>; <em>e</em>

<strong>sealed abstract class </strong>Mutability <strong>case object </strong>Const <strong>extends </strong>Mutability

MConst <strong>const case object </strong>Var <strong>extends </strong>Mutability MVar <strong>var</strong>

<em>/</em><em><sub>* </sub></em><em>Addresses and Mutation </em><em><sub>*</sub></em><em>/ </em><strong>case class </strong>Assign(e1: Expr, e2: Expr) <strong>extends </strong>Expr

Assign(<em>e</em><sub>1</sub>, <em>e</em><sub>2</sub>) <em>e</em><sub>1</sub><em>=e</em><sub>2 </sub><strong>case object </strong>Null <strong>extends </strong>Expr

Null <strong>null case class </strong>A(addr: Int) <strong>extends </strong>Expr

A(…) <em>a </em><strong>case object </strong>Deref <strong>extends </strong>Uop Deref <em>∗</em>

<em>/* Functions */</em>

<strong>type </strong>Params = Either[ List[(String,Typ)], (PMode,String,Typ) ] <strong>case class </strong>Function(p: Option[String], paramse: Params, tann: Option[Typ], e1: Expr) <strong>extends </strong>Expr

Function(<em>p</em>, <em>params</em>, <em>tann</em>, <em>e</em><sub>1</sub>) <strong>function</strong><em>p</em>(<em>params</em>)<em>tanne</em><sub>1</sub>

<strong>sealed abstract class </strong>PMode <strong>case object </strong>PName <strong>extends </strong>PMode

PName <strong>name case object </strong>PVar <strong>extends </strong>PMode

PVar <strong>var case object </strong>PRef <strong>extends </strong>PMode PRef <strong>ref</strong>

<em>/* Casting */ </em><strong>case class </strong>Cast(t: Typ) <strong>extends </strong>Uop Cast(<em>τ</em><sup>) </sup><em>〈τ〉</em>

<em>/* Types */ </em><strong>case class </strong>TVar(tvar: String) <strong>extends </strong>Typ

TVar(<em>T</em>) <em>T </em><strong>case class </strong>TInterface(tvar: String, t: Typ) <strong>extends </strong>Typ TInterface(<em>T</em>, <em>τ</em>) <strong>Interface</strong><em>T τ</em>

Figure 3: Representing in Scala the abstract syntax of JAVASCRIPTY. After each <strong>caseclass </strong>or <strong>caseobject</strong>, we show the correspondence between the representation and the concrete syntax.

The code above should print 2 because x and y are aliases (i.e., they are bound to the same object value). Aliasing makes programs more difficult to reason about and is often the source of subtle bugs.

To model allocation, object literals of the form { <em>f </em>: <em>v </em>} are no longer values, rather they evaluate to an address <em>a</em>, which are then the values representing objects (as shown below):

values       <em>v </em>::<em>= </em>… <em>| a |</em><strong>null</strong>

With objects allocated on the heap, we also introduce the <strong>null </strong>value (or often called the <strong>null </strong>pointer). We consider an unbounded set of addresses that is disjoint from the <strong>null </strong>value (i.e., <em>a </em>cannot stand for <strong>null</strong>). Addresses <em>a </em>are also included in program expressions <em>e </em>because they arise during evaluation. However, there is no way to explicitly write an address in the source program. Addresses are an example of an enrichment of program expressions as an intermediate form solely to express small-step evaluation.

<strong>Parameter Passing Modes. </strong>We can annotate function parameters with <strong>var</strong>, <strong>ref</strong>, or <strong>name </strong>to specify a parameter passing mode. The annotation <strong>var </strong>says the parameter should be callby-value with an allocation for a new mutable parameter variable initialized the argument value. The <strong>ref </strong>and annotations specify call-by-reference and call-by-name, respectively. In Lab 4, all parameters were call-by-value with an immutable variable, conceptually a “<strong>const</strong>” parameter. This “call-by” terms are defined by their respective DOCALL rules in Figure 9. The intellectual exercise here is to decode what these “call-by” terms mean by reading their respective rules. Observe from the rules that the <strong>ref </strong>requires an intermediate language with addresses (and mutation to be interesting), but <strong>name </strong>could be a useful language feature in a pure setting. Call-by-name is a specific instance of <em>lazy evaluation</em>.

To simplify the lab implementation, we consider in the syntax two kinds of function parameters <em>params </em>that are either a sequence of pass-by-value with immutable variables <em>x </em>: <em>τ </em>(as before and conceptually “<strong>const</strong>” parameters) or a single parameter with one of the new parameter passing modes <em>mode x </em>: <em>τ</em>. This choice is purely for pedagogical reasons so that you do not need to deal with parameter lists when thinking about parameter passing modes. From the programmer’s perspective, this would be a bit strange, as one would expect to be able specify a parameter list with independent passing modes for each parameter. In the AST nodes, the two kinds of function parameters are implemented via the Scala Either type (see Figure 3).

<strong>Casting. </strong>In the previous lab, we carefully crafted a very nice situation where as long as the input program passed the type checker, then evaluation would be free of run-time errors. Unfortunately, there are often programs that we want to execute that we cannot completely check statically and must rely on some amount of dynamic (run-time) checking.

We want to re-introduce dynamic checking in a controlled manner, so we ask that the programmer include explicit casts, written <em>〈τ〉e</em>. Executing a cast may result in a dynamic type error but intentionally nowhere else. Our step implementation should only result in throwing DynamicTypeError when executing a cast. For simplicity, we limit the expressivity of casts to between object types.

The <strong>null </strong>value has type <strong>Null </strong>and is not directly assignable to something of object type, but we make <strong>Null </strong>castable to any object type. However, there is a cost to this flexibility, with <strong>null</strong>, we have to introduce another run-time check. We add another kind of run-time error for null dereference errors, which we write as nullerror and implement in step by throwing NullDereferenceError.

<strong>Abstract Syntax Trees. </strong>In Figure 3, we show the updated and new AST nodes. Note that Deref and Cast are Uops (i.e., they are unary operators).

(a) <strong>Exercise: Type Checking. </strong>The inference rules defining the typing judgment form are given in Figures 4, 5, and 6.

<ul>

 <li>Similar to before, we implement type inference with the function <strong>def </strong>typeInfer(env: Map[String,(Mutability,Typ)], e: Expr): Typ</li>

</ul>

that you need to complete. Note that the type environment maps a variable name to a pair of a mutability (either MConst or MVar) and a type.

<ul>

 <li>The type inference should use a helper function <strong>def </strong>castOk(t1: Typ, t2: Typ): Boolean</li>

</ul>

that you also need to complete. This function specifies when type t1 can be casted to type t2 and implements the judgment form <em>τ</em><sub>1</sub> <em>τ</em><sub>2 </sub>given in Figure 6.

A template for the Function case for typeInfer is provided that you may use if you wish.

Γ<em>` e </em>: <em>τ</em>

TYPENEG                                      TYPENOT                             TYPESEQ

<table width="548">

 <tbody>

  <tr>

   <td width="100">TYPEVARΓ<em>` x </em>: Γ(<em>x</em>)</td>

   <td width="176">Γ<em>`e</em><sub>1 </sub>: <strong>number</strong>Γ<em>`−e</em><sub>1 </sub>: <strong>number</strong></td>

   <td width="124">Γ<em>`e</em><sub>1 </sub>: <strong>bool</strong>Γ<em>` </em>!<em>e</em><sub>1 </sub>: <strong>bool</strong></td>

   <td width="147">Γ<em>`e</em><sub>1 </sub>: <em>τ</em><sub>1                                   </sub>Γ<em>`e</em><sub>2 </sub>: <em>τ</em><sub>2</sub>Γ<em>`e</em><sub>1 </sub>, <em>e</em><sub>2 </sub>: <em>τ</em><sub>2</sub></td>

  </tr>

 </tbody>

</table>

TYPEARITH                                                                                                                TYPEPLUSSTRING

Γ<em>`e</em><sub>1 </sub>: <strong>number                </strong>Γ<em>`e</em><sub>2 </sub>: <strong>number                </strong><em>bop∈ </em>{<em>+</em>,<em>−</em>,<em>∗</em>,/}                      Γ<em>`e</em><sub>1 </sub>: <strong>string                </strong>Γ<em>`e</em><sub>2 </sub>: <strong>string</strong>

Γ<em>`e</em><sub>1 </sub><em>bope</em><sub>2 </sub>: <strong>number                                                                        </strong>Γ<em>`e</em><sub>1 </sub><em>+e</em><sub>2 </sub>: <strong>string</strong>

TYPEINEQUALITYNUMBER

Γ<em>`e</em><sub>1 </sub>: <strong>number                </strong>Γ<em>`e</em><sub>2 </sub>: <strong>number                 </strong><em>bop∈ </em>{<em>&lt;</em>,<em>&lt;=</em>,<em>&gt;</em>,<em>&gt;=</em>}

Γ<em>`e</em><sub>1 </sub><em>bope</em><sub>2 </sub>: <strong>bool</strong>

TYPEINEQUALITYSTRING

Γ<em>`e</em><sub>1 </sub>: <strong>string                </strong>Γ<em>`e</em><sub>2 </sub>: <strong>string                 </strong><em>bop∈ </em>{<em>&lt;</em>,<em>&lt;=</em>,<em>&gt;</em>,<em>&gt;=</em>}

Γ<em>`e</em><sub>1 </sub><em>bope</em><sub>2 </sub>: <strong>bool</strong>

TYPEEQUALITY

Γ<em>`e</em><sub>1 </sub>: <em>τ              </em>Γ<em>`e</em><sub>2 </sub>: <em>τ              τ </em>has no function types              <em>bop∈ </em>{<em>===</em>,! <em>==</em>}

<table width="594">

 <tbody>

  <tr>

   <td colspan="2" width="594"></td>

  </tr>

  <tr>

   <td width="367">Γ<em>`e</em><sub>1 </sub><em>bope</em><sub>2 </sub>: <strong>bool</strong></td>

   <td width="227"> </td>

  </tr>

  <tr>

   <td width="367">TYPEANDORΓ<em>`e</em><sub>1 </sub>: <strong>bool               </strong>Γ<em>`e</em><sub>2 </sub>: <strong>bool             </strong><em>bop∈ </em>{&amp;&amp;,<em>||</em>}Γ<em>`e</em><sub>1 </sub><em>bope</em><sub>2 </sub>: <strong>bool</strong></td>

   <td width="227">TYPEPRINTΓ<em>`e</em><sub>1 </sub>: <em>τ</em><sub>1</sub>Γ<em>`</em><strong>console</strong>.<strong>log</strong>(<em>e</em><sub>1</sub>) : <strong>Undefined</strong></td>

  </tr>

  <tr>

   <td width="367">TYPEIFΓ<em>`e</em>1 : <strong>bool                  </strong>Γ<em>`e</em>2 : <em>τ              </em>Γ<em>`e</em>3 : <em>τ                   </em>TYPENUMBERΓ<em>`e</em><sub>1 </sub>? <em>e</em><sub>2 </sub>: <em>e</em><sub>3 </sub>: <em>τ                                                </em>Γ<em>`n </em>: <strong>number</strong></td>

   <td width="227">              TYPEBOOL                           TYPESTRINGΓ<em>`b </em>: <strong>bool                       </strong>Γ<em>`str </em>: <strong>string</strong></td>

  </tr>

  <tr>

   <td width="367">TYPEOBJECT</td>

   <td width="227">TYPEGETFIELD</td>

  </tr>

  <tr>

   <td colspan="2" width="594"><sup>T</sup><sup>YPE</sup><sup>U</sup><sup>NDEFINED                                                                                                    </sup>Γ<em>`e<sub>i </sub></em>: <em>τ<sub>i                     </sub></em>(for all <em>i</em>)                                         Γ<em>`e </em>: { …, <em>f </em>: <em>τ</em>,… }Γ<em>`</em><strong>undefined </strong>: <strong>Undefined                                    </strong>Γ<em>` </em>{ …, <em>f<sub>i </sub></em>: <em>e<sub>i</sub></em>,… } : { …, <em>f<sub>i </sub></em>: <em>τ<sub>i</sub></em>,… }                                    Γ<em>`e</em>.<em>f </em>: <em>τ</em></td>

  </tr>

 </tbody>

</table>

Figure 4: Typing of non-imperative primitives and objects of JAVASCRIPTY (no change from the previous lab).

Γ<em>` e </em>: <em>τ</em>

TYPEDECL                                                                                TYPEFUNCTION

Γ<em>`e</em><sub>1 </sub>: <em>τ</em><sub>1                         </sub>Γ[<em>mutx 7→τ</em><sub>1</sub>] <em>`e</em><sub>2 </sub>: <em>τ</em><sub>2                                                                </sub>Γ[<strong>const</strong><em>x 7→ τ</em>] <em>`e</em><sub>1 </sub>: <em>τ</em><em>0</em>

Γ<em>`mut x =e</em><sub>1</sub>; <em>e</em><sub>2 </sub>: <em>τ</em><sub>2                                                                             </sub>Γ<em>` </em><strong>function </strong>(<em>x </em>: <em>τ</em>) <em>e</em><sub>1 </sub>: (<em>x </em>: <em>τ</em>) <em>⇒τ</em><em>0</em>

TYPEFUNCTIONANN                                                                  TYPERECFUNCTION

Γ[<strong>const</strong><em>x →7 τ</em>] <em>`e</em><sub>1 </sub>: <em>τ</em><em>0                                                      </em>Γ[<strong>const</strong><em>x</em><sub>0 </sub><em>7→ τ</em><em>00</em>][<strong>const</strong><em>x 7→τ</em>] <em>`e</em><sub>1 </sub>: <em>τ</em><em>0                                   </em><em>τ<sup>00 </sup>= </em>(<em>x </em>: <em>τ</em>) <em>⇒τ</em><em>0</em>

Γ<em>` </em><strong>function </strong>(<em>x </em>: <em>τ</em>) : <em>τ</em><em>0 </em><em>e</em><sub>1 </sub>: (<em>x </em>: <em>τ</em>) <em>⇒τ</em><em>0                                                                                               </em>Γ<em>` </em><strong>function</strong><em>x</em><sub>0</sub>(<em>x </em>: <em>τ</em>) : <em>τ</em><em>0 </em><em>e</em><sub>1 </sub>: <em>τ</em><em>00</em>

TYPEFUNCTIONMODE

Γ[mut(<em>mode</em>)<em>x 7→τ</em>] <em>`e</em><sub>1 </sub>: <em>τ</em><em>0</em>

Γ<em>` </em><strong>function </strong>(<em>modex </em>: <em>τ</em>) <em>e</em><sub>1 </sub>: (<em>modex </em>: <em>τ</em>) <em>⇒τ</em><em>0</em>

TYPEFUNCTIONANNMODE

Γ[mut(<em>mode</em>)<em>x 7→τ</em>] <em>`e</em><sub>1 </sub>: <em>τ</em><em>0</em>

Γ<em>` </em><strong>function </strong>(<em>modex </em>: <em>τ</em>) : <em>τ</em><em>0 </em><em>e</em><sub>1 </sub>: (<em>modex </em>: <em>τ</em>) <em>⇒τ</em><em>0</em>

TYPERECFUNCTIONMODE                                                                                                              <sub>def</sub>

Γ[<strong>const</strong><em>x</em>0 <em>→7 τ</em><em>00</em>][mut(<em>mode</em>)<em>x 7→τ</em>] <em>`e</em>1 : <em>τ</em><em>0 </em><em>⇒                                </em>mut(<strong>name</strong>) defdef<em>=== </em><strong>const</strong>

mut(<strong>var</strong>)            <strong>var </strong>Γ<em>` </em><strong>function</strong><em>x</em>0(<em>modex </em>: <em>τ</em>) : <em>τ</em><em>0 </em><em>e</em>1 : (<em>modex </em>: <em>τ</em>) <em>τ<sup>0  </sup></em>mut(<strong>ref</strong>) <strong>var</strong>

Figure 5: Typing of objects and binding constructs of JAVASCRIPTY.

Γ<em>` e </em>: <em>τ</em>

TYPEASSIGNVAR           TYPEASSIGNFIELD TYPENULLΓ<em>`e</em><sub>1 </sub>: { …, <em>f </em>: <em>τ</em>,… }      Γ<em>`e</em><sub>2 </sub>: <em>τ</em>

Γ<em>`</em><strong>null </strong>: <strong>Null</strong>Γ<em>`e</em><sub>1</sub>.<em>f =e</em><sub>2 </sub>: <em>τ</em>

<table width="619">

 <tbody>

  <tr>

   <td colspan="3" width="619">TYPECALLΓ<em>`e </em>: (<em>x</em><sub>1 </sub>: <em>τ</em><sub>1</sub>,…,<em>x<sub>n </sub></em>: <em>τ<sub>n</sub></em>) <em>⇒τ</em><em>0                              </em>Γ<em>`e</em><sub>1 </sub>: <em>τ</em><sub>1                   </sub><em>···             </em>Γ<em>`e<sub>n </sub></em>: <em>τ<sub>n</sub></em>Γ<em>`e</em>(<em>e</em><sub>1</sub>,…,<em>e<sub>n</sub></em>) : <em>τ</em><em>0</em>TYPECALLNAMEVAR                                                                                        TYPECALLREF</td>

  </tr>

  <tr>

   <td colspan="2" width="389">               Γ<em>`e</em><sub>1 </sub>: (<em>modex </em>: <em>τ</em>) <em>⇒τ</em><em>0                           </em>Γ<em>`e</em><sub>2 </sub>: <em>τ            mode 6=</em><strong>ref</strong></td>

   <td rowspan="2" width="230">Γ<em>`e</em><sub>1 </sub>: (<strong>ref</strong><em>x </em>: <em>τ</em>) <em>⇒τ</em><em>0                                   </em>Γ<em>`le</em><sub>2 </sub>: <em>τ</em>Γ<em>`e</em><sub>1</sub>(<em>le</em><sub>2</sub>) : <em>τ</em><em>0</em><em>τ</em>1 <em>τ</em>2</td>

  </tr>

  <tr>

   <td width="249">Γ<em>`e</em><sub>1</sub>(<em>e</em><sub>2</sub>) : <em>τ</em><em>0 </em>Requires Additional Dynamic Checking</td>

   <td width="140">TYPECASTΓ<em>`e</em><sub>1 </sub>: <em>τ</em><sub>1                             </sub><em>τ</em><sub>1</sub> <em>τ</em>Γ<em>`〈τ〉e</em>1 : <em>τ</em></td>

  </tr>

  <tr>

   <td rowspan="2" width="249">CASTOKEQ                        CASTOKNULL<em>τ</em> <em>τ                                     </em><strong>Null</strong> { … }</td>

   <td width="140">CASTOKOBJECT<em>↑ </em><em>τ</em><em>i </em><em>=τ</em><em>0i</em></td>

   <td width="230">(for all 1 <em>≤i ≤n ≤m</em>)</td>

  </tr>

  <tr>

   <td colspan="2" width="370">{ …, <em>f<sub>i </sub></em>: <em>τ<sub>i</sub></em>,…, <em>f<sub>n </sub></em>: <em>τ<sub>n</sub></em>,…, <em>f<sub>m </sub></em>: <em>τ<sub>m </sub></em>} { …, <em>f<sub>i </sub></em>: <em>τ<sup>0</sup><sub>i</sub></em>,…, <em>f<sub>n </sub></em>: <em>τ</em><em>0</em><em><sub>n </sub></em>}</td>

  </tr>

 </tbody>

</table>

CASTOKOBJECT<em>↓</em>

<em>τ<sub>i </sub>=</em><em><sup>τ</sup></em><em>0</em><em><sub>i                        </sub></em>(for all 1 <em>≤i ≤n ≤m</em>)

{ …, <em>f<sub>i </sub></em>: <em>τ<sub>i</sub></em>,…, <em>f<sub>n </sub></em>: <em>τ<sub>n </sub></em>} { …, <em>f<sub>i </sub></em>: <em>τ<sub>i</sub><sup>0</sup></em>,…, <em>f<sub>n </sub></em>: <em>τ<sub>n</sub></em><em>0</em>,…, <em>f<sub>m </sub></em>: <em>τ</em><em>0</em><em><sub>m </sub></em>}

Figure 6: Typing of imperative and type casting constructs of JAVASCRIPTY.

(b) <strong>Exercise: Reduction. </strong>We also update step from Lab 4. A small-step operational semantics is given in Figures 7–10.

The small-step judgment form is now as follows:<a href="#_ftn1" name="_ftnref1"><sup>[1]</sup></a>

<em>〈M</em>,<em>e〉−→〈M</em><em>0</em>,<em>e<sup>0</sup>〉</em>

that says informally, “In memory <em>M</em>, expression <em>e </em>steps to a new configuration with memory <em>M</em><em>0 </em>and expression <em>e</em><em>0</em>.” The memory <em>M </em>is a map from addresses <em>a </em>to contents <em>k</em>, which include values and object values. The presence of a memory <em>M </em>that gets updated during evaluation is the hallmark of <em>imperative computation</em>.

Note that the change in the judgment form necessitates updating <em>all </em>rules—even those that do not involve imperative features as in Figure 7. For these rules, the memory <em>M </em>is simply threaded through (see Figure 7).

<ul>

 <li>The step function now has the following signature <strong>def </strong>step(e: Expr): DoWith[Mem,Expr]</li>

</ul>

corresponding to the updated operational semantics. This function needs to be completed.

Seeing the DoWith[Mem,Expr] type as an encapsulated Mem<strong>=&gt;</strong>(Mem,Expr), we see how the judgment form <em>〈M</em>,<em>e〉 −→ 〈M</em><em>0</em>,<em>e<sup>0</sup>〉 </em>corresponds to the signature of step. In particular, the signature our step is conceptually

<strong>def </strong>step(e: Expr): (Mem <strong>=&gt; </strong>(Mem, Expr))

The step function is thus conceptually a curried function that takes as input first <em>e</em>, which returns a function that takes <em>M </em>to return (<em>M</em><em>0</em>,<em>e</em><em>0</em>).

<em>The Crucial Observation. </em>The main advantage of using the encapsulated computation type DoWith[Mem,Expr] is that we can put this common-case threading into the DoWith data structure.

Some rules require allocating fresh addresses. For example, DOOBJECT specifies allocating a new address <em>a </em>and extending the memory mapping <em>a </em>to the object. The address <em>a </em>is stated to be fresh by the constraint that <em>a ∉ </em>dom(<em>M</em>). In the implementation, you call memalloc(k) to get a fresh address with the memory cell initialized to contents k.

(c) <strong>Exercise: Call-By-Name. </strong>The final wrinkle in our interpreter is that call-by-name requires substituting an arbitrary expression into another expression. Thus, we must be careful to avoid free variable capture (cf., Notes 3.2). We did not have to consider this case before because we were only ever substituting values that did not have free variables.

In this lab, you will need to modify your substitute function to avoid free variable capture. A function to rename bound variables is given that <strong>def </strong>avoidCapture(avoidVars: Set[String], e: Expr): Expr

<em>〈M</em>,<em>e〉−→〈M</em><em>0</em>,<em>e<sup>0</sup>〉</em>

DONEG DONOT <em>n</em><em>0 </em><em>=−n b</em><em>0 </em><em>=¬b </em>DOSEQ

<em>〈M</em>,<em>−n〉−→〈M</em>,<em>n<sup>0</sup>〉                           〈M</em>,!<em>b〉−→〈M</em>,<em>b<sup>0</sup>〉                           〈M</em>,<em>v</em><sub>1 </sub>, <em>e</em><sub>2</sub><em>〉−→〈M</em>,<em>e</em><sub>2</sub><em>〉</em>

<table width="526">

 <tbody>

  <tr>

   <td width="285">DOARITH <em>n<sup>0 </sup>=n</em><sub>1 </sub><em>bopn</em><sub>2                   </sub><em>bop∈ </em>{<em>+</em>,<em>−</em>,<em>∗</em>,/}<em>〈M</em>,<em>n</em><sub>1 </sub><em>bopn</em><sub>2</sub><em>〉−→〈M</em>,<em><sup>n</sup></em><em><sup>0</sup></em><em>〉</em></td>

   <td width="242">DOPLUSSTRING<em>str<sup>0 </sup>=str</em><sub>1</sub><em>+str</em><sub>2</sub><em>〈M</em>,<em>str</em><sub>1 </sub><em>+str</em><sub>2</sub><em>〉−→〈M</em>,<em>str<sup>0</sup>〉</em></td>

  </tr>

  <tr>

   <td width="285">DOINEQUALITYNUMBER <em>b<sup>0 </sup>=n</em><sub>1 </sub><em>bopn</em><sub>2      </sub><em>bop∈ </em>{<em>&lt;</em>,<em>&lt;=</em>,<em>&gt;</em>,<em>&gt;=</em>}<em>〈M</em>,<em>n</em><sub>1 </sub><em>bopn</em><sub>2</sub><em>〉−→〈M</em>,<em><sup>b</sup></em><em><sup>0</sup></em><em>〉</em></td>

   <td width="242">DOINEQUALITYSTRING <em>b<sup>0 </sup>=str</em><sub>1 </sub><em>bopstr</em><sub>2    </sub><em>bop∈ </em>{<em>&lt;</em>,<em>&lt;=</em>,<em>&gt;</em>,<em>&gt;=</em>}<em>〈M</em>,<em>str</em><sub>1 </sub><em>bopstr</em><sub>2</sub><em>〉−→〈M</em>,<em><sup>b</sup></em><em><sup>0</sup></em><em>〉</em></td>

  </tr>

 </tbody>

</table>

DOEQUALITY

<em>b<sup>0 </sup>= </em>(<em>v</em>1 <em>bopv</em>2)                  <em>bop∈ </em>{<em>===</em>,! <em>==</em>}                DOANDTRUE                                           DOANDFALSE

<em>〈M</em>,<em>v</em><sub>1 </sub><em>bopv</em><sub>2</sub><em>〉−→〈M</em>,<em><sup>b</sup></em><em><sup>0</sup></em><em>〉                            〈M</em>,<strong>true </strong>&amp;&amp; <em>e</em><sub>2</sub><em>〉−→〈M</em>,<em>e</em><sub>2</sub><em>〉                     〈M</em>,<strong>false </strong>&amp;&amp; <em>e</em><sub>2</sub><em>〉−→〈M</em>,<strong>false</strong><em>〉</em>

DOPRINT DOORTRUE DOORFALSE             <em>v</em><sub>1 </sub>printed

<em>〈M</em>,<strong>true</strong><em>||e</em><sub>2</sub><em>〉−→〈M</em>,<strong>true</strong><em>〉                    〈M</em>,<strong>false</strong><em>||e</em><sub>2</sub><em>〉−→〈M</em>,<em>e</em><sub>2</sub><em>〉                           〈M</em>,<strong>console</strong>.<strong>log</strong>(<em>v</em><sub>1</sub>)<em>〉−→〈M</em>,<strong>undefined</strong><em>〉</em>

SEARCHUNARY DOIFTRUE      DOIFFALSE          <em>〈M</em>,<em>e</em><sub>1</sub><em>〉−→〈M</em><em>0</em>,<em>e</em><sub>1</sub><em>0 </em><em>〉</em>

<em>〈M</em>,<strong>true </strong>? <em>e</em><sub>2 </sub>: <em>e</em><sub>3</sub><em>〉−→〈M</em>,<em>e</em><sub>2</sub><em>〉                    〈M</em>,<strong>false </strong>? <em>e</em><sub>2 </sub>: <em>e</em><sub>3</sub><em>〉−→〈M</em>,<em>e</em><sub>3</sub><em>〉                   〈M</em>,<em>uope</em><sub>1</sub><em>〉−→〈M</em><em>0</em>,<em>uope</em><sub>1</sub><em><sup>0 </sup>〉</em>

SEARCHBINARY1                                                                                                 SEARCHBINARY2

<em>〈M</em>,<em>e</em><sub>1</sub><em>〉−→〈M</em><em>0</em>,<em>e</em><sub>1</sub><em>0 </em><em>〉                                                     〈M</em>,<em>e</em><sub>2</sub><em>〉−→〈M</em><em>0</em>,<em>e</em><sub>2</sub><em>0 </em><em>〉</em>

<em>〈M</em>,<em>e</em><sub>1 </sub><em>bope</em><sub>2</sub><em>〉−→〈M</em><em>0</em>,<em>e</em><sub>1</sub><em>0 </em><em>bope</em><sub>2</sub><em>〉                               〈M</em>,<em>v</em><sub>1 </sub><em>bope</em><sub>2</sub><em>〉−→〈M</em><em>0</em>,<em>v</em><sub>1 </sub><em>bope</em><sub>2</sub><em><sup>0 </sup>〉</em>

SEARCHPRINT                                                                                        SEARCHIF

<em>〈M</em>,<em>e</em><sub>1</sub><em>〉−→〈M</em><em>0</em>,<em>e</em><sub>1</sub><em>0 </em><em>〉                                                              〈M</em>,<em>e</em><sub>1</sub><em>〉−→〈M</em><em>0</em>,<em>e</em><sub>1</sub><em>0 </em><em>〉</em>

<em>〈M</em>,<strong>console</strong>.<strong>log</strong>(<em>e</em><sub>1</sub>)<em>〉−→〈M</em><em>0</em>,<strong>console</strong>.<strong>log</strong>(<em>e</em><sub>1</sub><em>0 </em>)<em>〉                         〈M</em>,<em>e</em><sub>1 </sub>? <em>e</em><sub>2 </sub>: <em>e</em><sub>3</sub><em>〉−→〈M</em><em>0</em>,<em>e</em><sub>1</sub><em>0 </em>? <em>e</em><sub>2 </sub>: <em>e</em><sub>3</sub><em>〉</em>

Figure 7: Small-step operational semantics of non-imperative primitives of JAVASCRIPTY. The only change compared to the previous lab is the threading of the memory.

renames bound variables in e to avoid variables given in avoidVars. Note that you will also need to call the function <strong>def </strong>freeVars(e: Expr): Set[String] that computes the set of free variables of an expression.

<strong>Memory. </strong>One might notice that in our operational semantics, the memory <em>M </em>only grows and never shrinks during the course of evaluation. Our interpreter only ever allocates memory and never deallocates! This choice is fine in a mathematical model and for this lab, but a production run-time system must somehow enable collecting <em>garbage</em>—allocated memory locations that are no longer used by the running program. Collecting garbage may be done manually by the programmer (as in C and C++) or automatically by a <em>conservative garbage collector </em>(as in JavaScript, Scala, Java, C#, Python).

One might also notice that we have a single memory instead of a <em>stack of activation records </em>for local variables and a <em>heap </em>for objects as discussed in Computer Systems. Our interpreter instead simply allocates memory for local variables when they are encountered (e.g., DOVAR). It never deallocates, even though we know that with local variables, those memory cells become inaccessible by the program once the function returns. The key observation is that the traditional stack is not essential for local variables but rather is an optimization for automatic deallocation based on function call-and-return.

<strong>Type Safety.            </strong>There is delicate interplay between the casts that we permit statically with

<em>τ</em>1 <em>τ</em>2

and the dynamic checks that we need to perform at run-time (i.e., in

<em>〈M</em>,<em>e〉−→〈M</em><em>0</em>,<em>e<sup>0</sup>〉</em>

as with TYPEERRORCASTOBJ or NULLERRORDEREF).

We say that a static type system (e.g., our Γ<em>` e </em>: <em>τ </em>judgement form) is <em>sound </em>with respect to an operational semantics (e.g., our <em>〈M</em>,<em>e〉−→〈M</em><em>0</em>,<em>e<sup>0</sup>〉</em>) if whenever our type checker defined by our typing judgment says a program is well-typed, then our interpreter defined by our small-step semantics never gets stuck (i.e., never throws StuckError).

Note that if the equality checks <em>τ<sub>i </sub>=</em><em><sup>τ0</sup><sub>i </sub></em>in the premises of CASTOKOBJECT<em>↑ </em>and CASTOKOBJECT<em>↓ </em>were changed slightly to cast ok checks (i.e., <em>τ<sub>i</sub></em> <em>τ<sup>0</sup><sub>i</sub></em>), then our type system would become unsound with respect to our current operational semantics. For <strong>extra credit</strong>, carefully explain why by giving an example expression that demonstrates the unsoundness. Then, carefully explain what run-time checking you would add to regain soundness. First, give the explanation in prose, and then, try to formalize it in our semantics (if the challenge excites you!).

<em>〈M</em>,<em>e〉−→〈M</em><em>0</em>,<em>e<sup>0</sup>〉</em>

DOOBJECT                                                                                  DOGETFIELD

<em>a ∉ </em>dom(<em>M</em>)                                                        <em>M</em>(<em>a</em>) <em>= </em>{ …, <em>f </em>: <em>v</em>,… }

<em>〈M</em>,{ <em>f </em>: <em>v </em>}<em>〉−→〈M</em>[<em>a 7→ </em>{ <em>f </em>: <em>v </em>}],<em>a〉                                    〈M</em>,<em>a</em>.<em>f 〉−→〈M</em>,<em>v〉</em>

SEARCHOBJECT                                                                                    SEARCHGETFIELD

<em>〈M</em>,<em>e<sub>i</sub>〉−→〈M</em><em>0</em>,<em>e<sub>i</sub></em><em>0</em><em>〉                                                    〈M</em>,<em>e</em><sub>1</sub><em>〉−→〈M</em><em>0</em>,<em>e</em><sub>1</sub><em>0 </em><em>〉</em>

<em>〈M</em>,{ …, <em>f<sub>i </sub></em>: <em>e<sub>i</sub></em>,… }<em>〉−→〈M</em><em>0</em>,{ …, <em>f<sub>i </sub></em>: <em>e<sub>i</sub></em><em>0</em>,… }<em>〉                            〈M</em>,<em>e</em><sub>1</sub>.<em>f 〉−→〈M</em><em>0</em>,<em>e</em><sub>1</sub><em>0 </em>.<em>f 〉</em>

DOVAR DOCONST           <em>a ∉ </em>dom(<em>M</em>)

<em>〈M</em>,<strong>const</strong><em>x = v</em><sub>1</sub>; <em>e</em><sub>2</sub><em>〉−→〈M</em>,<em>e</em><sub>2</sub>[<em>v</em><sub>1</sub>/<em>x</em>]<em>〉                             〈M</em>,<strong>var</strong><em>x = v</em><sub>1</sub>; <em>e</em><sub>2</sub><em>〉−→〈M</em>[<em>a 7→ v</em><sub>1</sub>],<em>e</em><sub>2</sub>[<em>∗a</em>/<em>x</em>]<em>〉</em>

<table width="508">

 <tbody>

  <tr>

   <td width="231">DODEREF <em>a ∈ </em>dom(<em>M</em>)<em>〈M</em>,<em>∗a〉−→〈M</em>,<em>M</em>(<em>a</em>)<em>〉</em></td>

   <td width="277">SEARCHDECL<em>〈M</em>,<em>e</em><sub>1</sub><em>〉−→〈M</em><em>0</em>,<em>e</em><sub>1</sub><em>0 </em><em>〉</em><em>〈M</em>,<em>mut x =e</em><sub>1</sub>; <em>e</em><sub>2</sub><em>〉−→〈M</em><em>0</em>,<em>mut x =e</em><sub>1</sub><em>0 </em>; <em>e</em><sub>2</sub><em>〉</em></td>

  </tr>

  <tr>

   <td width="231">DOASSIGNVAR <em>a ∈ </em>dom(<em>M</em>)<em>〈M</em>,<em>∗a = v〉−→〈M</em>[<em>a 7→ v</em>],<em>v〉</em></td>

   <td width="277">DOASSIGNFIELD<em>M</em>(<em>a</em>) <em>= </em>{ …, <em>f </em>: <em>v</em>,… }<em>〈M</em>,<em>a</em>.<em>f = v<sup>0</sup>〉−→〈M</em>[<em>a 7→ </em>{ …, <em>f </em>: <em>v</em><em>0</em>,… }],<em>v<sup>0</sup>〉</em></td>

  </tr>

  <tr>

   <td width="231">SEARCHASSIGN1</td>

   <td width="277">SEARCHASSIGN2</td>

  </tr>

 </tbody>

</table>

<em>〈M</em>,<em>e</em><sub>1</sub><em>〉−→〈M</em><em>0</em>,<em>e</em><sub>1</sub><em>0 </em><em>〉          e</em><sub>1 </sub><em>6=lv</em><sub>1                                                           </sub><em>〈M</em>,<em>e</em><sub>2</sub><em>〉−→〈M</em><em>0</em>,<em>e</em><sub>2</sub><em>0 </em><em>〉</em>

<em>〈M</em>,<em>e</em><sub>1 </sub><em>=e</em><sub>2</sub><em>〉−→〈M</em><em>0</em>,<em>e</em><sub>1</sub><em>0 </em><em>=e</em><sub>2</sub><em>〉                                     〈M</em>,<em>lv</em><sub>1 </sub><em>=e</em><sub>2</sub><em>〉−→〈M</em><em>0</em>,<em>lv</em><sub>1 </sub><em>=e</em><sub>2</sub><em>0 </em><em>〉</em>

DOCALL                                                                                                DOCALLREC

<em>v =</em><strong>function </strong>(<em>x</em><sub>1 </sub>: <em>τ</em><sub>1</sub>,…,<em>x<sub>n </sub></em>: <em>τ<sub>n</sub></em>)<em>tanne                                               v =</em><strong>function</strong><em>x</em>(<em>x</em><sub>1 </sub>: <em>τ</em><sub>1</sub>,…,<em>x<sub>n </sub></em>: <em>τ<sub>n</sub></em>)<em>tanne</em>

<em>〈M</em>,<em>v</em>(<em>v</em><sub>1</sub>,…<em>v<sub>n</sub></em>)<em>〉−→〈M</em>,<em>e</em>[<em>v<sub>n</sub></em>/<em>x<sub>n</sub></em>]<em>···</em>[<em>v</em><sub>1</sub>/<em>x</em><sub>1</sub>]<em>〉                                 〈M</em>,<em>v</em>(<em>v</em><sub>1</sub>,…<em>v<sub>n</sub></em>)<em>〉−→〈M</em>,<em>e</em>[<em>v<sub>n</sub></em>/<em>x<sub>n</sub></em>]<em>···</em>[<em>v</em><sub>1</sub>/<em>x</em><sub>1</sub>][<em>v</em>/<em>x</em>]<em>〉</em>

SEARCHCALL1

<em>〈M</em>,<em>e〉−→〈M</em><em>0</em>,<em>e<sup>0</sup>〉</em>

<em>〈M</em>,<em>e</em>(<em>e</em><sub>1</sub>,…,<em>e<sub>n</sub></em>)<em>〉−→〈M</em><em>0</em>,<em>e</em><em>0</em>(<em>e</em><sub>1</sub>,…,<em>e<sub>n</sub></em>)<em>〉</em>

SEARCHCALL2

<em>〈M</em>,<em>e<sub>i</sub>〉−→〈M</em><em>0</em>,<em>e<sub>i</sub></em><em>0</em><em>〉</em>

<em>〈M</em>,<sup>¡</sup><strong>function</strong><em>p</em>(<em>x </em>: <em>τ</em>) <em>e</em><sup>¢</sup>(<em>v</em><sub>1</sub>,…,<em>v<sub>i</sub></em><em>−</em>1,<em>e<sub>i</sub></em>,…,<em>e<sub>n</sub></em>)<em>〉−→〈M</em><em>0</em>,<sup>¡</sup><strong>function</strong><em>p</em>(<em>x </em>: <em>τ</em>) <em>e</em><sup>¢</sup>(<em>v</em><sub>1</sub>,…,<em>v<sub>i</sub></em><em>−</em>1,<em>e<sub>i</sub></em><em>0</em>,…,<em>e<sub>n</sub></em>)<em>〉</em>

Figure 8: Small-step operational semantics of objects, binding constructs, variable and field assignment, and function call of JAVASCRIPTY.

<em>〈M</em>,<em>e〉−→〈M</em><em>0</em>,<em>e<sup>0</sup>〉</em>

<table width="612">

 <tbody>

  <tr>

   <td width="287">DOCALLNAME <em>v =</em><strong>function </strong>(<strong>name</strong><em>x</em><sub>1 </sub>: <em>τ</em>)<em>tanne</em><sub>1</sub><em>〈M</em>,<em>v</em>(<em>e</em><sub>2</sub>)<em>〉−→〈M</em>,<em>e</em><sub>1</sub>[<em>e</em><sub>2</sub>/<em>x</em><sub>1</sub>]<em>〉</em></td>

   <td width="325">DOCALLRECNAME <em>v =</em><strong>function</strong><em>x</em>(<strong>name</strong><em>x</em><sub>1 </sub>: <em>τ</em>)<em>tanne</em><sub>1</sub><em>〈M</em>,<em>v</em>(<em>e</em><sub>2</sub>)<em>〉−→〈M</em>,<em>e</em><sub>1</sub>[<em>e</em><sub>2</sub>/<em>x</em><sub>1</sub>][<em>v</em>/<em>x</em>]<em>〉</em></td>

  </tr>

  <tr>

   <td width="287">DOCALLVAR <em>v =</em><strong>function </strong>(<strong>var</strong><em>x</em><sub>1 </sub>: <em>τ</em>)<em>tanne</em><sub>1               </sub><em>a ∉ </em>dom(<em>M</em>)<em>〈M</em>,<em>v</em>(<em>v</em><sub>2</sub>)<em>〉−→〈M</em>[<em>a 7→ v</em><sub>2</sub>],<em>e</em><sub>1</sub>[<em>∗a</em>/<em>x</em><sub>1</sub>]<em>〉</em></td>

   <td width="325">DOCALLRECVAR <em>v =</em><strong>function</strong><em>x</em>(<strong>var</strong><em>x</em><sub>1 </sub>: <em>τ</em>)<em>tanne</em><sub>1            </sub><em>a ∉ </em>dom(<em>M</em>)<em>〈M</em>,<em>v</em>(<em>v</em><sub>2</sub>)<em>〉−→〈M</em>[<em>a 7→ v</em><sub>2</sub>],<em>e</em><sub>1</sub>[<em>∗a</em>/<em>x</em><sub>1</sub>][<em>v</em>/<em>x</em>]<em>〉</em></td>

  </tr>

  <tr>

   <td width="287">DOCALLREF <em>v =</em><strong>function </strong>(<strong>ref</strong><em>x</em><sub>1 </sub>: <em>τ</em>)<em>tanne</em><sub>1</sub><em>〈M</em>,<em>v</em>(<em>lv</em><sub>2</sub>)<em>〉−→〈M</em>,<em>e</em><sub>1</sub>[<em>lv</em><sub>2</sub>/<em>x</em><sub>1</sub>]<em>〉</em>SEARCHCALLVAR</td>

   <td width="325">DOCALLRECREF <em>v =</em><strong>function</strong><em>x</em>(<strong>ref</strong><em>x</em><sub>1 </sub>: <em>τ</em>)<em>tanne</em><sub>1</sub><em>〈M</em>,<em>v</em>(<em>lv</em><sub>2</sub>)<em>〉−→〈M</em>,<em>e</em><sub>1</sub>[<em>lv</em><sub>2</sub>/<em>x</em><sub>1</sub>][<em>v</em>/<em>x</em>]<em>〉</em></td>

  </tr>

  <tr>

   <td colspan="2" width="612"><em>〈M</em>,<em>e</em><sub>2</sub><em>〉−→〈M</em><em>0</em>,<em>e</em><sub>2</sub><em>0 </em><em>〉</em></td>

  </tr>

 </tbody>

</table>

<em>〈M</em>,<sup>¡</sup><strong>function</strong><em>p</em>(<strong>var</strong><em>x </em>: <em>τ</em>) <em>e</em><sub>1</sub><sup>¢</sup>(<em>e</em><sub>2</sub>)<em>〉−→〈M</em><em>0</em>,<sup>¡</sup><strong>function</strong><em>p</em>(<strong>var</strong><em>x </em>: <em>τ</em>) <em>e</em><sub>1</sub><sup>¢</sup>(<em>e</em><sub>2</sub><em>0 </em>)<em>〉</em>

SEARCHCALLREF

<em>〈M</em>,<em>e</em><sub>2</sub><em>〉−→〈M</em><em>0</em>,<em>e</em><sub>2</sub><em>0 </em><em>〉          e</em><sub>2 </sub><em>6=lv</em><sub>2</sub>

<em>〈M</em>,<sup>¡</sup><strong>function</strong><em>p</em>(<strong>ref</strong><em>x </em>: <em>τ</em>) <em>e</em><sub>1</sub><sup>¢</sup>(<em>e</em><sub>2</sub>)<em>〉−→〈M</em><em>0</em>,<sup>¡</sup><strong>function</strong><em>p</em>(<strong>ref</strong><em>x </em>: <em>τ</em>) <em>e</em><sub>1</sub><sup>¢</sup>(<em>e</em><sub>2</sub><em>0 </em>)<em>〉</em>

Figure 9: Small-step operational semantics of function call with parameter passing modes of JAVASCRIPTY.

<em>〈M</em>,<em>e〉−→〈M</em><em>0</em>,<em>e<sup>0</sup>〉</em>

DOCAST                                                            DOCASTNULL

<em>v 6=</em><strong>null         </strong><em>v 6= a                                       τ= </em>{ … } or <strong>Interface</strong><em>T </em>{ … }

<em>〈M</em>,<em>〈τ〉v〉−→〈M</em>,<em>v〉                                        〈M</em>,<em>〈τ〉</em><strong>null</strong><em>〉−→〈M</em>,<strong>null</strong><em>〉</em>

DOCASTOBJ

<table width="593">

 <tbody>

  <tr>

   <td width="413"><em>M</em>(<em>a</em>) <em>= </em>{ … }                          <em>τ= </em>{ …, <em>f<sub>i </sub></em>: <em>τ<sub>i</sub></em>,… } or <strong>Interface</strong><em>T </em>{ …, <em>f<sub>i </sub></em>: <em>τ<sub>i</sub></em>,… }<em>〈M</em>,<em>〈τ〉a〉−→〈M</em>,<em>a〉</em>TYPEERRORCASTOBJ</td>

   <td width="180"><em>f<sub>i </sub>∈ </em>dom(<em>M</em>(<em>a</em>))                      for all <em>i</em></td>

  </tr>

  <tr>

   <td width="413"><em>M</em>(<em>a</em>) <em>= </em>{ … }                       <em>τ= </em>{ …, <em>f<sub>i </sub></em>: <em>τ<sub>i</sub></em>,… } or <strong>Interface</strong><em>T </em>{ …, <em>f<sub>i </sub></em>: <em>τ<sub>i</sub></em>,… }<em>〈M</em>,<em>〈τ〉a〉−→ </em>typeerror</td>

   <td width="180"><em>f<sub>i </sub>∉ </em>dom(<em>M</em>(<em>a</em>))                for some <em>i</em></td>

  </tr>

  <tr>

   <td width="413">NULLERRORGETFIELD                                          NULLERRORASSIGNFIELD<em>〈M</em>,<strong>null</strong>.<em>f 〉−→ </em><sup>nullerror                     </sup><em>〈M</em>,<strong>null</strong>.<em>f =e〉−→ </em>nullerror</td>

   <td width="180">typeerror and nullerror propagation rules elided</td>

  </tr>

 </tbody>

</table>

Figure 10: Small-step operational semantics of type casting and null dereference errors of

JAVASCRIPTY. Ignore the “or <strong>Interface </strong>…” parts unless attempting the extra credit implementation.

<ol start="4">

 <li><strong>Extra Credit: Type Declarations and Recursive Types</strong>.</li>

</ol>

This exercise is for extra credit. Please only attempt this exercise if you have fully completed the rest of the lab.

Object types become quite verbose to write everywhere, so we introduce type declarations for them: <strong>interface </strong><em>T τ </em>; <em>e</em>

that says declare at type name <em>T </em>defined to be type <em>τ </em>that is in scope in expression <em>e</em>. We limit <em>τ </em>to be an object type. We do not consider <em>T </em>and <em>τ </em>to be same type (i.e., conceptually using name type equality for type declarations), but we permit casts between them. This choice enables typing of recursive data structures, like lists and trees (called recursive types).

(a) <strong>Lowering: Removing Interface Declarations. </strong>Type names become burdensome to work with as-is (e.g., requiring an environment to remember the mapping between <em>T </em>and <em>τ</em>). Instead, we will simplify the implementation of our later phases by first getting rid of <strong>interface </strong>type declarations, essentially replacing <em>τ </em>for <em>T </em>in <em>e</em>. We do not quite do this replacement because <strong>interface </strong>type declarations may be recursive and instead replace <em>T </em>with a new type form <strong>Interface </strong><em>T τ </em>that bundles the type name <em>T </em>with its definition <em>τ</em>. In <strong>Interface </strong><em>T τ</em>, the type variable <em>T </em>should be considered bound in this construct.

This “lowering” should be implemented in the function <strong>def </strong>removeInterfaceDecl(e: Expr): Expr

This function is very similar to substitution, but instead of substituting for program variables <em>x </em>(i.e., Var(<em>x</em>)), we substitute for type variables <em>T </em>(i.e., TVar(<em>T</em>)). Thus, we need an environment that maps type variable names <em>T </em>to types <em>τ </em>(i.e., the env parameter of type Map[String,Typ]).

In the removeInterfaceDecl function, we need to apply this type replacement anywhere the JAVASCRIPTY programmer can specify a type <em>τ</em>. We implement this process by recursively walking over the structure of the input expression looking for places to apply the type replacement.

Finally, we remove interface type declarations

<strong>interface </strong><em>T τ </em>; <em>e</em>

by extending the environment with [<em>T 7→</em><strong>Interface </strong><em>T τ</em>] and applying the replacement in <em>e</em>.

<h1>(b) Updating Type Checking and Reduction</h1>

To update type checking with interface declarations, we only need to update castOk with the rules given in Figure 11.

The update to step is also quite small. We only need to update a few cases corresponding to casting shown in Figure 10.

<em>τ</em>1 <em>τ</em>2

<table width="404">

 <tbody>

  <tr>

   <td width="246">CASTOKROLL <em>τ</em><sub>1</sub> <em>τ</em><em>0</em><sub>2</sub>[<strong>Interface</strong><em>T τ</em><em>0</em><sub>2</sub>/<em>T</em>]<em>τ</em><sub>1</sub> <strong>Interface</strong><em>T τ</em><em>0</em><sub>2</sub></td>

   <td width="158">CASTOKUNROLL<em>τ</em><em>0</em><sub>1</sub>[<strong>Interface</strong><em>T τ</em><em>0</em><sub>1</sub>/<em>T</em>] <em>τ</em><sub>2</sub><strong>Interface</strong><em>T τ</em><em>0</em><sub>1</sub> <em>τ</em><sub>2</sub></td>

  </tr>

 </tbody>

</table>

Figure 11: Type casting with interfaces.

<a href="#_ftnref1" name="_ftn1">[1]</a> Technically, the judgment form is not quite as shown because of the presence of the run-time error “markers” typeerror and nullerror.