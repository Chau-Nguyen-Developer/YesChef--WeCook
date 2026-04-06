# Reserved Words and Lexical Rules

## 1. Reserved Words

### Domain-Specific Structural Keywords

* `info` – metadata about the recipe
* `configuration` – settings for voice interaction, commands, and devices
* `recipe` – defines a program
* `ingredients` – declares ingredients required for a recipe
* `substitutions` – allows ingredient replacements
* `steps` – an ordered set of instructions executed by the cook
* `devices` – declares devices controlled by the program
* `as` – device role binding

### Future Keywords

Borrowed from common programming languages:

* `if`
* `else`
* `return`
* `import`
* `switch`
* `case`
* `default`

---

## 2. Lexical Rules

### Identifiers

Identifiers are used for:

* Recipe names
* Step names
* Device names
* Ingredient keys

#### Rules

* May contain letters (`A–Z`, `a–z`) and underscores (`_`) only
* Must begin with a letter
* Cannot contain whitespace
* Are **case-insensitive** (normalized to lowercase)

  * Example: `salt` and `Salt` are equivalent
* Cannot use reserved words
* Multi-word identifiers must use underscores

  * Example: `bell_pepper`

#### Valid Identifiers

```
bell_pepper
prep_eggs
kitchen_speaker
Salt
salt
```

#### Invalid Identifiers

```cpp
1bellpepper   // Must begin with a letter
bell pepper   // Whitespace not allowed
ingredients   // Reserved keyword
```

---

### Operators and Delimiters

| Symbol | Description                 |
| ------ | --------------------------- |
| `->`   | Substitution operator       |
| `{ }`  | Block delimiters            |
| `( )`  | Method calls                |
| `;`    | Statement terminator        |
| `:`    | Key-value separator         |
| `.`    | Member access / method call |

---

### Whitespace

* Used to separate tokens
* Otherwise ignored
* Indentation is **not significant** (unlike Python)

---

### Comments

* `//` – Single-line comment
* `/* ... */` – Multi-line comment
