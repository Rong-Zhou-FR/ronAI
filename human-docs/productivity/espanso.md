# Espanso, EXPAND SO !

## Text expander

If you are tired of typing the same phrase 300 times in a day. Get espanso.

## config: `~/.config/espanso/match`

xxx.yml<
```yml
matches:
  - trigger: ":loc:"
    replace: "locate -ib "

  - trigger: 'fk'
    replace: '\frac{$|$}{}'
    word: false

  - trigger: "nff"
    replace: >-
      nvim $(find . -type f -iname $|$ | head -n 1)
```
