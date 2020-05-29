---
layout: post
title: Máy tính chạy như thế nào?
categories: [reverse]
---

Đàn anh luôn bảo học reverse nên hiểu rõ cách mã assembly chạy thay vì cứ dùng tool để decompile chương trình. Sau đây mình sẽ thử nghịch 1 bài reverse nho nhỏ, coi như mình nhập môn reverse engineering :v

Các bạn của mình có 1 bài lab reverse khá thú vị, và thay vì dùng IDA để decompile như các bạn ấy thì mình sẽ demo cách mình reverse bài này bằng mã assembly và debug.

Binary các bạn có thể tìm thấy tại [đây](/binaries/bomb.zip)

Chạy thử nào :D Có vẻ như nó bắt nhập gì đó, gõ đại "helloworld" xem sao!

![main](/assets/bomb/re1.png)

Toang rồi! Bây giờ mình sẽ thử debug bằng gdb, nhưng dùng gdb khá ......... tốn công, nên mình dùng thêm peda.

Đầu tiên cần xem hàm main bằng lệnh **disass main**

Ok, hàm main cũng không có vẻ khó chịu lắm, chạy thử xem sao đã, dùng lệnh **start**

Chương trình nhảy tới <main+20>, dùng lệnh **next** để nhảy tới địa chỉ tiếp theo (những lần sau chỉ cần Enter là được).

Ở <main+154> chương trình gọi hàm **initialize_bomb**, mình thử xem hàm bằng **disass initialize_bomb** thì cũng không thấy gì đặc biệt. Chạy tiếp nào :)))

Ở <main+191>, chương trình gọi hàm **read_line** và mình cần nhập 1 chuỗi, mình nhập *helloworld* như hồi nãy, chạy tiếp và chương trình fail??? Sau khi gọi hàm **read_line** xong, giá trị con trỏ tới chuỗi vừa nhập (nằm trong **eax**) được gán vào [ebp - 0xc], sau đó giá trị ở [ebp - 0xc] được push vào stack, làm tham số cho hàm **phase_1**.

### Phase_1

```assembly
gdb-peda$ disass phase_1
Dump of assembler code for function phase_1:
   0x08048c20 <+0>:     push   ebp
   0x08048c21 <+1>:     mov    ebp,esp
   0x08048c23 <+3>:     sub    esp,0x8
   0x08048c26 <+6>:     sub    esp,0x8
   0x08048c29 <+9>:     push   0x804a478
   0x08048c2e <+14>:    push   DWORD PTR [ebp+0x8]
   0x08048c31 <+17>:    call   0x804915c <strings_not_equal>
   0x08048c36 <+22>:    add    esp,0x10
   0x08048c39 <+25>:    test   eax,eax
   0x08048c3b <+27>:    je     0x8048c42 <phase_1+34>
   0x08048c3d <+29>:    call   0x80493c4 <explode_bomb>
   0x08048c42 <+34>:    nop
   0x08048c43 <+35>:    leave
   0x08048c44 <+36>:    ret
End of assembler dump.
```

Ở địa chỉ **0x08048c2e** push [ebp + 0x8] vào stack để làm tham số cho hàm **strings_not_equal** chính là chuỗi mình vừa nhập, vậy thì giá trị được push trước đó (**0x804a478**) cũng là chuỗi gì đó, để so sánh với chuỗi của mình.

Muốn biết, mình đặt breakpoint ở lệnh push đầu, dùng lệnh **b *0x08048c29** sau khi đã nhập xong helloworld, và gõ **next** để nhảy tới breakpoint đó.

![phase_1](/assets/bomb/re6.png)

Gõ Enter để tới lệnh kế tiếp, mình thấy rằng tham số kia chính là chuỗi **I am just a renegade hockey mom.**, nếu tiếp tục Enter mình tới được lệnh **call strings_not_equal**, guessed arguments của peda cũng là chuỗi đó :))) 

Chuyện tiếp theo thì dễ hiểu rồi, không phải chuỗi đó thì hàm **explode_bomb** được gọi, và exit chương trình.

