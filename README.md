# Od Atomu do Grafu: Przewidywanie aktywności cząsteczek wobec HIV (GNN) 🧬💻

[![Python 3.8+](https://img.shields.io/badge/python-3.8+-blue.svg)](https://www.python.org/downloads/)
[![PyTorch](https://img.shields.io/badge/PyTorch-EE4C2C?style=flat&logo=pytorch&logoColor=white)](https://pytorch.org/)
[![PyTorch Geometric](https://img.shields.io/badge/PyG-PyTorch_Geometric-green.svg)](https://pytorch-geometric.readthedocs.io/)
[![OGB](https://img.shields.io/badge/Dataset-OGB_molhiv-orange.svg)](https://ogb.stanford.edu/)

## 📌 O projekcie
Projekt stanowi kompleksowe badanie ewolucji architektur sieci neuronowych w zadaniu binarnej klasyfikacji cząsteczek chemicznych pod kątem ich zdolności do hamowania replikacji wirusa HIV. 

Rozwiązanie bazuje na zbiorze **OGB-MolHIV** (~41 tysięcy grafów molekularnych). Zastosowano w nim niezwykle wymagający podział **Scaffold Split** (podział pod względem szkieletu strukturalnego), który wymusza na modelach rzeczywistą generalizację praw chemii, a nie proste zapamiętywanie danych treningowych. Projekt został oceniony na maksymalną ocenę akademicką (5.0).

## 🚀 Technologie
* **Język:** Python
* **Deep Learning:** PyTorch, PyTorch Geometric (PyG)
* **Ewaluacja i dane:** Open Graph Benchmark (OGB), Scikit-learn
* **Wizualizacja:** Matplotlib, Seaborn, NetworkX

## 🧠 Architektury i Ewolucja Modeli
Eksperyment przeprowadzono w ustandaryzowanych fazach, sprawdzając, jak uwzględnianie kolejnych poziomów topologii wpływa na metrykę **ROC-AUC**:

1. **MLP (Baseline):** Ślepota na strukturę grafu (~4.8k parametrów).
2. **Deep GCN:** Konwolucja odkrywająca sąsiedztwo atomów, ale ignorująca rodzaj wiązań (~28k parametrów).
3. **GIN (Graph Isomorphism Network):** Asymilacja złożonych izomerów poprzez użycie wielowarstwowych sieci MLP (silna ekspresyjność matematyczna) (~1.8 mln parametrów).
4. **GINE + Virtual Node (State-of-the-Art):** Pełna asymilacja cech krawędzi (wiązań chemicznych) połączona z Węzłem Wirtualnym zapewniającym globalny przepływ informacji (~3.7 mln parametrów).

## 🔬 Rygor Metodologiczny
Z uwagi na ogromną pojemność końcowych modeli grafowych, wdrożono ścisły protokół badawczy chroniący przed przeuczeniem:
* **Validation Deception:** Zdiagnozowano i zneutralizowano zjawisko optymalizacji wyłącznie pod zbiór walidacyjny za pomocą skracania treningu oraz dynamicznego `ReduceLROnPlateau`.
* **Stabilność wyników:** Każdy model w każdej fazie był trenowany niezależnie na **5 losowych ziarnach (seeds)**. Ostateczne wyniki to twarde średnie wraz z odchyleniem standardowym.

## 📊 Ostateczne Wyniki (Test ROC-AUC)

| Model | Parametry | Średni ROC-AUC (5 seedów) | Najlepszy model (Max) |
| :--- | :--- | :---: | :---: |
| **MLP** | ~4.8 tys. | 0.6767 ± 0.014 | 0.6912 |
| **Deep GCN** | ~28 tys. | 0.7559 ± 0.012 | 0.7681 |
| **GIN** | ~1.8 mln | 0.7703 ± 0.005 | 0.7758 |
| **GINE + VN** | ~3.7 mln | **0.7518 ± 0.016** | **0.7820 🏆** |

> *GINE wykazało najwyższy potencjał predykcyjny (peak performance), natomiast architektura GIN charakteryzowała się największą stabilnością treningu.*

## ✂️ Kompresja (Pruning) i Fine-Tuning
W fazie 5 sprawdzono redundantność rekordowego modelu GINE za pomocą **L1 Unstructured Pruning**.
* Redukcja wag o 20% nie spowodowała utraty ROC-AUC (0.7810), dowodząc przeparametryzowania sieci.
* Brutalne usunięcie **30% wag liniowych** (redukcja o ok. 1 milion parametrów) obniżyło wynik do 0.7677.
* **Wniosek badawczy:** Krótki *Fine-Tuning* zoptymalizowanym krokiem uczenia zaledwie podniósł wynik do **0.7682**. Udowadnia to, że usunięte wagi nie były tylko matematycznym szumem, lecz kodowały istotne zależności strukturalne chemii, których mniejsza sieć nie potrafiła odbudować.

## ⚙️ Uruchomienie projektu
Sklonuj repozytorium i zainstaluj wymagane pakiety:
```bash
git clone [https://github.com/kevooo49/OGB-MolHIV](https://github.com/kevooo49/OGB-MolHIV)
cd TwojeRepozytorium
pip install -r requirements.txt
```

Uruchom główny notatnik `OGB_MolHIV.ipynb` w środowisku Jupyter lub Google Colab (zalecane GPU).
