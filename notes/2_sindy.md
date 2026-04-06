# SINDy: Giải thích chi tiết từ đầu vào, đầu ra, phương pháp đến đánh giá

## 1) SINDy là gì?

**SINDy (Sparse Identification of Nonlinear Dynamical Systems)** là một phương pháp học **phương trình động lực** từ dữ liệu chuỗi thời gian. Thay vì học một mô hình đen hộp chỉ để dự báo, SINDy cố gắng tìm ra **công thức phương trình vi phân** mô tả sự tiến hóa của hệ theo thời gian.

Ta thường bắt đầu từ hệ:

[
\dot{x}(t)=f(x(t))
]

Trong đó:

* (x(t)): trạng thái của hệ tại thời điểm (t)
* (\dot{x}(t)): đạo hàm theo thời gian của trạng thái
* (f): quy luật động lực chưa biết

Ý tưởng cốt lõi của SINDy là:

> Dù ta không biết chính xác (f), nhiều hệ động lực trong thực tế có thể được biểu diễn bằng **một số rất ít hạng tử** trong một thư viện các hàm ứng viên lớn hơn.

Ví dụ, phương trình thật có thể chỉ cần các hạng tử như:

* (x)
* (y)
* (xy)
* (x^2)

trong khi thư viện ban đầu có rất nhiều khả năng khác.

---

## 2) Bài toán mà SINDy giải

Giả sử ta chỉ quan sát được dữ liệu theo thời gian:

[
x(t_1), x(t_2), \dots, x(t_m)
]

Mục tiêu là từ dữ liệu đó khôi phục lại phương trình:

[
\dot{x}(t)=f(x(t))
]

Nói cách khác, ta muốn đi từ:

* **dữ liệu quỹ đạo**

đến:

* **phương trình vi phân diễn giải được**

Đây là điểm khác biệt lớn với nhiều mô hình machine learning thông thường: SINDy không chỉ muốn dự báo tốt mà còn muốn **khôi phục cấu trúc phương trình**.

---

## 3) Input của SINDy là gì?

### 3.1 Input toán học

Thông thường, ta xây hai ma trận:

[
X =
\begin{bmatrix}
x(t_1)^T \
x(t_2)^T \
\vdots \
x(t_m)^T
\end{bmatrix}
,\qquad
\dot{X} =
\begin{bmatrix}
\dot{x}(t_1)^T \
\dot{x}(t_2)^T \
\vdots \
\dot{x}(t_m)^T
\end{bmatrix}
]

Trong đó:

* mỗi hàng của (X) là trạng thái tại một thời điểm
* mỗi hàng của (\dot X) là đạo hàm tại cùng thời điểm đó

### 3.2 Ví dụ trực quan

Giả sử hệ có 2 biến trạng thái (x_1, x_2):

| t   | (x_1) | (x_2) |
| --- | ----: | ----: |
| 0.0 |  1.00 |  0.00 |
| 0.1 |  0.99 | -0.10 |
| 0.2 |  0.97 | -0.19 |
| 0.3 |  0.94 | -0.28 |

Khi đó:

[
X=
\begin{bmatrix}
1.00 & 0.00\
0.99 & -0.10\
0.97 & -0.19\
0.94 & -0.28
\end{bmatrix}
]

Còn (\dot X) có thể:

* được đo trực tiếp
* hoặc được ước lượng số từ chuỗi thời gian

### 3.3 Những gì người dùng phải chọn thêm

Ngoài dữ liệu, thực tế còn phải chọn:

* cách tính đạo hàm
* thư viện đặc trưng
* thuật toán sparse regression

Đây là ba thành phần quyết định chất lượng của SINDy.

---

## 4) Output của SINDy là gì?

Output là một **hệ phương trình động lực rõ ràng**.

Ví dụ đầu ra có thể là:

[
\dot{x}_1 = -0.1x_1 + 2x_2
]
[
\dot{x}_2 = -3x_1 -0.2x_2 + 0.5x_1x_2
]

Tức là thay vì chỉ học một hàm dự báo mơ hồ, SINDy trả ra một biểu thức toán học có thể:

* đọc được
* kiểm tra được
* phân tích ổn định được
* tích phân mô phỏng lại được

---

## 5) Công thức lõi của SINDy

SINDy xây một thư viện đặc trưng (\Theta(X)) và tìm ma trận hệ số (\Xi) sao cho:

[
\dot X \approx \Theta(X)\Xi
]

Trong đó:

* (\Theta(X)): các hạng tử ứng viên
* (\Xi): hệ số của các hạng tử
* (\Xi) được ép **thưa** để phần lớn hệ số bằng 0

Đây là trái tim của phương pháp.

---

## 6) Giải thích từng bước của method

