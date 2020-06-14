---
layout: post
title: Biểu diễn từ trong các bài toán xử lý ngôn ngữ tự nhiên.
excerpt: "Biểu diễn từ là 1 trong những bài toán đầu tiên gặp phải của người làm NLP. 
Trước khi đưa dữ liệu vào các mô hình học máy về NLP, việc đầu tiên ta cần làm là mã hoá các dữ liệu text này thành dữ liệu dạng số.
Việc mã hoá này với mục đích biểu diễn các từ về 1 không gian vector D chiều, trong đó, 
mỗi chiều của không gian vector này biểu diễn 1 khía cạnh về mặt ngữ nghĩa của từ. Ví dụ như: khía cạnh thời gian (quá khứ, hiện tại, tương lai), 
khía cạnh thái độ (phũ phàng, tôn trọng,...)

Trong bài viết này, ta sẽ tìm hiểu các phương pháp để xây dựng không gian vector này và việc biểu diễn từ trong không gian vector đó."
categories: [NLP]
comments: false
---
{% katexmm %}
Biểu diễn từ là 1 trong những bài toán đầu tiên gặp phải của người làm NLP. 
Trước khi đưa dữ liệu vào các mô hình học máy về NLP, việc đầu tiên ta cần làm là mã hoá các dữ liệu text này thành dữ liệu dạng số.
Việc mã hoá này với mục đích biểu diễn các từ về 1 không gian vector $D$ chiều, trong đó, 
mỗi chiều của không gian vector này biểu diễn 1 khía cạnh về mặt ngữ nghĩa của từ. Ví dụ như: khía cạnh thời gian (quá khứ, hiện tại, tương lai), 
khía cạnh thái độ (phũ phàng, tôn trọng,...)

Trong bài viết này, ta sẽ tìm hiểu các phương pháp để xây dựng không gian vector này và việc biểu diễn từ trong không gian vector đó.
## One-hot vector.

Biểu diễn từ dưới dạng **one-hot vector** là phương pháp biểu diễn đơn giản nhất. Khi đó, mỗi từ sẽ được mã hoá thành 1 vector $|V|$ chiều ($V$ là tập từ vựng, $|V|$ là kích thước của tập từ vựng) với $|V|-1$ giá trị 0 và giá trị 1 duy nhất ở vị trí có số thứ tự trùng với vị trí của từ đó trong tập từ vựng.
Ta có thể nhìn trực quan như dưới đây:

$$
w^{anh} = \begin{bmatrix}1 \\0 \\0 \\. \\. \\. \\0\end{bmatrix}, \quad
w^{ánh} = \begin{bmatrix}0 \\1 \\0 \\. \\. \\. \\0\end{bmatrix}, \quad
..., \quad
w^{yêu} =\begin{bmatrix}0 \\0 \\0 \\. \\. \\. \\1\end{bmatrix}
$$

Với cách biểu diễn đơn giản này, ta biểu diễn mỗi từ như 1 đối tượng độc lập. Cách biểu diễn này không cho ta thấy trực tiếp được mối liên hệ giữa các từ.
Phương pháp này còn có yếu điểm về việc số chiều của dữ liệu sẽ quá lớn khi đối mặt với 1 bộ từ vựng có kích thước lớn & tính thưa thớt của dữ liệu.


## Phương pháp dựa trên phân tích SVD.

Dựa trên tư tưởng rằng: Các từ có ý nghĩa giống nhau thường xuất hiện cùng các từ giống nhau. Ví dụ: xét 2 câu:
- Cô gái này rất xinh đẹp.
- Cô gái này rất xinh xắn.

Hai từ `xinh đẹp`, `xinh xắn` đều cùng xuất hiện với các từ: `cô gái`,`này`, `rất` nên có khả năng có ý nghĩa giống nhau. Các phương pháp này thường xây dựng các vector biểu diễn từ theo mô típ: chạy lặp qua cả bộ dữ liệu văn bản để đếm số lần xuất hiện cùng nhau của các cặp từ, từ đó xây dựng ma trận các từ xuất hiện đồng thời (word co-occurence matrix) $X$, sau đó thực hiện phân tích SVD với ma trận X này: $X = U_k\Sigma_k V_k^T$. Cuối cùng, lấy chính các dòng của ma trận $U$ làm vector biểu diễn từ tương ứng.

