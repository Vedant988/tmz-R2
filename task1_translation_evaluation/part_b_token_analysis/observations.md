# Part B: Token Analysis Observations

## What I noticed from the Token Distribution EDA

While looking at how the token-to-word ratios compare between the English source text and the IndicTrans2 Tamil predictions, I noticed some pretty huge differences in how the tokenizer handles both languages.

**1. English is Extremely Vocabulary-Efficient**
Notice how tightly the English tokens map to the actual word counts:
* *"This is a test sentence."* -> 5 words, 7 tokens.
* *"Data structures and algorithms..."* -> 9 words, 11 tokens.
This proves the tokenizer has a massive native English vocabulary. It barely has to slice any words into sub-words; it just recognizes them whole.

**2. Tamil Word Counts Actually Drop**
Look at rows 6, 7, and 8 from our dataset. The Tamil translations consistently use fewer raw words than the English source.
* *"He is reading a book in the library."* (8 English words) translates to just 4 Tamil words: *"நூலகத்தில் ஒரு புத்தகத்தைப் படிக்கிறார்."*
This perfectly illustrates Tamil as a "pro-drop" language that bakes prepositions (like "in the") and pronouns (like "He") straight into the surrounding words.

**3. The Token Explosion (The Agglutination Effect)**
This is the most striking part of the data. Even though Tamil uses fewer words, the token count completely skyrockets because those densely packed words have to be severely fragmented.
* Look at Row 6: *"Please let me know if you need any help."* is 11 English tokens. The Tamil version is only 6 words, but it fragments into **67 tokens**! (That's over 11 tokens per word).
* Look at Row 10: The Tamil translation for *"Data structures and algorithms..."* uses just 7 words but explodes into **76 tokens**!

**The Verdict**
The data completely proves that when you deploy a model like `indictrans2` for Tamil, it is going to chew through your context window limit and VRAM much faster than it would for English. This is purely because of how aggressively the tokenizer has to slice up those complex, highly agglutinative Tamil words into recognizable sub-word chunks.