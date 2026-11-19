# Chinese Name Generator

A small character-level Transformer for Chinese name generation, built in Google Colab using a corpus of **600 synthetic full names**: **540 for training and 60 for validation**. The app lets you explore temperature sampling and attention.

Built and trained by **[Kira Yugova](https://github.com/li-yimei)** during the *Artificial Intelligence and Machine Learning* Transformers workshop.

**[Try the live app](https://chinese-name-generator-li-yimei.streamlit.app/)** · **[Explore the training notebook](1_name_generator.ipynb)**

## Demo

<img width="948" alt="Chinese name generator showing model statistics, generation controls, and generated names" src="https://github.com/user-attachments/assets/1c5c60d9-2967-406d-9068-4988be094a41" />

## What you can explore

- Generate up to 20 names at a time.
- Start a name with one or two characters from the model's vocabulary, such as `李` or `王`.
- Adjust the sampling temperature from **0.3 to 1.5**.
- Inspect attention heatmaps for both heads in the final Transformer block.
- See whether each generated name is already in the source list or is a new combination.

## Model

The model predicts the next character from the characters already present. A causal mask prevents it from looking ahead. Generation samples one character at a time until the boundary token is selected or the length limit is reached.

| Property | Deployed model |
|---|---|
| Architecture | Decoder-only Transformer, implemented in PyTorch |
| Trainable parameters | **87,328** |
| Transformer blocks | 2 |
| Attention heads per block | 2 |
| Embedding dimension | 64 |
| Vocabulary | 159 Chinese characters + 1 shared start/end token |
| Context length | 4 positions, including the start token |
| Maximum generated name length | 3 Chinese characters |
| Dropout during training | 0.2 |
| Web interface | Streamlit with Altair attention charts |

## Dataset

[`data/chinese_names.txt`](data/chinese_names.txt) contains **600 unique synthetic full names** in simplified Chinese: a surname followed by a personal name. There are **120 two-character names** and **480 three-character names**.

The list was prepared with AI assistance for this learning experiment. It is an intentionally constructed corpus; its frequencies do not represent the population distribution of Chinese names.

The file uses UTF-8, with one name per line. The notebook splits it into **540 training names** and **60 validation names**, using random seed **42**.

## Training

| Setting | Value |
|---|---|
| Training objective | Next-character cross-entropy |
| Optimizer | AdamW |
| Training steps | 2,000 |
| Batch size | 64 |
| Learning rate | 0.003 |
| Checkpoint selection | Lowest validation loss, evaluated every 100 steps |

The Chinese-name run recorded in the notebook took **21 seconds** and selected the checkpoint from **step 100**. Timing depends on the runtime and configuration; this is a record of that experiment.

## Using the app

1. Open the [live demo](https://chinese-name-generator-li-yimei.streamlit.app/).
2. Leave the prefix empty, or enter supported Chinese characters.
3. Choose a temperature and the number of names.
4. Click **Invent names**.
5. Expand **Look inside the model** to inspect attention.

Lower temperatures favour higher-scoring characters. Higher temperatures give more weight to less likely choices and usually produce more varied results.

## Interpreting the output

- **`new` means absent from the 600-name source list.** It does not establish that a name has never been used elsewhere.
- The model uses only its 159 known Chinese characters. Unsupported prefix characters are omitted and listed in the app. The current model uses at most two supported prefix characters.
- The training corpus is small and synthetic. Generated combinations may be unusual, repeat source names, or lack a natural meaning. Their linguistic and cultural suitability has not been evaluated.

## Repository files

| File | Purpose |
|---|---|
| `app.py` | Streamlit interface, model architecture, and generation code |
| `model.pt` | Trained weights, model settings, vocabulary, source names, and author metadata |
| `requirements.txt` | Python dependencies |
| `1_name_generator.ipynb` | Workshop exercises, training, evaluation, and export |
| `data/chinese_names.txt` | The synthetic training corpus |

## Run locally

Run these commands in a terminal:

```bash
git clone https://github.com/li-yimei/name-generator.git
cd name-generator
python -m pip install -r requirements.txt
python -m streamlit run app.py
```

On macOS, use `python3` if `python` is unavailable. Open the local URL printed by Streamlit, usually `http://localhost:8501`.

## Retrain in Colab

The notebook includes Google Drive setup for the workshop folder. Adjust its working-directory path to your own copy before running it.

Use these settings and run the notebook from top to bottom:

```python
LIST = "chinese_names"
SETTINGS = {"d": 64, "heads": 2, "blocks": 2}
```

Step 10 exports the new checkpoint to `app/model.pt` relative to the notebook's working directory. Copy that checkpoint next to the repository's root-level `app.py` to update the deployed model.

## Credits

The model and application are adapted from the Transformers workshop for *Artificial Intelligence and Machine Learning*. Kira Yugova adapted the experiment, trained the Chinese-name model in Colab, and deployed the app on Streamlit Community Cloud. The synthetic dataset was prepared with AI assistance.
