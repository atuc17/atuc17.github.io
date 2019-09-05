---
layout: post
title: Introduction to Cryptograph
categories: [cryptography]
---
Khi nhắc tới toán, hẳn nhiều bạn sẽ nghĩ: "Một môn học đầy công thức và tính toán nhức cả đầu". 
Thực tế đúng là vậy =)))) Tất nhiên mật mã học, 1 lĩnh vực dựa trên toán, cũng gây đau đầu không kém.

Nhưng mật mã bao quanh chúng ta, từ những trò chơi mật thư, đến kiểu gõ Telex tiếng Việt, những lá thư tình chả ai đọc được, hay thậm chí teencode. Trong lý thuyết thông tin, các hệ mật mã giúp bảo vệ an toàn và riêng tư cho người dùng internet, hoặc để tạo ra các mã độc tống tiền
(wannacry). 

Mật mã học (**cryptography**) là một lĩnh vực nghiên cứu các loại mật mã đã được phát minh, hoặc tạo ra mật mã mới, hoặc phá các loại mật mã.
Có thể ban đầu hơi khó nhằn (đúng tính chất của nó), nhưng mình mong series này sẽ giúp các bạn tiếp cận được mật mã học và bổ sung 1 số kiến 
thức cần thiết cho các project của các bạn.

Về các khái niệm và yếu tố của 1 hệ mật mã (**cryptosystem**), các bạn có thể tham khảo quyển ***Bài giảng An toàn và bảo mật thông tin*** 
của Đại học Nha Trang. Ở đây mình xin phép giới thiệu các cách tiếp cận để phá các mật mã minh họa bằng các challenge CTF.

Như mình đã nói, mật mã học dựa trên toán, vì vậy 1 số kiến thức toán cơ bản các bạn có thể tìm hiểu trước khi vào series bao gồm:
- Thuật toán Euclid/ Thuật toán Euclid mở rộng (**Extended Euclidean Algorithm**)
- Định lý Euler/ Định lý Fermat nhỏ
- Định lý số dư Trung Hoa (cách tìm nghiệm)
- Các phép toán trên ma trận

Nếu chưa hiểu thì vẫn không sao, trong các bài mở đầu mình sẽ cố gắng giải thích. Mình mong các bạn sẽ có cái nhìn mới về mật mã học, 
và tầm quan trọng của nó trong ngành mình (đặc biệt là các ngành IT).
