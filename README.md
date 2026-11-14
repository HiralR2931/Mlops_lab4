# MLOps Lab 4: Word Count Pipeline with Apache Beam

This lab builds a word count pipeline using **Apache Beam (Python)** and the local **DirectRunner**. It extends the original Apache Beam word count example (which used only *King Lear*) by running it on a larger dataset, cleaning the text, and adding a top-20 analysis with a chart.

## Dataset

Four Shakespeare tragedies from Google's public `dataflow-samples` bucket:

| File | Play |
|---|---|
| `hamlet.txt` | Hamlet |
| `macbeth.txt` | Macbeth |
| `othello.txt` | Othello |
| `romeoandjuliet.txt` | Romeo and Juliet |

The notebook downloads the files automatically into `data/tragedies/` over HTTPS.

## Changes from the original lab

1. **New dataset:** reads four plays at once using the glob pattern `data/tragedies/*.txt`, instead of a single file.
2. **Lowercasing:** words like `KING`, `King` and `king` are counted as one word.
3. **Text cleaning:** strips leading and trailing apostrophes (e.g. `'tis` becomes `tis`).
4. **Stopword filtering:** removes common English and Early Modern English words (`the`, `and`, `thou`, `hath`, ...) and words shorter than 3 letters.
5. **CSV output:** results are written as `word,count` with a header row into a single file.
6. **Top 20 branch:** a second pipeline branch uses `beam.combiners.Top.Of` to find the 20 most frequent words.
7. **Visualization:** a bar chart of the top 20 words using pandas and matplotlib.

## Pipeline

```
Read lines → Find words (lowercase) → Clean words → Remove stopwords
→ Pair with 1 → Group and sum
    ├── Format CSV → Write all word counts
    └── Top 20 → Format → Write top 20
```

## Repository structure

```
Mlops_lab4/
├── data/                              # Input text files (the four plays)
├── outputs/                           # Pipeline results
│   ├── tragedies_word_counts-00000-of-00001.csv
│   └── tragedies_top20-00000-of-00001.csv
├── mlops_Apache_Beam_Python.ipynb     # Main notebook
└── README.md
```

## How to run

1. Open `mlops_Apache_Beam_Python.ipynb` in Google Colab or Jupyter.
2. Run the first cell to install Apache Beam:
   ```python
   !pip install --quiet apache-beam
   ```
   In Colab, click **Restart session** if prompted.
3. Run all cells in order (**Runtime → Run all**).

## Results

Top 10 words across the four plays:

| Rank | Word | Count |
|---|---|---|
| 1 | hamlet | 485 |
| 2 | lord | 473 |
| 3 | iago | 361 |
| 4 | othello | 339 |
| 5 | good | 337 |
| 6 | romeo | 324 |
| 7 | macbeth | 311 |
| 8 | love | 303 |
| 9 | cassio | 244 |
| 10 | lady | 242 |

**Observation:** character names rank very high, partly because the scripts label each speaker's lines with their name (e.g. `HAMLET:`). This means the counts include speaker labels as well as mentions in the dialogue.

## Tools

- Python 3
- Apache Beam (DirectRunner)
- Google Colab
- pandas, matplotlib
