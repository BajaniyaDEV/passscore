# 🔐 PassScore

**Measuring password predictability with character-level neural networks**

PassScore trains a character-level language model on 100,000 common passwords. The model learns what typical passwords look like, so it can score any new password: **the more "normal" a password looks to the model, the more predictable (and weaker) it is.**

> Built as a project for the *Neural Networks & Deep Learning* course at Lambton College, following Andrej Karpathy's [Neural Networks: Zero to Hero](https://www.youtube.com/playlist?list=PLAqhIrjkxbuWI23v9cThsA9GvCAUhRvKZ) series (makemore).

---

## 📌 How it works

1. Each password is treated as a sequence of characters, with a special `<>` token marking start and end.
2. A model learns the probability of each next character, given the previous ones.
3. For a new password, we compute the **average negative log-likelihood (NLL)** per character:

   $$\text{score} = -\frac{1}{n}\sum_{i=1}^{n} \ln P(c_i \mid c_{<i})$$

4. **Low score → predictable password ❌ · High score → unpredictable password ✅**

The baseline for random guessing is `ln(81) ≈ 4.39` (81 possible characters).

---

## 🧠 Models

Each model improves on the previous one:

| # | Model | Context | Key ideas |
|---|-------|---------|-----------|
| 1 | Bigram (counting) | 1 character | Count tables, smoothing |
| 2 | Bigram (neural net) | 1 character | One-hot encoding, softmax, gradient descent |
| 3 | MLP | 3 characters | Embeddings, hidden layer, train/val/test split |
| 4 | MLP + BatchNorm | 3 characters | Kaiming init, Batch Normalization |
| 5 | WaveNet-style | 8 characters | Hierarchical layers |

---

## 📊 Results

| Model | Train loss | Val loss |
|-------|-----------|----------|
| Random baseline | 4.39 | 4.39 |
| Bigram (counting) | 2.67 | – |
| Bigram (neural net) | _TBD_ | _TBD_ |
| MLP | _TBD_ | _TBD_ |
| MLP + BatchNorm | _TBD_ | _TBD_ |
| WaveNet-style | _TBD_ | _TBD_ |

### Example scores (bigram model)

| Password | Score | Verdict |
|----------|-------|---------|
| `qwerty` | 2.48 | Very predictable ❌ |
| `password123` | 2.65 | Very predictable ❌ |
| `iloveyou2` | 3.21 | Predictable ❌ |
| `Tr0ub4dor` | 4.84 | Harder ⚠️ |
| `qT7#vLm2!x` | 6.36 | Unpredictable ✅ |

<!-- Add your loss chart here: ![Loss comparison](images/loss_comparison.png) -->

---

## 🔍 Key findings

- Common passwords are **highly predictable**: even a bigram model reduces loss by 1.72 compared with random guessing (vs. 0.85 for English names).
- Loss values **cannot be compared directly** across datasets with different vocabulary sizes. Compare against each dataset's random baseline instead.
- _Add findings from the MLP and WaveNet models here._

---

## ⚠️ Challenges

| Challenge | Solution |
|-----------|----------|
| `.` appears in real passwords, so it can't be the start/end token | Used a separate `<>` token |
| Unseen character pairs gave probability 0 → infinite loss | +1 smoothing / weight regularization |
| Large vocabulary (81 vs 27 characters) | Embeddings instead of one-hot inputs (MLP) |
| Longer passwords build up more total loss | Score by **average** loss per character |
| _Add more as you go_ | |

---

## 🚀 Getting started

```bash
git clone https://github.com/BajaniyaDEV/passscore.git
cd passscore
pip install -r requirements.txt
```

Download the dataset:

```bash
wget https://raw.githubusercontent.com/danielmiessler/SecLists/master/Passwords/Common-Credentials/xato-net-10-million-passwords-100000.txt -O data/passwords.txt
```

Then open the notebooks in `notebooks/` in order, or run them in [Google Colab](https://colab.research.google.com/).

---

## 📁 Project structure

```
passscore/
├── data/               # dataset (downloaded, not committed)
├── notebooks/
│   ├── 01_bigram.ipynb
│   ├── 02_bigram_nn.ipynb
│   ├── 03_mlp.ipynb
│   ├── 04_batchnorm.ipynb
│   └── 05_wavenet.ipynb
├── images/             # charts for this README
├── requirements.txt
├── LICENSE
└── README.md
```

---

## 🛡️ Ethics & limitations

- Trained only on **publicly available** common-password lists used in security research.
- For **defensive and educational use**: helping people avoid weak passwords.
- **Don't type your real passwords** into any demo.
- The score measures *similarity to common passwords*. It is not a complete password-strength meter (it doesn't check for breaches or estimate cracking time).

---

## 🙏 Acknowledgements

- [Andrej Karpathy – makemore](https://github.com/karpathy/makemore) and the *Neural Networks: Zero to Hero* series
- [SecLists](https://github.com/danielmiessler/SecLists) by Daniel Miessler, for the password dataset

## 📄 License

MIT. See [LICENSE](LICENSE).