Chạy lại chương trình bằng lệnh **start** và tới lúc nhập thì nhập chuỗi này vào. Lần này **explode_bomb** không được gọi và thoát khỏi hàm **phase_1** thành công. Tiếp tục enter. Tới <main+234> lại thêm 1 hàm **read_line**, mình nhập chuỗi **1 2 3 4** và xem thử hàm **phase_2** ngay đó.

### Phase_2

```assembly
gdb-peda$ disass phase_2
Dump of assembler code for function phase_2:
   0x08048c45 <+0>:     push   ebp
   0x08048c46 <+1>:     mov    ebp,esp
   0x08048c48 <+3>:     sub    esp,0x38
   0x08048c4b <+6>:     mov    eax,DWORD PTR [ebp+0x8]
   0x08048c4e <+9>:     mov    DWORD PTR [ebp-0x2c],eax
   0x08048c51 <+12>:    mov    eax,gs:0x14
   0x08048c57 <+18>:    mov    DWORD PTR [ebp-0xc],eax
   0x08048c5a <+21>:    xor    eax,eax
   0x08048c5c <+23>:    sub    esp,0x8
   0x08048c5f <+26>:    lea    eax,[ebp-0x24]
   0x08048c62 <+29>:    push   eax
   0x08048c63 <+30>:    push   DWORD PTR [ebp-0x2c]
   0x08048c66 <+33>:    call   0x80490dc <read_six_numbers>
   0x08048c6b <+38>:    add    esp,0x10
   0x08048c6e <+41>:    mov    eax,DWORD PTR [ebp-0x24]
   0x08048c71 <+44>:    test   eax,eax
   0x08048c73 <+46>:    jns    0x8048c7a <phase_2+53>
   0x08048c75 <+48>:    call   0x80493c4 <explode_bomb>
   0x08048c7a <+53>:    mov    DWORD PTR [ebp-0x28],0x1
   0x08048c81 <+60>:    jmp    0x8048ca6 <phase_2+97>
   0x08048c83 <+62>:    mov    eax,DWORD PTR [ebp-0x28]
   0x08048c86 <+65>:    mov    eax,DWORD PTR [ebp+eax*4-0x24]
   0x08048c8a <+69>:    mov    edx,DWORD PTR [ebp-0x28]
   0x08048c8d <+72>:    sub    edx,0x1
   0x08048c90 <+75>:    mov    ecx,DWORD PTR [ebp+edx*4-0x24]
   0x08048c94 <+79>:    mov    edx,DWORD PTR [ebp-0x28]
   0x08048c97 <+82>:    add    edx,ecx
   0x08048c99 <+84>:    cmp    eax,edx
   0x08048c9b <+86>:    je     0x8048ca2 <phase_2+93>
   0x08048c9d <+88>:    call   0x80493c4 <explode_bomb>
   0x08048ca2 <+93>:    add    DWORD PTR [ebp-0x28],0x1
   0x08048ca6 <+97>:    cmp    DWORD PTR [ebp-0x28],0x5
   0x08048caa <+101>:   jle    0x8048c83 <phase_2+62>
   0x08048cac <+103>:   nop
   0x08048cad <+104>:   mov    eax,DWORD PTR [ebp-0xc]
   0x08048cb0 <+107>:   xor    eax,DWORD PTR gs:0x14
   0x08048cb7 <+114>:   je     0x8048cbe <phase_2+121>
   0x08048cb9 <+116>:   call   0x8048830 <__stack_chk_fail@plt>
   0x08048cbe <+121>:   leave
   0x08048cbf <+122>:   ret
End of assembler dump.
```