## Bước 1: Thu thập dữ liệu trạng thái theo thời gian

Ta quan sát trajectory của hệ:

[
x(t_1), x(t_2), \dots, x(t_m)
]

### Ví dụ

Giả sử thu được dữ liệu:

[
X=
\begin{bmatrix}
1.0 & 2.0\
1.2 & 2.5\
1.6 & 3.1\
2.1 & 4.0
\end{bmatrix}
]

Mỗi hàng là một thời điểm, mỗi cột là một biến trạng thái.

### Ý nghĩa

SINDy bắt đầu từ **trajectory data**, không bắt đầu từ giả thuyết phương trình cụ thể.

---

## Bước 2: Tính đạo hàm (\dot X)

Nếu không đo trực tiếp đạo hàm, ta phải ước lượng:

[
\dot x(t_i)\approx \frac{x(t_{i+1})-x(t_i)}{\Delta t}
]

### Ví dụ

Nếu (\Delta t=1), với dữ liệu:

* (x: 1.0 \to 1.2 \to 1.6 \to 2.1)
* (y: 2.0 \to 2.5 \to 3.1 \to 4.0)

thì đạo hàm xấp xỉ là:

[
\dot X \approx
\begin{bmatrix}
0.2 & 0.5\
0.4 & 0.6\
0.5 & 0.9
\end{bmatrix}
]

### Ý nghĩa

Bước này rất quan trọng vì nếu đạo hàm bị sai do noise, mô hình SINDy về sau cũng sẽ sai.

---

## Bước 3: Xây feature library (\Theta(X))

Thư viện đặc trưng là tập các hạng tử ứng viên mà ta cho phép phương trình sử dụng.

### Ví dụ thư viện polynomial bậc 2 với 2 biến (x, y)

[
\Theta(x,y) = [1, x, y, x^2, xy, y^2]
]

### Ví dụ tại một điểm

Nếu (x=2, y=3), thì vector đặc trưng là:

[
[1, 2, 3, 4, 6, 9]
]

### Ví dụ ma trận thư viện

Với ba điểm dữ liệu ((1,2), (2,3), (3,4)):

[
\Theta(X)=
\begin{bmatrix}
1 & 1 & 2 & 1 & 2 & 4\
1 & 2 & 3 & 4 & 6 & 9\
1 & 3 & 4 & 9 & 12 & 16
\end{bmatrix}
]

### Trực giác

Mỗi cột là một giả thuyết term:

* có hằng số không?
* có (x) không?
* có (xy) không?
* có (y^2) không?

---

## Bước 4: Giải sparse regression

Ta cần tìm (\Xi) sao cho:

[
\dot X \approx \Theta(X)\Xi
]

nhưng với yêu cầu rằng đa số phần tử của (\Xi) bằng 0.

### Ví dụ trực giác

Giả sử với phương trình (\dot x), hồi quy ban đầu cho ra:

[
\dot x = 0.00\cdot 1 + 1.01x + 0.00y + 0.00x^2 + 0.99xy + 0.00y^2
]

Sau sparse thresholding, ta giữ lại:

[
\dot x \approx 1.01x + 0.99xy
]

Các hạng tử nhỏ bị loại bỏ.

---

## Bước 5: STLSQ hoạt động như thế nào?

STLSQ = **Sequentially Thresholded Least Squares**.

Đây là optimizer phổ biến nhất khi bắt đầu với SINDy.

### Quy trình

#### Lần 1: fit least squares trên toàn bộ thư viện

Ví dụ hệ số ra:

[
[0.01,\ 1.03,\ -0.02,\ 0.004,\ 0.98,\ 0.001]
]

#### Lần 2: threshold

Chọn ngưỡng, ví dụ 0.05. Những hệ số nhỏ hơn ngưỡng bị cho về 0:

[
[0,\ 1.03,\ 0,\ 0,\ 0.98,\ 0]
]

#### Lần 3: refit trên support còn lại

Chỉ còn hai term (x) và (xy), ta fit lại:

[
\dot x = 1.00x + 1.00xy
]

### Ý nghĩa

STLSQ lặp giữa:

* fit
* cắt các hệ số nhỏ
* fit lại

cho tới khi mô hình ổn định.

---

## Bước 6: Học riêng cho từng phương trình trạng thái

Nếu hệ có nhiều biến trạng thái, SINDy học từng phương trình riêng.

Ví dụ hệ 2 biến:

[
\dot x = f_1(x,y)
]
[
\dot y = f_2(x,y)
]

Khi đó ma trận hệ số (\Xi) có nhiều cột:

* cột 1 ứng với (\dot x)
* cột 2 ứng với (\dot y)

### Ví dụ

Giả sử:

