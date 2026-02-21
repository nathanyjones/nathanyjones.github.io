# Regex for Python

This tutorial serves as a guide for using regular expressions in python. We will first briefly look at what regex is and why it is useful, then how to create and format regular expressions, and finally how to use the regex library in python.

## Introduction to Regex

### What is Regex?

Simply put, regular expressions are strings constructed to define patterns used to match sequences of text. 

### When is Regex Useful?

Regex has a variety of applications across many fields. In the field of data science, regex is commonly used for data cleaning. 

For example, suppose you have a large dataset that stores a variety of dates as strings. Now, further suppose that these dates do not have a uniform format. For instance, "03/04/26", "3/4/26", and "03/4/2026" all represent the same date, but the slight differences in format may make them harder to gather and parse. Regex allows you to construct a pattern that matches these variations, allowing them to be identified and processed equally as dates.

Another place where regex is useful is extracting structured data from unstructured text. For instance, given a group of forms, regex might be used to validate proper formatting for the inputs, and then extract certain identifiable features, including ID numbers, phone numbers, names, and others.

## Building Regular Expressions

A regular expression can be built from several key elements: Literal characters, character classes, quantifiers, grouping symbols, alternation, and anchors.

___

### 1. Literal Characters

Literal characters are simply characters that match themselves.

**Example:**

```yaml
Pattern:            "Hello world"

Matches:            "Hello world"
                    "This program says, Hello world!"

Does not Match:     "Goodbye"       # Does not contain "Hello world"
                    "hello World"   # Case Sensitive
                    "HelloWorld"    # Whitespace Sensitive
                    ...
```

___

### 2. Character Classes

A character class is a set of characters that will match any one of the characters in the set. Character classes are generally denoted with square brackets as shown below.

**Example:**

```yaml
Pattern:            "[abc][123]"

Matches:            "a2"
                    "b1"
                    "c3"
                    ...

Does not Match:     "d"     # Not in the character class
                    "!"     # Not in the character class
                    ...
```

You can also specify ranges in the form: "[a-z]", which will match any character between lowercase 'a' and lowercase 'z'. 

**Example:**

```yaml
Pattern:            "[a-zA-Z0-9]"

Matches:            "a"
                    "Q"
                    "9"
                    ...

Does not Match:     "!"     # Not in class ranges
                    ...
```

There are certain predefined character classes in regex that assigned to shorthand **metacharacters** for ease of use. These are accessed using a backslash and an identifying character, a few of which can be found in the following table:

| Shorthand | Equivalent To | Matches |
|-----------|--------------|----------|
| `\d` | `[0-9]` | Any digit (0–9) |
| `\D` | `[^0-9]` | Any non-digit |
| `\w` | `[a-zA-Z0-9_]` | Word characters (letters, digits, underscore) |
| `\W` | `[^a-zA-Z0-9_]` | Non-word characters |
| `\s` | `[ \t\n\r\f\v]` | Any whitespace character |
| `\S` | `[^ \t\n\r\f\v]` | Any non-whitespace character |

\**Note that the \^ symbol at the start of a character class indicates the negation of the proceeding characters. More on this later.*

**Example:**

```yaml
Pattern:        "\w\w\s\d\d"

Matches:        "Az 63"
                "27 27"
                ...

Does not Match: "ABC23"     # No matching whitespace character
                "ab cd"     # 'cd' doesn't match \d (digit) identifier
                ...
```

___

### 3. Quantifiers

Quantifiers provide a way to specify repeated characters or character classes, and is the alternative to rewriting characters or character classes. 

To use a quantifier, you generally type the respective metacharacter immediately after the character or character class you wish to repeat. The following table contains a list of the most common quantifiers and the number of repeats they specify:

| Quantifier | Meaning | Example Pattern | Matches | Does Not Match |
|------------|----------|----------------|----------|----------------|
| `*` | 0 or more of the previous element | `ab*` | `a`, `ab`, `abb` | `b` |
| `+` | 1 or more of the previous element | `ab+` | `ab`, `abb` | `a` |
| `?` | 0 or 1 of the previous element (optional) | `colou?r` | `color`, `colour` | `colouur` |
| `{n}` | Exactly n occurrences | `\d{3}` | `123` | `12`, `1234` |
| `{n,}` | n or more occurrences | `\d{2,}` | `12`, `12345` | `1` |
| `{n,m}` | Between n and m occurrences | `\d{2,4}` | `12`, `123`, `1234` | `1`, `12345` |

**Example:**

```yaml
Pattern:        "a?\d+"

Matches:        "a123"
                "1"
                "12"
                ...

Does not Match: "a"         # \d+ requires one or more digits
                ...
```

Note that if we want a regular expression to match the character that a quantifier uses, like '+', you must specify that you want to use the character and not the quantifier by using the backslash character right before.

___

### 4. Grouping Symbols

Suppose we wanted to match a string consisting of a repeated 'abc' subsequence. Intuitively, we can use parentheses to group pieces of a regular expression together to more fully utilize quantifiers and other operators.

**Examples:**

```yaml
Pattern:        "(abc)+"

Matches:        "abc"
                "abcabcabc"
                ...

Does not Match: "a"     # Note (abc) is a string, not the character class [abc].
                ""      # The + quantifier indicates 'abc' must appear at least once.
                ...
```

```yaml
Pattern:        "([efg]\d+)+"

Matches:        "e123"
                "f1g23e456"
                ...

Does not Match: "efg123"     # Each [efg] character must be followed by one or more digits.
                ...
```

___

### 5. Alternation

Using a character class with two elements allows us to match either character once. Often, we want to accomplish a similar task with whole words, rather than single characters. 



___

### 6. Anchors

Anchors are symbols that match positions, and are often used to ensure that your regular expression matches only whole worlds. 

For example, suppose you want to find all instances of the word 'the' in a string of text.

```yaml
Pattern:        "the"

String:         "the mother and the father had a child"

Matches:        "the"   # Start of string
                "the"   # Inside "mo[the]r"
                "the"   # Standalone word
                "the"   # Inside "fa[the]r"
```

Clearly, this is not the intended behavior, and anchors present a solution. The most common anchors can be found in the following table:

| Anchor | Meaning                                    | Matches Position                     | Example Pattern | What It Matches                                |
| ------ | ------------------------------------------ | ------------------------------------ | --------------- | ---------------------------------------------- |
| `^`    | Start of string                            | Before the first character           | `^Hello`        | `"Hello world"` but not `"Say Hello"`          |
| `$`    | End of string                              | After the last character             | `world$`        | `"Hello world"` but not `"worldwide"`          |
| `\b`   | Word boundary                              | Between word and non-word characters | `\bthe\b`       | `"the cat"` but not `"mother"`                 |
| `\B`   | Not a word boundary                        | Inside a word                        | `\Bthe\B`       | `"mother"` but not `"the cat"`                 |

Using the word boundary anchor, we can construct an expression with the intended behavior for the example above:

```yaml
Pattern:        "\bthe\b"

String:         "the mother and the father had a child"

Matches:        "the"   # Start of string
                "the"   # Standalone word
```

___


## Using Regex with Python's 're' Library

Python has a specific library for regex called 're', which can be imported as follows:

```py
import re
```





|     Header 1        |    Header 2   |
| ------------------- | ------------- |
| Literal Characters: | Hello World   |
