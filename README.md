# De-Novo-Molecular-Generation-with-Graph-Neural-Networks-on-MOSES

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/Babakmamnoon/De-Novo-Molecular-Generation-with-Graph-Neural-Networks-on-MOSES/blob/main/De_Novo_Molecular_Generation.ipynb)

## Overview

This repository contains an industry-grade de novo molecular generation pipeline built on:

- **Dataset:** MOSES — a curated subset of ZINC Clean Leads (~1.9M drug-like molecules)  
- **Representation:** RDKit molecular graphs + SMILES sequences  
- **Model:** Graph-based autoencoder  
  - **Encoder:** Graph Neural Network (GCN-based)  
  - **Decoder:** GRU-based SMILES generator conditioned on latent vectors  
- **Goal:** Learn a continuous latent space of drug-like molecules and generate novel, valid, and diverse compounds.

The project is designed for **Google Colab** and follows best practices in modern cheminformatics and deep generative modeling.

---

## Project Structure

- `GNN_DeNovo_MOSES.ipynb`  
  Main Colab notebook implementing the full pipeline:
  - Environment setup (RDKit, PyTorch, PyTorch Geometric)
  - MOSES dataset download and EDA
  - Molecular graph featurization
  - SMILES tokenization
  - GNN encoder + GRU decoder architecture
  - Training loop and learning curves
  - Latent space visualization (t-SNE)
  - De novo generation and evaluation metrics

- `README.md`  
  This documentation file.

---

## Methodology

### Dataset

We use the **MOSES** benchmark dataset, refined from the ZINC Clean Leads collection:

- Molecular weight: 250–350 Da  
- Rotatable bonds: ≤ 7  
- XlogP: ≤ 3.5  
- Medicinal chemistry and PAINS filters applied  
- Only common drug-like atoms (C, N, O, S, F, Cl, Br, H)

For Colab runtime, we subsample up to 100k molecules from the MOSES train split.

### Molecular Representation

- **Graphs:**  
  - Nodes: atoms with features (element type, hybridization, aromaticity, formal charge, H count, ring membership)  
  - Edges: bonds with features (bond type, conjugation, ring membership)  
  - Implemented via RDKit → PyTorch Geometric `Data` objects.

- **SMILES:**  
  - Character-level tokenization with special tokens (`<bos>`, `<eos>`, `<pad>`)  
  - Fixed maximum length (e.g., 120 characters) for Colab-friendly training.

### Model Architecture

1. **Graph Encoder (DrugEncoderGNN)**  
   - 3-layer GCN with residual connections and batch normalization  
   - Global mean + max pooling to obtain a fixed-size representation  
   - Final MLP to produce a latent vector `z` (e.g., 256-dim)

2. **SMILES Decoder (SmilesDecoderGRU)**  
   - Embedding layer for SMILES tokens  
   - GRU-based decoder conditioned on `z` via a latent-to-hidden projection  
   - Teacher forcing during training  
   - Autoregressive generation at inference (greedy or sampling)

3. **Training Objective**  
   - Cross-entropy loss over SMILES tokens (ignoring padding)  
   - The encoder learns a latent space that captures molecular structure; the decoder reconstructs SMILES.

---

## Metrics & Visualizations

The notebook includes:

- **Training Loss Curves**  
  - Cross-entropy loss vs. epoch for monitoring convergence.

- **Latent Space Visualization**  
  - t-SNE projection of latent vectors to 2D  
  - Qualitative assessment of structure in the learned latent space.

- **Generation Metrics**  
  - **Validity:** fraction of generated SMILES that RDKit can parse into molecules  
  - **Uniqueness:** fraction of valid molecules that are unique  
  - **Novelty:** fraction of unique molecules not present in the training set

- **Property Distributions**  
  - KDE plots comparing train vs. generated distributions for:
    - Molecular weight (MW)  
    - logP  
    - QED  
  - Checks whether generated molecules remain in a drug-like regime similar to MOSES.

These metrics and visuals are standard in molecular generative modeling and provide a practical, industry-relevant view of model performance.

---

## How to Run (Colab)

1. Open Google Colab.  
2. Upload `GNN_DeNovo_MOSES.ipynb` or create a new notebook and copy cells from the provided file.  
3. Run cells sequentially:
   - Environment setup  
   - Dataset download & EDA  
   - Featurization & tokenization  
   - Model definition  
   - Training  
   - Evaluation & generation

Colab will automatically detect GPU if available.

---

## Extensions & Future Work

This project is intentionally modular and can be extended with:

- **MOSES official metrics** (Fréchet ChemNet Distance, internal diversity, etc.)  
- **Conditional generation** (e.g., optimizing logP, QED, or other properties)  
- **Graph-based decoders** (JT-VAE-style, graph grammars, or diffusion models)  
- **Multi-objective optimization** (e.g., combining potency predictions with generative modeling)

---

## References

- Polykovskiy et al., *Molecular Sets (MOSES): A Benchmarking Platform for Molecular Generation Models*, Frontiers in Pharmacology, 2020.  
- Jin et al., *Junction Tree Variational Autoencoder for Molecular Graph Generation*, ICML 2018.  
- RDKit: Open-source cheminformatics toolkit.  
- PyTorch Geometric: Geometric deep learning extension library for PyTorch.

---

## License

MIT
