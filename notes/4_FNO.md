# Fourier Neural Operator (FNO) và Physics-Informed Neural Operator (PINO)

## 1. Bài toán gốc: Operator Learning

Trong PDE cổ điển:

[
\mathcal{F}(u, a) = 0
]

* (a): input (initial condition, boundary condition, hệ số vật lý)
* (u): nghiệm cần tìm

Neural Operator học ánh xạ:

[
G: a \mapsto u
]

Tức là học **function-to-function mapping**.

---

## 2. Input / Output

### Input

[
a: D \to \mathbb{R}^{c_{in}}
]

### Output

[
u: D \to \mathbb{R}^{c_{out}}
]

Ví dụ:

* Darcy flow: (a(x,y) \to u(x,y))
* Navier–Stokes: (\omega_t \to \omega_{t+1})

---

## 3. Kiến trúc tổng thể FNO

[
u = Q \circ L^{(L)} \circ ... \circ L^{(1)} \circ P (a)
]

* (P): lifting (embedding)
* (L): operator layers
* (Q): projection

---

## 4. Một FNO Layer

[
L(z) = \sigma(Wz + b + K(z))
]

* (Wz + b): pointwise linear
* (K(z)): global operator

---

## 5. Spectral Convolution

[
K(z) = \text{IFFT}(R \cdot \text{FFT}(z))
]

### Quy trình:

1. FFT
2. Giữ low modes
3. Nhân trọng số
4. IFFT

---

## 6. Pseudocode

```
Input: a
z0 = P(a)

for l in layers:
    z_hat = FFT(z)
    z_hat = truncate(z_hat)
    z_hat = R * z_hat
    y = IFFT(z_hat)
    z = activation(y + Wz + b)

u = Q(z)
```

---

## 7. Loss FNO

[
\mathcal{L} = |G(a) - u|^2
]

---

## 8. PINO

Loss tổng:

[
\mathcal{L} = L_{data} + \lambda L_{physics} + \lambda L_{bc}
]

### PDE residual

[
r = \mathcal{F}(\hat u, a)
]

---

## 9. So sánh

| Model | Learning             |
| ----- | -------------------- |
| FNO   | data-driven operator |
| PINO  | operator + physics   |

---

## 10. Pipeline

### FNO

a → lifting → spectral layers → projection → u

### PINO

Same + physics loss + optional test-time optimization

---

## 11. Key Insight

* FFT giúp global interaction
* Low modes chứa thông tin quan trọng
* Activation trong spatial domain giúp phục hồi high-frequency

---

## 12. Tổng kết

FNO = học operator nhanh, global
PINO = thêm physics constraint, robust hơn
