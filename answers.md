## Question B.1:
### Part a:

The command `(assert)` fails because internally it uses the PVS desicion procedures, and to add the formulae in the 
sequent to the internal efficient data structure of true assumption maintained by the desicion procedures it uses
`(record)`. However, `(record)` does not work for recursive `IF` structures without simplification. Internally `(assert)`
uses `(simplify)` to simplify the formulae in the sequent, which can only remove one level of `IF` if it is able to 
resolve the condition to `TRUE`, `FALSE`, or if the condition is already recorded. In the current case no such 
simplifications are possible, and a case split is necessary, and thus `(simplify)` fails to simplify the formulae, and
`(record)` fails to record them. This is why `(assert)` fails.

### Part b:

The command `(split)` fails because split can only split the sequent if there is a formula with a top level disunction,
that is, a formula with `IF` at the top level, an antecedent with `OR` or `IMPLIES` at the top level or a consequent with
`AND` or `<=>` at the top level. The top level construct of the only formula in the sequent in this case is `=`, and so
`(split)` fails.

The recursive definintion of `(prop)` simply tries `(flatten)` and `(split)` repeatedly to recursively remove top level 
propositional connectives in the goal. In this case, the goal has no top level propositional connective, and `(split)`
or `(flatten)` has no effect. Thus, `(prop)` fails.

The command `(ground)` performs propositional simplifications followed by further simplifications performed by `(assert)`.
As the top level connective in the only formula in the sequent is not propositional, no propositional simplification is
possible. We have also seen that `(assert)` itself fails in this case, and thus `(ground)` produces no change.

### Part c:

The required manual proof using `(case)` is saved as `iflemma-1`. The number of case splits was minimised by spliting into 
cases according to the conditions on the right hand side, that is, each condition in the right hand `IF` is a case. This
was achieved by applying case on the disjunction of all the conditions disjuncted with the negation of the conjunction of
all the conditions. The negation term in above was computed by hand. This produces a total of 6 cases.

### Part d:

We note that expressions of the form `F(IF A THEN B ELSE C) can be transformed so that the top level connective is `IF` by
"lifting" the `IF`, transforming the expression to `IF A THEN F(B) ELSE F(C)`. The `(lift-if)` command looks at all the 
leftmost `IF` connectives in a formula and selects the innermost one to apply the lifting transformation to it.

### Part e:

Applying the `(lift-if)` command lifts all the `IF` conditions on the left of the `=` to above the `=`, transforming the
formula from the form:

```
(IF z = y
         THEN (IF w = v THEN 5
               ELSIF z = x THEN (IF w = u THEN 3 ELSE f(z) ENDIF)
               ELSE f(z)
               ENDIF)
       ELSIF z = x THEN 3
       ELSE f(z)
       ENDIF) = R
```

to the form:

```
(IF z = y
         THEN (IF w = v THEN 5 = R
               ELSIF z = x THEN (IF w = u THEN 3 = R
                                          ELSE f(z) = R
                                          ENDIF)
               ELSE f(z) = R
               ENDIF)
       ELSIF z = x THEN 3 = R
       ELSE f(z) = R
       ENDIF)
```

so that the top level connectives some depth are now `IF`. The command also substitutes `TRUE` or `FALSE` into the instances
of the boolean expression the lifted ifs branch on within the copied instances of `R` depending on which branch of the lifted 
if the instance of `R` lies in. Applying `(ground)` proves the resulting formula. This proof is saved as `iflemma-liftif`.

The command `(lift-if * )` has the exact same effect as `(lift-if)`, and exactly as before the proof can be completed with
`(ground)`. This proof is saved as `iflemma-liftif-star`.

### Part f:

As we saw in the proof of `iflemma`, applying `(lift-if)` to the initial sequent in the proof of `iflemma1` lifts the `IF` to 
the left of the `=` above the `=`, transforming the formula in a manner very similar to how the formula was transformed while
proving `iflemma`. Similar to the previous case, the decision condition expressions for the `IF` in the right hand are substituted
according to which branch the instance of the right hand expression belongs. Applying `(ground)` on the transformed formula
completes the proof, as before. This proof is saved as `iflemma1-1`.
