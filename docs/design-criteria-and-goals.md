# Overview

## Design Rationale and Goals

### Domain

**Smart Kitchen Applications**
For this project, the scope is limited to kitchen-specific applications to ensure feasibility.

---

### Issues to Address

Cooking is often based on static recipes, but real-world cooking introduces uncertainty:

* Noisy environments
* Interruptions
* Missing ingredients
* Time constraints

WeCook addresses these by supporting:

* Substitutions
* Recovery logic (e.g., restart if butter burns)
* Fault tolerance (e.g., power loss handling)
* Reusable recipe programs with parameters and metadata

---

### Design Motivation

Many recipes require managing multiple components simultaneously. This language aims to:

* Unify smart kitchen devices under a single framework
* Enable automation and coordination between devices
* Allow users to replicate recipes consistently at home or in professional kitchens

The language emphasizes **simplicity and high-level abstractions** to remain accessible to non-programmers.

---

### Intended Use

* Professional kitchens
* Smart home kitchens

A head chef defines programs that assistants or automated systems execute in real time, coordinating multiple devices.

---

## Design Criteria

| Criterion                | Description                                                                                                                                                |
| ------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Simplicity**           | Limited feature set focused on cooking concepts (e.g., Recipe, Ingredients, qty). Device actions resemble real-world actions (`timer.start`, `oven.heat`). |
| **Orthogonality**        | Separation of concerns (e.g., Recipe vs Configuration). Configuration applies globally unless overridden.                                                  |
| **Level of Abstraction** | High-level abstraction; users interact with cooking concepts instead of low-level data structures. Device connectivity is hidden behind simple APIs.       |
| **Cost**                 | Easy to learn due to familiar culinary terminology. Compiled for fast execution, though compilation may take longer.                                       |
| **Portability**          | Platform-independent; programs can run across different systems.                                                                                           |
| **Expressivity**         | Limited by design. Strict syntax ensures precision and safety in execution.                                                                                |

---

## Example Code

```cpp
// RECIPE: Scrambled Eggs

Info {
    chef: "Julie Maestre";
    prep_time: 5min;
    cook_time: 5min;
    serving_size: 1;
    calories: 423;
    keywords: {"Scrambled Eggs", "Peppers", "Onions"};
}

Configuration
{
    UserInteraction
    {
        confirmEachStep: no;
        words_confirm: {"yes", "ok", "confirmed", "complete"};
        words_deny: {"no", "cancel", "stop"};
    };

    Commands
    {
        repeat: {"repeat", "again"};
        pause: {"wait"};
        resume: {"continue"};
        stop: {"stop"};
    };

    Devices
    {
        alexa: bluetooth(id="AA:BB:CC:DD:EE:FF") as speaker;
        samsung_stove: bluetooth(id="AA:BB:CC:DD:EE:FF") as stove;
    };
}

Recipe ScrambledEggs
{
    Ingredients
    {
        eggs {qty: 2ct};
        milk {qty: 1tbsp};
        butter {qty: 2tbsp};
        red_bell_pepper {qty: 0.5ct};
    };

    Substitutions
    {
        cilantro -> parsley;
        butter -> oil;
        hotsauce -> optional;
    };

    Steps
    {
        @prepEggs
        {
            tts("Crack the ${eggs} in a small bowl. Stir in the ${milk} and whisk well.");
            wait_for_reply;
        }

        @meltButter
        {
            tts("Melt some ${butter} in a skillet over medium-high heat.");
            stove.heat(MED_HIGH);
            wait_for_reply;
        }

        @addPeppers
        {
            tts("Add the peppers and cook for 3–4 minutes.");
            timer.start(60 * 3);
        }

        ...
    };
}
```

---

## Language Overview

### Keywords

* `Info` – metadata about the recipe
* `Configuration` – settings for interaction and devices
* `Recipe` – defines a program
* `Ingredients` – declares ingredients
* `Substitutions` – ingredient replacements
* `Steps` – execution instructions

---

### Built-In Functions

* `tts("phrase")` – text-to-speech
* `wait_for_reply` – pauses until user responds
* `timer.start(seconds)` – starts a timer
* `stove.heat(level)` – sets stove heat level
* `oven.temp(value)` – sets oven temperature
* `device.boil()` – heats to boiling point

---

### Devices

Voice and timing functions bind to default devices defined in `Configuration`.

---

### Operators

| Symbol | Description           |
| ------ | --------------------- |
| `->`   | Substitution operator |

---

### Punctuation

| Symbol  | Description            |
| ------- | ---------------------- |
| `{}`    | Block delimiters       |
| `()`    | Method calls           |
| `:`     | Key-value separator    |
| `;`     | Statement terminator   |
| `${}`   | Variable interpolation |
| `@name` | Step declaration       |

---

### Identifiers

* Recipe names: `ScrambledEggs`
* Step names: `prepEggs`
* Device names: `stove`, `oven`

---

### Data Types

* `qty` – quantity with units

  * Volume: tsp, tbsp, cup
  * Weight: oz, lb, kg
  * Count: ct
  * Temperature: C, F
* `tag` – labels (e.g., dairy, vegan)
* `boolean` – `yes` / `no`

---

### Comments

* `//` – single-line comment
* `/* ... */` – multi-line comment
