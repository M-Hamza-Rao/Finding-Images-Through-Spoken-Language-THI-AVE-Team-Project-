# Audio--Image Retrieval with Contrastive Learning

## Project overview

Build a system that retrieves images from spoken descriptions. Using the
Flickr8k Audio Caption Corpus and its corresponding Flickr8k images,
train an audio encoder and an image encoder to map speech and images
into a shared embedding space. Matching image--audio pairs should have
higher similarity than mismatched pairs.

## Goals

-   Prepare aligned image and spoken-caption data.
-   Establish a simple baseline.
-   Train and evaluate a contrastive audio--image model.
-   Measure retrieval quality and generalization.
-   Deliver a small demo that searches images using speech.

## Dataset

-   **Audio:** [Flickr8k Audio Caption
    Corpus](https://sls.csail.mit.edu/downloads/flickraudio/index.cgi)
-   **Images:** Obtain the corresponding Flickr8k image dataset from the
    official dataset instructions.
-   Each audio caption must be linked to its source image using the
    dataset's filenames or metadata.
-   Check licensing, access requirements, data integrity, and actual
    audio duration before training.
-   Keep the dataset outside Git; commit only scripts and documentation,
    not the raw media.

## Work plan

### Phase 1 --- Setup and data audit

-   Create the repository structure and Python environment.
-   Download the audio and matching images.
-   Inspect metadata, file formats, missing files, and image--audio
    links.
-   Calculate the total usable audio duration.
-   Create a reproducible manifest of image paths, audio paths, and pair
    IDs.

**Output:** validated dataset manifest and data-loading script.

### Phase 2 --- Data preparation and splits

-   Standardize audio loading and preprocessing (sample rate, mono
    conversion if needed, duration handling).
-   Apply the image preprocessing required by the selected pretrained
    image encoder.
-   Split by **image ID** into train, validation, and test sets so
    captions for one image cannot leak across splits.
-   Verify that each retained audio recording maps to the correct image.

**Output:** reproducible train/validation/test splits.

### Phase 3 --- Baseline

-   Build a simple speech-to-text plus text--image retrieval baseline,
    if time permits.
-   Record baseline results using the same held-out test set as the main
    model.

**Output:** baseline retrieval metrics and example results.

### Phase 4 --- Audio--image contrastive model

-   Select pretrained image and audio encoders that can be trained or
    fine-tuned.
-   Add projection layers if needed so both encoders output vectors of
    the same dimension.
-   Train with paired image--audio examples using a contrastive
    objective (e.g., symmetric in-batch contrastive loss).
-   Use validation performance for model selection; save checkpoints and
    configuration.
-   Track training loss and validation retrieval metrics.

**Output:** trained checkpoint and reproducible training configuration.

### Phase 5 --- Retrieval pipeline

-   Encode all gallery images with the trained image encoder and cache
    their embeddings.
-   Encode each new spoken query with the trained audio encoder.
-   Rank image embeddings by cosine similarity to the query embedding.
-   Return the top-k images and scores.

**Output:** working audio-query-to-image retrieval pipeline.

### Phase 6 --- Evaluation

-   Report Recall@1, Recall@5, and Recall@10 where appropriate.
-   Compare matched-pair similarity with mismatched-pair similarity.
-   Inspect successful and failed retrieval examples.
-   Test paraphrase/generalization cases where suitable data is
    available.
-   Investigate one-shot/few-shot generalization using a clearly defined
    protocol agreed with the supervisor.
-   Compare with the baseline if implemented.

**Output:** results table, plots, and qualitative examples.

### Phase 7 --- Demo and report

-   Build a small interface to record/upload speech and display the
    top-k retrieved images.
-   Document dataset preparation, encoders, training setup, metrics,
    limitations, and results.
-   Add setup and reproduction instructions to the README.

**Output:** demonstrator and final report.

## Suggested timeline (8 weeks)

  Week   Focus                                   Milestone
  ------ --------------------------------------- ----------------------------
  1      Setup, download, inspect data           Dataset manifest
  2      Preprocessing and leakage-safe splits   Prepared data
  3      Baseline and encoder selection          Baseline results
  4--5   Contrastive training and tuning         Trained model
  6      Retrieval pipeline and evaluation       Metrics and error analysis
  7      Demo                                    Working prototype
  8      Experiments, documentation, report      Final deliverables

Adjust this schedule to the university deadline and available compute.

## Evaluation protocol

-   Use image-level disjoint train/validation/test splits.
-   Select checkpoints using validation data only.
-   Run final metrics on the test set once the setup is fixed.
-   Report Recall@K and include qualitative examples.
-   Document the exact split seed, model versions, hyperparameters, and
    preprocessing.

## Repository structure

``` text
audio-image-retrieval/
├── README.md
├── requirements.txt
├── configs/
│   └── default.yaml
├── data/
│   └── README.md          # download and layout instructions; no raw data
├── notebooks/
│   └── exploratory_analysis.ipynb
├── src/
│   ├── data/
│   │   ├── build_manifest.py
│   │   └── dataset.py
│   ├── models/
│   │   ├── encoders.py
│   │   └── contrastive_loss.py
│   ├── train.py
│   ├── evaluate.py
│   ├── retrieve.py
│   └── demo.py
├── tests/
│   └── test_data_pairs.py
├── results/
│   └── README.md
└── reports/
    └── README.md
```

## Definition of done

-   [ ] Every training audio clip is linked to the correct image.
-   [ ] Data splits prevent image-level leakage.
-   [ ] Training runs reproducibly and saves a checkpoint.
-   [ ] A spoken query returns ranked images.
-   [ ] Recall@K and qualitative results are reported.
-   [ ] Baseline comparison is included if feasible.
-   [ ] Demo and README explain how to reproduce the project.

## Risks and decisions to confirm

-   Confirm with the supervisor which pretrained encoders and
    contrastive objective are acceptable.
-   Verify the dataset actually provides at least the required 20 hours
    of usable audio; do not rely on an estimate without measuring the
    downloaded files.
-   If training resources are limited, freeze pretrained encoders
    initially and train projection layers before attempting full
    fine-tuning.
-   Keep raw dataset files and large model checkpoints out of Git;
    document how to obtain them.