```
2 lệnh
0x08048c4b <+6>:     mov    eax,DWORD PTR [ebp+0x8]; 
0x08048c4e <+9>:     mov    DWORD PTR [ebp-0x2c],eax
 thực ra là lấy con trỏ tới chuỗi đã nhập lúc nãy gán vào 1 biến cục bộ
 ```

 Sau đó sẽ có 1 hàm **read_six_numbers** với tham số là chuỗi vừa nhập và 1 biến cục bộ địa chỉ [ebp - 0x24].

 Mình sẽ đặt breakpoint tại lệnh **push eax**. 

 ![phase_2](/assets/bomb/re8.png)

 Hàm **read_six_numbers**, đúng như tên gọi là input 6 số nguyên, các bạn có thể disass hàm đó. Vì mình nhập không đủ nên bị exit. Bây giờ nhập lại **1 2 3 4 5 6**. 6 số này được gán theo stack từ địa chỉ [ebp - 0x24] đi lên.

 **Lưu ý**: khi các bạn không thoát khỏi gdb bằng lệnh **quit** thì những lần sau chạy **start**, breakpoint vẫn ở nguyên nhưng nếu quit rồi vào lại gdb thì breakpoint sẽ bị reset (mất hết).

 Mình có kinh nghiệm là các lệnh cmp, jmp và add 0x1 thường sẽ là vòng lặp. Ở đây mình có **0x08048c7a <+53>:    mov    DWORD PTR [ebp-0x28],0x1** rồi tới lệnh **0x08048c81 <+60>:    jmp    0x8048ca6 <phase_2+97>**, tính toán 1 hồi lại thấy bộ lệnh

 ```assembly
0x08048ca2 <+93>:    add    DWORD PTR [ebp-0x28],0x1
0x08048ca6 <+97>:    cmp    DWORD PTR [ebp-0x28],0x5
0x08048caa <+101>:   jle    0x8048c83 <phase_2+62>
```

Mình gọi tạm biến cục bộ ở [ebp - 0x28] là i, thì dựa trên 5 lệnh này mình rút ra 1 thứ mà nhập môn lập trình ai cũng học: 

```C++
for (i = 1; i <= 5; i++) { 
    // tính toán gì đó ở giữa các lệnh này 
}
```

Bây giờ mình bắt đầu chạy từ vị trí **0x8048c7a <phase_2+53>** là chỗ gán i = 1 ấy. Sau khi so sánh i và 5 thì bắt đầu tính toán ở <phase_2+62>

```assembly
0x08048c83 <+62>:    mov    eax,DWORD PTR [ebp-0x28]
0x08048c86 <+65>:    mov    eax,DWORD PTR [ebp+eax*4-0x24]
0x08048c8a <+69>:    mov    edx,DWORD PTR [ebp-0x28]
0x08048c8d <+72>:    sub    edx,0x1
0x08048c90 <+75>:    mov    ecx,DWORD PTR [ebp+edx*4-0x24]
0x08048c94 <+79>:    mov    edx,DWORD PTR [ebp-0x28]
0x08048c97 <+82>:    add    edx,ecx
0x08048c99 <+84>:    cmp    eax,edx
0x08048c9b <+86>:    je     0x8048ca2 <phase_2+93>
0x08048c9d <+88>:    call   0x80493c4 <explode_bomb>
```

Nếu chịu khó phân tích thì các bạn sẽ thấy quy luật là trong 6 số nguyên vừa nhập thì số_sau = số_trước + i - 1 :))) nhưng lúc mình làm thì do 1 tí "lỡ tay". Do mình enter quá đà, tới lệnh **cmp eax,edx** thì mình thấy eax và edx đều là 1 (giống như input). Từ đây mình chỉ chú ý vào lệnh này. Lặp lần 2 vẫn ok, tới lần 3 eax là 3 còn edx là 4!!! Toang rồi! Và mình bị exit bởi hàm **explode_bomb**

Thử lại vận may :))) lần này input 1 2 4 5 6 7. Tiếp tục quá trình trên, mình toang tại lần lặp 4, eax là 5 và edx là 7 :))) Từ 1 mà ra 1, 2, 4, 7 thì cũng khá rõ ràng rồi, số sau là số trước + i - 1. Nhưng loop chỉ check 5 số, còn số cuối?

