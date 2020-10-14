---
layout: post
title: Các phương pháp xử lý NaN trong dữ liệu.
excerpt: "Khi thực hiện các dự án phân tích dữ liệu với môi trường dữ liệu thật, tôi thấy rào cản lớn nhất mà có thể làm thiên lệch đi mọi phân tích đã thực hiện chính là các giá trị NaN.
Vậy NaN là gì?
NaN là viết tắt của Not a Number, là một giá trị đặc biệt trong dữ liệu, biểu thị việc dữ liệu bị khuyết thiếu. Trong mỗi ngôn ngữ lập trình/Framework khác nhau, việc thể hiện các dữ liệu khuyết thiếu này có thể khác nhau, ví dụ trong Python, các giá trị None được hiểu là các giá trị bị khuyết thiếu.
"
categories: [General Data Preprocessing]
comments: true
---
Khi thực hiện các dự án phân tích dữ liệu với môi trường dữ liệu thật, tôi thấy rào cản lớn nhất mà có thể làm thiên lệch đi mọi phân tích đã thực hiện chính là các giá trị NaN.

## Vậy NaN là gì?

NaN là viết tắt của Not a Number, là một giá trị đặc biệt trong dữ liệu, biểu thị việc dữ liệu bị khuyết thiếu. Trong mỗi ngôn ngữ lập trình/Framework khác nhau, việc thể hiện các dữ liệu khuyết thiếu này có thể khác nhau, ví dụ trong Python, các giá trị None được hiểu là các giá trị bị khuyết thiếu.
Bạn đọc có thể nghĩ các giá trị NaN chỉ đơn giản là 0 vì nó thể hiện sự khuyết thiếu của dữ liệu. Đây chính là 1 cái bẫy đối với người làm dữ liệu. Sự khác biệt giữa 0 và NaN ở chỗ 0 là một giá trị dạng số đã xác định còn NaN đơn giản là giá trị đó chưa được xác định là bao nhiêu. 

## Các giá trị NaN nguy hiểm như thế nào?

Có 2 tác động chính của các giá trị NaN đến quá trình phân tích dữ liệu/mô hình hóa dữ liệu như sau:

- Tạo ra sự thiên lệch, dẫn đến thiếu chính xác trong việc tính toán các giá trị thống kê như trung bình, trung vị, … Từ đó, cung cấp sai thông tin cho người đọc dữ liệu
- Các thuật toán sử dụng trong phần mô hình hóa dữ liệu không có chức năng xử lý dữ liệu bị khuyết thiếu. Dữ liệu đầu vào cho các thuật toán này buộc phải đầy đủ



## Làm thế nào để xử lý các dữ liệu bị khuyết thiếu?
Từ 2 tác động nêu ra phía trên, ta đã thấy được mức độ khó chịu của các giá trị NaN như thế nào với một người làm khoa học dữ liệu. Vậy làm thế nào để xử lý các giá trị này? Dưới đây là một vài giải pháp cho vấn đề:
- Loại bỏ các bản ghi chứa dữ liệu khuyết thiếu trước khi đi vào phân tích hoặc mô hình hóa. Tuy nhiên đây không phải là một sự lựa chọn tốt, vì theo cách này, ta đã đánh đổi bằng việc làm mất mát thông tin, đặc biệt khi làm việc với các bộ dữ liệu nhỏ
- Thay thế dữ liệu NaN bằng các giá trị xác định. Trong bản tin này sẽ đi sâu vào phương pháp điền giá trị này.

Có rất nhiều cách để điền vào các giá trị bị khuyết thiếu. Dưới đây là một vài phương pháp phổ biến:
- Thay thế bằng các giá trị cụ thể (ví dụ 0)
- Thay thế bằng các con số đại diện cho dữ liệu (ví dụ như trung bình, trung vị)
- Sử dụng phương pháp MICE hoặc KNN

Phần tiếp theo, ta sẽ tìm hiểu về các phương pháp, tiến hành thử nghiệm và đánh giá mức độ hiệu quả của từng phương pháp

