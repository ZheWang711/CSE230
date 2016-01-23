# Lambda Calculus

This file serves as your book's preface, a great place to describe your book's content and ideas.

$$
iszero 1 = 1(\lambda b.false)true = \lambda f.\lambda s.fs (\lambda b. \text{false) true} =  (\lambda s.\lambda b.\text{false s) true} $$

What's the next step to do? Computing the left term:

$$(\lambda s.\lambda b.\text{false s) true} = (\lambda s.false)true = false$$

or, substituting $$ true $$ into s?

$$ (\lambda s.\lambda b.\text{false s) true} = \lambda b.\text{false true} = false $$

Although the final results are the same, which step is correct, or both of them are correct (in general situation).