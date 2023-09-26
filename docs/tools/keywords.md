The algorithm for extracting keywords follows a structured pipeline with multiple phases.

## Default Keywords Stems Tree

The algorithm commences with a predefined list of keywords that serve as fundamental features for the extraction process. This approach aims to create an efficient keyword extractor without relying on natural language processors.

The list of default keywords can be found in `keywords.py`. It also encompasses combined keywords, which present a notable challenge. Certain words may change their meaning based on the context in which they are used.

During this phase, starting from the default keyword list, the algorithm constructs a dictionary. In this dictionary, the keys represent stems derived from the first tokens of the keywords, and the corresponding values are lists of keywords sharing the same stem. This grouping facilitates the efficient matching of similar keywords. This dictionary is referred to as `keywords_stems_tree`.

## Keyword Extraction

In this phase, the algorithm initially tokenizes the text into a list of words. It subsequently removes all stopwords based on the [Snowball stemmer](https://snowballstem.org/). The algorithm then iterates through each token, obtaining its stem. If the stem is found in the `keywords_stems_tree`, the algorithm enters a nested loop to check if the token sequence matches any of the keywords associated with that stem. If a match is found, it increments the count for that specific keyword.

## Keyword Ranking

The keyword-count pairs are sorted in descending order, primarily based on the count. The algorithm ultimately returns the top keywords while ensuring that keywords with the same count as the fifth-ranked keyword are included.

This structured approach allows the algorithm to efficiently identify and prioritize keywords within a given text.
