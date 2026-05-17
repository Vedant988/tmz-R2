# Part B: Token Analysis Observations

## What I noticed from the Token Distribution EDA

While looking at how the token-to-word ratios compare between the English source text and the IndicTrans2 Tamil predictions, I noticed some pretty huge differences in how the tokenizer handles both languages.

**1. English tokens stay mostly intact**
* For English, the tokens-per-word ratio is pretty tightly packed around 1.1 to 1.5. 
* This basically means the tokenizer's vocabulary already knows most of the English words as whole words, so it rarely has to chop them up into smaller sub-words.

**2. Tamil gets chopped up a lot (High Agglutination)**
* When you look at the density plot for Tamil, it's way more spread out. The token-to-word ratio is super high—usually hitting somewhere between 6 to 12 tokens for just a single word!
* **Why does this happen?** Tamil is a highly agglutinative language. That means a single Tamil word often mashes the root word, tense, prepositions, and pronouns all into one long string. 
* Since you can't realistically fit every single possible word combination into a fixed tokenizer vocabulary, the `indictrans2` tokenizer is forced to rely heavily on sub-word splitting to break these massive words down into basic chunks it can actually recognize.

**3. Sequence Expansion in Translation**
* One interesting takeaway: when translating from English to Tamil, the total token count goes way up. This happens even if the actual *word count* drops (since Tamil naturally drops a lot of explicit pronouns and standalone prepositions).
* **The takeaway:** Because of this high expansion rate, inferencing in Tamil is going to eat up a much larger context window and take up noticeably more VRAM per sentence compared to just dealing with English.