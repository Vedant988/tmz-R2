# Part C: Indic Token Behavior Observations

## 1. Density & Footprint (`tokenization_comparison.csv`)

Looking at the Characters-per-Token ratios, the contrast between English and Tamil is massive.

*   **English (High Density):** The `en_chars_per_tok` averages around 4.0 to 5.0. This shows the tokenizer is highly efficient, packaging almost entire English words into single tokens.
*   **Tamil (Low Density / High Fragmentation):** The `ta_chars_per_tok` drops drastically, often sitting between 1.5 and 2.5 characters per token. 

**My Takeaway (The Token Explosion):** 
To represent the exact same semantic meaning, Tamil requires significantly more tokens than English. Because Transformer self-attention scales quadratically ($O(N^2)$), translating into Tamil isn't just generating longer sequences—it's consuming disproportionately more VRAM and compute power per sentence than English.

---

## 2. Subword Splitting (`tamil_token_patterns.csv`)

This file gave a really clear look at how the model actually chops up the translated sentences. Here's what the subword breakdowns reveal:

**The Agglutinative Effect**
Tamil is highly agglutinative. A single word like "காய்கறிகள்" (vegetables) or "செல்கிறோம்" (we go) glues together the root word, plural markers, tense markers, and pronouns. 
Since the tokenizer can't store every possible glued combination in its fixed vocabulary, you can see it forcefully slicing these complex words into smaller, recognizable morphemes (like ripping the tense suffix off the root verb).

**SentencePiece vs. BPE**
I noticed an underscore (`_` or the special Unicode block equivalent) at the start of many tokens. This is classic SentencePiece behavior. 
Unlike standard Byte-Pair Encoding (BPE)—which assumes that spaces are the only real boundaries between words—SentencePiece treats the space itself as just another character. This is absolutely crucial for Indic languages where word boundaries are less strict and complex diacritics often span across multiple characters.

**Unified Devanagari Script Mapping (Cross-Lingual Transfer)**
A shocking observation from the token breakdown is that the Tamil text is entirely tokenized into **Devanagari script** (e.g., `थेके`, `प्रोडक्ट्स`). The tokenizer doesn't split native Tamil characters like "க"; instead, it transliterates the Tamil text into a unified Indic script (Devanagari) under the hood. This is a brilliant architectural decision by AI4Bharat to achieve cross-lingual transfer: by mapping all 22 Indic languages to a single phonetic script space, the model shares vocabulary across languages, massively reducing OOV (Out of Vocabulary) errors and allowing low-resource languages to piggyback on high-resource language data.

---

## Final Thoughts for Part C

The data here essentially proves it mathematically: if you apply English-centric tokenization strategies to morphologically rich Indic languages, you get massive sequence expansion. 

While the `indictrans2` model does a great job mitigating this by using a custom, trained SentencePiece vocabulary, you can't entirely escape linguistics. The compute penalty for handling complex Tamil agglutination remains a serious factor to consider for real-world deployment.
