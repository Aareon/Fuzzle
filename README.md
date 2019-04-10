# Fuzzle
Python search engine with support for tags.

## Key Features
 - Searching through strings.
 - Ignoring low coverage results.
 - Sorting results by accuracy.
 - Support for dictionaries.

## Using
The search engine can be used by forking a version of the repository and dragging the `searcher_new.py` script into your project folder. Through an import you will have access to it's only (current!) member, a function, `find()` which takes three arguments:
 - **options:** Here you feed the function a list of either dictionaries or strings. If you are feeding it dictionaries, they will have to have the attributes `key` and `tags`. Tags can be present, but it isn't required. `tags` is a list of strings which is the fallback if the searcher does not find the searched value in the `key`.
 - **search:** Represents the term you're looking for which the searcher will use and split up into single parts to find how coverage and accuracy results to return in order from best to worst.
 - **coverage_multiplier (optional):** This value is defaulted to 0.02975, increasing it will result in more results and lowering it will require the search to be found 100% in the options you want returned.
 
The `find()` function returns a list of results in the form of dictionaries. If you fed it a list of strings into it you will find those strings in the `key` field of each result and if you feed dictionaries the result will contain the original fields as well as the new fields `accuracy`, `category` and `match`.

`match` is a boolean which, when `True`, will be the only result the searcher returns and the `accuracy` goes from 0 to 1 with 1 being the highest accuracy and 0 the lowest. `category` is a field that the searcher uses to sort the results with each category meaning the following. Always the lowest category available for the result will be stored in the field `category` which stand all have their own meanings:

**Category 0:** Full-match.

**Category 1:** The *full* search was found in the result.

**Category 2:** The result starts with the search.

**Category 3:** The search is in the tags.

**Category 4:** The result starts with a word in the search.

**Category 5:** (Parts of) the key were found in the search.

**Category 6:** The key starts with the search.

**Category 7:** A word in the search was found in a tag.

## Example
(demo.py)[demo.py] can be downloaded along with the folder 

## Links


## Roadmap
 - [x] Removing irrelevant results.
 - [x] Supporting tags.
 - [ ] Add support for custom objects.
 - [ ] Return the match at first position and the rest as well.
 - [ ] Models for different search types.
