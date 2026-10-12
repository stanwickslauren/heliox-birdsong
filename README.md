# Heliox birdsong: code and data for the figures

Code and derived data for

> Stanwicks LL, Ostrowski LM, Todesco M, Siddiqui A, McPherson T, Gorman JC, Gentner TQ.
> *Real-Time Sensorimotor Error Processing Shapes Ongoing Song Production in Two Species of Songbirds.*
> [journal, year, DOI]

Each notebook in `notebooks/` draws the panels of one or more figures from a single file in `data/`, and fits the models behind any statistics shown on those panels. The notebooks in `data_pipeline/` show how the files in `data/` were made from the recordings.

## Contents

```
notebooks/        one notebook per figure (or group of panels); reads data/, writes figures/
data/             the derived data the notebooks read (download below)
data_pipeline/    how data/ was made; needs the raw recordings and does not run outside the lab
figures/          created when the notebooks run
environment.yml   the Python environment the figures were made with
requirements.txt  the same versions, for pip
```

## Which notebook makes which figure

| Figure | Notebook | Data file |
|---|---|---|
| Fig. 1B | `umap_examples_figures.ipynb` | `umap_examples_data.pkl` (and `umap_example_spectrograms.pkl` for the optional "run a new UMAP" example) |
| Fig. 1 C-D, Fig. 2 B and D | `spectrogram_examples_figures.ipynb` | `spectrogram_examples_data.pkl` |
| Fig. 2 A, C, G-J; Supp. Fig. S2 | `syllable_features_figures.ipynb` | `syllable_features_data.pkl` |
| Fig. 3 | `transition_structure_figures.ipynb` | `transition_structure_data.pkl` |
| Fig. 4 C-G | `song_structure_figures.ipynb` | `song_structure_data.pkl` |
| Fig. 4 H-I; Supp. Fig. S4 | `kgram_figures.ipynb` | `kgram_data.pkl` |
| Fig. 5 A-E; Supp. Fig. S6 | `within_song_figures.ipynb` | `within_song_data.pkl` |
| Fig. 5 F-H | `vae_latent_figures.ipynb` | `vae_latent_data.pkl` |
| Fig. 5 I-M; Supp. Table S1 | `exposure_course_figures.ipynb` | `exposure_course_data.pkl` |
| Fig. 6 C-I; Supp. Fig. S7 | `s4_nll_figures.ipynb` | `s4_figure_data.pkl` |
| Supp. Fig. S1 | `clustering_validation_figures.ipynb` | `clustering_validation_data.pkl` |
| Supp. Fig. S3 | `mutual_information_figures.ipynb` | `mutual_information_data.pkl` |
| Supp. Fig. S5 | `repetition_figures.ipynb` | `repetition_data.pkl` |

## Running the notebooks

1. Clone the repository and create the environment:

   ```bash
   git clone https://github.com/[account]/[repository].git
   cd [repository]
   conda env create -f environment.yml
   conda activate heliox-birdsong
   ```

   Without conda, use a Python 3.8 environment and `pip install -r requirements.txt` (this builds `hdbscan` from source, so it needs a C compiler).

2. The data are already in `data/`, so there is nothing to download. Eventually: download the data from [Zenodo DOI] and put the `.pkl` files in `data/`.

3. Open any notebook with `jupyter notebook` and run it top to bottom, or run them all from the command line:

   ```bash
   cd notebooks
   for nb in *.ipynb; do
       jupyter nbconvert --to notebook --execute --inplace --ExecutePreprocessor.timeout=-1 "$nb"
   done
   ```

Figures are saved to `figures/` as PNG and PDF. Some notebooks also save a table of every statistic on their panels there (for example `s4_nll_statistics.csv`).

The notebooks find `data/` and `figures/` on their own when run from the repository folder or from `notebooks/`. To read or write somewhere else, set `HELIOX_DATA_DIR` and `HELIOX_FIG_DIR` before starting Jupyter.

Each notebook takes seconds to a few minutes. The "run a new UMAP" example at the end of `umap_examples_figures.ipynb` takes longer.

## The data files

