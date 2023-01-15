# Regular Expression Cheat Sheet

To process regexes, you will use a "regex engine". Each of these engines use slightly different syntax called regex flavor. A list of popular engines can be found [here](https://www.regular-expressions.info/tools.html). Python has its own engine.

Since regex describes patterns of text, it can be used to check for the existence of patterns in a text, extract substrings from longer strings, and help make adjustments to text. Regex can be very simple to describe specific words, or it can be more advanced to find vague patterns of characters like the top-level domain in a url.  


## Definitions

- **Literal Character:** A literal character is the most basic regular expression you can use. It simply matches the actual character you write. So if you are trying to represent an "r", you would write r. 
- **Metacharacter:** Metacharacters signify to the regex engine that the following character has a special meaning. You typically include a `\`  in front of the metacharacter and they can do things like signify the beginning of a line, end of a line, or to match any single character. 
- **Character Class:** A character class (or character set) tells the engine to look for one of a list of characters. It is signified by `[` and `]` with the characters you are looking for in the middle of the brackets. 
- **Capture Group:** A capture group is signified by opening and closing, round parenthesis. They allow you to group regexes together to apply other regex features like quantifiers to the group.  


## Anchors

Anchors match a position before or after other characters.

| **Syntax** | **Description** | **Example pattern** | **Example matches** | **Example non-matches** |
| --- | --- | --- | --- | --- |
| `^` | match start of line | `^r` | <span style="background-color: #ffd8b5;">**r**</span>abbit <br> <span style="background-color: #ffd8b5;">**r**</span>accoon | parrot <br> ferret |
| `$` | match end of line | `$t` | rabbi<span style="background-color: #ffd8b5;">**t**</span> <br> foo<span style="background-color: #ffd8b5;">**t**</span> | trap <br> star |
| `\A` | match start of line | `\Ar` | <span style="background-color: #ffd8b5;">**r**</span>abbit <br> <span style="background-color: #ffd8b5;">**r**</span>accoon | parrot <br> ferret |
| `\Z` | match end of line | `t\Z` | rabbi<span style="background-color: #ffd8b5;">**t**</span> <br> foo<span style="background-color: #ffd8b5;">**t**</span> | trap <br> star |
| `\b` | match characters at the start or end of a word | `\bfox\b` | the red <span style="background-color: #ffd8b5;">**fox**</span> ran <br> the <span style="background-color: #ffd8b5;">**fox**</span> ate | foxtrot <br> foxskin scarf |
| `\B` | match characters in the middle of other non-space characters | `\Bee\B` | tr<span style="background-color: #ffd8b5;">**ee**</span>s <br> b<span style="background-color: #ffd8b5;">**ee**</span>f | bee <br> tree |


## Matching types of character

Rather than matching specific characters, you can match specific types of characters such as letters, numbers, and more.

| **Syntax** | **Description** | **Example pattern** | **Example matches** | **Example non-matches** |
| --- | --- | --- | --- | --- |
| `.` | Anything except for a linebreak | `c.e` | <span style="background-color: #ffd8b5;">**cle**</span>an <br> <span style="background-color: #ffd8b5;">**che**</span>ap | acert <br> cent |
| `\d` | match a digit | `\d` | <span style="background-color: #ffd8b5;">**6060**</span>-<span style="background-color: #ffd8b5;">**842**</span> <br> <span style="background-color: #ffd8b5;">**2**</span>b\|^<span style="background-color: #ffd8b5;">**2**</span>b | two <br> **___ |
| `\D` | match a non-digit | `\D` | <span style="background-color: #ffd8b5;">**The**</span> 5 <span style="background-color: #ffd8b5;">**cats ate**</span> <br> 12 <span style="background-color: #ffd8b5;">**Angry men**</span> | 52 <br> 10032 |
| `\w` | match word characters | `\wee\w` | t<span style="background-color: #ffd8b5;">**rees**</span> <br> <span style="background-color: #ffd8b5;">**bee4**</span> | The bee <br> eels eat meat |
| `\W` | match non-word characters | `\Wbat\W` | At <span style="background-color: #ffd8b5;">**bat**</span> <br> Swing the <span style="background-color: #ffd8b5;">**bat**</span> fast | wombat <br> bat53 |
| `\s` | match whitespace | `\sfox\s` | the <span style="background-color: #ffd8b5;">**fox**</span> ate <br> his <span style="background-color: #ffd8b5;">**fox**</span> ran | it’s the fox. <br> foxfur |
| `\S` | match non-whitespace | `\Sfox\S` | the <span style="background-color: #ffd8b5;">**fox**</span> <br> the <span style="background-color: #ffd8b5;">**fox**</span> | foxy <br> foxes |


## Character classes

Character classes are sets or ranges of characters.

| **Syntax** | **Description** | **Example pattern** | **Example matches** | **Example non-matches** |
| --- | --- | --- | --- | --- |
| `[xy]` | match several characters | `gr[ea]y` | <span style="background-color: #ffd8b5;">**gray**</span> <br> <span style="background-color: #ffd8b5;">**grey**</span> | green <br> greek |
| `[x-y]` | match a range of characters | `[a-e]` | <span style="background-color: #ffd8b5;">**a**</span>m<span style="background-color: #ffd8b5;">**be**</span>r <br> <span style="background-color: #ffd8b5;">**b**</span>r<span style="background-color: #ffd8b5;">**a**</span>n<span style="background-color: #ffd8b5;">**d**</span> | fox <br> join |
| `[^xy]` | Does not match several characters | `gr[^ea]y` | <span style="background-color: #ffd8b5;">**green**</span> <br> <span style="background-color: #ffd8b5;">**greek**</span> | gray <br> grey |
| `[\^-]` | match metacharacters inside the character class | `4[\^\.-+*/]\d` | <span style="background-color: #ffd8b5;">**4^3**</span> <br> <span style="background-color: #ffd8b5;">**4.2**</span> | 44 <br> 23 |


## Repetition

Rather than matching single instances of characters, you can match repeated characters.

| **Syntax** | **Description** | **Example pattern** | **Example matches** | **Example non-matches** |
| --- | --- | --- | --- | --- |
| `x*` | match zero or more times | `ar*o` | cac<span style="background-color: #ffd8b5;">**ao**</span> <br> c<span style="background-color: #ffd8b5;">**arro**</span>t | arugula <br> artichoke |
| `x+` | match one or more times | `re+` | g<span style="background-color: #ffd8b5;">**ree**</span>n <br> t<span style="background-color: #ffd8b5;">**ree**</span> | trap <br> ruined |
| `x?` | match zero or one times | `ro?a` | <span style="background-color: #ffd8b5;">**roa**</span>st <br> <span style="background-color: #ffd8b5;">**ra**</span>nt | root <br> rear |
| `x{m}` | match m times | `\we{2}\w` | <span style="background-color: #ffd8b5;">**deer**</span> <br> <span style="background-color: #ffd8b5;">**seer**</span> | red <br> enter |
| `x{m,}` | match m or more times | `2{3,}4` | 671-<span style="background-color: #ffd8b5;">**2224**</span> <br> <span style="background-color: #ffd8b5;">**2222224**</span> | 224 <br> 123 |
| `x{m,n}` | match between m and n times | `12{1,3}3` | <span style="background-color: #ffd8b5;">**123**</span>4 <br> <span style="background-color: #ffd8b5;">**12223**</span>84 | 15335 <br> 1222223 |
| `x*?, x+?, etc.` | match the minimum number of times - known as a lazy quantifier | `re+?` | t<span style="background-color: #ffd8b5;">**re**</span>e <br> f<span style="background-color: #ffd8b5;">**re**</span>eeee | trout <br> roasted |


## Capturing, alternation & backreferences

In order to extract specific parts of a string, you can capture those parts, and even name the parts that you captured.

| **Syntax** | **Description** | **Example pattern** | **Example matches** | **Example non-matches** |
| --- | --- | --- | --- | --- |
| `(x)` | capturing a pattern | `(iss)+` | M<span style="background-color: #ffd8b5;">**ississi**</span>ppi<br>mi<span style="background-color: #ffd8b5;">**ss**</span>ed | mist<br>persist |
| `(?:x)` | create a group without capturing | `(?:ab)(cd)` | Match: <span style="background-color: #ffd8b5;">**abcd**</span><br>Group 1: <span style="background-color: #ffd8b5;">**cd**</span> | acbd |
| `(?<name>x)` | create a named capture group | `(?<first>\d)(?<scrond>\d)\d*` | Match: <span style="background-color: #ffd8b5;">**1325**</span><br>first: 1<br>second: 3 | 2<br>hello |
| `(x\|y)` | match several alternative patterns | `(re\|ba)` | <span style="background-color: #ffd8b5;">**re**</span>d<br><span style="background-color: #ffd8b5;">**ba**</span>nter | rant<br>bear |
| `\n` | reference previous captures where n is the group index starting at 1 | `(b)(\w*)\1` | <span style="background-color: #ffd8b5;">**blob**</span><br><span style="background-color: #ffd8b5;">**brib**</span>e | bear<br>bring |
| `\k<name>` | reference named captures | `(?<first>5)(\d*)\k<first>` | <span style="background-color: #ffd8b5;">**51245**</span><br><span style="background-color: #ffd8b5;">**55**</span> | 523<br>51 |


## Lookahead

You can specify that specific characters must appear before or after you match, without including those characters in the match.

| **Syntax** | **Description** | **Example pattern** | **Example matches** | **Example non-matches** |
| --- | --- | --- | --- | --- |
| `(?=x)` | looks ahead at the next characters without using them in the match | `an(?=an)` <br> `iss(?=ipp)` | b<span style="background-color: #ffd8b5;">**an**</span>ana <br> Miss<span style="background-color: #ffd8b5;">**issi**</span>ppi | band <br> missed |
| `(?!x)` | looks ahead at next characters to not match on | `ai(?!n)` | f<span style="background-color: #ffd8b5;">**ai**</span>l <br> brail | faint <br> train |
| `(?<=x)` | looks at previous characters for a match without using those in the match | `(?<=tr)a` | tr<span style="background-color: #ffd8b5;">**a**</span>il <br> tr<span style="background-color: #ffd8b5;">**a**</span>nslate | bear <br> streak |
| `(?<!x)` | looks at previous characters to not match on | `(?!tr)a` | be<span style="background-color: #ffd8b5;">**a**</span>r <br> transl<span style="background-color: #ffd8b5;">**a**</span>te | trail <br> strained |


## Literal matches and modifiers

Modifiers are settings that change the way the matching rules work.

| **Syntax** | **Description** | **Example pattern** | **Example matches** | **Example non-matches** |
| --- | --- | --- | --- | --- |
| `\Qx\E` | match start to finish | `\Qtell\E`<br>`\Q\d\E` | <span style="background-color: #ffd8b5;">**tell**</span><br><span style="background-color: #ffd8b5;">**\d**</span> | I’ll tell you this<br>I have 5 coins |
| `(?i)x(?-i).` | set the regex string to case-insensitive | `(?i)te(?-i)` | s<span style="background-color: #ffd8b5;">**T**</span>ep<br><span style="background-color: #ffd8b5;">**tE**</span>ach | Trench<br>bear |
| `(?x)x(?-x)` | regex ignores whitespace | `(?x)t a p(?-x)` | <span style="background-color: #ffd8b5;">**tap**</span><br><span style="background-color: #ffd8b5;">**tap**</span>dance | c a t<br>rot a potato |
| `(?s)x(?-s)` | turns on single-line/DOTALL mode which makes the "." include new-line symbols (\n) in addition to everything else  | `(?s)first and second(?-s) and third` | <span style="background-color: #ffd8b5;">**first and**</span> <br> <span style="background-color: #ffd8b5;">**Second and third**</span> | first and <br> second <br> and third |
| `(?m)x(?-m)` | Changes ^ and $ to be end of line rather than end of string | `^eat and sleep$`| <span style="background-color: #ffd8b5;">**eat and sleep**</span> <br> <span style="background-color: #ffd8b5;">**eat and**</span> <br> <span style="background-color: #ffd8b5;">**sleep**</span> | treat and sleep <br> eat and sleep.  |


## Unicode

Regular expressions can work beyond the Roman alphabet, with things like Chinese characters or emoji.

- **Code Points**: The hexadecimal number used to represent an abstract character in a system like unicode. 
- **Graphemes**: Is either a codepoint or a character. All characters are made up of one or more graphemes in a sequence.

| **Syntax** | **Description** | **Example pattern** | **Example matches** | **Example non-matches** |
| --- | --- | --- | --- | --- |
| `\X` | match graphemes | `\u0000gmail` | <span style="background-color: #ffd8b5;">**@gmail**</span> <br> www.email<span style="background-color: #ffd8b5;">**@gmail**</span> | gmail<br> @aol |
| `\X\X` | Match special characters like ones with an accent | `\u00e8` or `\u0065\u0300` | <span style="background-color: #ffd8b5;">**è**</span> | e |
