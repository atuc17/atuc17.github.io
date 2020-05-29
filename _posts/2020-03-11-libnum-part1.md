---
layout: post
title: thư viện cryptography CTF viết bằng Python
categories: [cryptography]
---
Đây là project viết các hàm mình thường sử dụng khi chơi CTF mảng cryptography.

Project này bao gồm các module: 
1. common.py: gồm các 
- hàm tính ước chung lớn nhất d của 2 số nguyên a và b
- tìm 2 số nguyên x, y thỏa mãn ax + by = d với a, b như trên
- mở rộng hàm trên để tính nghịch đảo của số a trong modulo n
- hàm tính căn bậc n của số nguyên lớn a 

Đầu tiên là hàm **gcd(a, b)**, hàm này trả về ước chung lớn nhất của 2 số a, b bằng thuật toán Euclid dựa trên nhận xét sau: gcd(a, b) = gcd(b, a%b)

Nhận xét trên có đúng không? 

Ta có nhận xét sau: với 2 số x, y bất kì (x > y), ta luôn viết được dưới dạng x = py + q (với 0 <= q < y, y là số chia và q là thương). Giả sử a > b, vậy a = pb + q với 0 <= q < b. Nếu d = gcd(a, b) thì (a-b) chia hết d, Vậy nếu đặt a = r0, b = r1, thì:

r0 = k1 * r1 + r2

r1 = k2 * r2 + r3

r2 = k3 * r3 + r4

.......

r(p-1) = k(p) * r(p) ====> số dư là 0