[
\Xi=
\begin{bmatrix}
0 & 0\
1 & -2\
0 & 0\
0 & 0\
1 & 0\
0 & 3
\end{bmatrix}
]

với thứ tự feature là ([1,x,y,x^2,xy,y^2])

thì ta đọc được:

[
\dot x = x + xy
]
[
\dot y = -2x + 3y^2
]

---

## 7) Một ví dụ hoàn chỉnh, nhỏ và dễ theo dõi

Giả sử hệ thật là:

[
\dot x = -x + 2y
]
[
\dot y = -0.5y
]

Ta giả sử chưa biết công thức này, chỉ có dữ liệu.

### 7.1 Dữ liệu trạng thái

| t |   x |    y |
| - | --: | ---: |
| 0 | 1.0 |  2.0 |
| 1 | 4.0 |  1.0 |
| 2 | 3.0 |  0.5 |
| 3 | 1.0 | 0.25 |

### 7.2 Đạo hàm thật tại các điểm

Tại ((1,2)):

[
\dot x = -1 + 2(2)=3,\quad \dot y=-0.5(2)=-1
]

Tại ((4,1)):

[
\dot x=-4+2(1)=-2,\quad \dot y=-0.5
]

Tại ((3,0.5)):

[
\dot x=-3+1=-2,\quad \dot y=-0.25
]

Do đó:

[
\dot X=
\begin{bmatrix}
3 & -1\
-2 & -0.5\
-2 & -0.25
\end{bmatrix}
]

### 7.3 Chọn library tuyến tính

[
\Theta(X)=[1, x, y]
]

Suy ra:

[
\Theta(X)=
\begin{bmatrix}
1 & 1 & 2\
1 & 4 & 1\
1 & 3 & 0.5
\end{bmatrix}
]

### 7.4 Học phương trình (\dot x)

Ta tìm:

[
\dot x = c_0 + c_1x + c_2y
]

và lời giải đúng là:

[
c_0=0,\quad c_1=-1,\quad c_2=2
]

nên:

[
\dot x = -x + 2y
]

### 7.5 Học phương trình (\dot y)

Tương tự:

[
\dot y = d_0 + d_1x + d_2y
]

và lời giải là:

[
d_0=0,\quad d_1=0,\quad d_2=-0.5
]

nên:

[
\dot y = -0.5y
]

### 7.6 Ý nghĩa

Nếu thư viện lớn hơn, ví dụ thêm (x^2, xy, y^2), thì SINDy vẫn có thể dùng sparsity để cuối cùng giữ lại đúng các term cần thiết.

---

## 8) Ví dụ hệ phi tuyến thực sự: Lorenz

Một hệ rất nổi tiếng là Lorenz:

[
\dot x = \sigma(y-x)
]
[
\dot y = x(\rho-z)-y
]
[
\dot z = xy-\beta z
]

Ta thấy phương trình này dùng các term như:

* (x)
* (y)
* (z)
* (xy)
* (xz)

Tức là dù hệ phi tuyến phức tạp, cấu trúc của nó vẫn khá thưa trong một thư viện polynomial phù hợp.

Đó là lý do Lorenz là benchmark kinh điển cho SINDy.

---

## 9) Tại sao feature library lại quan trọng?

Vì SINDy chỉ có thể chọn trong những gì ta đưa vào thư viện.

### Ví dụ đúng

Nếu hệ thật là:

[
\dot x = x - xy
]

và thư viện chứa:

[
[1, x, y, xy, x^2, y^2]
]

thì SINDy có cơ hội tìm đúng.

### Ví dụ sai

Nếu thư viện chỉ chứa:

[
[1, x, y]
]

thì mô hình không thể nào biểu diễn term (xy). Khi đó phương trình khôi phục chắc chắn bị sai.

---

## 10) Vai trò của optimizer

Optimizer là thành phần quyết định cách chọn ra mô hình sparse.

### Ví dụ trực giác

Least squares bình thường có thể cho:

[
\dot x = 0.01 + 1.02x - 0.04y + 0.98xy + 0.01x^2
]

Optimizer sparse sẽ ép về:

[
\dot x = 1.02x + 0.98xy
]

Nghĩa là bỏ các hệ số rất nhỏ để làm mô hình gọn, dễ diễn giải hơn.

---

## 11) SINDy mạnh ở đâu và yếu ở đâu?

### Điểm mạnh

* cho phương trình dễ giải thích
* phù hợp với các hệ có cấu trúc sparse
* hữu ích khi ta muốn hiểu cơ chế động lực chứ không chỉ dự báo

### Điểm yếu

* nhạy với noise
* nhạy với bước ước lượng đạo hàm
* nhạy với thiết kế feature library
* nếu thư viện quá lớn thì dễ bị ill-conditioned hoặc overfit

---

