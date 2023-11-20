# Emoji Search

Emoji Search is a Python-based CLI (Command Line Interface) application that allows users to semantically search emojis.

## Installation

You can install it from source:

```bash
pip install git+https://github.com/jacobmarks/emoji_search.git
```

## Usage

From the command line, use the `emoji-search` command, followed by a search term.

```bash
emoji-search beautiful sunset
```

This will return a list of emojis that most closely match the search term:

```plaintext
+-------+-------------------+---------+
| Emoji |       Name        | Unicode |
+-------+-------------------+---------+
|  🌞   |   sun with face   | U+1F31E |
|  🌇   |      sunset       | U+1F307 |
|  🌅   |      sunrise      | U+1F305 |
|  🔆   |   bright button   | U+1F506 |
|  🌆   | cityscape at dusk | U+1F306 |
+-------+-------------------+---------+
```

You can use quotation marks around your search term if you would like, but it is not necessary.

### Flags

You can specify the number of results you would like to see by using the `-n` or `--num_results` flag.

```bash

emoji-search -n 3 sleepy
```

This will return the top 3 results:

```plaintext
+-------+-------------------+---------+
| Emoji |       Name        | Unicode |
+-------+-------------------+---------+
|  🌞   |   sun with face   | U+1F31E |
|  🌇   |      sunset       | U+1F307 |
|  🌅   |      sunrise      | U+1F305 |
+-------+-------------------+---------+
```

You can also add `-c` or `--copy` to copy the top result to your clipboard.

```bash
emoji-search -c happy
```

```plaintext
Searching for: a happy family
+----------+---------------------------------+------------------------------------------------------+
|  Emoji   |              Name               |                       Unicode                        |
+----------+---------------------------------+------------------------------------------------------+
|    👪    |             family              |                       U+1F46A                        |
|    👨‍👩‍👧    |    family: man, woman, girl     |        U+1F468 U+200D U+1F469 U+200D U+1F467         |
|    👩‍👦‍👦    |     family: woman, boy, boy     |        U+1F469 U+200D U+1F466 U+200D U+1F466         |
|    👩‍👩‍👧‍👦    | family: woman, woman, girl, boy | U+1F469 U+200D U+1F469 U+200D U+1F467 U+200D U+1F466 |
|    👩‍👩‍👦‍👦    | family: woman, woman, boy, boy  | U+1F469 U+200D U+1F469 U+200D U+1F466 U+200D U+1F466 |
+----------+---------------------------------+------------------------------------------------------+
Copied 👪 to clipboard.
```

## How it Works

Emoji Search is a semantic search engine that uses a three-step process to find the most relevant emojis for a given search query.

### Step 1: Rapid Filtering with CLIP

The first step is a top-level sieve that uses the [CLIP](https://github.com/openai/CLIP)
model from OpenAI. CLIP is a neural network trained on a variety of image-text pairs, and
is able to semantically match images and text.

We use pre-computed CLIP embeddings of captions/descriptions of the emojis generated by GPT-4,
which are of the form "A photo of ...", and compare them to the embedding of the search query,
selecting candidates to move on to the next step. Distances are calculated using cosine similarity.

### Step 2: Cross-Encoder Ranking

The second step uses a cross-encoder model, DistilRoBERTa, to rank the candidates from the first step.
We compare the query against two inputs: the emoji name and the emoji description. Separate rankings
are generated for each of these types of inputs.

### Step 3: Reciprocal Rank Fusion

The third step combines the two rankings from the second step using reciprocal rank fusion. This
results in a final ranking of the emojis, which is returned to the user, potentially truncated
based on the number of results requested.

## Contributing

Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.
