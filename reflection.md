# Lab 5 – Reflection

**1. Which issues were easiest and hardest to fix?**
The easiest were the style ones, like `== None`, the missing newline and the missing docstrings, because the tool tells you exactly what to change. The hardest was `eval()` in `compute_weighted_gpa`. I couldn't just swap one call for another. I had to change the function's design so it takes a number instead of an expression string, and then update how `main` calls it.

**2. Did the tools report any false positives, or miss anything?**
Nothing was strictly false, but a few findings were low value. The Bandit warning about `random` for the PIN is only serious if the PIN protects something, though it was easy enough to fix. More important, the tools missed things I only saw by running the program. The GPA printed 4.35, which is impossible on a 4.0 scale, and the admin token was being printed to the console. Neither was flagged as such.

**3. How would I integrate static analysis into my workflow?**
I'd run Flake8 and Pylint in a pre-commit hook and run all three tools in CI on every pull request, with Bandit failing the build on medium or high findings. That way problems are caught while the change is small, instead of in one big cleanup like this lab.

**4. What improvements did I see in code quality, security and readability?**
Pylint went from 5.51 to 10.00, Bandit went from 6 findings to 0, and Flake8 went from 8 to 0. The code is also easier to read now, with docstrings, consistent names and shorter functions. It's safer too, since there's no `eval`, shell call or hardcoded secret. The mutable default argument was a real bug, not just a style issue, because logs would have leaked between unrelated calls.
