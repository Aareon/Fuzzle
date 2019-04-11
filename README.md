# Fuzzle
Python search engine with support for tags.

![Python Version](https://img.shields.io/badge/python-3.5+-blue.svg)
![Version](https://img.shields.io/badge/version-0.7-orange.svg)
[![Discord](https://img.shields.io/badge/See_it_in_action-purple.svg?logo=discord&logoColor=white&labelColor=697ec4&color=7289da)](https://discord.gg/njYdKYH)

## Key Features
 - Searching through strings.
 - Ignoring low coverage results.
 - Sorting results by accuracy.
 - Support for dictionaries.

## Using
The search engine can be used by forking a version of the repository and dragging the [Searcher Script](searcher_new.py) script into your project folder. Through an import you will have access to it's only (current!) member, a function, `find()` which takes three arguments:
 - **options:** Here you feed the function a list of either dictionaries or strings. If you are feeding it dictionaries, they will have to have the attributes `key` and `tags`. Tags can be present, but it isn't required. `tags` is a list of strings which is the fallback if the searcher does not find the searched value in the `key`.
 - **search:** Represents the term you're looking for which the searcher will use and split up into single parts to find how coverage and accuracy results to return in order from best to worst.
 - **return_all (optional):** Boolean whether only a full-match should be returned or all similar results. Does not consider a tag-match as a full-match (yet) and is defaulted to `True`.
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
The [Demo File](demo.py) can be downloaded along with the [data](data) folder which contains a few datasets that can be used to test the searcher. Simply running the [Demo File](demo.py) allows you to pick a category and search through the data with the current dataset containing the following:

 - **Movies:** A list of 28 795 movies from 1990 to the present day with the source being a JSON file found [here](https://raw.githubusercontent.com/prust/wikipedia-movie-data/master/movies.json) which was restructured to turn the movies' names into keys and their cast into the tags. This allows you to search for a movie not only by name, but also by actor.
 - **Games:** A list of 3409 games scraped from the [SteamAPI]() which uses the game's name as the key as well as the genre(s), platform(s) and category(-ies) as tags.
   - **Categories:** The SteamAPI has (so far) returned 27 unique categories including `captions available`, `multi-player`, `online multi-player`, `includes source SDK`, `includes level editor`, `in-app purchases`, `shared/split screen`, `full controller support`, `MMO`, `online co-op`, `cross-platform multiplayer`, `partial controller support`, `steam achievements`, `local co-op`, `steam leaderboards`, `stats`, `commentary available`, `steam turn notifications`, `steam workshop`, `steam cloud`, `single-player`, `steam trading cards`, `co-op`, `local multi-player`.
   - **Genres:** The data contained 30 unique genres so far including `action`, `utilities`, `gore`, `strategy`, `animation & modeling`, `photo editing`, `education`, `sports`,`simulation`, `web publishing`, `documentary`, `sexual content`, `software training`, `tutorial`, `indie`, `rpg`, `massively multiplayer`, `design & illustration`, `game development`, `video production`, `nudity`, `audio production`, `casual`, `free to play`, `racing`, `adventure`, `violent`, `early access` and `accounting`.
 - **Companies:** A list of 5002 companies with their respective industry, state and city as tags which allows searches such as "california" or "food" to yield brands that do not contain the searched keyword in their name but instead are based in a specific state, city or are active in a certain industry.
 - **Countries:** A list of countries (presumably with duplicate values) with most of their major cities added as tags to allow finding a country by searching for a city.

A simple example which can be copy-pasted to understand how the engine is used:

```python
### Author: Dan6erbond ###
###  Date: 10.04.2019  ###
###    Version: 1.1    ###
import searcher_new as searcher

### defining a list of options with tags ###
options = [
    {
        "key": "GitHub",
        "tags": ["Nat Friedman", "Tom Preston-Werner", "Chris Wanstrath", "Scott Chacon", "P. J. Hyett"]
        },
    {
        "key": "Google",
        "tags": ["Alphabet Inc.", "YouTube", "Sundar Pichai", "Larry Page", "Sergey Bin"]
        },
    {
        "key": "Reddit",
        "tags": ["Steve Huffman", "Alexis Ohanian", "Aaron Swartz"]
        }
    ]

search = "Steve" # the searched string

results = searcher.find(options, search)

if len(results) == 0:
    print("Couldn't find any results!")
elif results[0]["match"]:
    print("Found {}!".format(results[0]["key"]))
else:
    for result in results:
        print(result["key"]) # will result in a single print being "Reddit"
```

## Performance
Comparing the performance to a fairly common search engine [Whoosh]() as well as the [old search engine]() yields that Fuzzle has a lot of space up for getting faster but seems to be significantly easier to setup given you use dictionaries for your dataset. Whoosh is a faster but does not find results for every search because it seems to be limited to using a single field at once to search through:

```2080 games
28795 movies
5002 companies
240 countries

Searching for Quantum of Solace in movies.
Whoosh: 1 results | 0:00:00.022969 - Top Result: Quantum of Solace
Searcher: 3103 results | 0:00:00.276369 - Top Result: Quantum of Solace
New Searcher: 1 results | 0:00:00.802663 - Top Result: Quantum of Solace

Searching for Spider in movies.
Whoosh: 18 results | 0:00:00.009969 - Top Result: Spider-Man: Into the Spider-Verse
Searcher: 20 results | 0:00:00.106716 - Top Result: The Spider
New Searcher: 21 results | 0:00:00.238396 - Top Result: Spider-Man: Into the Spider-Verse

Searching for United States of America in countries.
Whoosh: 0 results | 0:00:00.027924 - Top Result: No results
Searcher: 7 results | 0:00:00.001994 - Top Result: United States Minor Outlying Islands
New Searcher: 240 results | 0:00:00.149739 - Top Result: United States

Searching for Suhr in countries.
Whoosh: 0 results | 0:00:00.027087 - Top Result: No results
Searcher: 0 results | 0:00:00.000994 - Top Result: No results
New Searcher: 1 results | 0:00:00.035910 - Top Result: Switzerland

Searching for Food in companies.
Whoosh: 3 results | 0:00:00.003987 - Top Result: Global Food Solutions
Searcher: 17 results | 0:00:00.017953 - Top Result: Global Food Solutions
New Searcher: 139 results | 0:00:00.031915 - Top Result: Foodmate US

Searching for California in companies.
Whoosh: 1 results | 0:00:00.004018 - Top Result: California Energy Solutions
Searcher: 2 results | 0:00:00.016953 - Top Result: California Energy Solutions
New Searcher: 671 results | 0:00:00.057874 - Top Result: California Energy Solutions

Searching for Deep Rock in games.
Whoosh: 1 results | 0:00:00.003021 - Top Result: Deep Rock Galactic
Searcher: 11 results | 0:00:00.010970 - Top Result: Deep Rock Galactic
New Searcher: 4 results | 0:00:00.034905 - Top Result: Deep Rock Galactic

Searching for Linux in games.
Whoosh: 0 results | 0:00:00.003024 - Top Result: No results
Searcher: 0 results | 0:00:00.009944 - Top Result: No results
New Searcher: 342 results | 0:00:00.019932 - Top Result: Escape from the police```

## Links
 - **[SteamAPI](https://store.steampowered.com/api):** The source of the list of games.
 - **[Awesome JSON Datasets](https://github.com/jdorfman/awesome-json-datasets):** Source of the list of movies as well as countries (without the cities).
 - **[Cities of the World](https://github.com/lutangar/cities.json):** List of cities with their corresponding Country Code which was migrated to [countries.json](/data/places/countries.json).
 - **[Inc 5000](https://sethwaite.com/download-inc-5000-2017-data-set/):** A ranking of the 5000 quickest growing privately-held companies in America.

## Roadmap
 - [x] Removing irrelevant results.
 - [x] Supporting tags.
 - [ ] Support for prioritizing different fields.
 - [ ] Add support for custom objects.
 - [ ] Return the match at first position and the rest as well.
 - [ ] Models for different search types.
 - [ ] Discord bot framework.