Each `.pkl` file is a Python dictionary of pandas tables, saved with pandas 1.5.3. Each also has a `meta` entry recording when it was made, the settings used and the package versions. Load a file with `pd.read_pickle`. Bird names start with `s_` (starling) or `z_` (zebra finch), and `condition` is `regair` (regular air) or `heliox`.

| File | Size | Contents |
|---|---|---|
| `umap_examples_data.pkl` | 0.7 MB | UMAP coordinates and syllable groups of one starling and one zebra finch |
| `umap_example_spectrograms.pkl` | 20 MB | The syllable spectrograms of those two birds, for running a new UMAP |
| `spectrogram_examples_data.pkl` | 30 MB | The spectrogram images of the example songs and syllables |
| `syllable_features_data.pkl` | 23 MB | BioSound features, duration, ISI and sound level of every syllable |
| `transition_structure_data.pkl` | 5.4 MB | Every syllable transition of every bird, and the two example birds |
| `song_structure_data.pkl` | 7.6 MB | Syllable timing by song position, and motifs per zebra finch song |
| `kgram_data.pkl` | 14 MB | Recurring starling syllable sequences (k-grams), their ISIs and split-half stability |
| `within_song_data.pkl` | 2.5 MB | Starling syllable positions within songs, and per-song tempo measures |
| `vae_latent_data.pkl` | 14 kB | Saved results of the VAE analysis |
| `exposure_course_data.pkl` | 27 MB | Syllable and song measures in recording order across days of exposure |
| `s4_figure_data.pkl` | 27 MB | S4 model negative log-likelihood per song, syllable and gap, with covariates |
| `clustering_validation_data.pkl` | 115 kB | HDBSCAN threshold sweep and subsampling for each starling |
| `mutual_information_data.pkl` | 0.8 MB | Bootstrap mutual information of syllable transitions per bird |
| `repetition_data.pkl` | 3.3 MB | Starling syllable transitions and repeat runs |

## How the data were made

`data_pipeline/data_figs.ipynb` builds every file above except `s4_figure_data.pkl`, which `data_pipeline/s4_nll_build_data.ipynb` builds. `s4_nll_build_data.ipynb` also reads three sets of files written by `data_figs.ipynb`, so `data_figs.ipynb` runs first. Both start from files on the Gentner lab cluster: the recordings, the per-bird syllable tables and the saved outputs of earlier steps. They will not run elsewhere. They are included so that every step from those inputs to the figure data can be read, and the output saved under each cell is from the run that made the files in `data/`.

These earlier steps are described in the Methods and are not part of this repository:

* **Segmentation and UMAP**: syllables were segmented and embedded with the AVGN pipeline (Sainburg et al. 2019, https://github.com/timsainb/AVGN) and clustered with HDBSCAN.
* **BioSound features**: the 18 predefined acoustic features of every syllable, from the BioSound class of the Theunissen lab's soundsig package (Elie and Theunissen 2018, https://github.com/theunissenlab/soundsig). `data_figs.ipynb` starts from the saved feature tables.
* **S4 models**: one autoregressive SaShiMi model per bird (Goel et al. 2022, "It's Raw! Audio Generation with State-Space Models", https://github.com/state-spaces/s4/tree/main/models/sashimi), trained on that bird's regular air song and used to score every song in both conditions (Methods 4.4.2). `s4_nll_build_data.ipynb` starts from each model's saved per-sample negative log-likelihood traces and from the two export pickles of the S4 analysis notebooks.
* **VAE**: one model per bird (Methods 4.4.9). `vae_latent_data.pkl` collects its saved results unchanged.
* **Mutual information**: mutual information of syllable transitions, estimated with code adapted from Sainburg et al. (2019, "Parallels in the sequential organization of birdsong and human speech", Nature Communications, https://github.com/timsainb/ParallelsBirdsongLanguagePaper). The bootstrap was run beforehand; `data_figs.ipynb` collects its saved results.

The raw recordings are [available from ... / on request from the corresponding authors].

## Citation

If you use this code or data, please cite the paper above.

## License

The code is released under the MIT License (see `LICENSE`). [Data license, e.g. CC BY 4.0.]

## Contact

Lauren Stanwicks (lstanwic@ucsd.edu) and Timothy Gentner (tgentner@ucsd.edu)
