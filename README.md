#  Optimizing Deep Learning for Brain Tumor Classification

### **A Comparative Ablation Study of Preprocessing & Augmentation Strategies using VGG16**

![Python](https://img.shields.io/badge/Python-3.10-blue)
![TensorFlow](https://img.shields.io/badge/TensorFlow-2.x-orange)
![Status](https://img.shields.io/badge/Status-Completed-green)

---

##  Project Overview

This project explores how preprocessing and augmentation strategies impact the performance of deep learning models for **brain tumor classification** (Glioma, Meningioma, Pituitary).
Most prior works incorrectly use **slice-wise splitting**, causing **data leakage** and artificially high accuracy.
We fix this by enforcing a strict **Patient-Level Split**, ensuring real clinical generalization.

We conducted a controlled **8-Experiment Ablation Study** comparing preprocessing techniques with and without augmentation using a fixed **VGG16** architecture.

---

##  Key Findings

1. **Preprocessing alone is not enough** — accuracy stalls at ~88%.
2. **Destructive preprocessing harms performance** — Skull-stripping & CLAHE + Augmentation caused underfitting (~82%).
3. **Best Strategy** → **Bilateral Denoising + Augmentation**

   *  **94.00% Accuracy**
   *  **0.991 AUC**
   * Proves that *cleaning the image before augmentation* yields optimal results.

---

#  Methodology

### 1. Dataset & Splitting
* **Source:** Figshare Brain Tumor Dataset (Cheng et al.).
* **Data Size:** 3,064 T1-weighted MRI images from 233 patients.
* **Leakage Prevention:** We implemented a custom splitting algorithm that groups images by **Patient ID**.
    * **Training:** 70% (163 Patients)
    * **Validation:** 15% (35 Patients)
    * **Testing:** 15% (35 Patients)
    * *Result:* Zero overlap of patients between sets.
* **Data Verification:** We performed a perceptual hash check (`imagehash`) on the entire dataset. While 26 visually identical images were found, our analysis confirmed that **all duplicates resided within the same split partition** (e.g., a training image duplicate was only found in training). This confirms that **no data leakage occurred** between train/test sets.


###  Patient-Level Split (Leakage-Free)

| Set        | Patients | Percentage |
| ---------- | -------- | ---------- |
| Train      | 163      | 70%        |
| Validation | 35       | 15%        |
| Test       | 35       | 15%        |

 **Zero overlap** of patients between sets.

---

## 2️ Model Architecture (VGG16)

* Pretrained **VGG16** (ImageNet) with frozen base
* Custom classification head:

  * Flatten → Dense(512) → Dropout(0.6)
  * Dense(256) → Dropout(0.5)
  * Softmax(3)
* Regularization: EarlyStopping, Dropout
* Same architecture used across all experiments for fairness

---

## 3️ Experimental Setup

We tested **3 preprocessing methods** + baseline:

* **Skull-Stripping** (Otsu thresholding + morphology)
* **CLAHE** (Histogram equalization)
* **Bilateral Filtering** (Edge-preserving denoising)
* Compared each *with* and *without* augmentation.

---

#  Results Summary

|  Exp  | Strategy            | Augmentation |  Accuracy  |    AUC    | Outcome                    |
| :---: | ------------------- | :----------: | :--------: | :-------: | -------------------------  |
|   1   | Baseline            |       ❌      |   87.92%   |   0.970   | Weak generalization       |
|   2   | **Aug Only**        |       ✅      | **92.16%** | **0.992** | Major improvement         |
|   3   | Skull-Stripping     |       ❌      |   88.77%   |   0.968   | Minor change              |
|   4   | CLAHE               |       ❌      |   88.98%   |   0.977   | Minor change              |
|   5   | Denoising           |       ❌      |   88.14%   |   0.973   | No improvement            |
|   6   | Skull-Strip + Aug   |       ⚠️      |   81.78%   |   0.960   | **Underfitting**          |
|   7   | CLAHE + Aug         |       ⚠️      |   82.63%   |   0.955   | **Underfitting**          |
| **8** | **Denoising + Aug** |       ✅     | **94.03%** | **0.991** | 🏆 **Best Configuration** |

---

#  Visuals

All experiment logs, preprocessing examples, Grad-CAM visualizations, and training curves are available in the **Master Notebook** inside the repo.

---

#  Installation

```bash
pip install tensorflow opencv-python-headless scikit-learn pandas seaborn matplotlib tqdm mat73
```

---

#  Acknowledgments

Special thanks to:

* **Mr. Himangshu Pal** — Supervision, conceptual guidance, feedback.

---

#  Author & Contributions

### **Latchan Chhetri**

🔗 LinkedIn: [https://www.linkedin.com/in/latchan-chhetri-704380297/](https://www.linkedin.com/in/latchan-chhetri-704380297/)

**Contributions:**

* Project Conceptualization
* Methodology & Experimental Design
* Data Splitting Algorithm
* Preprocessing Pipeline Implementation
* Model Training & Ablation Study
* Visualization, Analysis & Interpretation
* Writing – Original Draft

---

### **Aman Kumar**

**Contributions:**

* Compute resource support
* Manuscript approval
* Minor administrative assistance

---

### **Himangshu Pal**

**Contributions:**

* Supervision
* Review & Editing
* Conceptual oversight

##  Full Jupyter Notebook (Link)
If GitHub cannot render the full notebook due to its size.  
The complete ablation study notebook (with all outputs, visualizations, and experiment logs) is available on Google Colab:

**Open Notebook on Google Colab**  
https://colab.research.google.com/drive/1_LDk5Kr7Fo2emU5qR0cuDmMw01Q13K39?usp=sharing

