# Regular Expression Quick Reference

## Special characters

- `.` matches any single character
- `*` matches zero or more of the preceding character or group
- `+` matches one or more of the preceding character or group
- `?` matches zero or one of the preceding character or group
- `{n}` matches exactly n of the preceding character or group
- `{n,m}` matches between n and m of the preceding character or group
- `^` matches the start of a line
- `$` matches the end of a line
- `|` acts as an "or" operator, matching the preceding character or group or the following character or group
- `(...)` groups characters or other regular expressions together
- `[...]` matches any one character in the set
- `[^...]` matches any one character not in the set
- `\` escapes special characters

## Character classes

- `\d` matches any digit (equivalent to [0-9])
- `\D` matches any non-digit (equivalent to [^0-9])
- `\w` matches any word character (equivalent to [A-Za-z0-9_])
- `\W` matches any non-word character (equivalent to [^A-Za-z0-9_])
- `\s` matches any whitespace character (including space, tab, and newline)
- `\S` matches any non-whitespace character (equivalent to [^\s])

## Anchors

- `\b` matches a word boundary
- `\B` matches a non-word boundary

## POSIX Character classes

- `[[:alnum:]]` matches any alphanumeric character
- `[[:alpha:]]` matches any alphabetic character
- `[[:digit:]]` matches any digit
- `[[:lower:]]` matches any lowercase character
- `[[:upper:]]` matches any uppercase character
- `[[:punct:]]` matches any punctuation character
- `[[:space:]]` matches any whitespace character

## Modifiers

- `i` makes the match case-insensitive
- `m` makes the ^ and $ match the start and end of each line rather than the entire string
- `g` performs a global match (find all matches rather than stopping after the first match)
- `s` allows the . to match newline characters


## Examples

### Matching specific characters

```python
import re

# Match "cat" or "dog"
pattern = "cat|dog"
text = "I love my cat, but I also love my dog"
print(re.findall(pattern, text)) # Output: ["cat", "dog"]

# Match "cat" only if it's not preceded by "I love my"
pattern = "(?<!I love my )cat"
text = "I love my cat, but I also love my dog"
print(re.findall(pattern, text)) # Output: []

# Match "cat" or "dog" or "bird"
pattern = "cat|dog|bird"
text = "I love my cat, but I also love my dog, and my pet bird"
print(re.findall(pattern, text)) # Output: ["cat", "dog", "bird"]
```

### Matching multiple characters

```python
import re

# Match any digit
pattern = "\d"
text = "My phone number is 555-555-5555"
print(re.findall(pattern, text)) # Output: ["5", "5", "5", "5", "5", "5", "5", "5", "5"]

# Match any word character
pattern = "\w"
text = "my_variable_name"
print(re.findall(pattern, text)) # Output: ["m", "y", "_", "v", "a", "r", "i", "a", "b", "l", "e", "n", "a", "m", "e"]

# Match any non-word character
pattern = "\W"
text = "my_variable_name"
print(re.findall(pattern, text)) # Output: ["_"]

# Match any whitespace character
pattern = "\s"
text = "My name is John\nI live in New York"
print(re.findall(pattern, text)) # Output: [" ", "\n"]
```

### Matching groups

```python
import re

# Match "cat" or "dog" and group them
pattern = "(cat|dog)"
text = "I love my cat, but I also love my dog"
print(re.findall(pattern, text)) # Output: ["cat", "dog"]

# Match "cat" or "dog" and group them and their preceding words
pattern = "(I love my )(cat|dog)"
text = "I love my cat, but I also love my dog"
print(re.findall(pattern, text)) # Output: [("I love my ", "cat"), ("I love my ", "dog")]
```

### Matching ranges

```python
import re

# Match any number or letter
pattern = "[0-9A-Za-z]"
text = "My password is P@$$w0rd"
print(re.findall(pattern, text)) # Output: ["M", "y", "p", "a", "s", "s", "w", "o", "r", "d", "i", "s", "P", "w", "0", "r", "d"]

# Match any number or letter but not a specific character
pattern = "[^P@$]"
text = "My password is P@$$w0rd"
print(re.findall(pattern, text)) # Output: ["y", " ", "a", "s", "s", "w", "o", "r", "d", " ", "i", "s", " ", "w", "0", "r", "d"]
```

### Using anchors

```python
import re

# Match "cat" or "dog" at the start of the line
pattern = "^(cat|dog)"
text = "cat is a pet\ndog is a pet"
print(re.findall(pattern, text)) # Output: ["cat"]

# Match "cat" or "dog" at the end of the line
pattern = "(cat|dog)$"
text = "a pet is cat\na pet is dog"
print(re.findall(pattern, text)) # Output: ["dog"]
```

### Using POSIX Character classes

```python
import re

# Match any alphanumeric character
pattern = "[[:alnum:]]"
text = "my_variable_name_123"
print(re.findall(pattern, text)) # Output: ["m", "y", "_", "v", "a", "r", "i", "a", "b", "l", "e", "n", "a", "m", "e", "1", "2", "3"]

# Match any lowercase character
pattern = "[[:lower:]]"
text = "My Name Is John"
print(re.findall(pattern, text)) # Output: ["y", "a", "m", "e", "s", "i", "o", "h", "n"]

# Match any punctuation character
pattern = "[[:punct:]]"
text = "my_variable_name_123!"
print(re.findall(pattern, text)) # Output: ["!"]
```

### Using modifiers

```python
import re

# Perform a case-insensitive match
pattern = "cat"
text = "I love my Cat, but I also love my dog"
print(re.findall(pattern, text, re.IGNORECASE)) # Output: ["Cat"]

# Perform a global match
pattern = "a"
text = "banana"
print(re.findall(pattern, text, re.IGNORECASE)) # Output: ["a", "a"]

# Perform a multiline match
pattern = "^dog"
text = "cat is a pet\ndog is a pet"
print(re.findall(pattern, text, re.MULTILINE)) # Output: ["dog"]

# Perform a single line match
pattern = "a."
text = "cat\ndog\nbird"
print(re.findall(pattern, text, re.DOTALL)) # Output: ["at", "og"]

# Perform a search and replace using a regular expression
pattern = "cat"
text = "I love my cat, but I also love my dog"
print(re.sub(pattern, "dog", text)) # Output: "I love my dog, but I also love my dog"
```