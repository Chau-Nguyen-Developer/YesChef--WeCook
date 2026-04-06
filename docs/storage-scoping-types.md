# Storage, Scoping, and Types

## 1. Structure and Semantics of Arithmetic Expressions

### Operators and Types

WeCook supports a minimal set of operators for cooking-related math (e.g., scaling recipes, unit conversion, timers, appliance automation).

* Operators may be **unary, binary, or ternary**
* Supports **prefix** (e.g., `++`, `--`) and **infix** operators

### Expression Purity

Expressions are designed to remain **pure** (no side effects) to avoid unsafe or unpredictable behavior in kitchen automation.

### Precedence and Associativity

* Follows **PEMDAS**
* Parentheses take highest priority
* Binary operators: **left-to-right**
* Assignment operators: **right-to-left**

### Operator Overloading

* No user-defined overloading
* Built-in operators support implicit unit conversion

#### Example

```cpp
// Built-in overloading for quantity arithmetic
// Both are volume: compiler converts cups to tbsp

total_butter: 2tbsp + 0.25cup;   // Result: 6tbsp

// Incompatible types – compile error
bad_mix: 2tbsp + 350F;           // ERROR
```

---

### Type Coercion

#### Rules

* **Integer → Float**: Promoted automatically
* **Unit Conversion**: Right operand converts to left operand's unit

#### Example

```cpp
2ct * 1.5 → 3.0
2tbsp + 1cup → 18tbsp
```

### Explicit Casting

```cpp
servings: 3ct;
total_flour: 1.5cup * to_float(servings);   // 4.5cup
rounded: to_int(total_flour);               // 4

oven.temp(to_temp(180C, F));               // 356F
```

---

### Operator Table

| Operator Type  | Symbol    | Description            | Precedence |
| -------------- | --------- | ---------------------- | ---------- |
| Unary          | NOT, -    | Negation               | Highest    |
| Increment      | ++        | Increase by 1          |            |
| Decrement      | --        | Decrease by 1          |            |
| Multiplicative | * /       | Scaling, time          |            |
| Additive       | + -       | Combine quantities     |            |
| Relational     | < > <= >= | Comparisons            |            |
| Equality       | == !=     | Equality check         |            |
| Logical AND    | AND       | Both true              |            |
| Logical OR     | OR        | One true               |            |
| Substitution   | ->        | Ingredient replacement |            |
| Assignment     | = :       | Assign values          | Lowest     |

---

### Overflow and Underflow

* **Integer Overflow**: Runtime error + system shutdown
* **Float Overflow**: Runtime error (no infinity)
* **Float Underflow**: Rounded to zero
* **Division by Zero**: Runtime error + shutdown

---

## 2. Logical Expressions

### Operators

| Operator         | Symbol | Description       |
| ---------------- | ------ | ----------------- |
| Logical AND      | and    | True if both true |
| Logical OR       | or     | True if one true  |
| Logical NOT      | not    | Inverts value     |
| Equal            | ==     | Equality check    |
| Not Equal        | !=     | Inequality check  |
| Greater Than     | >      | Left > Right      |
| Less Than        | <      | Left < Right      |
| Greater or Equal | >=     | Left ≥ Right      |
| Less or Equal    | <=     | Left ≤ Right      |

### Short-Circuit Evaluation

* Uses **lazy evaluation**
* Stops evaluating once result is determined

---

## 3. Assignments

### Simple Assignment

Syntax:

```cpp
{type}: {value}
```

#### Example

```cpp
Info {
    chef: "Mom With a Prep";
    prep_time: 10min;
    cook_time: 12min;
    serving_size: 6;
    calories: 423;
    keywords: {"Cookie", "Dessert"};
}
```

---

### Conditional Assignment

Supports:

* `if / else`
* Ternary operator

#### Example

```cpp
if (stove.surface_temp >= 300F) {
    timer.start(60 * 10);
} else {
    wait.for.reply();
}

// Ternary
(stove.surface_temp >= 300F ? timer.start(600) : wait.for.reply());
```

---

### Compound Assignments

Not supported.

```cpp
// NOT ALLOWED
pasta.qty += 7

// ALLOWED
pasta.qty = pasta.qty + 7
```

---

### Multiple and Parallel Assignments

Supported.

---

### Unary Assignment Operators

Supported:

* `++`
* `--`

---

### Assignments as Expressions

Allowed (useful in loops).

---

## Full Code Example

```cpp
Info {
    chef: "Mom With a Prep";
    prep_time: 10min;
    cook_time: 12min;
    serving_size: 6;
    calories: 423;
    keywords: {"Cookie", "Dessert"};
}

Configuration {
    Devices {
        samsung_oven: bluetooth(id="AA:BB:CC:DD:EE:01") as oven;
    }

    UserInteraction {
        confirmEachStep: yes;
        confirm: {"yes", "ok", "confirmed", "complete"};
        continue: {"next", "continue"};
        deny: {"no", "cancel", "stop"};
        repeat: {"repeat", "again"};
        pause: {"wait"};
        resume: {"continue"};
        stop: {"stop"};
    }
}

Recipe Chocolate_Chip_Cookies {
    Ingredients {
        butter {qty: 0.5cup; prep: melted; type: unsalted}
        granulated_sugar {qty: 0.5cup}
        brown_sugar {qty: 0.5cup; prep: packed}
        egg {qty: 1ct}
        vanilla_extract {qty: 1tsp}
        flour {qty: 1.5cup; type: all_purpose}
        baking_soda {qty: 0.5tsp}
        salt {qty: 0.25tsp}
        chocolate_chips {qty: 2cup; type: semisweet}
    }

    Steps {
        @preheat_oven {
            oven.preheat(350F);
            tts("Preheat oven to 350F and prepare tray");
        }

        @bake {
            if (samsung_oven.temp >= 350F) {
                timer.start(60 * 12);
            } else {
                wait_for_reply(resume);
            }
        }

        @end {
            tts("Recipe complete. Shutting down.");
            endRecipe();
        }
    }
}
```
