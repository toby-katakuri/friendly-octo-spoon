# BPE（Byte-Pair Encoding）

### 首先是对文本进行基础分词
```python
import re
def wordpunct_tokenize(text):
    _pattern = r"\w+|[^\w\s]+"
    _regexp = re.compile(_pattern, flags=re.UNICODE | re.MULTILINE | re.DOTALL)
    return _regexp.findall(text)
```
**正则表达式pattern的解释**

Let's break down the `_pattern` used in the `wordpunct_tokenize` function step by step:

The `_pattern` is a regular expression pattern represented as a raw string in Python. It is used to define the rules for tokenizing the input text into individual tokens (words and punctuation).
The pattern consists of two parts separated by the `|` (OR) operator:

1. `\w+`: This part matches one or more word characters (letters, digits, and underscores). (匹配字符、单词) Let's break it down further:
   - `\w`: This is a shorthand character class that matches any word character. It is equivalent to `[a-zA-Z0-9_]`. It matches letters (both lowercase and uppercase), digits, and underscores.
   - `+`: This is a quantifier that specifies that the preceding pattern (in this case, `\w`) should occur one or more times. It ensures that the regular expression matches whole words, not just individual characters.
    
2. `[^\w\s]+`: This part matches one or more non-word and non-whitespace characters. (匹配标点符号) Let's break it down further:
   - `[^\w\s]`: This is a character class that uses the `^` (caret) as a negation operator, meaning it matches any character that is not in the `\w` (word character) class or the `\s` (whitespace character) class.
   - `+`: As in the previous part, this quantifier specifies that the preceding pattern should occur one or more times. It ensures that the regular expression matches sequences of non-word and non-whitespace characters.

To summarize, the `_pattern` is designed to match two types of tokens in the input text:

1. Words: These consist of one or more word characters (letters, digits, and underscores).
2. Punctuation: These consist of one or more non-word and non-whitespace characters.

By using the `|` (OR) operator to combine these two patterns, the regular expression can tokenize the input text, separating words and punctuation symbols into individual tokens.

**关于`^`的用法**

In regular expressions, the caret symbol (`^`) has two different behaviors depending on its position within the pattern:

1. Outside character class: When the caret is placed outside a character class (square brackets `[...]`), it functions as an anchor that matches the start of a line or the start of the input string.

2. Inside character class: When the caret is placed inside a character class, it functions as a negation operator, indicating that the character class should match any character that is not listed within the brackets.

Let's explore both cases in more detail:

1. Outside Character Class (Line Start Anchor):
When the caret (`^`) is used outside a character class, it serves as an anchor that asserts the position at the start of a line or the start of the input string. It does not consume any characters; instead, it asserts that the next match should start at the beginning of a line or the beginning of the input string.

For example, consider the regular expression `^Hello`:

- In the input string "Hello, World!", the regular expression matches the substring "Hello" because it appears at the start of the string.
- In the input string "Hi, Hello, World!", the regular expression does not match anything because "Hello" does not appear at the start of the string.

Example:

```python
import re

# Input text
text = "Hello, World!\nHi, Hello, World!"

# Regular expression with ^ outside character class
pattern = r"^Hello"
regexp = re.compile(pattern)

# Find all matches
matches = regexp.findall(text)
print(matches)
```

Output:
```
['Hello']
```

Explanation:
In this example, the regular expression `^Hello` matches "Hello" only when it appears at the start of a line (the first occurrence). It does not match "Hello" in "Hi, Hello, World!" because the second occurrence of "Hello" is not at the start of a line.

2. Inside Character Class (Negation):
When the caret (`^`) is used inside a character class, it negates the class, making it match any character that is not listed within the brackets.

For example, consider the regular expression `[^aeiou]`:

- In the input string "Hello", the regular expression matches the letters "H" and "l" because they are not vowels (not "a," "e," "i," "o," or "u").
- In the input string "apple", the regular expression matches the letters "p" and "l" because they are not vowels.

Example:

```python
import re

# Input text
text = "Hello, apple!"

# Regular expression with ^ inside character class
pattern = r"[^aeiou]"
regexp = re.compile(pattern, flags=re.IGNORECASE)

# Find all matches
matches = regexp.findall(text)
print(matches)
```

Output:
```
['H', 'l', 'l', ',', ' ', 'p', 'p', 'l', '!']
```

Explanation:
In this example, the regular expression `[^aeiou]` matches all the non-vowel characters in the input text, regardless of their case (due to the `re.IGNORECASE` flag). It matches "H," "l," ",", " ", "p," "p," "l," and "!".