## Thiết kế thí nghiệm
Để đánh giá tính hiệu quả của việc thay thế dữ liệu khuyết thiếu, ở đây, tôi sử dụng bộ dữ liệu IRIS về các loài hoa. Tiến hành thay thế ngẫu nhiên các giá trị thuộc tính của 10% số lượng bản ghi thành NaN. Sau đó tiến hành khôi phục lại bằng các phương pháp kể trên và đánh giá sai số của các giá trị được điền vào dựa trên thước đo MSE.

### Phương pháp 1: Điền bằng 1 giá trị xác định

Trong phương pháp này, ta sẽ thay thế các giá trị NaN bằng 0. Trong một vài trường hợp, nó có thể là ý tưởng tốt, như với dữ liệu tiêu dùng cho một sản phẩm, nếu bị khuyết thiếu, ta có thể xem như việc không tiêu dùng tiền cho sản phẩm đó. Tuy nhiên trong 1 số trường hợp, nó có thể là ý tưởng tồi, như về dữ liệu tuổi chẳng hạn.
Hình dưới đây thể hiện các giá trị NaN (dấu *) sau khi đã được điền

<img src="/img/nan-handling-replace-0.png" alt="nan-handling-by-replace-0" width="200">


### Phương pháp 2: Điền bằng giá trị đại diện cho dữ liệu

Với phương án này, ta sẽ tính toán các giá trị đại diện cho dữ liệu (không tính các giá trị NaN) và điền vào các vị trí khuyết thiếu những giá trị này. Các giá trị đại diện như trung bình có tính chất rất thú vị, nó không hề thay đổi sau khi ta thêm các giá trị mean này vào dữ liệu. Trong hình vẽ dưới đây, ta có thể thấy được, sau khi xử lý dữ liệu bị khuyết thiếu, phân bố dữ liệu dường như không thay đổi so với các bản ghi chứa đầy đủ dữ liệu.


<img src="/img/nan-handling-replace-stat.png" alt="nan-handling-by-replace-stat" width="200">

### Phương pháp 3: Thay thế bằng phương pháp MICE hoặc KNN
MICE là viết tắt của Multiple Imputation by Chained Equations. Ý tưởng của phương pháp này xuất phát từ: Dữ liệu trong cột bị khuyết thiếu có thể biểu diễn theo giá trị của các cột còn lại. Tức ta có thể xây dựng một mô hình hồi quy/phân loại với biến mục tiêu là biến chứa dữ liệu khuyết thiếu, các biến đầu vào là các cột chứa dữ liệu đầy đủ còn lại, từ đó có thể dự đoán ra giá trị của các điểm dữ liệu khuyết thiếu. Phương pháp này được thể hiện như trong Hình 3 dưới đây.

<img src="/img/nan-handling-replace-mice.png" alt="nan-handling-by-replace-mice" width="200">


Với phương pháp KNN, ta có thể xác định được k bản ghi là hàng xóm của bản ghi chứa dữ liệu bị thiếu. Sau đó điền vào phần dữ liệu khuyết thiếu giá trị trung bình của những bản ghi hàng xóm này. Mô phỏng thực nghiệm của phương pháp này như trong Hình 4 phía dưới.
    

<img src="/img/nan-handling-replace-knn.png" alt="nan-handling-by-replace-knn" width="200">

## Đo lường hiệu quả của xử lý NaN

Ta tiến hành khôi phục lại điểm dữ liệu NaN bằng các phương pháp kể trên và đánh giá sai số của các giá trị được điền vào dựa trên thước đo MSE.

Đánh giá kết quả thử nghiệm của các phương pháp trên được cho bởi bảng dưới đây:
<img src="/img/handle-nan-result.png" alt="handle-nan-result" width="200">

Ta có thể thấy hầu hết trong các trường hợp, trong bộ dữ liệu này, phương pháp MICE hoặc KNN cho kết quả tốt nhất.

## Kết luận: 
Trong bài viết này, ta đã được tiếp cận với các phương án xử lý dữ liệu khuyết thiếu cũng như phương pháp để đánh giá kết quả xử lý. Bạn đọc có thể tiến hành các thử nghiệm cũng như đánh giá để tìm ra phương pháp phù hợp nhất cho bài toán của mình./

