# **RO-index**

Quantifying fluorescent protein sensor cytGRX-roGFP2 redox state with a reduced–oxidized (RO) index.

Example image: <a href='https://nph.onlinelibrary.wiley.com/cms/asset/43d9b2d6-30c5-46de-b688-b91628468678/nph71392-fig-0004-m.jpg'>https://nph.onlinelibrary.wiley.com/cms/asset/43d9b2d6-30c5-46de-b688-b91628468678/nph71392-fig-0004-m.jpg</a>


# Procedure

## 1. Load image

### 1.1 Read confocal microscopy CZI file.

### 1.2 Extract fluorescence channels:
- **E405**: 405 nm excitation channel
- **E488**: 488 nm excitation channel
- **PI**: Propidium iodide channel

<br>

## 2. Root region segmentation

For each channel $C \in \{E_{405}, E_{488}\}$:

### 2.1 Normalize intensity values of $C$ to the range $[0, 255]$.

### 2.2 Apply a median filter.

### 2.3 Compute an Otsu threshold $T_c$.

### 2.4 Generate binary mask $M_c$ such that

$$M_c(x,y) = \begin{cases} 1, & C(x,y) > T_c \\ 0, & \text{otherwise} \end{cases}$$

### 2.5 Retain the masked image

$$C' = C \cdot M_c$$

<br>

## 3. Protein presence mask

$$M_{\text{protein}} = M_{405} \lor M_{488}$$

where $\lor$ denotes the logical OR operator.

<div style='page-break-after: always;'></div>
<br>

## 4. Saturated pixel removal

For each processed channel $C' \in \{E_{405}', E_{488}'\}$:

### 4.1 Extract all non-zero pixels.

### 4.2 Compute percentile thresholds

$$T_{\text{high}} = P_{99}(C')$$

### 4.3 Ties handling
To avoid too many tied data, only when the ratio of unique intensity values exceeds 0.3, set:

$$C'(x,y) = 0 \quad \text{if} \quad C'(x,y) > T_{\text{high}}$$

<br>

## 5. Redox index calculation

### 5.1 Square both channels

$$E_{405}'' = (E_{405}')^2$$

$$E_{488}'' = \left( E_{488}' \cdot \frac{\max(E_{405}')}{\max(E_{488}')} \right)^2$$

### 5.2 Compute the redox index (RO)

$$RO = \frac{E_{405}'' - E_{488}''}{E_{405}'' + E_{488}''}$$

### 5.3 Replace undefined values with zero.

### 5.4 Restrict analysis to valid protein regions

$$RO = RO \cdot M_{\text{protein}}$$

<br>

## 6. Reduced–Oxidized (RO) index classification

$$-1 \leq RO \leq 1$$

- **RO &gt; 0** indicates a more oxidized state
- **RO &lt; 0** indicates a more reduced state
