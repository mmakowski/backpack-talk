* I would like to blame Derek for making me commit to this talk, but I only have myself to blame. A while ago I struggled to understand the design behind Backpack -- which will be subject of this talk -- and thought "what better way to learn than to deliver a talk on it?". After I told Derek "sure, I'll do it" I realised that most of the info about it is in very technical papers that deal with semantics of the system and assume a fair bit of background knowledge. There is no working implementation yet. The only approachable info is on Edward's blog.
* Well, there you go. I'm going to be talking about new developments in Haskell in front of some of the most accomplished Haskell developers out there. Scary.
* Myself, I don't know much about Haskell. The first time I've used it was over 10 years ago, but after completing a medium-sized project I never had a chance to work with it again. I'm interested in writing good software and the state of the art though, so I attend FP meetups and conferences.
* Mostly I don't like what I see in the software world, but every once in a while I see a piece of software that makes me say "Wow! That is elegant!"
* One of those moments was when a year ago Anil Madhavapeddy spoke about MirageOS at FPX conference.
* Mirage is a system for deploying applications to bare metal (actually, Xen) without intermediary OS
-> Unikernel diagram
* I love this from engineering standpoint because it gets rid of a lot of unnecessary complexity such as process scheduling, user management etc
* What does an app written in Mirage look like?
-> App diagram
* One of the nice things about Mirage is that it makes it easy to run the app on a Unix, and only swap in the Xen back-end when compiling for production. No code needs to change.
* Haskell's HaLVM requires code to be aware of hypervisor
-> Exhibit 1: Why OCaml

* we love haskell for how it enables composable code
* only in-the-small, not in-the-large
* ML is split into in-the-small and in-the-large constructs
* in the small is similar to Haskell: types, functions etc.
* in the large:
    * structures are like Haskell modules; they contain definitions of types and function
    * signatures describe module interfaces
    * structures can match signatures; the matching is structural, not nominal like in mainstream OO
    * functors take structures that implement specified signatures as arguments and produces another structure
    * so e.g. `Cohttp` is a functor that given a structure that matches signature `TCP` yields a structure that matches signature `HTTP`. Two different structures that match `TCP` can be produced using `MirTCP` and `UnixSocket` functors.
* TODO: OO, scala
* Haskell does not have similar facilities for parameterising modules
* Programmers try to roll their own
-> Exhibit 2: tagstream "functor"
* We use data types to express signatures. A functor would be a function that takes an `IntegerSig a` and produces a `RationalSig a`.
* TODO: what's wrong with this approach?
    * awkward, must pass dictionary around
    * carrier types have to be made visible (why would anyone care about my representation of integer type in `RationalSig`)?

-> Exhibit 3: tagsoup "functor"
* We use type classes to describe signatures. A functor would be an instance of a type class with constraint representing the parameterising singature.
* TODO: what's wrong with this approach?
    * more difficult type inference
    * it is often recommended that type classes are used for abstractions that are governed by specific laws (e.g. algebraic constructs); StringLike does not have such laws and thus breaks expectations
    * forces programmer to constantly abstract over the string type and apply, even though most likely we are going to use the same string type in entire program
* alternatives? Duplicated code, no parametricity.

* Backpack will provide a better way. New constructs to compose modules without hackery.
* Haskell already has structures -- those are existing modules.
* Signature modules: `hsig` extension, just function types, and type declarations, no definitions. The declarations are also called "holes".
* Key difference to ML modules: no functors. Instead, modules can depend on signatures, and implementations are mixed in at compile time.
* How is it going to be assembled together? Via cabal. I will show how based on the finest-grained package layout possible -- each module and signature in its own package -- but bear in mind this is not required, you can have packages which aggregate signatures and modules. See Edward's post "A taste of cabalized backpack".
* A "pure signature package" exposes `IntegerSig`
* The implementation package exposes a fully implemented module. Note that it does not refer to `integer-sig` anywhere.
* The "functor" package is indefinite, because it depends on the signature
* Finally, the "mixin" (or "functor application") package does not contain any code, just specifies which packages need to be combined to produce a fully defined implementation of Rational.
* Note: here we defined signature in a separate package. In this example it would have been equally appropriate for the `rational` package to internally specify the signature it depends on.

TODO: versioning of signatures
TODO: I always viewed cabal as yet another build tool. Now it is clear that Cabal is actually the language for programming in-the-large
TODO: how is it better (Scott's comment), double-vision problem
TODO: applicative vs generative semantics
