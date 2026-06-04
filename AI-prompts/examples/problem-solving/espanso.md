# Problem: User frustration over espanso

## context

user use espanso for:

- text expansion (e.g., sbs > step by step)
- typing less common char (e.g., :u: > ŭ
- writing katex formulas (e.g., fk > \frac{}{})

## user issues

- large plain yml config is hard to read for humans 
  - chronological order, no automatic sorting
  - difficult to read/locate a particular substitution later
    - can use text search, but since exact trigger/replace is often not known, need to usually experiement with multiple search terms. Low efficiency
- cursor positioning is frustratingly unstable
  - whenver the "replace" text contains quotes ('' or ""), the cursor placeholder `$|$` behaves unexpectedly

## user testimony

- espanso used on average every 2-3 minutes
- frequent addition of new trigger/replace pairs to yml needed
- frequent frustration/lost productivity over unexpected behaviour/forgotten triggers

## You should

- understand the user issues
  - refer to espanso doc
  - perform online searches
    - for feedback from other users
    - for potential workarounds/solutions
- propose solution with least long-term effort consistently addressing the issue
  - simple workaround ?
  - espanso extension ?
  - a FOSS alternative ?
  - writing an extension/a complementary program to espanso ?
  - writing a custom alternative of espanso ? 
    - user is junior software engineer with javascript (vue.js+nest.js) and python proficiency
    - would the effort be proportionate to gain ?
