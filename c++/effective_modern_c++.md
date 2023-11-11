Effective Modern C++
====================

Deducing Types
--------------
* [ ] Item 1: Understand template type deduction.
* [ ] Item 2: Understand `auto` type deduction.
* [ ] Item 3: Understand `decltype`
* [ ] Item 4: Know how to view deduced types.

auto
----
* [ ] Item 5: Prefer `auto` to explicit type declarations.
* [ ] Item 6: use the explicitly typed initializer idiom when `auto` decudes undesired types.

Moving to Modern C++
--------------------
* [ ] Item 7:
* [ ] Item 8:
* [ ] Item 9:
* [ ] Item 10:
* [ ] Item 11:
* [ ] Item 12:
* [ ] Item 13:
* [ ] Item 14:
* [ ] Item 15:
* [ ] Item 16:
* [x] Item 17: Understand special member function generation.
  *  **TLTR**
      * when `=default` is used, do it to all five if default ones needed
      * dtor is noexcept by default
      * dtor is virtual when base class has virtual dtor
      * ideally dtor blocks copy and move
      * ideally any move or copy blocks copy and move
    * special member functions: default ctor, dtor, copy operations, move operations
    * generate special member functions when **used** but not declared
    * all generated functions are non-virtual public inline except dtor
    * dtor is virtual if the base class has virtual dtor
    * generate default ctor when no ctor declared
    * generate move operations when no declaration of any move, copy operations, or dtor.
    * generate copy operations when used yet not declared. (historical reason)
    * move operations could be 'moved' by copy operations if not move-enabled.
    * declare a copy operation stops the generation of any move operation, but not the other copy operation
    * declare a move operation stops the generation of any copy or move operation
    * declare dtor stops the generation of move operations, (should but not done for legacy code) for copy operations.
    * move operations (construction and assignment) are 
    * catch: adding a customized dtor will stop the generation of move operations, which causes copy operations being called instead.
    * rule of three: copy ctor, copy assign, dtor
    * rule of five: copy ctor, copy assign, move ctor, move assign, dtor
    * member function templates never suppress generation of special member functions
    * **(explicitly declared) A blocks (the generation of) B**
    * dtor blocks move operations
    * dtor *should but not yet* block copy operations
    * copy operation blocks move operations
    * move operation blocks copy and move operations
    * copy operation *should but not yet* block the other copy operation

Smart Pointers
--------------
* [ ] Item 18:
* [ ] Item 19:
* [ ] Item 20:
* [ ] Item 21:
* [ ] Item 22:

Rvalue References, Move Semantics, and Perfect Forwarding
---------------------------------------------------------
* a parameter is always an lvalue, even if its type is an rvalue reference.
* [x] Item 23: Understand `std::move` and `std::forward`.
  * `std::move` and `std::forward` generate no code, they are function templates do casting
  * rvalue reference returned from functions are rvalues.
* [x] Item 24: Distinguish universal references from rvalue references.
  * auto&& is universal reference
  * if type deduction does not occur -> rvalue references
  * universal references -> rvalue reference if inited with rvalues
  * universal references -> lvalue reference if inited with lvalues
* [ ] Item 25:
* [ ] Item 26:
* [ ] Item 27:
* [ ] Item 28:
* [ ] Item 29:
* [ ] Item 30:

Lambda Expressions
------------------
* [ ] Item 31:
* [ ] Item 32:
* [ ] Item 33:
* [ ] Item 34:

The Concurrency API
-------------------
* [ ] Item 35:
* [ ] Item 36:
* [ ] Item 37:
* [ ] Item 38:
* [ ] Item 39:
* [ ] Item 40:

Tweaks
------
* [ ] Item 41:
* [ ] Item 42:
