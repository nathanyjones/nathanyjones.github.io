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

A regular expression can be built from several key elements: Literal characters, character classes, quantifiers, grouping symbols, alternation, and anchors. We will go over each of these elements, but for a more in depth description, check out the [python re library documentation](https://docs.python.org/3/library/re.html)

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

Using a character class with two elements allows us to match either character once. Often, we want to accomplish a similar task with whole words, rather than single characters. This can be done with the pipe '|' symbol. Using the grouping symbols with alternation allows you to specify options for words and characters to match.

**Examples:**

```yaml
Pattern:            "the (cat)|(dog)|(frog)"

String:             "the cat and the dog sleep with a frog"

Matches:            "the cat"
                    "the dog"

Does not Match:     "a frog"    # Missing 'the', otherwise would match

```

___

### 6. Anchors

Anchors are symbols that match positions, and are often used to ensure that your regular expression matches only whole words. 

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

### Bringing it all Together

All of the pieces we've covered can be used in harmony to create regular expressions to meet our needs. Going back to the date example from the beginning, we can use these tools to create a regular expression that matches dates with slight variations in format:

```yaml
Matches:        "3/4/26"
                "03/04/26"
                "03/4/2026"

Pattern:        "\b\d{1,2}/\d{1,2}/\d{2,4}\b"
```

Note that this is a somewhat naive approach for identifying dates, and this pattern will also match nonexistent dates, such as "74/74/1974". However, there are other techniques for date validation, and this pattern will catch all dates of this format. Furthermore, we could fine-tune this pattern to match other date formats to catch more edge cases, which could be easily accomplished with more examples of date formats to match.

## Using Regex in Python

### Python's 're' Library 

Now that we know how to create regular expressions, we can learn how to make use of them in python. Python has a specific library for regex called `re`, which can be imported as follows:

```py
import re
```

The `re` library has several functions, both for matching strings and for modifying strings. You can find the full documentation [here](https://docs.python.org/3/library/re.html).

A few of the useful python `re` matching functions are listed in the following table:

| Function                        | Description                                                          | Returns                   | Example                           |
| ------------------------------- | -------------------------------------------------------------------- | ------------------------- | --------------------------------- |
| `re.search(pattern, string)`    | Searches the string for the first occurrence of the pattern anywhere | Match object or `None`    | `re.search(r"\d+", "abc123")`     |
| `re.match(pattern, string)`     | Checks for a match only at the beginning of the string               | Match object or `None`    | `re.match(r"\d+", "123abc")`      |
| `re.fullmatch(pattern, string)` | Ensures the entire string matches the pattern                        | Match object or `None`    | `re.fullmatch(r"\d+", "123")`     |
| `re.findall(pattern, string)`   | Returns all non-overlapping matches as a list                        | List of matches           | `re.findall(r"\d+", "a1b22c333")` |
| `re.finditer(pattern, string)`  | Returns an iterator of match objects                                 | Iterator of Match objects | `re.finditer(r"\d+", "a1b22")`    |

Notice the r character used before the pattern string. In python, the r specifies a raw string, preventing python from interpreting backslashes as escape characters. Additionally, functions like re.search() return a match object, so we use .group() to extract the actual matched text.

**Example:**

```py
text = "John: 123-456-7890, Jane: 555-555-5555"
pattern = r"\d{3}-\d{3}-\d{4}"

# re.search() — finds first match anywhere
print(re.search(pattern, text).group())
# Output: 123-456-7890

# re.match() — only checks beginning of string
print(re.match(pattern, text))
# Output: None

# re.findall() — returns all matches
print(re.findall(pattern, text))
# Output: ['123-456-7890', '555-555-5555']
```

In some cases, we want to modify text rather than just search or extract it. Python’s `re` library also provides functions for replacing, splitting, or counting matches. These allow you to efficiently clean, transform, or restructure strings.

| Function                                | Description                                            | Returns             | Example                         |
| --------------------------------------- | ------------------------------------------------------ | ------------------- | ------------------------------- |
| `re.sub(pattern, replacement, string)`  | Replaces matches with a replacement string             | Modified string     | `re.sub(r"\d+", "#", "a1b22")`  |
| `re.subn(pattern, replacement, string)` | Same as `sub`, but also returns number of replacements | (new_string, count) | `re.subn(r"\d+", "#", "a1b22")` |
| `re.split(pattern, string)`             | Splits a string at each match of the pattern           | List of substrings  | `re.split(r"\s+", "a b   c")`   |

Depending on the method, these functions will return either a modified string, a tuple, or a list.

**Example:**

```py
text = "John: 123-456-7890, Jane: 555-555-5555"
pattern = r"\d{3}-\d{3}-\d{4}"

# Replace all phone numbers with a placeholder
print(re.sub(pattern, "XXX-XXX-XXXX", text))
# Output: John: XXX-XXX-XXXX, Jane: XXX-XXX-XXXX

# Replace and get number of replacements
print(re.subn(pattern, "XXX-XXX-XXXX", text))
# Output: ('John: XXX-XXX-XXXX, Jane: XXX-XXX-XXXX', 2)

# Split the string at each phone number
print(re.split(pattern, text))
# Output: ['John: ', ', Jane: ', '']
```

These functions allow you to easily transform text in customizable ways, which is useful for reformatting data, replacing sensitive information, cleaning text, and much more.

### Using regex with Pandas

Finally, pandas makes it easy to use regular expressions to clean, extract, and transform data in a DataFrames. To use regex methods on DataFrames, you typically call the method on DataFrame.str.

| Function / Method                                  | Description                                                              | Returns                   | Example                                              |
| -------------------------------------------------- | ------------------------------------------------------------------------ | ------------------------- | ---------------------------------------------------- |
| `df['col'].str.contains(pattern)`                  | Returns a boolean Series indicating whether each row matches the pattern | Boolean Series            | `df['text'].str.contains(r"\d+")`                    |
| `df['col'].str.match(pattern)`                     | Checks if the **entire string** in each row matches the pattern          | Boolean Series            | `df['text'].str.match(r"\d{3}-\d{3}-\d{4}")`         |
| `df['col'].str.replace(pattern, repl, regex=True)` | Replaces matches with a replacement string (regex must be enabled)       | Series with replaced text | `df['text'].str.replace(r"\d+", "#", regex=True)`    |
| `df['col'].str.extract(pattern)`                   | Extracts **capture groups** from the pattern into new columns            | DataFrame                 | `df['text'].str.extract(r"(\d{3})-(\d{3})-(\d{4})")` |
| `df['col'].str.findall(pattern)`                   | Returns **all matches** in each row as a list                            | Series of lists           | `df['text'].str.findall(r"\d{3}-\d{3}-\d{4}")`       |

```py
import pandas as pd

df = pd.DataFrame({
    "name": ["John", "Jane"],
    "contact": ["123-456-7890", "555-555-5555"]
})

# Check which rows contain a phone number
print(df['contact'].str.contains(r"\d{3}-\d{3}-\d{4}"))
# Output:
# 0    True
# 1    True
# Name: contact, dtype: bool

# Extract area codes (first three digits) into a new column
df['area_code'] = df['contact'].str.extract(r"(\d{3})")
print(df)
# Output:
#    name                     contact area_code
# 0  John  123-456-7890       123
# 1  Jane  555-555-5555       555

# Replace phone numbers with a placeholder
df['contact_masked'] = df['contact'].str.replace(r"\d{3}-\d{3}-\d{4}", "XXX-XXX-XXXX", regex=True)
print(df)
# Output:
#    name                     contact area_code   contact_masked
# 0  John  123-456-7890       123                 XXX-XXX-XXXX
# 1  Jane  555-555-5555       555                 XXX-XXX-XXXX
```

Note that the regex parameter in the replace method should be set to True whenever you want to treat the pattern as regex. Furthermore, the pattern for the extract method should be enclosed in parentheses.

These built-in pandas string methods allow you to combine regex with pandas to quickly clean, extract, and transform text across DataFrame columns.

## Conclusion

Regular expressions are powerful for finding, extracting, and changing pieces of text. Python's re library makes it easy to identify and replace patterns in string, and pandas allows you to do this across columns of data efficiently.

Whether you’re cleaning messy datasets, extracting phone numbers, IDs, or other info, or just masking sensitive data, regex makes it way easier and more flexible than trying to do everything with plain python.

Try messing around with your own patterns on some text or data you have, see what you can extract, or try replacing things in different ways. Regex is best learned through experimentation, so I urge you to practice these concepts yourself. And feel free to reference the tables in this tutorial as a quick guide in your exploration.

You can start experimenting with constructing regex expressions right now with [regex101.com](https://regex101.com/), or learn more about regex [here](https://www.regular-expressions.info/).
