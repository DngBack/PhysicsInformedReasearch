# TÀI LIỆU TỔNG HỢP CÁC LOẠI DATASET TRONG PDE / PINN / NEURAL OPERATOR

---

## 1. Giới thiệu

Trong các bài toán liên quan đến **PDE (Partial Differential Equations)** và các mô hình học máy như:

* PINN (Physics-Informed Neural Networks)
* DeepONet
* FNO / PINO

khái niệm "dataset" **không giống machine learning truyền thống**.

Thay vì chỉ có dạng:

```
input -> label
```

chúng ta có nhiều loại dữ liệu khác nhau, phục vụ các mục tiêu huấn luyện khác nhau.

---

## 2. Tổng quan các loại dataset

Có thể chia thành 4 loại chính:

| Loại | Mô tả ngắn               | Mô hình tiêu biểu  |
| ---- | ------------------------ | ------------------ |
| 1    | Dữ liệu điểm vật lý      | PINN               |
| 2    | Dữ liệu quan sát có nhãn | Data-informed PINN |
| 3    | Dữ liệu hàm → giá trị    | DeepONet           |
| 4    | Dữ liệu trường → trường  | FNO / PINO         |

---

## 3. Loại 1: Dữ liệu điểm vật lý (Physics points)

### 3.1 Khái niệm

Đây là dữ liệu dùng trong **PINN**.

Không cần biết nghiệm thật ở mọi điểm.
Thay vào đó, ta cung cấp:

* Điểm trong miền (collocation points)
* Điểm trên biên (boundary points)
* Điểm điều kiện đầu (initial points)

---

### 3.2 Mục tiêu huấn luyện

Huấn luyện mạng sao cho:

* Thỏa mãn PDE
* Thỏa mãn điều kiện biên
* Thỏa mãn điều kiện đầu

---

### 3.3 Dạng sample

#### Collocation point

```
input = (x, t)
label = none
```

#### Boundary point

```
input = (x, t) trên biên
label = giá trị biên
```

#### Initial point

```
input = (x, 0)
label = giá trị ban đầu
```

---

### 3.4 Ví dụ cụ thể

Bài toán nhiệt 1D:

* Collocation: (0.3, 0.7)
* Boundary: (0, 0.4) → 0
* Initial: (0.6, 0) → sin(pi*0.6)

---

### 3.5 Mô hình

* PINN

---

### 3.6 Trực giác

Không dạy bằng đáp án.
Dạy bằng luật + vài điều kiện.

---

## 4. Loại 2: Dữ liệu quan sát có nhãn

### 4.1 Khái niệm

Dữ liệu có nhãn thực:

* đo từ cảm biến
* mô phỏng
* nghiệm solver

---

### 4.2 Mục tiêu

* Khớp dữ liệu thật
* Có thể kết hợp với PDE

---

### 4.3 Sample

```
input = (x, t)
label = u(x, t)
```

---

### 4.4 Ví dụ

```
(0.4, 0.2) → 0.73
```

---

### 4.5 Mô hình

* Data-informed PINN
* Inverse PINN

---

### 4.6 Trực giác

Có thêm “đáp án mẫu” để học tốt hơn.

---

## 5. Loại 3: Dữ liệu hàm → giá trị (Triple)

### 5.1 Khái niệm

Dữ liệu cho **DeepONet**.

Mỗi sample có 3 phần:

```
(function input, query point) → output value
```

---

### 5.2 Mục tiêu

Học toán tử:

```
f → u
```

---

### 5.3 Sample

```
input1 = function (vd: permeability field)
input2 = (x, y)
output = u(x, y)
```

---

### 5.4 Ví dụ

Darcy flow:

* Input: permeability field
* Query: (x, y)
* Output: pressure tại (x, y)

---

### 5.5 Mô hình

* DeepONet

---

### 5.6 Trực giác

Học quy tắc:

"Nếu đầu vào là hàm này → đầu ra sẽ như thế nào"

---

## 6. Loại 4: Dữ liệu trường → trường (Field-to-field)

### 6.1 Khái niệm

Dữ liệu dạng lưới (grid):

```
field → field
```

---

### 6.2 Mục tiêu

Học ánh xạ:

```
input field → output field
```

---

### 6.3 Sample

```
input = tensor (H × W)
output = tensor (H × W)
```

---

### 6.4 Ví dụ

Darcy flow:

* Input: permeability map
* Output: pressure map

---

### 6.5 Mô hình

* FNO
* PINO

---

### 6.6 Trực giác

Giống image-to-image nhưng là dữ liệu vật lý.

---

## 7. So sánh tổng hợp

| Loại           | Input            | Output       | Mô hình     |
| -------------- | ---------------- | ------------ | ----------- |
| Physics points | (x,t)            | u(x,t)       | PINN        |
| Labeled data   | (x,t)            | giá trị thật | PINN + data |
| Triple         | function + point | value        | DeepONet    |
| Field-to-field | field            | field        | FNO/PINO    |

---

## 8. Ví dụ tổng hợp

### 8.1 PINN

```
(0.3, 0.7)
(0, 0.4) → 0
(0.6, 0) → sin(pi*0.6)
```

### 8.2 DeepONet

```
(permeability, (x,y)) → pressure
```

### 8.3 FNO

```
permeability map → pressure map
```

---

## 9. Kết luận

Nhớ 3 câu:

* PINN: học từ điểm + vật lý
* DeepONet: học từ hàm + điểm
* FNO: học từ field → field

---

## 10. Hướng học tiếp

1. PINN cơ bản
2. Data-informed PINN
3. DeepONet
4. FNO / PINO

---

Tài liệu này giúp bạn hiểu toàn bộ landscape dataset trong PDE/AI từ mức cơ bản đến nâng cao.
