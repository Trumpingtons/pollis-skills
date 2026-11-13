---
name: translate-to-rust
description: "Translate a Julia function to Rust with the user: find it, explain the plan (the Rust type of each argument, the memory that stays with Julia, the order of operations kept, what does not translate as it is), and once the user agrees, write a Rust crate built as a C dynamic library and a Julia function with the original's arguments and result that calls it with @ccall, in new files beside it, built with cargo, checked to give the same result and timed against the original. Never changes the original. Triggers include 'translate this function to Rust', 'rewrite this Julia code in Rust', 'call Rust from Julia', 'Translate with Agent'."
metadata:
  updated: "2026-11-13"
---

# Translate a Julia Function to Rust

Translates a Julia function to Rust, with the user, so that Julia calls the Rust version as it called the original: you read the function, explain your plan, and only then write a Rust crate and the Julia function that calls it, in new files. The original function and its file are never changed. The user builds and compares the two in the Build and Compare steps of **Compose > Compilation Lab > Translate Julia to Rust...**.

## What you are given

The first message from Pollis names the function, the call to translate (the function with its arguments, as the user wrote them on the page) and the name of the Julia function that is to call the Rust version, called **the Rust version's name** below. For the page's example it also names the file that defines it. If the message names no function (the skill was started some other way), ask the user which function and which call, or to start this task from Pollis, with **Translate with Agent** in the Translate step of Translate Julia to Rust.

## What you write

Beside the function's file, never over a file that exists:

- **`NAME/`**, a Rust crate named after the Rust version's name: `Cargo.toml` with `edition = "2024"` and `[lib] crate-type = ["cdylib"]`, and `src/lib.rs`. The function Julia calls is `#[unsafe(no_mangle)] pub unsafe extern "C" fn`, takes only numbers of fixed size and pointers, and has a `# Safety` section in its documentation comment saying what the pointers must be.
- **`NAME.jl`**, which defines the Julia function `NAME`, with the original's arguments and result. It finds the library at `NAME/target/release/` (`lib<crate>.dylib`, `lib<crate>.so`, or `<crate>.dll` on Windows, with `Libdl.dlext`), builds the crate with `cargo build --release` when the library is missing, prepares the memory and calls the library with `@ccall`. The page's Build step includes it with `include("NAME.jl")`.

Use only the Rust standard library unless the plan, agreed with the user, names a crate and the reason for it.

## Steps

1. **Find the function.** With a file named, read it. Otherwise look for its definition in the current folder and its subfolders (`function NAME(` or `NAME(...) =`); with several methods, the one the call reaches. If you cannot find it, ask the user for the file, or for the code. Find out the types and sizes of the arguments of the call: from the file, or by asking. Read every function it calls that is not in Base: those have to be translated too, or stay in Julia.
2. **Check the tools.** `cargo --version`, Rust 1.85 or later for the 2024 edition. Without it, tell the user to install Rust with rustup (rustup.rs) and stop until they have.
3. **Explain the plan, and wait.** Before writing any code, tell the user:
   - the Rust type of each argument and of the result, and how each crosses the boundary: `Float64` as `f64`, `Int64` as `i64`, `Int32` as `i32`, a `Bool` as an `i32`, a complex number as its two parts, an array as a pointer and its dimensions;
   - the memory: Julia allocates every array the result needs and passes its pointer; Rust reads and writes it as a slice made with `std::slice::from_raw_parts` or `from_raw_parts_mut`, in one `unsafe` block, and never keeps a pointer after the call;
   - the indices: from 0 in Rust, and Julia's column order, element `(i, j)` of a matrix at `(i - 1) + (j - 1) * size(A, 1)`;
   - the order of the floating-point operations, kept as in the original so that the result agrees to the last bit; no `mul_add` where the original has `a * b + c`, and no sum in another order, unless the user agrees to results that agree only to rounding;
   - what does not translate as it is, and what you propose for it: dispatch on several argument types, values of no fixed size (`String`, `BigFloat`, arrays of arrays), allocation inside loops, calls to Julia packages, errors the original throws (a Rust panic at the edge of an `extern "C"` function aborts the Julia session, so return an error code that the Julia function turns into a Julia error).

   Most important first. Then ask whether to go ahead, and write nothing until the user agrees; change the plan as they ask.
4. **Write the crate and the Julia function**, as above. In `src/lib.rs`, a comment at the top saying what the crate computes and which Julia function it translates, and a short comment where the Rust differs from the Julia (an index shifted by one, a type made explicit, a conversion with `as`). In `NAME.jl`, a comment above the `@ccall` saying which memory Julia owns and how each argument crosses.
5. **Build and check it**, in a Julia session of your own started in the function's folder, with the original's file included and arguments like the user's (simulated with a fixed seed when they are not in the file; say so):
   - **Build**: `cargo build --release` in the crate's folder, with no warnings left. A library already opened by a Julia session stays as it was: after a change to `src/lib.rs`, start a new session.
   - **The same result**: `isequal` of the two results, else `isapprox` for floating-point results whose operations had to change order; on the user's kind of arguments and on a few others, small and large, and the edge cases of the original (an empty array, a single value). A different result stops here: fix it, or go back to the plan with the user.
   - **The times**: `minimum(@benchmark ...)` of both, with the arguments interpolated (`$x`), and their allocations. Report them as they are: a Rust version is not always faster than type-stable Julia.
6. **Report**: the files written; how each argument crosses and which memory Julia owns; what you changed from the original and why; the result check; the times and allocations of the original and of the Rust version, side by side, and how many times as fast it is. Then the next steps on the page: **Build**, which includes `NAME.jl`, and **Compare**, which checks the result and times the two again.

## Rules

- Never change the original function or its file; write only the new crate and the new Julia file.
- Never write code before the user has agreed to the plan.
- Keep the result: a translation that gives another answer is not a translation.
- Give the times measured, never "faster" or "the same" without the numbers.
- Never use DataFrames; write `transpose(A)`, never `A'`.