Lúc nãy mình đã nói ở vị trí [ebp - 0x2c] là con trỏ tới mảng 6 số nguyên. Vậy -0x24 là a[0], -0x20 là a[1], ....., -0x10 là a[4] và -0xc là a[5]. Ở <phase_2+104> mình thấy số này sẽ được đem so sánh với 0x14 = 20 bằng phép xor. Vậy là ngoài việc thỏa số sau = số trước + i - 1, số cuối còn phải bằng 20 nữa. Suy ngược quá trình mình có 20 -> 15 -> 11 -> 8 -> 6 -> 5. Vậy 5 6 8 11 15 20 là 6 số cần tìm.

Chạy lại chương trình, lần này mình đã pass phase_1 và phase_2, giờ tới phase_3.

### Phase_3

Ở phase_3 này mình cũng phải nhập 1 cái gì đó. Mình nhập **1**, sau đó dùng lệnh **disass phase_3** và đặt breakpoint tại **0x8048cc6 <phase_3+6>**. Gõ lệnh **next** để nhảy tới breakpoint này.

![phase_3](/assets/bomb/re9.png)

Tại vị trí này, tham số đầu của hàm **phase_3** ([ebp + 0x8]) được gán vào 1 biến cục bộ ([ebp - 0x2c]). Enter 1 tí các bạn sẽ thấy 1 loạt các lệnh **push eax**, là dấu hiệu của 1 lời gọi hàm nào đó =)))) Quả nhiên, đó là hàm **sscanf**. Google 1 tí thì nó khá giống scanf nhưng thêm 1 tham số ở đầu là chuỗi giá trị sau đó mới là format string. Như vậy nếu ở scanf("%d %c", &a, &b) sau đó ta nhập a và b, thì ở sscanf("4 k", "%d %c", &a, &b), a là 4 còn b là ký tự 'k'.

![phase_3](/assets/bomb/re10.png)

Tham số đầu tiên của hàm **sscanf** được push cuối cùng, là biến cục bộ [ebp - 0x2c] mình vừa đề cập ("1") là chuỗi mình nhập. Tham số thứ 2 là format string "%d %c %d". Vậy là mình cần nhập 3 biến.

Giá trị trả về của hàm này (trong eax) là số biến được input thành công, được gán qua [ebp - 0x10] và biến này được so sánh với 2. Nếu lớn hơn thì pass qua hàm **explode_bomb** :))) Ở đây format string nhập 3 biến nhưng mình chỉ đưa mỗi "1" nên toang!

Start lại, lần này ở phase_3 mình nhập "1 a 2". Lần này thì mình input đầy đủ 3 biến rồi, debug tiếp.

![phase_3](/assets/bomb/re11.png)

Ở đây chương trình so sánh [ebp - 0x18] với 7. Quay lại 1 chút, các tham số được push vào hàm **sscanf** lần lượt là [ebp - 0x14], [ebp - 0x1a], [ebp - 0x18], 0x804a499 (đây là format string "%d %c %d") và [ebp - 0x2c]. Trong kiến trúc 32 bit, tham số của hàm được push theo thứ tự ngược lại. Ví dụ lời gọi hàm **add(a, b)** thì thứ tự push vào stack sẽ là push a rồi push b.

Ở đây, với format string là "%d %c %d" thì [ebp - 0x18] là "%d" đầu tiên, [ebp - 0x1a] là "%c" và [ebp - 0x14] là "%d" thứ hai. Vậy nếu tham số đầu tiên lớn hơn 7, chương trình nhảy tới vị trí <phase_3+276>, và kế đó là lời gọi hàm **explode_bomb**! Do mình nhập 1 nên không vấn đề. Lúc này eax là là 1 ([ebp - 0x18]). 2 lệnh tiếp theo là **mov    eax,DWORD PTR [eax*4+0x804a4a4]** và **jmp eax**. 

Nếu các bạn để ý, thì địa chỉ **DWORD PTR [eax*4+0x804a4a4]** thay đổi tùy vào input của mình (ở đây là 1). Nên bây giờ chương trình nhảy tới vị trí **0x8048d3c <phase_3+124>**. Ở đây byte 0x70 được gán vào vị trí [ebp - 0x19] (hơi lạ).

