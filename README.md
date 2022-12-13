# Exceptions-and-references
Welcome to ERτL! 

ERτL stands for exceptions and references typed language and is prounced "urtle" and spelled with a lowercase tau (τ).

There are two main tasks in hw7: to implement the type-melding operator (pretty easy) and to check the types of ERτL programs (less easy).

Before embarking on these tasks, copy hw6/HW6.scala to hw7/HW7.scala in your repository.

The next order of business is to add the exception type to the code. For this, you only need to do the following:

*These line numbers are approximate, since I don't know what you might have done with your code.

1) Around line 35, add case TyExn to the Type enum (on its own line).

2) Around line 75, add case TyExn => "E" to unparseTy.

3) Around line 220, add case "E" => Some(TyExn,tl) to nextTy (we will write the exception type as E in the surface language).

That's all you need to do to add the exception type to the system.

After that, under the comment // === type checking (around line 320) add these function stubs:

def meld(tau1:Type, tau2:Type): Option[Type] = throw new Exception("todo: meld")

def typeOf(gamma:TypeEnv, t:Term): Type = throw new Exception("todo: typeOf")
There are formalizations of type melding and type checking below.

There is also a new interpret function that incorporates type checking. Please replace the old one with this one.

@main def interpret(codeOrFilename: String): Unit = {
  var code = codeOrFilename
  try {
    code = Source.fromFile(codeOrFilename).getLines.mkString
  } catch {
    case e: java.io.FileNotFoundException => "pass" // do nothing                                             
  }
  println(code)
  println()
  val toks = scan(code)
  val term = parse(toks)
  try {
    val tau  = typeOf(emptyTypeEnv,term)
    println("type:")
    println(unparseTy(tau))
  } catch {
    case e => {
      println(s"exception during typechecking: $e")
      System.exit(1)
    }
  }
  println()
  val rtrm = rewrite(term)
  println("after rewriting:")
  println(unparse(rtrm))
  println()
  println("evaluation:")
  try {
    val stps = steps(rtrm)
    for s <- stps do {
      println(unparse(s(0)))
    }
    val lastStore = stps.last(1)
    println(s"store:${storeString(lastStore)}")
  } catch {
    case e => println("exception during evaluation\n$e")
  }
}

In the figures that follow, a red capital E means exception type, a capital M in a blue box means the Morph type, and a pink circle-plus operator is an infix type melding operator.

I have not included typing rules like "0 is a Nat" because you can infer them, but any typing rule that's at all complicated is included here.

First, here's the definition of type melding:


And here are the typing rules. Enjoy!


^^^ Please note: corrected let rule! It was missing the conclusion.






Clarifications:

In the app rule, the meld in the premise is melding tau1 and (tau2 -> Morph).

In the try rule, the meld in the premise is melding tau2 and (Exn -> Morph).
