Effective Modern C++
====================

Deducing Types
--------------
* [x] Item 1: Understand **template** type deduction.
  * `template<typename T> void f(ParamType param);`, use `f(expr)` to deduce types of `T` and `ParamType`
  * `template<class T> void f(T& t); int x; f(x);`, `T` is `int`
  * `template<class T> void f(T& t); const int cx; f(cx);`, `T` is `const int`
  * `template<class T> void f(T& t); const int& rx = x; f(rx);`, `T` is `const int`
  * `template<class T> void f(const T& t); f(x);`, `T` is `int`
  * `template<class T> void f(const T& t); f(cx);`, `T` is `int`
  * `template<class T> void f(const T& t); f(rx);`, `T` is `int`
  * change `T&` to `T*` pretty much the same
  * `template<class T> void f(T&& t); f(x);` `T` is `int&`, `t` is `int&`
  * `template<class T> void f(T&& t); f(cx);` `T` is `const int&`, `t` is `const int&`
  * `template<class T> void f(T&& t); f(rx);` `T` is `const int&`, `t` is `const int&`
  * `template<class T> void f(T&& t); f(42);` `T` is `int`, `t` is `int&&`
  * `template<class T> void f(T t); f(x);` `T` is `int`, `t` is `int`
  * `template<class T> void f(T t); f(cx);` `T` is `int`, `t` is `int`
  * `template<class T> void f(T t); f(rx);` `T` is `int`, `t` is `int`
  * `template<class T> void f(T t); f(array);` `T` is `decltype(&array[0])`
  * `template<class T> void f(T& t); f(array);` `T` is `decltype(array)`
* [x] Item 2: Understand `auto` type deduction.
  * `auto` type deduction is almost the same as template type deduction.
  * `const auto& cx = expr`, `auto` is the `T` in template, and `const auto&` is the **type specifier** `paramType` in template.
  * `auto&& rx = x` here `rx` is `int&`
  * `auto&& rx = cx` here `rx` is `const int&`
  * `auto&& rx = 42` here `rx` is `int&&`
  * `const char s[] = "hello";`
  * `auto x = s` here `x` is `const char *`
  * `auto& x = s` here `x` is `const char(&)[6]`
  * `void someFunc(int, double);`
  * `auto f = someFunc` here `f` is `void(*)(int, double)`
  * `auto& f = someFunc` here `f` is `void(&)(int, double)`
  * `auto v = {1, 2, 3}`, `v` is `std::initializer_list<int>`
  * `template<class T> void f(T t)` cannot accept `f({1, 2, 3})`, it cannot deduce type of `T`
  * `template<class T> void f(std::initializer_list<T> t)` accepts `f({1, 2, 3})`
  * the only real difference between auto and template type deduction is that auto assumes that a braced initializer represents a std::initializer_list, but template type deduction doesn’t.
  * `auto` used as lambda parameter or return type cannot deduce to `std::initializer_list`, using template type deduction instead of auto type deduction.
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
  * `foo(Bar())` the `Bar()` is rvalue, so if we have both `foo(Bar bar)` and `foo(Bar&& bar)`, then compiler will be confused.
  * `foo(Bar())` will pick up `foo(const Bar& bar)` if `foo(Bar&& bar)` does not exist, since it can bind rvalue to a const lvalue ref.
  * `foo(bar)` `bar` is lvalue, it will use `foo(Bar bar)` when both `foo(Bar bar)` and `foo(Bar&& bar)` provided.
  * `foo(cbar)` will confuse compiler when `foo(Bar bar)`, `foo(const Bar& bar)` both provided.
  * `foo(bar)` will use `foo(Bar& bar)`, when both `foo(Bar& bar)` and `foo(const Bar& bar)` exist.
  * `foo(bar)` will use `foo(const Bar& bar)` when `foo(Bar& bar)` is not available.
  * `foo(bar)` will confuse compiler when `foo(Bar bar)` coexists with any of `foo(Bar& bar)` or `foo(const Bar& bar)` 
* [ ] Item 29:
* [ ] Item 30:

Lambda Expressions
------------------
* [x] Item 31: Avoid default capture modes.
* [x] Item 32: Use init capture to move objects into closures.
* [x] Item 33: Use `decltype` on `auto&&` parameters to `std::foward` them.
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
