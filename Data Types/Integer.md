# Integer primitive type

## 1. What is `Integer` in Apex?

- `Integer` is a primitive data type in Apex for whole numbers (no decimals).
- It is a 32‑bit signed integer and its value range is **-2,147,483,648** to **2,147,483,647**.
- Use `Integer` when you need counters, indexes, IDs (logic-level), loop counters, or any whole-number arithmetic where fraction precision is not required.

```java
Integer count = 0;
Integer max = 100;
for (Integer i = 0; i < max; i++) {
    count += 1;
}
System.debug('Final count: ' + count);
```

---

## 2. Core behaviours & tips

- Default initialization: an uninitialized `Integer` field has the value `0` inside Apex classes and triggers.
- Nullability: `Integer` variables declared as objects can be `null` (for example, `Integer i;` then `i` is `null` until assigned). Be careful to avoid `NullPointerException` when calling methods on a `null` value.
- Casting vs `valueOf()`: you can cast from other numeric types (like `Decimal`) to `Integer`, and you can also use `Integer.valueOf(String)` to parse. Casting can be faster than `valueOf` for numeric-to-numeric conversions.

```java
Decimal d = 12.9;
Integer a = (Integer)d;       // results in 12 (truncates fractional part)
Integer b = Integer.valueOf(String.valueOf(d)); // parse from string (works but clunkier)
```

---

## 3. Useful `Integer` class methods (quick reference)

> The `Integer` _primitive_ has a few helper class/instance methods provided by Apex. Below are the most commonly used ones with short descriptions and examples.

- `Integer.valueOf(String s)` — parse a string and return an `Integer`. Throws a `NumberFormatException` if the string cannot be parsed.

```java
try {
    Integer n = Integer.valueOf('123'); // returns 123
    Integer bad = Integer.valueOf('12a3'); // throws exception
} catch (Exception ex) {
    System.debug('Parsing failed: ' + ex.getMessage());
}
```

- `Integer.toString()` — convert an `Integer` to its string representation.

```java
Integer n = 42;
String s = n.toString(); // '42'
```

- `Integer.format()` — **returns a string representation formatted according to the context user's locale.**

```java
Integer n = 1000000;
String formatted = n.format();
System.debug('Formatted: ' + formatted);
```

> Important: `format()` is _locale-aware_. That means the exact string returned depends on the running user's locale settings (see the _Local context_ section below for an example).

- `Integer.compareTo(Integer other)` — compare two integers (useful for sorting or custom comparison logic).

```java
Integer a = 5;
Integer b = 7;
Integer cmp = a.compareTo(b); // negative value because 5 < 7
```

- `Integer.hashCode()` and `Integer.equals(Object obj)` — standard object methods available on boxed primitives.

---

## 4. Errors & defensive coding

- When parsing: always defend against bad input.

```java
public static Integer safeParse(String s, Integer fallback) {
    if (s == null) return fallback;
    try {
        return Integer.valueOf(s);
    } catch (Exception ex) {
        return fallback;
    }
}
```

- When calling instance methods on potentially `null` Integers, check for `null` first.

```java
Integer maybe = null;
if (maybe != null) {
    System.debug(maybe.format());
} else {
    System.debug('No value set');
}
```

---

## 5. Local context: `format()` and the user's locale (your local example)

The `format()` instance method returns a string using the **context user's locale**. In Apex you can check the current user's locale with `UserInfo.getLocale()`.

```java
String locale = UserInfo.getLocale();
System.debug('User locale: ' + locale);

Integer n = 1000000;
System.debug('n.format() -> ' + n.format());
```

**Local context example (India):**

- If the context user's locale is set to **`en_IN`** (English — India) then numeric grouping typically follows Indian numbering rules (lakhs/crores). For example, `1000000` may be formatted as `10,00,000`.
- If the locale is **`en_US`** (English — United States) the same number is usually formatted as `1,000,000`.

This difference matters when you display numbers to end users or build strings that depend on locale-sensitive formatting. When you need locale-neutral strings for storage or programmatic parsing, use `toString()` or explicit formatting logic rather than `format()`.

---

## 6. Full example — Putting it together

```java
public class IntegerTutorial {
    public static void demo() {
        // Show locale and formatting
        System.debug('Context user locale: ' + UserInfo.getLocale());

        Integer participants = 1234567;
        System.debug('Raw toString(): ' + participants.toString());
        System.debug('Locale formatted: ' + participants.format());

        // Safe parse
        String userInput = '2000';
        Integer parsed = safeParse(userInput, 0);
        System.debug('Parsed user input: ' + parsed);

        // Comparison example
        Integer a = 10;
        Integer b = 20;
        System.debug('compareTo result: ' + a.compareTo(b));
    }

    private static Integer safeParse(String s, Integer fallback) {
        if (s == null) return fallback;
        try {
            return Integer.valueOf(s);
        } catch (Exception ex) {
            return fallback;
        }
    }
}
```

Run `IntegerTutorial.demo()` in Execute Anonymous or a unit test to see the output.

---

## 7. When _not_ to use `Integer`

- For fractional numbers (money, rates), use `Decimal`.
- For very large integers beyond 32 bits, use `Long`.

---