Ở 2 dòng lệnh tiếp theo, [ebp - 0x14] so sánh với 0x28c. Ban nãy mình đã phân tích, [ebp - 0x14] là tham số cuối cùng mình nhập vào, là 2 => khác 0x28c :))) toang rồi.

Chạy lại, lần này ở phase_3 này mình nhập "1 a 652". Lúc này chương trình nhảy tới 1 lệnh **nop** (không làm gì cả) và nhảy tới **<phase_3+309>**. Ở đây có 2 lệnh **movzx  eax,BYTE PTR [ebp-0x1a]** và **cmp    BYTE PTR [ebp-0x19],al**. Biến ở vị trí [ebp - 0x1a] ở đây là ký tự "a" mình đã nhập. Các bạn nhớ [ebp - 0x19] lưu gì không? :v Ban nãy mình đã phân tích là nó được gán bằng 0x70 (ký tự 'p'). Fail tiếp.

Chạy lại, rút "kinh nghiệm", mình nhập "1 p 652" và mình nhảy tới vị trí **<phase_3+323>**. Tiếp đó, chương trình so sánh [ebp - 0xc] và biến toàn cục gs:0x14. Mình dùng lệnh **info variables** để xem biến toàn cục thì cũng không có vấn đề gì, có lẽ là do compiler thôi vì sau đó hàm cũng kết thúc.

Vậy input ở phase_3 là "1 p 652". **Tuy nhiên**, ban nãy mình đã nói input đầu tiên (của mình là 1) quyết định việc hàm sẽ nhảy tới đâu, miễn là không lớn hơn 7. Các bạn có thể thử sức với input khác và reverse ra 2 input còn lại. Chúc các bạn thành công.

### Phase_4

Giờ tới **phase_4**, đầu tiên mình cũng nhập 1 như **phase_3**, **disass phase_4** để xem sơ hàm, đặt breakpoint tại **0x08048e8e** và nhảy tới đó bằng lệnh **next**.

![phase_4](/assets/bomb/re12.png)

Bắt đầu từ vị trí **<phase_4+26>** mình lại thấy 1 loạt các lệnh **push** và hàm **sscanf** được gọi, y như **phase_3**. Giống như trước, từ **<phase_4+47>** chương trình kiểm tra số lượng biến được nhập thành công và kiểm tra có bằng 2 hay không. 

Nhìn ngược lên các lệnh push ở trước, mình thấy tham số của hàm **sscanf** là [ebp - 0x2c] (chuỗi mình nhập lúc nãy), **0x804a4c4** (format string "%d %d"), [ebp - 0x20] và [ebp - 0x1c]. Nghĩa là ở đây ta nhập 2 biến.

Chạy lại, mình nhập "1 2". Lúc này chương trình không nhảy tới **explode_bomb** nữa mà tới lệnh **mov    eax,DWORD PTR [ebp-0x20]** (ở đây là 1 do mình input). Lệnh test để kiểm tra tính chẵn lẻ của eax, và jump nếu là số có dấu. Sau đó chương trình so sánh biến [ebp - 0x20] này với 0xe = 14, nếu nhỏ hơn hoặc bằng sẽ không bị **NỔ**.

Input của mình là 1 nên pass. Tiếp đó, chương trình gán [ebp - 0x14] = 0, gọi hàm **func4** với các tham số từ trái qua phải là [ebp - 0x20] (ở đây là 1), 0 và 14. Mình **disass func4** và đặt breakpoint tại **0x08048e1d**. Gõ lệnh next để nhảy tới đó.

Chờ 1 chút. Từ đầu tới giờ mình chưa nói đến mô hình của stack frame. Mình sẽ demo ở **func4** này.

| Address | Value | Note
| ebp + 0x10 | 0xe |
| ebp + 0xc | 0x0 |
| ebp + 0x8 | 0x1 | => Input của mình
| ebp + 0x4 | Return address | => Địa chỉ trả về
| ebp | | => Đây là nơi làm chuẩn
| ebp - 0x4 | ebp cũ | => Có được từ lệnh **push ebp** ở đầu hàm
| ebp - 0x8 | Biến cục bộ |
| ebp - 0xc | Biến cục bộ |
| ....... | ........| 

