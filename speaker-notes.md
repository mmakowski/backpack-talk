
* I'm interested in writing good software and the state of the art
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
-> Exhibit 2: "functor"?
* ML is split into in-the-small and in-the-large constructs
