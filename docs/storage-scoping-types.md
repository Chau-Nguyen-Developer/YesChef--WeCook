# Storage, Scope, and Data Types

## 1. Storage

### Static or Dynamic Allocation

Storage should be static. Since ingredient data is declared at the very start, we should ensure that the program compiles safely before executing the cooking instructions to avoid safety issues. However, substitutions may be performed dynamically since the user may or may not decide mid-step to substitute an ingredient.

In our code example, all ingredients are declared statically before the cooking steps are executed. During compilation, the compiler will verify that all quantity values have valid units, confirm that there are no duplicate ingredient names, and allocate storage for each ingredient record. However, substitutions will be dynamically resolved if triggered by the cook during step execution.

### Code Example – Static Allocation

```cpp
Recipe PastaAlOlio
{
        Ingredients
        {
                pasta        {qty: 16oz, tags: {"grain"}};
                garlic       {qty: 6ct, prep: minced};
                olive_oil    {qty: 3tbsp, tags: {"vegan"}};
                chili_flakes {qty: 1tsp, optional: yes};
        };

        Substitutions
        {
                // Resolved dynamically at runtime if triggered
                chili_flakes -> red_pepper_powder;
        };

        Steps
        {
                @boilPasta
                {
                        tts("Boil ${pasta} until al dente.");
                        timer.start(60 * 10);
                }
        };
}
```

---

## 2. Scoping

### Semantics of Blocks

WeCook uses curly braces `{ }` to define blocks. The primary block types are:

* Recipe
* Ingredients
* Substitutions
* Steps
* Configuration
* Info

The language also supports conditional blocks:

* If
* If-Else
* Switch

### Code Example – Semantics of Blocks

```cpp
Recipe GrilledCheese
{

    Ingredients
        {
                bread  {qty: 2ct};
                cheese {qty: 2oz, tags: {"dairy"}};
                butter {qty: 1tbsp};
        };

        Steps
        {
                @grillSandwich
                {
                        stove.heat(MED);
                        tts("Butter the ${bread} and assemble sandwich.");
                        wait_for_reply;
                        
                        if (stove.surface_temp >= 300F)
                        {
                                tts("Pan is ready. Place sandwich now.");
                        }
                        else
                        {
                                tts("Wait for pan to heat up.");
                                wait_for_reply;
                        }
                
                         timer.start(60 * 3);
                         tts("Flip the sandwich.");
                         timer.start(60 * 3);
                        
                         ...
                }
        };
}
```

---

### Nested Functions

WeCook does not support nested functions.

Steps are sequential and self-contained. Shared logic should be extracted into separate recipes or handled through substitutions.

### Code Example – Nested Functions

```cpp
Steps
{
        @boilWater
        {
                // define steps here…
        }
        @cookPasta
        {
                // define steps here…
        }
        @outerStep
        {
                @innerStep { ... }    // COMPILE ERROR
        }
};
```

---

### Order of Declaration

A variable must be declared before it is used.

* Ingredients must be declared in `Ingredients`
* Devices must be declared in `Configuration`

### Code Example – Order of Declaration

```cpp
Recipe Salsa
{
  Ingredients
  {
    tomato {...}; onion {...}; cilantro {...}; lime {...};
  };

  Steps
  {
    @mixIngredients
    {
      tts("Combine ${tomato}, ${onion}, and ${cilantro}.");
      tts("Squeeze ${lime} juice over the mixture.");
      ...
    }
    @dicing
    {
    tts("Dice the ${jalapeno}.");  // COMPILE ERROR
    }
  };
}
```

---

### Globals

Globals are allowed. Configuration blocks act as globals and can be accessed within Steps.

### Static vs Dynamic Scoping

WeCook uses **static (lexical) scoping**. Scope is determined by source code structure, not runtime execution.

---

## 3. Type System

### Primitive Types

* int
* float
* quantity (with units)
* string
* boolean (YES / NO)

#### Quantity Units

* Volume: tsp, tbsp, cup
* Weight: oz, lb, kg
* Count: ct (integer)
* Temperature: C, F

Volume, weight, and temperature are floats. Count is an integer.

---

### Pointers and References

* No pointers
* References are allowed
* Arrays are supported

---

### Type Checking

Types are inferred from values and units.

Static type checking is enforced to prevent runtime errors.

Example of invalid expression:

```cpp
1tbsp + 350F   // ERROR
```

---

### Strings

Strings are fixed-length arrays of characters.

---

### Enumeration Types

Built-in symbolic constants:

* Units (tbsp, oz, etc.)
* Heat levels (LOW, MED, HIGH)

Compiler assigns internal numeric values.

---

### Arrays

* Statically allocated
* No multidimensional arrays

Supported operations:

* Insertion
* Deletion
* Element-wise arithmetic

---

### Associative Arrays

Key-value pairs are supported.

---

### Records and Tuples

Used for:

* Ingredients
* Info
* Configuration

Example:

```cpp
eggs {qty: 2ct, tags: {"animal product"}}
```

---

### Lists

Steps behave like a sequential list and are executed top-to-bottom.

---

### Unions

Not supported.

---

### Garbage Collection

Mark-and-Sweep garbage collection is used.

---

## Full Code Example

```cpp
Info
{
    chef: "Mom With a Prep";
    prep_time: 10min;
    cook_time: 12min;
    serving_size: 6
    calories: 423
    keywords: {"Cookie", "Dessert"};
}

Configuration
{
    Devices
    {
        samsung_oven: bluetooth(id="AA:BB:CC:DD:EE:01") as oven;
    }
    UserInteraction
    {
        confirmEachStep: yes;
        confirm: {"yes", "ok", "confirmed", "complete"};
        continue: {"next", "continue"};
        deny: {"no", "cancel", "stop"};
        repeat: {"repeat", "again"};
        pause: {"wait"};
        resume: {"continue"};
        stop: {"stop"};
    };
};

Recipe Chocolate_Chip_Cookies
{
    Ingredients
    {
      butter {qty: 0.5cup; prep: melted; type: unsalted}
      flour {qty: 1.5cup; type: all_purpose}
    }    

    Steps
    {
      @preheat_oven
      {
        oven.preheat(350F);
      }

      @bake
      {
        timer.start(60*12);
      }

      @end
      {
        endRecipe();
      }
}
```