## 12) Evaluation: đánh giá SINDy thế nào?

Ta không nên chỉ nhìn một con số loss duy nhất.

## 12.1 Derivative fit

So sánh:

[
\hat{\dot X} = \Theta(X)\hat{\Xi}
]

với (\dot X) thật.

### Metrics

* MSE
* RMSE
* MAE
* (R^2)

### Ví dụ

Nếu đạo hàm thật là:

[
[3.0, -2.0, -2.0]
]

và dự đoán là:

[
[2.9, -1.8, -2.2]
]

thì RMSE nhỏ nghĩa là fit local dynamics khá tốt.

---

## 12.2 Trajectory / rollout error

Sau khi có phương trình, ta tích phân hệ để sinh ra trajectory dự đoán rồi so với dữ liệu thật.

### Metrics

* trajectory RMSE
* normalized rollout error
* forecast error theo horizon
* error trên held-out initial conditions

### Ví dụ

Nếu mô hình học ra gần đúng:

[
\dot x = -0.95x + 1.9y
]
[
\dot y = -0.48y
]

thì ta kiểm tra xem mô phỏng từ cùng điều kiện đầu có bám trajectory thật không.

---

## 12.3 Sparsity / model complexity

Vì mục tiêu là mô hình gọn, cần đo:

* số lượng nonzero terms
* số term mỗi phương trình
* trade-off giữa accuracy và complexity

### Ví dụ

Nếu hai mô hình có RMSE gần như nhau:

* Model A: 3 term
* Model B: 12 term

thường ta thích Model A hơn vì đúng tinh thần sparse và dễ diễn giải hơn.

---

## 12.4 Structural recovery

Nếu biết ground-truth equation, có thể đánh giá:

* support recovery
* precision / recall / F1 trên tập term
* coefficient error
* exact equation match

### Ví dụ

Ground truth:

[
\dot x = -x + 2y
]

Model học ra:

[
\dot x = -1.01x + 1.98y + 0.01xy
]

thì:

* coefficient error nhỏ
* support gần đúng
* có 1 false positive nhỏ là (xy)

---

## 12.5 Dynamical fidelity

Đôi khi quan trọng nhất là mô hình có tái tạo đúng hành vi động lực hay không:

* fixed points
* stability
* limit cycles
* phase portrait
* attractor shape
* spectral properties

Một mô hình có RMSE nhỏ nhưng sai loại attractor vẫn là mô hình chưa tốt.

---

## 13) Một protocol đánh giá thực dụng

Nếu làm nghiên cứu hoặc thực nghiệm, nên chia evaluation thành bốn nhóm:

### A. Fit cục bộ

* RMSE trên (\dot X)
* (R^2) trên (\dot X)

### B. Dynamics

* rollout RMSE
* held-out trajectory error
* unseen initial condition error

### C. Parsimony

* số nonzero terms
* complexity-adjusted score

### D. Equation recovery

* support precision/recall/F1
* coefficient error
* exact match rate nếu có ground truth

---

## 14) Sơ đồ tư duy ngắn gọn

### Input

* dữ liệu trạng thái (X)
* thời gian (t)
* hoặc đạo hàm (\dot X)

### Method

* tính (\dot X)
* xây feature library (\Theta(X))
* giải sparse regression để tìm (\Xi)

### Output

* hệ phương trình thưa, diễn giải được

### Evaluation

* fit đạo hàm
* fit rollout
* sparsity
* recovery đúng cấu trúc phương trình

---

## 15) Một câu tóm tắt dễ nhớ

> SINDy lấy dữ liệu quỹ đạo của một hệ, tạo ra nhiều hạng tử ứng viên, rồi dùng sparse regression để giữ lại số ít hạng tử đủ để viết ra phương trình động lực của hệ.

---

## 16) Ba câu hỏi quan trọng để tự kiểm tra đã hiểu chưa

### Câu 1: Dữ liệu gì đi vào?

Chuỗi thời gian của trạng thái, và tốt nhất là cả đạo hàm hoặc khả năng ước lượng đạo hàm.

### Câu 2: Model học cái gì?

Model học ma trận hệ số sparse (\Xi) trên một thư viện đặc trưng (\Theta(X)).

### Câu 3: Model trả ra cái gì?

Model trả ra một hệ phương trình ODE rõ ràng, ví dụ:

[
\dot x = -x + 2y
]

---

## 17) Gợi ý học tiếp

Sau khi hiểu phần này, hai bước tiếp theo hợp lý nhất là:

1. Làm một ví dụ số đầy đủ với ma trận (\Theta(X)), (\Xi), thresholding từng bước
2. Viết code PySINDy cho một hệ như Lotka–Volterra hoặc Lorenz và kiểm tra toàn bộ pipeline