**Lưu ý**: Trong kiến trúc 32 bit, *giá trị trả về* của 1 hàm nằm trong biến **eax**, còn vị trí **ebp + 4** là *địa chỉ trả về*, tức là địa chỉ mà program counter sẽ nhảy tới sau khi hàm kết thúc.

Bây giờ thì mình đang ở hàm **func4**. Đầu tiên eax = [ebp + 0x10] = 0xe, sau đó eax = eax - [ebp + 0xc] = 0xe - 0 = 0xe. Sau đó edx được gán bằng eax và dịch phải 0x1f bit (shr). Mục đích là để edx thành 0 thôi. Sau đó eax = eax + edx và dịch phải eax 1 bit => eax = 0xe / 2 = 7 và edx = eax = 7.

Tiếp đó, lệnh **mov    eax,DWORD PTR [ebp+0xc]** gán eax = [ebp + 0xc] = 0, sau đó eax = eax + edx = 7 (7 được gán vào [ebp - 0xc]), và được mang đi so sánh với [ebp + 0x8] (là 1 với input của mình). Do 7 > 1 nên lệnh **jle** không thực thi. Các bạn sẽ thấy ở các lệnh kế tiếp, eax được load từ [ebp - 0xc] là 7, và eax trừ 1 còn 6.

Đến đây, lại 1 lời gọi hàm **func4** nữa. Đệ quy! Dựa trên phân tích nãy giờ, nếu gọi prototype của hàm **func4** là **func4(x, y, z)** thì hàm sẽ dừng khi 7 <= x. Mình dừng chương trình bằng tổ hợp Ctrl-B. Chạy lại bằng lệnh **start**.

Tổng kết ở đây, mình có mã giả C như sau:

```C
int func4(int x, int y, int z) {
   if (x > 7)
      return func4(x-1, y, z);
}
```
Lần này input cho **phase_4** mình để 8 2 để không dính đệ quy hồi nãy và nhảy tới **<func4+67>**. Ở **<func4+67>**, eax được gán là 7 ([ebp - 0xc]) và đem đi so sánh với [ebp + 0x8] (là 8 với input của mình). Nếu 7 <= x thì sẽ nhảy tới **<func4+106>**, không thì ........... lại 1 cái đệ quy khác :))) Thêm vào mã giả bên trên mình có:

```C
int func4(int x, int y, int z) {
   int local_c = y + 7; // [ebp - 0xc] = [ebp + 0xc] + 7, các bạn xem lại code bên trên
   if (x > 7)
      return func4(x-1, y, z);
   if (x < 7)
      return func4(x, local_c + 1, z);
}
```

Vậy để "thoát" đống đệ quy này, mình cho x = 7 (tại mình lười :v). Ctrl-B để quit và start lại. Lần này mình nhập "7 2". Chương trình, đúng như dự đoán, nhảy tới **<func4+106>**. Từ đây eax được gán là 0 và return.

Wait!!! Lúc này mình đã thoát khỏi hàm **func4** và quay lại **phase_4**. Chương trình so sánh giá trị trả về của **func4** (là 0) và [ebp - 0x14] (lúc nãy gán là 0). Vậy là mình pass chỗ này. Chạy tiếp, eax được gán bởi [ebp - 1c] (là 2 - input thứ hai cho hàm **phase_4**), và so sánh với [ebp - 0x14] = 0. Thôi xong! Toi rồi!

Chạy lại :))) Tham số của **phase_4** sẽ là 7 0 và mình pass **phase_4**. Sau đó mình xem **phase_5**.

### Phase_5

2 lần mắc lừa rồi. Giờ mình **disass phase_5** trước và xem cần bao nhiêu tham số :)))

Có hàm **<string_length>**, chắc chỉ cần 1 tham số thôi, nhưng sau đó eax (kết quả của hàm **<string_lenght>**) được so với 6. Vậy là mình cần nhập 1 chuỗi 6 ký tự.

