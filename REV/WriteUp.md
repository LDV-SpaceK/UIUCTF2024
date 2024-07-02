# Summarize

![image](https://github.com/LDV-SpaceK/UIUCTF2024/assets/138242812/497f9e65-a95f-4ed6-b3cb-3a9644059edc)

Check file và ta có được vài thông tin cơ bản của file này.

![image](https://github.com/LDV-SpaceK/UIUCTF2024/assets/138242812/0c4e7e61-72ff-497c-a5df-3f26958e7b5d)

Bắt tay vào phân tích nó nào. Ở đây tôi thấy logic khá đơn giản của bài này là nhập 6 số đầu vào sau đó kiểm tra nó và in ra @@.

![image](https://github.com/LDV-SpaceK/UIUCTF2024/assets/138242812/478b2016-feff-4514-9872-9f8455d9a0a5)

Kiểm tra kĩ hơn phần check 6 số. Ta thấy được có 1 hàm kiểm tra các giá trị đầu vào xem nó nằm xong khoảng `0x5F5E100` và `0x3B9AC9FF` hay không

![image](https://github.com/LDV-SpaceK/UIUCTF2024/assets/138242812/34fac064-8355-4d23-981c-15ddd4990ecf)

Tại đây tôi sẽ phân tích cụ thể hàm `sub_40163D`. Hàm này nó lặp tới khi nào a1 và a2 bằng 0. Bản chất thật sự của hàm này là thực hiện phép cộng nhị phân của hai số a1 và a2, và trả về kết quả của phép cộng đó. 
* Công nhị phân là : https://phannhatchanh.com/blog/cac-phep-toan-co-ban-tren-he-nhi-phan

![image](https://github.com/LDV-SpaceK/UIUCTF2024/assets/138242812/fddefa72-3fa8-4cc6-9b45-07ea0218ef84)

Tương tự như các hàm còn lại tôi đã sâu chuỗi lại và dùng z3 để tìm ra các giá trị của nó. 

```
from z3 import *

solver = Solver()

a1 = BitVec('a1', 32)
a2 = BitVec('a2', 32)
a3 = BitVec('a3', 32)
a4 = BitVec('a4', 32)
a5 = BitVec('a5', 32)
a6 = BitVec('a6', 32)


solver.add(a1 > 0x5F5E100, a2 > 0x5F5E100, a3 > 0x5F5E100, a4 > 0x5F5E100, a5 > 0x5F5E100, a6 > 0x5F5E100)
solver.add(a1 <= 0x3B9AC9FF, a2 <= 0x3B9AC9FF, a3 <= 0x3B9AC9FF, a4 <= 0x3B9AC9FF, a5 <= 0x3B9AC9FF, a6 <= 0x3B9AC9FF)

v7 = a1 - a2
v18 = (v7 + a3) % 0x10AE961
v19 = (a1 + a2) % 0x1093A1D
v8 = 2 * a2
v9 = 3 * a1
v10 = v9 - v8
v20 = v10 % (a1 ^ a4)
v11 = a3 + a1
v21 = (a2 & v11) % 0x6E22
v22 = (a2 + a4) % a1
v12 = a4 + a6
v23 = (a3 ^ v12) % 0x1CE628
v24 = (a5 - a6) % 0x1172502
v25 = (a5 + a6) % 0x2E16F83

solver.add(v18 == 4139449, v19 == 9166034, v20 == 556569677, v21 == 12734, v22 == 540591164, v23 == 1279714, v24 == 17026895, v25 == 23769303)


if solver.check() == sat:
    model = solver.model()
    result = {d.name(): model[d].as_long() for d in model.decls()}
    print(result)
else:
    print("No solution found")
```

![image](https://github.com/LDV-SpaceK/UIUCTF2024/assets/138242812/1b905d64-b72f-4071-afba-87395486b7eb)

và tôi có flag là : `uiuctf{2a142dd72e87fa9c1456a32d1bc4f77739975e5fcf5c6c0}`

