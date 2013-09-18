jaysoned
========

Quick 'n Dirty JSON Concatenation/Compression utility

I needed a way to take a bunch of JSON files needed by an iOS app, put them together (and preferably compress them) to 
coalesce disk seeks into a single efficient one. I added Build Phase that calls this script like this :

    /usr/local/bin/node /usr/local/share/npm/bin/jaysoned ${BUILT_PRODUCTS_DIR}/${UNLOCALIZED_RESOURCES_FOLDER_PATH}/ ${BUILT_PRODUCTS_DIR}/${UNLOCALIZED_RESOURCES_FOLDER_PATH}/json.snz
    rm ${BUILT_PRODUCTS_DIR}/${UNLOCALIZED_RESOURCES_FOLDER_PATH}/*.json

1. Which basically looks for all `.json` files in the unlocalized resource location of the app's product directory, hands them
to `jaysoned` and removes them when done. 
2. `jaysoned` parses them and makes one, larger JSON file with keys being the original
files' names, and values being the files' contents as javascript objects. 
3. Then, it takes the resulting large js object, stringifies it, compresses it using [Snappy][1], Google's fast compression 
library and writes the result to `json.snz`, jaysoned's second argument.

The app, as soon as one of these JSON objects is needed, reads the file, uncompresses it using [Snappy-ObjC][2] and caches 
the resulting JSON object for later use.

[1]: https://code.google.com/p/snappy/
[2]: https://github.com/matehat/Snappy-ObjC