Remember that the behavior of the caret symbol (`^`) changes depending on its position within the regular expression pattern. Outside a character class, it anchors the match to the start of a line or input string, and inside a character class, it negates the class, matching characters that are not listed in the brackets.

**关于re.compile中flags的解释**

The `re.compile` function in Python is used to compile a regular expression pattern into a regex object that can be used for pattern matching. It takes an optional second argument called `flags`, which allows you to specify various options that modify the behavior of the regular expression. The `flags` parameter is a combination of one or more constants provided by the `re` module, and you can combine them using the bitwise OR operator (`|`).

Here are the commonly used `flags` in regular expressions:

1. `re.IGNORECASE` or `re.I`: This flag makes the pattern case-insensitive, meaning it will match both uppercase and lowercase letters interchangeably.

2. `re.MULTILINE` or `re.M`: This flag enables multiline matching. It changes the behavior of the `^` and `$` anchors. With this flag, `^` matches the start of each line, and `$` matches the end of each line.

3. `re.DOTALL` or `re.S`: This flag allows the dot (`.`) metacharacter to match any character, including newline characters (`\n`). By default, the dot only matches any character except a newline.

4. `re.UNICODE` or `re.U`: This flag enables Unicode-aware matching. It is useful when dealing with patterns that involve Unicode characters(可以使用其他字符编码，如其他语言).

5. `re.VERBOSE` or `re.X`: This flag enables verbose mode, allowing you to write the regular expression in a more readable and organized manner. It ignores whitespace and comments within the pattern and allows you to split the pattern into multiple lines.

**关于flags `re.MULTILINE`, `re.DOTALL`, and `re.UNICODE`的例子**

Let's provide examples for each of the flags `re.MULTILINE`, `re.DOTALL`, and `re.UNICODE`:

Example 1: `re.MULTILINE`
The `re.MULTILINE` flag affects how the `^` and `$` anchors behave, allowing them to match the start and end of individual lines in a multi-line input.

```python
import re

# Example text with multiple lines
text = """Line 1: This is the first line.
Line 2: This is the second line.
Line 3: This is the third line."""

# Matching lines that start with "Line"
pattern = r"^Line.*$"
regexp = re.compile(pattern, flags=re.MULTILINE)

# Find all matches
matches = regexp.findall(text)
print(matches)
```

Output:
```
['Line 1: This is the first line.', 'Line 2: This is the second line.', 'Line 3: This is the third line.']
```

Explanation:
With the `re.MULTILINE` flag, the `^` matches the start of each line, and the `$` matches the end of each line. As a result, the regular expression `^Line.*$` matches any line that starts with "Line" and captures the entire line.

Example 2: `re.DOTALL`
The `re.DOTALL` flag allows the dot (`.`) metacharacter to match any character, including newline characters (`\n`).

```python
import re

# Example text with multiple lines
text = """This is the first line.
This is the second line.
This is the third line."""

# Matching everything between "This" and "line."
pattern = r"This.*line."
regexp = re.compile(pattern, flags=re.DOTALL)

# Find all matches
matches = regexp.findall(text)
print(matches)
```

Output:
```
['This is the first line.\nThis is the second line.\nThis is the third line.']
```

Explanation:
With the `re.DOTALL` flag, the dot (`.`) can match across newline characters. The regular expression `This.*line.` captures the entire text between the first occurrence of "This" and the last occurrence of "line," including newline characters.

Example 3: `re.UNICODE`
The `re.UNICODE` flag enables Unicode-aware matching, ensuring that the regular expression pattern works correctly with Unicode characters.

```python
import re

# Example text with Unicode characters
text = "Hello, 你好, नमस्ते, مرحبا"

# Matching words using Unicode character class
pattern = r"\w+"
regexp = re.compile(pattern, flags=re.UNICODE)

# Find all matches
matches = regexp.findall(text)
print(matches)
```

Output:
```
['Hello', '你好', 'नमस्ते', 'مرحبا']
```

Explanation:
With the `re.UNICODE` flag, the `\w` character class matches Unicode word characters, including non-ASCII characters like Chinese, Hindi, and Arabic characters. As a result, the regular expression `\w+` captures each word in the input text, including words with non-ASCII characters.

These examples demonstrate how each flag can modify the behavior of regular expression matching to suit specific requirements, such as matching across lines, matching any character (including newlines), and handling Unicode characters correctly.