### Xây dựng ma trận từ xuất hiện đồng thời

Các biến thể trong xây dựng Word co-occurence Matrix X xuất phát từ tiêu chí xác định 2 từ xuất hiện đồng thời. Cụ thể, ta có thể coi 2 từ xuất hiện là đồng thời nếu nằm trong cùng 1 văn bản, hoặc nếu 2 từ nằm cách nhau không quá $k$ từ.

> Câu hỏi rằng: Tại sao các dòng của ma trận $U_k$ lại có thể sử dụng để biểu diễn từ? Liệu có thể thể hiện được mối liên quan giữa các từ hay không?

Ta sẽ xây dựng câu trả lời cho câu hỏi này dựa trên hướng tiếp cận sau. Giả sử ta chỉ được sử dụng 1 khía cạnh duy nhất để biểu diễn ý nghĩa của từ. Khi đó, mỗi từ sẽ được biểu diễn bằng 1 con số, thể hiện mức độ mạnh yếu của khía cạnh ứng với từ đó. Một khía cạnh được biểu diễn thành 1 vector trong không gian $R^{|D|}$ thể hiện phân bố của từ trong khía cạnh đó. Ví dụ khía cạnh `gia đình` sẽ có phân bố tập trung vào các từ như: `vợ, chồng, anh, em, bố, mẹ, họ hàng,...`. Ta có thể hiểu nôm na như việc xấp xỉ ma trận $X$ thành tích 2 vector $uv^T$,$u, v \in R^{|V|}$ với $v$ là vector thể hiện khía cạnh. Việc xây dựng khía cạnh này để đảm bảo việc thể hiện tốt nhất tính chất của bộ dữ liệu, chính vì thế việc xấp xỉ ma trận X thành tích $uv^T$ phải được thực hiện theo cách tốt nhất. Theo Eckart–Young–Mirsky theorem[^1], Truncated SVD là phương pháp giúp xấp xỉ ma trận tốt nhất (best low-rank approximation). Do đó, ở đây ta sẽ sử dụng SVD để xác định 2 vector $u$ và $v$. Tiếp tục mở rộng theo hướng suy nghĩ này, ta sẽ sử dụng k khía cạnh để biểu diễn 1 từ. Tương tự như trên, vector biểu diễn mỗi khía cạnh này và vector thể hiện trọng số của từng khía cạnh ứng với các từ sẽ được dựng từ phương pháp SVD. Từ đây, ta có thể thấy được các dòng của ma trận $U_k$ phù hợp để biểu diễn vector hoá các từ.

## Vector hoá bằng phương pháp lặp.

Ý tưởng: xây dựng 1 mô hình mà các tham số của mô hình chính là các word vectors. Thực hiện huấn luyện mô hình với 1 hàm mục tiêu. Cụ thể, tại mỗi vòng lặp, thực hiện tính toán sai số (giá trị của hàm mục tiêu), cập nhật lại tham số cho mô hình để giảm sai số. Quá trình lặp này có thể được thực hiện đến khi sai số của mô hình hội tụ. Khi đó, mô hình đã học được cách biểu diễn vector cho các từ. Trong bài này, ta sẽ đi vào 2 phương pháp cụ thể: Word2Vec và GloVe.

### Word2Vec

Cốt lõi của phương pháp này nằm ở 
- 2 mô hình: CBOW & Skip-gram
- 2 kỹ thuật: Negative Sampling & Hierarchical Softmax

**Continuous Bag of Words (CBOW)** là mô hình được xây dựng với mục đích dự báo khả năng xuất hiện của 1 từ với ngữ cảnh cho trước. Ngữ cảnh được hiểu là tập các từ xuất hiện xung quanh với khoảng cách nhỏ hơn 1 ngưỡng $m$ cho trước. Ví dụ, ngữ cảnh của từ `chính` trong câu `Thủ tướng Chính phủ Việt Nam` với $m=2$ là {`thủ, tướng, phủ, việt`}

