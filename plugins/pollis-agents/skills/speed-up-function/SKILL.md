---
name: speed-up-function
description: "Speed up a Julia function with the user: find it, diagnose where its time goes (type stability, allocations, a profile), explain the plan, each change with what in the diagnosis calls for it, and once the user agrees, write a faster version in a new file beside it, with the reason for each change in a comment, checked to give the same result and timed against the original. Never changes the original. Triggers include 'speed up this function', 'make this Julia code faster', 'why is this function slow', 'Speed Up with Agent'."
---

# Speed Up a Function

Makes a Julia function faster, with the user: you diagnose it, explain your plan and the reasoning behind it, and only then write a faster version, in a new file. The original function and its file are never changed. The user compares the two in the Compare step of **Compose > Profiling > Speed Up a Function...**.

## What you are given

The first message from Pollis names the function, the call to speed up (the function with its arguments, as the user wrote them on the page) and the name of the faster version. For the page's example it also names the file that defines it. If the message names no function (the skill was started some other way), ask the user which function and which call, or to start this task from Pollis, with **Speed Up with Agent** in the Change step of Speed Up a Function.

## Steps

1. **Find the function.** With a file named, read it. Otherwise look for its definition in the current folder and its subfolders (`function NAME(` or `NAME(...) =`); with several methods, the one the call reaches. If you cannot find it, ask the user for the file, or for the code. Find out, too, what the arguments of the call are (their types and sizes): from the file, or by asking. A speed-up that holds for a vector of 10 values may not hold for one of a million.
2. **Diagnose**, in a Julia session of your own started in the function's folder, with the file included and arguments like the user's (simulated with a fixed seed when they are not in the file; say so):
   - **Type stability**: `@code_warntype` of the call, and `@report_opt` of JET, to find variables whose type changes and calls resolved at run time.
   - **Allocations**: `@ballocations` and `@ballocated` of BenchmarkTools, with the arguments interpolated (`$x`).
   - **Where the time goes**: `Profile.@profile` of a few calls after a first one, read with `Profile.print(format = :flat, sortedby = :count, mincount = 10)`.
   - **Its time**: `minimum(@benchmark f($x) seconds = 1)`, the time to beat.

   Read the code too: work repeated in a loop whose result does not change, a global variable read inside the function, an array allocated in each pass, slices copied, a matrix looped over by rows.
3. **Explain the plan, and wait.** Before writing any code, tell the user:
   - what the diagnosis found, with its evidence: the variable inferred as a `Union` or `Any`, the bytes allocated per call, the line or function that takes most of the samples, and the time of a call;
   - each change you propose, and the reasoning for it: which finding it answers, why it should make the call faster, and what it costs, if anything (readability, generality, a different order of floating-point operations);
   - what you leave out, and why: a change that would alter the result, or would gain little for the code it adds.

   Most important first. Then ask whether to go ahead, and write nothing until the user agrees; change the plan as they ask.
4. **Write the faster version** in a new file beside the function's, named after the faster version (`NAME.jl`), never over a file that exists. It defines only the faster version, under the name Pollis gave, with the same arguments, and starts with a comment that lists each change and its reason, one line each, as explained in step 3. A short comment at each change says what it is. Keep the original's arguments, result and behaviour: the same types in, the same type and value out.
5. **Check it**, in your session:
   - **The same result**: `isequal`, else `isapprox` for floating-point results, which a different order of operations can change in the last digits; on the user's kind of arguments and on a few others, small and large, and the edge cases of the original (an empty vector, a single value). A different result stops here: fix it, or go back to the plan with the user.
   - **Faster**: `minimum(@benchmark ...)` of both, and their allocations. A change that does not gain goes out, unless the user wants it.
6. **Report**: the file written; each change, with the reason for it and what it gained; the times and allocations of the original and of the faster version, side by side, and how many times as fast it is; and what you left out. Then the next step on the page: include the new file in the Julia REPL (`include("PATH")`) and run **Compare**, which checks the result and times the two again.

## Rules

- Never change the original function or its file; write only the new file.
- Never write code before the user has agreed to the plan.
- Keep the result: a faster function that gives another answer is not a speed-up.
- Give the times measured, never "faster" or "the same" without the numbers.
- Never use DataFrames; write `transpose(A)`, never `A'`.