### 词典训练过程
```python
class BPETokenizer():
    special = ['<UNK>', '<PAD>', '<END>', '<MASK>']

    def __init__(self, vocab_size=1000, lowercase=True, basic_tokenizer=wordpunct_tokenize):
        self.lowercase = lowercase
        self.vocab_size = vocab_size
        self.basic_tokenizer = basic_tokenizer

    def fit(self, corpus: list, max_steps=10000, out_fn='vocab.txt'):
        '''
        分词器训练，返回训练得到的vocabulary
        '''

        ############### 统计初始词典 ###############
        # 将所有字母变为小写
        if self.lowercase:
            corpus = [s.lower() for s in corpus]
        word_corpus = Counter([tuple(data) + ("</w>",) for data in toolz.concat(map(self.basic_tokenizer, corpus))])
        vocab = self._count_vocab(word_corpus)

        ############### 逐步合并初始词典中的高频二元组 ###############
        for i in range(max_steps):
            word_corpus, bi_cnt = self._fit_step(word_corpus)
            vocab = self._count_vocab(word_corpus)
            if len(vocab) >= self.vocab_size or bi_cnt < 0: break

        ############### 将一些特殊词加入最终的词典 ###############
        for s in self.special:
            if s not in vocab:
                vocab.insert(0, (s, 99999))

        ############### 导出词典 ###############
        with open(out_fn, 'w') as f:
            f.write('\n'.join([w for w, _ in vocab]))
        self.vocab = [token for token, _ in vocab]
        return vocab

    def _count_vocab(self, word_corpus):
        _r = Counter([data for data in toolz.concat([word * cnt for word, cnt in word_corpus.items()])])
        _r = sorted(_r.items(), key=lambda x: -x[1])
        return _r

    def _fit_step(self, word_corpus):
        ngram = 2
        bigram_counter = Counter()

        ############### 以步长1，窗口尺寸2，在每个单词上滚动，统计二元组频次 ###############
        for token, count in word_corpus.items():
            if len(token) < 2: continue
            for bigram in toolz.sliding_window(ngram, token):
                bigram_counter[bigram] += count

        ############### 选出频次最大的二元组 ###############
        if len(bigram_counter) > 0:
            max_bigram = max(bigram_counter, key=bigram_counter.get)
        else:
            return word_corpus, -1
        bi_cnt = bigram_counter.get(max_bigram)

        ############### 从corpus中将最大二元组出现的地方替换成一个token ###############
        for token in word_corpus:
            _new_token = tuple(' '.join(token).replace(' '.join(max_bigram), ''.join(max_bigram)).split(' '))
            if _new_token != token:
                word_corpus[_new_token] = word_corpus[token]
                word_corpus.pop(token)
        return word_corpus, bi_cnt
```

## toolz.concat
```python
import toolz
corpus = ["Hello, world!", "How are you?"]

def basic_tokenizer(text):
    return wordpunct_tokenize(text)

# Using map to tokenize the corpus
tokenized_iter = map(basic_tokenizer, corpus)
print(tokenized_iter)
```
```
<map object at 0x00000175B363FB80>
```
```python
# Converting the iterator to a list to see the tokenized sequences
tokenized_corpus = list(map(basic_tokenizer, corpus))
print(tokenized_corpus)
```
```
[['Hello', ',', 'world', '!'], ['How', 'are', 'you', '?']]
```
```python
for data in toolz.concat(map(basic_tokenizer, corpus)):
    print(data)
```
```
Hello
,
world
!
How
are
you
?
```
```python
l = [tuple(data) + ("</w>",) for data in toolz.concat(map(basic_tokenizer, corpus))]
print(l)
print(Counter(l))
```
```
[('H', 'e', 'l', 'l', 'o', '</w>'), (',', '</w>'), ('w', 'o', 'r', 'l', 'd', '</w>'), ('!', '</w>'), ('H', 'o', 'w', '</w>'), ('a', 'r', 'e', '</w>'), ('y', 'o', 'u', '</w>'), ('?', '</w>')]
Counter({('H', 'e', 'l', 'l', 'o', '</w>'): 1, (',', '</w>'): 1, ('w', 'o', 'r', 'l', 'd', '</w>'): 1, ('!', '</w>'): 1, ('H', 'o', 'w', '</w>'): 1, ('a', 'r', 'e', '</w>'): 1, ('y', 'o', 'u', '</w>'): 1, ('?', '</w>'): 1})
```