Kiến trúc của mô hình này như sau:
<img src="/img/cbow.png" alt="CBOW Architecture" width="200">

*Hình 1[^2]: Kiến trúc CBOW*

Trong đó, $x_{1k}$ đến $x_{Ck}$ là các vector one-hot trong $R^{|V|}$, $W_{V\times N}$ là ma trận tham số, thể hiện cách biểu diễn vector của toàn bộ các từ trong bộ từ vựng $V$, Output $y \in R^{D}$ của mô hình chính là phân bố của từ mục tiêu (từ cần dự đoán).

Thông qua việc huấn luyện mô hình này, ta sẽ thu về ma trận $W_{VxN}$.

**Skip-gram** là mô hình được xây dựng với mục đích dự báo về ngữ cảnh của 1 từ cho trước, là bài toán ngược với CBOW.

Kiến trúc của mô hình này như sau:
<img src="/img/skip-gram.png" alt="Skip-gram Architecture" width="200">

*Hình 2[^2]: Kiến trúc Skip-gram*

Như phần trên đã trình bày, ta có thể huấn luyện mô hình CBOW hoặc Skip-gram là đã có thể thu được bộ giá trị vector ứng với các từ. Tuy nhiên, có phát sinh vấn đề sau trong quá trình huấn luyện mô hình: Việc tính toán phân bố của từ mục tiêu bằng softmax là quá đắt khi số lượng từ vựng lớn.

Cụ thể, hàm softmax được cho bởi:

$$p(x_i) = \frac{e^{x_i}}{\sum_{j=1}^{|V|} e^{x_j}}$$

*Word2Vec* đề xuất 2 ý tưởng để có thể khắc phục được vấn đề này, gồm Negative Sampling và Hierarchical Softmax

**Negative Sampling**

Với ý tưởng này, thay vì sử dụng output là phân bố xác suất của từ mục tiêu, ta chỉnh sửa mô hình với output thành xác định **1 từ** có phải là từ mục tiêu hay không. Tức, ở đây thay vì phải tính toán giá trị của hàm *softmax*, ta chỉ cần tính toán giá trị của hàm *sigmoid*. Nhưng, **1 từ** ở đây là từ nào? Nếu như là toàn bộ các từ trong từ vựng, vậy thì hiệu quả tính toán so với sử dụng **softmax** là bằng **0**. Negative Sampling, như tên gọi, chỉ thực hiện lấy sample $k$ từ sai từ bộ từ vựng. Phân bố xác suất của việc lấy ra các từ, qua kết quả thực nghiệm, tốt nhất là lấy từ Unigram Language Model (tức phân bố của các từ trong bộ dữ liệu) mũ 3/4 [^3].

**Hierarchical Softmax**

*Hierarchical Softmax* sử dụng cây nhị phân, với các lá ứng với các từ. Khi đó, xác suất xảy ra 1 từ sẽ được tính toán với độ phức tạp $O(log(|V|))$ thay vì $O(|V|)$ như *softmax*. Đánh đổi lại, mô hình sẽ cần học thêm cách biểu diễn vector của các node trong cây. 

<img src="/img/hierarchical-softmax.png" alt="Hierarchical Softmax" width="200">

*Hình 3[^2]: Biểu diễn cây nhị phân cho Hierarchical Softmax*

Việc dựng cây này cũng có thể được tối ưu hơn thông qua việc sử dụng phân bố xác suất xuất hiện của các từ, từ đó, dựng lên cây Huffmann.

### GloVe

[^1]: [Low-rank Approximation](https://en.wikipedia.org/wiki/Low-rank_approximation)
[^2]: [Natural Language Processing with Deep Learning- CS224n](http://web.stanford.edu/class/cs224n/index.html#schedule)
[^3]: [Efficient estimation of word representations in vector space](https://arxiv.org/abs/1301.3781)


{% endkatexmm %}