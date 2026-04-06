# Deep Operator Networks (DeepONet) & Physics-Informed DeepONet

---

## 1. Bài toán: Operator Learning

### Function vs Operator

* Function:
  [
  f: \mathbb{R}^d \to \mathbb{R}
  ]

* Operator:
  [
  \mathcal{G}: u \mapsto s
  ]

Trong đó:

* (u): hàm đầu vào
* (s): hàm đầu ra

Ví dụ PDE:
[
\mathcal{G}(u_0, a, f) = y(x,t)
]

---

## 2. Ý tưởng DeepONet

DeepONet học ánh xạ:
[
(u, y) \mapsto \mathcal{G}(u)(y)
]

* (u): hàm đầu vào
* (y): tọa độ truy vấn
* output: giá trị của hàm đầu ra tại (y)

---

## 3. Đầu vào / Đầu ra

### Input 1: Hàm (u)

Lấy mẫu tại sensor:
[
[u(x_1), ..., u(x_m)]
]

### Input 2: Query point

[
y = (x, t)
]

### Output

[
\hat{s}(y) \approx \mathcal{G}(u)(y)
]

---

## 4. Kiến trúc DeepONet

### 4.1 Branch Net

[
\mathbf{b}(u) = [b_1(u), ..., b_p(u)]
]

### 4.2 Trunk Net

[
\mathbf{t}(y) = [t_1(y), ..., t_p(y)]
]

### 4.3 Kết hợp

[
\hat{\mathcal{G}}(u)(y) = \sum_{k=1}^p b_k(u) t_k(y)
]

Hoặc:
[
\hat{\mathcal{G}}(u)(y) = \mathbf{b}(u)^T \mathbf{t}(y)
]

---

## 5. Loss DeepONet

[
\mathcal{L}_{data} = \frac{1}{N} \sum |\hat{\mathcal{G}}(u)(y) - \mathcal{G}(u)(y)|^2
]

---

## 6. Pipeline tính toán

1. Sample (u) → vector
2. Branch net → (\mathbf{b}(u))
3. Input (y)
4. Trunk net → (\mathbf{t}(y))
5. Inner product → output
6. Compute loss
7. Backprop

---

## 7. Ví dụ

### Antiderivative

[
s(x) = \int_0^x u(\xi) d\xi
]

DeepONet học:
[
(u, x) \mapsto s(x)
]

---

## 8. Physics-Informed DeepONet

### Ý tưởng

Thêm PDE constraint:
[
\mathcal{N}[s](y) = 0
]

---

## 9. Physics Loss

[
\mathcal{L}_{phys} = \sum |\mathcal{N}[\hat{s}(y)]|^2
]

---

## 10. Tổng Loss

[
\mathcal{L} = \lambda_d \mathcal{L}*{data} + \lambda_p \mathcal{L}*{phys} + \lambda_{bc} \mathcal{L}*{bc} + \lambda*{ic} \mathcal{L}_{ic}
]

---

## 11. Quy trình PI-DeepONet

1. Encode (u)
2. Query ((x,t))
3. Predict (\hat{s})
4. Compute derivatives (autodiff)
5. PDE residual
6. Optimize loss

---

## 12. PINN vs DeepONet

| Model    | Mapping            |
| -------- | ------------------ |
| PINN     | ((x,t) \to u(x,t)) |
| DeepONet | (u \to s(\cdot))   |

---

## 13. Công thức cốt lõi

[
\hat{\mathcal{G}}(u)(y) = \sum b_k(u) t_k(y)
]

---

## 14. Insight

* Branch: encode function
* Trunk: encode location
* Dot product: combine → output

---

## 15. Tóm tắt

* DeepONet: học operator từ data
* PI-DeepONet: thêm physics constraint

---

## 16. Key Takeaway

DeepONet = Function embedding + Basis expansion

PI-DeepONet = DeepONet + PDE con