Mình đặt breakpoint ở **0x08048f23** trong hàm **phase_5**. Gõ **next** để nhảy tới đó và dễ dàng pass hàm **<string_length>**. Sau đó [ebp - 0x10] và [ebp - 0x14] được gán là 0, rồi nhảy tới "chỗ nào đó" .....

Ở "chỗ nào đó", [ebp - 0x14] được so sánh với 5, nếu nhỏ hơn hoặc bằng thì nhảy ngược về **<phase_5+50>** (để ý chút thì nó chính là lệnh ngay kế lệnh *nhảy tới "chỗ nào đó"*) :v Nhìn giống vòng lặp, mình xem tiếp.

![phase_5](/assets/bomb/re13.png)

edx được gán bởi [ebp - 0x14] (bằng 0), eax được gán bởi [ebp + 0x8] (con trỏ tới chuỗi làm tham số cho **phase_5**). Cộng edx vào eax và lấy KÝ TỰ tại vị trí eax (mới) (lệnh **movzx  eax,BYTE PTR [eax]**). Sau đó lệnh **and eax, 0xf** là để bỏ 4 bit đầu của eax thôi. Ở đây eax của mình là 0x61 (ký tự 'a') thành 0x1.

Sau đó, eax được gán bởi **DWORD PTR [eax*4+0x804c180]**. Cái này có vẻ giống 1 mảng toàn cục gì đó, nên mình thử **x/16 0x804c180** và kết quả khá chua :)))

![phase_5](/assets/bomb/re14.png)

Dẹp! Mình chịu :((( Mình gõ **next** để tiếp tục debug thì eax đã thành 0xa. [ebp - 0x10] được cộng thêm eax, và [ebp - 0x14] tăng lên 1. Lúc nãy mình đã phân tích là [ebp - 0x14] sẽ được so sánh với 0x5 và nhảy lên **<phase_5+50>**. Từ đây mình có mã giả: 

```C
int local_10 = 0;
int local_14 = 0;
for (local_10 = 0; local_10 <= 5; local_10++) {
   eax = array[local_10]; // mov    eax,DWORD PTR [eax*4+0x804c180]
   local_10 += eax;
}
```

Nhưng cái mảng array kia làm sao thì mình không biết. Mình đành debug tiếp và để ý, sau mỗi lần lệnh đó được thực hiện thì giá trị của eax là bao nhiêu. Sau khi chạy xong chuỗi thì mình rút ra:

| Character | Output
| a | 0xa
| b | 0x6
| c | 0x1
| d | 0xc
| e | 0x10
| f | 0x9

Sau khi kết thúc vòng lặp (**<phase_5+87>**), chương trình so sánh [ebp - 0x10] (là local_10 trong mã giả) với 0x31, bằng thì đúng còn không thì toang :))))

Phải có 1 cách kết hợp nào đó để tổng lại là 0x31. Nhưng mình thấy với 6 ký tự trên thì khó mà tìm tổ hợp nào có tổng là 0x31. Nên mình chạy lại chương trình với chuỗi khác là "ghijkl" và có:

| Character | Output
| g | 0x3
| h | 0x4
| i | 0x7
| j | 0xe
| k | 0x5
| l | 0xb

Lúc này mình có khá đủ số rồi, mình tách 0x31 = 49 = 55-6 = 1+2+3+4+5+7+8+9+10 rồi nhóm 3 = 0x3, 1+8 = 0x9, 2+4 = 0x6, 5+7=0xc, 9 = 0x9 và 10 = 0xa. Dò ngược lại mình có chuỗi "gfbdfa".

Easy game!!! :)))

![phase_4](/assets/bomb/re15.png)

**NHƯNG**, mình hí hửng đi khoe đám bạn thì bị cười vô mặt :))) hóa ra còn 1 **secret_phase** nữa, mình đã không nghĩ tới điều này. Mình phải thừa nhận là cách làm này nhiều thứ không thể kiểm soát vì khó theo dõi function call.

Các bạn mình bảo **secret_phase** nằm trong hàm **phase_difused**.

I am just a renegade hockey mom.

5 6 8 11 15 20

1 p 652

7 0

gfbdfa