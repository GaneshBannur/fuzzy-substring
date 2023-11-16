<font size=3>

# Fast Fuzzy Substring Search

This repo implements an algorithm I came up with for fast fuzzy substring search, i.e., searching for the best match substring of a query in a corpus. Given a query string and a corpus string, the algorithm tries to find the substring in the corpus which is the most similar to the query.  

The algorithm focuses on speed while providing the optimal result in most cases. It is not possible to guarantee that the result is optimal without some level of brute forcing, which slows down the search significantly. This is because at some point (after possibly narrowing down the search region in the corpus) such an algorithm would have to check every substring. Hence this algorithm does not provide that guarantee but it has been seen empirically that it provides the optimal result in most cases.

The algorithm uses Levenshtein distance as the measure of distance between the query and substrings of the corpus. It tries to find the substring of the corpus which has the least Levenshtein distance from the query. However, as mentioned previously, it is not guaranteed to find the optimal result.

The article explaining the algorithm in detail is at [https://ganeshbannur.github.io/2023/09/03/fuzzy-substring.html](https://ganeshbannur.github.io/2023/09/03/fuzzy-substring.html).  
The article is an extension of the Stackoverflow answer I gave [https://stackoverflow.com/a/77019582](https://stackoverflow.com/a/77019582).

Note: Substrings are called ngrams in the code.

## Installation

This code requires `python-Levenshtein`

    pip install levenshtein

## Example Usage
```python
from fuzzy_substring import get_best_match

query = "ipsum dolor"
corpus = "lorem 1psum dlr sit amet"

match = get_best_match(query, corpus)

print("Best match")
print(match['best_match'])
print(f"Minimum Levenshtein distance: {match['min_ld']}")
```

Output

    Best match
    1psum dlr
    Minimum Levenshtein distance: 3

There are three keyword arguments to `get_best_match`
- `case_sensitive` (bool, Default = `False`)

- `quick_scan_factor` (float > 0, Default = `2`): ***TLDR. Increase this if the algorithm is entirely missing the region in which the best match exists. Decrease to gain a speedup (although decreasing `step_factor` will likely yield a much greater speedup).***  
Increasing `quick_scan_factor` decreases the step size that the algorithm takes when scanning the corpus (using a sliding window) to find the region in which the best match may exist. Smaller steps give the algorithm better chances of finding the region in which the best match exists. Empirically, the default `quick_scan_factor = 2` works well, giving the algorithm the chance to find the region while also being sufficiently fast.

- `step_factor` (float > 0, Default = `128`): ***TLDR. Increase this if the algorithm is getting the general region of the best match correct but not the exact position. Decrease to gain a speedup.***  
Increasing `step_factor` increases the number of substring lengths considered during the thorough search of the narrowed down corpus, hence increasing the chances of finding the optimal substring with minimum Levenshtein distance. However increasing it will lead to increased runtime (because more substrings need to be checked) and memory usage (more substrings need to be stored - the implementation creates a list of substrings over which it iterates instead of directly iterating over the corpus. If the corpus is directly iterated over then memory would not be an issue). Empirically, the default `step_factor = 128` works well, compromising between speed and chances of an optimal solution. 

- `favour_smallest` (bool, Default = `False`): ***TLDR. The default value is seen to be better empirically. Set to `True` if you want the smallest match.***  
Among all the substrings that the algorithm ends up searching, more than one may have minimum Levenshtein distance. In this case we have to choose whether we prefer the smallest or largest of these. Empirically it is found to be better to prefer the largest match and so the default is  `favour_smallest = False`. Perhaps because it is easier for people to skip over extraneous characters in a larger match than fill in missing characters in a smaller match.

</font>
