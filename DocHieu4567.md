_**Các lệnh ASM cơ bản**_  
  - Assembly là ngôn ngữ của “Reverse Engineering”
  - So sánh LEA và MOV: Đều gán từ phải sang trái
  - VD: MOV EAX, EBP ;Lấy giá trị, nội dung ở thanh ghi EBP, lưu vào thanh ghi EAX     (Tương ứng  a = *b trong ngôn ngữ C++)
        LEA EAX, EBP ;Lấy địa chỉ của thanh ghi EBP trên stack, lưu vào thanh ghi EAX  (Tương ứng  a = &b trong ngôn ngữ C++)

_Do chức năng Assemble của IDA không thể áp dụng đầy đủ cho tất cả các lệnh, chúng ta phải thay đổi các bytes thông qua chức năng Patch Bytes.  
Tuy nhiên, tốt hơn là nên sử dụng plugin Keypatch (một plugin được viết bởi hai anh Nguyen Anh Quynh (aka aquynh) & Thanh Nguyen (aka rd) đã giành được giải thưởng plugin contest của Hexrays năm 2016  
– https://www.hex-rays.com/contests/2016/index.shtml), cho phép patch trực tiếp các lệnh ASM vào binary.  
Để cài đặt và sử dụng plugin này các bạn xem tại đây: https://github.com/keystone-engine/keypatch  
Khi cài đặt thành công, tại màn hình của IDA, nhấn chuột phải tại một lệnh bất kỳ sẽ xuất hiện như hình:_  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/6b0eef85-2a59-48b8-abc4-4848523474ab)  
_Lựa chọn Patcher (Ctrl-Alt-K), ta thấy rằng việc viết lệnh trong Keypatch rất đơn giản và sau đó lệnh sẽ được chuyển đổi thành cú pháp của IDA khi nhấn Patch:_  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/3cef6f55-a74c-4688-9bce-5de4209855fc)  
_Dưới đây là kết quả sau khi sửa lệnh:_  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/6a7ec72b-5696-41a9-9112-4705b30e665c)

**#Các lệnh chuyển dữ liệu**  
```
  1.MOV  
    - Cú pháp: MOV dest, src:    
    - Ý nghĩa: Sao chép nội dung của toán hạng nguồn (src) tới đích (dest)  
    - Thao tác: dest <- src.  
    - Công dụng: Lệnh này được sử dụng để chuyển dữ liệu giữa:  
       + Các thanh ghi  
       + Một thanh ghi và một ô nhớ  
       + Hoặc chuyển trực tiếp một số vào một thanh ghi hay ô nhớ.
    - Ví dụ: Câu lệnh như sau: MOV EAX, EDI  ;EAX nhận giá trị của EDI
                                              ;còn EDI giữ nguyên giá trị, không bị thay đổi  
    - Hiểu cơ bản thì lệnh mov này có thể tương ứng với lệnh gán ở ngôn ngữ bậc cao.(VD: a = b trong c++)  
```
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/d8d27e61-7baf-42f5-a48e-1969f7d16f30)  ![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/511f30a5-cdcb-43a4-9403-16fd583cae7a)    
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/8f1e87f3-e19a-4311-a345-cba8f37b86fc)  ![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/b5bded58-145d-4393-acf5-3d280b47d575)  
```
2.Lệnh XCHG     
  - Cú pháp: XCHG A, B
  - Ý nghĩa:  Hoán đổi giá trị của A với giá trị của B.
  - Trường hợp áp dụng:  A và B có thể là hai thanh ghi, thanh ghi và ô nhớ, nhưng không được phép đồng thời là 2 ô nhớ.
  - Ví dụ: Với lệnh XCHG, giả sử nếu EAX có giá trị 0x12345678 và ESI có giá trị 0x55
    + Khi ta thực hiện lệnh XCHG thì thanh ghi EAX sẽ được gán giá trị 0x55 và thanh ghi ESI là 0x12345678.  
    + Như vậy, sau khi thực hiện lệnh XCHG thì giá trị của hai thanh ghi được hoán đổi cho nhau.
  - Lệnh XCHG cũng có thể được sử dụng để hoán đổi giữa giá trị thanh ghi với nội dung bộ nhớ được trỏ bởi thanh ghi:  
```
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/5fc3f504-24cb-4d57-ba36-8a9bd30cc3fe)  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/4c22c674-cb86-4204-b666-f42751ff2ad6)  

**Các câu lệnh tương tác với Stack, Stack là gì?**
```
1.Stack
  - Stack (Ngăn xếp) là một phần của bộ nhớ và là cấu trúc dữ liệu một chiều (các phần tử được cất vào và lấy ra từ cùng một đầu của cấu trúc).
  - Việc truy cập vào stack sẽ tuân theo cơ chế FILO, nghĩa là “Vào trước, ra sau” hay LIFO, “Vào sau, ra trước”.
  - Các bạn có thể hình dung Stack như một chồng đĩa, chiếc đĩa cuối cùng được xếp vào sẽ nằm trên đỉnh và chỉ có nó mới có thể được lấy ra đầu tiên.
  - Theo quy ước, Stack hướng về phía địa chỉ bộ nhớ thấp hơn.
  - Stack cho phép lưu trữ và khôi phục lại dữ liệu.
  - Đối với việc xử lý dữ liệu trên stack, có hai thao tác lệnh cơ bản:
    + PUSH, đẩy/lưu một phần tử vào đỉnh ngăn xếp và thao tác ngược lại của nó POP.
    + Loại bỏ/khôi phục một phần từ được đẩy vào cuối cùng ra khỏi ngăn xếp.  
2.Lệnh PUSH  
  - Lệnh này được dùng để thêm/ lưu dữ liệu vào trong ngăn xếp.
  - Toán hạng nguồn có thể là các thanh ghi dùng chung hoặc ô nhớ.
  - Sau mỗi lần thực hiện lệnh Push thì giá trị của thanh ghi ESP sẽ được giảm đi.
  - Thông thường, trong kiến trúc 32 bit, lệnh PUSH thường được sử dụng để truyền các tham số của một hàm vào ngăn xếp trước khi thực hiện lời gọi hàm bằng một lệnh CALL.
3.Lệnh POP
  - Lệnh này được dùng để lấy ra giá trị từ đỉnh của ngăn xếp.
  - Sau khi thực hiện lệnh thì giá trị của thanh ghi ESP sẽ được tăng lên để trỏ tới phần tử tiếp theo.
  - Lệnh POP thực hiện thao tác đọc giá trị trên đỉnh của ngăn xếp và chuyển nó đến thanh ghi đích.
  - VD: câu lệnh "POP EDI" sẽ đọc giá trị đầu tiên hay giá trị trên đỉnh của ngăn xếp và sao chép nó vào thanh ghi EDI, sau đó trỏ thanh ghi ESP vào giá trị bên          dưới và giá trị mới này sẽ trở thành đỉnh của ngăn xếp.
4.Lệnh LEA (LOAD EFFECTIVE ADDRESS)
  - Cú pháp: LEA A, B.
  - Lệnh LEA (nạp địa chỉ hiệu dung vào thanh ghi) thực hiện chuyển một địa chỉ được chỉ định trong B (nguồn) vào A (đích). Nội dung của B không bao giờ được         truy cập, nó sẽ luôn là một địa chỉ hoặc là kết quả của phép tính toán nằm trong dấu ngoặc vuông [] của toán hạng thứ hai. Lệnh LEA được sử dụng rất nhiều để     lấy địa chỉ bộ nhớ của các biến hoặc các tham số.
  - Mỗi tham số hoặc biến mà một hàm sử dụng sẽ có một địa chỉ do hệ thống cấp phát và một giá trị được lưu tại địa chỉ đó.
  - Lệnh LEA, mặc dù sử dụng cặp ngoặc [], nhưng nó chỉ chuyển địa chỉ đã được thực hiện tính toán bên trong ngoặc mà không truy cập vào nội dung của ô nhớ
  - Về bản chất, quá trình thực hiện thực ra chỉ là cộng hoặc trừ thanh ghi EBP với một giá trị hằng số để trỏ tới một địa chỉ nằm trên Stack mà thôi, tuy nhiên do IDA là công cụ hỗ trợ khả năng tương tác cao, nên nó sẽ đánh nhãn cho từng biến để ta có thể dễ dàng đặt lại tên khi phân tích.
  - Lệnh LEA cũng được sử dụng để thực hiện các tính toán nằm trong [], sau đó chuyển kết quả tính toán vào thanh ghi đích, mà không cần truy cập nội dung.
  - Các ví dụ:
    + LEA EAX, [4+5] ;Lệnh trên sẽ tính toán và gán 9 vào thanh ghi EAX, nó sẽ không chuyển nội dung của địa chỉ 0x9 như lệnh MOV sẽ thực hiện: MOV EAX, [4+5]
    + LEA EAX, [EBP – 0C] ;Lệnh trên chuyển kết quả của EBP – 0C, là địa chỉ bộ nhớ của biến thu được khi thực hiện phép tính EBP – 0C và gán cho thanh ghi EAX.        Khác với lệnh này MOV EAX, [EBP – 0C], ngoài việc tính toán EBP-0C để có được kết quả địa chỉ giống như lệnh LEA, lệnh này còn tìm kiếm nội dung của một 
      giá trị được lưu trữ tại địa chỉ đã tính toán và gán giá trị đó cho EAX.    
```
**Các lệnh tính toán**  

1.Lệnh ADD  
  - Cú pháp: ADD A, B  ;A = A + B
  - Giải thích: Câu lệnh ADD thực hiện cộng giá trị của B với A, kết quả tính toán sẽ lưu vào A. Tức là A = A + B. A ở đây có thể là một thanh ghi hoặc là nội        dung của một ô nhớ, B có thể là một thanh ghi, một hằng số hoặc nội dung của một ô nhớ. Tuy nhiên, trong câu lệnh ADD thì cả A và B không thể đồng thời là        nội dung của ô nhớ.
  - Trong trường hợp này, giá trị của cả hai thanh ghi sẽ được cộng với nhau và lưu vào EDI. Tất nhiên ta cũng có thể sử dụng các thanh ghi 16-bit và 8-bit.          Ví dụ:
    ADD AL, 8 
    ADD AX, 8
    ADD BX, AX
    ADD byte ptr ds: [EAX], 7

  - Lệnh trên cộng vào byte nội dung mà EAX trỏ đến với giá trị hằng số là 7 và lưu lại tại cùng một vị trí.
2.Lệnh SUB
  - Cú pháp của lệnh như sau:  SUB A, B ; A = A – B
  - Lệnh SUB cũng tương tự như lệnh ADD, ngoại trừ thay vì thực hiện cộng thì nó thực hiện trừ số nguyên và lưu kết quả vào A. Các kết hợp của lệnh có thể như sau:
  - ![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/666517d2-6437-4d0d-95e8-dfa0db9b4a5b)
3.Lệnh INC và DEC
  - Cú pháp:   INC A; A++
               DEC A; A–
  - Các lệnh trên thực hiện tăng hoặc giảm giá trị thanh ghi hoặc nội dung của một địa chỉ bộ nhớ đi 1. Trên thực tế đây có thể xem là một trường hợp đặc biệt       của phép cộng và trừ.
  - ![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/6eeb7ae7-12dc-4a5b-9218-c1957f1f0aa6)
  - Cả hai lệnh này thường hay được sử dụng trong các vòng lặp để tăng hoặc giảm biến đếm.
4.Lệnh IMUL
  - Đây là lệnh thực hiện phép tính nhân số có dấu và có hai dạng như sau:
    IMUL A, B    ; A = A * B 
    IMUL A, B, C ; A = B * C
  - Giải thích: Câu lệnh đầu tiên sẽ thực hiện nhân A với B, kết quả được bao nhiêu sẽ được lưu lại vào A.
                Câu lệnh thứ hai thì B và C được nhân với nhau và kết quả được lưu vào A.
                Trong cả hai trường hợp A chỉ có thể là một thanh ghi, B chỉ có thể là một thanh ghi hoặc nội dung của một vị trí bộ nhớ và C chỉ có thể là một                  hằng số.
  - Tại sao lại là câu lệnh imul (signed multiply) mà không phải là câu lệnh mul (unsigned multiply)? Đó là bởi trình Visual Studio dường như có một sự ưa           thích đối với lệnh imul. Kể cả khi bạn khai báo biến có kiểu unsigned trong chương trình, khi compile code và chuyển qua assembly thì sẽ thấy chương trình       sử dụng câu lệnh imul.
  - ![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/788be7ba-9930-47ee-a05c-a051522b2e32)
  - imul eax, [ecx] 
    imul esi, edi, 25 
    Một vài ví dụ khác của lệnh IMUL tìm thấy trong file Veviewer:
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/a791da8d-57a9-4866-b787-8231fa6dab65)
5.Lệnh DIV/ IDIV
  - Cú pháp của lệnh như sau:    DIV/ IDIV A
  - Trong câu lệnh này, A được hiểu là số chia. Số bị chia và thương số không được chỉ định bởi vì chúng luôn giống nhau. Tức là có 3 dạng như sau:
    Nếu A có kiểu byte, lấy giá trị của thanh ghi AX chia cho A, kết quả thương số lưu vào thanh ghi AL, phần dư lưu vào thanh ghi AH.
    Nếu A có kiểu word, lấy giá trị của cặp thanh ghi DX:AX chia cho A, kết quả thương số lưu vào thanh ghi AX, phần dư lưu vào thanh ghi DX.
    Nếu A có kiểu dword, lấy giá trị của cặp thanh ghi EDX:EAX chia cho A, kết quả thương số lưu vào thanh ghi EAX, phần dư lưu vào thanh ghi EDX.
  - Xem xét ví dụ:
  - ![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/cc579706-2d63-46bd-8a86-e46def9cae28)
  - Với lệnh trên, ví dụ nếu EAX = 5, EDX = 0 và ECX = 2, nó sẽ thực hiện phép chia số nguyên. Kết quả của phép chia 5 / 2 sẽ được 2 và dư 1. Khi đó kết quả là      2 được lưu vào thanh ghi EAX và số dư 1 sẽ được lưu vào thanh ghi EDX
  - Thông thường khi thực hiện phép chia, do thanh ghi EDX được sử dụng để lưu phần dư nên nó sẽ được thiết lập về 0 trước khi thực hiện phép tính. Để xóa EDX       về 0 có hai cách:
    Sử dụng câu lệnh XOR (chi tiết bên dưới): XOR EDX, EDX
    Sử dụng câu lệnh CDQ (như trên hình minh họa): Câu lệnh này thực hiện mở rộng bit dấu (bit 31) của thanh ghi EAX sang thanh ghi EDX. Nếu bit này có giá trị      0 thì EDX sẽ bằng 0.
    Điều tương tự sẽ xảy ra nếu như A là nội dung của một ô nhớ (dword ptr ds:[402000]), EDX:EAX sẽ được chia cho giá trị đó và kết quả sẽ được lưu trong EAX và     phần dư trong EDX.

**Các lệnh Logic**  

1.Lệnh AND, OR và XOR  
  - Cú pháp:  AND A, B ; A = A & B
              OR A, B  ; A = A | B
              XOR A, B ; A = A ^ B
  - Giải thích: Lệnh đầu tiên thực hiện phép AND giữa hai giá trị và lưu lại kết quả vào A, tương tự với các lệnh OR hoặc XOR. Mỗi phép tính đều sử dụng một                     bảng thật tương ứng của nó. A và B có thể là thanh ghi hoặc nội dung của địa chỉ bộ nhớ, tuy nhiên các thao tác giữa hai ô nhớ là không hợp lệ.
  - Lệnh hay được sử dụng nhiều nhất là XOR cùng một thanh ghi để dễ dàng xóa thanh ghi đó về 0. Ví dụ: XOR EAX,EAX. Dưới đây là bảng thật hay bảng chân lý (như     ở các trường đại học hay dạy) tương ứng cho từng lệnh:
  - ![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/5d1587fe-1d50-4cd4-89f9-4e2d2aec6533)![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/f232a30e-2775-43fa-ac88-402a35f4f0a7)![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/46aa5480-a08a-4de8-8a06-f31fcb9bb287)![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/36aa9b6f-1a30-44ac-a00b-3bc47d6569b3)
  - Lệnh AND có thể sử dụng để che đi/ giữ lại các bit nhất định của toán hạng đích. Bit 0 của mặt nạ sẽ xóa bit tương ứng, còn bit 1 của mặt nạ sẽ giữ nguyên       bit tương ứng của toán hạng đích.
  - Lênh OR có thể được sử dụng để thiết lập các bit xác định của toán hạng đích trong khi vẫn giữ nguyên các bit còn lại. Bit 1 cua mặt nạ sẽ thiết lập bit         tương ứng còn bit 0 của mặt nạ sẽ giữ nguyên bit tương ứng của toán hạng đích.
  - Lệnh XOR dùng để đảo các bit xác định của toán hạng đích trong khi vẫn giữ nguyên các bit còn lại. Bit 1 của mặt nạ làm đảo bit tương ứng còn bit 0 giữ          nguyên bit tương ứng của toán hạng đích.
  - Như đã nói, lệnh XOR dùng để xóa một thanh ghi về 0, bằng cách này sẽ thực hiện nhanh hơn lệnh MOV:
  - ![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/f5dda138-eca5-45e3-b263-e2b794f05bb3)
  - Một ví dụ đơn giản của lệnh AND:
    AND EAX, 0F
    Biểu diễn ở dạng binary thì 0F sẽ là 1111:
    ![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/8a9b1d76-a66b-4c1a-b57d-61b859aa0770)
    Dựa vào bảng thật, chúng ta thấy rằng nếu cả hai bit là 1 thì kết quả sẽ không thay đổi, trong khi các cặp bit khác sẽ cho kết quả là 0. Bằng cách này tôi       dễ dàng thiết lập lại tất cả các bit của một số là 0 và giữ nguyên 4 bit cuối cùng không thay đổi. Ví dụ:
    ![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/765fb6a8-40eb-4476-9c48-d87857581bd3)
    Như đã biết phép AND được biểu diễn bằng dấu “&”. Với câu lệnh như trên, ta sẽ giữ lại được 4 bit cuối.
    Lệnh OR được biểu diễn bằng dấu “|”, ví dụ như sau:
    ![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/228a177d-89ce-4baf-8497-2fa0bc0ad319)
2.Lệnh NOT
  - Cú pháp: NOT A.
  - Giải thích: Lệnh NOT thực hiện đảo ngược tất cả các bit của A và lưu lại kết quả vào A. Trong Python không có lệnh NOT, nhưng nó rất đơn giản nếu bạn có một     số nhị phân, ví dụ 0101 và bạn áp dụng lệnh NOT với số này:
  - ![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/ee77f5ac-7499-44ec-85c3-b1c558064e03)
  - Kết quả có được sau khi thực hiện đảo ngược từng bit một. Toàn bộ các bit 0 sẽ được thay bằng 1 và ngược lại.
  - ![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/9ffac5c6-b15a-428d-bf3c-63d7307cad43)
3.Lệnh NEG
  - Cú pháp: NEG A
  - Giải thích: chuyển đổi A thành –A (reg = 0 – reg). Trên thực tế, lệnh neg là kết quả của một lệnh not và add 1.
                Nó không giống như cú pháp ~ trong Python vì lệnh này chỉ là phép trừ đi 1.
    ![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/5cf1b397-34e9-4ea3-8649-01ffbc97cb20)
  - Nói cách khác, để thực hiện lệnh NEG bằng Python, bạn cần cộng thêm 1 vào kết quả.
  - ![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/8813bf84-1e7c-4b55-b274-f3b7cc6620ea)

**Các lệnh dịch bit SHL, SHR**  
SHL A, B; Dịch trái A đi B bit

SHR A, B; Dịch phải A đi B bit  
A có thể là một thanh ghi hoặc một vị trí bộ nhớ và B là một hằng số hay một thanh ghi 8-bit. Các lệnh này thực hiên phép dịch bit sang trái (SHL) và sang phải (SHR), các bit bên phải/trái được thay thế bằng các số 0, chúng ta hãy xem ví dụ.  
Nếu tôi có -1:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/3cc4e695-5b39-4867-918b-43843562620f)  
Khi thực hiện SHL 2, nó sẽ có kết quả:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/d6a5c1e7-7b9f-4ad2-be80-f4cb633922e3)  
Khi di chuyển các bit sang trái, mỗi lần dịch thì MSB sẽ được đưa qua cờ CF và 0 đưa vào LSB. Vì dịch đi 2, nên hai bit cuối cùng ở phía bên phải nhất sẽ được thay thế bằng 0.  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/0a1f3cc9-dd8c-47d0-8109-4a9d033b74ee)  
Tương tự khi ta thực hiện lệnh SHR. Các bit sẽ di chuyển sang phải, sau mỗi lần dịch thì LSB sẽ được đưa qua cờ CF còn 0 đưa vào MSB.  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/f98091dc-bf33-4278-b5df-cbd7d57e40b9)  
_Lưu ý: Việc dịch bit trái (phải) tương ứng với phép nhân (chia) cho lũy thừa 2._  
VD: shl eax, 0x2 à EAX << 2 or EAX = EAX * 4
shr eax, 0x2 à EAX >> 2 or EAX = EAX / 4  

**Lệnh nhảy không điều kiện**  
1.Lệnh JMP  
  - Cú pháp: JMP A
  - Giải thích: giống như lệnh goto trong lập trình bậc cao Lệnh JMP là một lệnh nhảy không phụ thuộc vào điều kiện và A sẽ là một địa chỉ bộ nhớ mà chúng ta                    muốn chương trình nhảy tới.
  - ![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/0879084d-98a2-44a8-8c3f-edcedd94ad84)
  - JMP SHORT là một lệnh nhảy ngắn gồm có 2 bytes, có khả năng nhảy về phía trước và ngược lại. Hướng nhảy được chỉ định bởi giá trị của byte thứ hai vì byte đầu tiên là opcode (0xEB) của lệnh. Lệnh này không thể nhảy quá xa.
  - ![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/a558e355-a7f2-4fcb-9344-4b7e98b379cb)
  - Nếu thiết lập tùy chọn trong IDA để hiển thị opcode của các lệnh, chúng ta sẽ thấy opcode EB tương ứng với lệnh JMP và lệnh này sẽ nhảy 5 bước về phía trước kể từ vị trí kết thúc lệnh. Nghĩa là địa chỉ đích của lệnh được tính như sau:
  - ![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/10c4a6f4-11f0-4ddc-82d4-9a742bbfc0ea)
  - Lấy địa chỉ bắt đầu của lệnh cộng với 2 là số bytes chiếm bởi lệnh và sau đó cộng thêm 5 (byte thứ hai – bước nhảy). Rõ ràng, việc nhảy tới hoặc lùi với một byte duy nhất không cho chúng ta đạt được bước nhảy xa. Bước nhảy cao nhất sẽ là 0x7f, chúng ta sẽ lấy một ví dụ bên dưới.  
  - Khi chúng ta thực hiện một số thay đổi sẽ phá vỡ cấu trúc của hàm, để thuận tiện ta nên tiến hành tạo một bản chụp (snapshot) cơ sở dữ liệu nhằm giúp ta quay trở lại trạng thái trước khi thay đổi. Hãy làm việc này vào bất cứ lúc nào bạn nghi ngờ về việc thay đổi sẽ có thể làm phá vỡ cấu trúc hàm và không biết cách nào để khôi phục lại.
  - ![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/a9469299-944b-4b07-83ca-1b1dfb7506cf)
  - IDA sẽ yêu cầu chúng ta đặt tên cho database được snapshot. Có thể quản lý snapshot này thông qua View > Database Snapshot Manager:
  - ![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/648cb21d-8454-46d0-a65d-a9a78e459660)
  - Tại đây, chúng ta có thể xem danh sách tất cả các snapshot và ngày chụp, cùng với nút Restore cho phép chúng ta có thể trở về trạng thái chúng ta muốn từ những bản snapshot mà chúng ta đã lưu.
**Lệnh nhảy có điều kiện (Jcc)**
  - Cú pháp: CMP A, B
  - Giải thích: so sánh toán hạng thứ nhất với toán hạng thứ hai và bật các cờ trên thanh ghi EFLAGS dựa theo kết quả tính toán (việc tính toán tương tự như lệnh SUB, nhưng khác ở chỗ kết quả tính toán không được lưu lại).
  - Tôi cần chương trình thực hiện so sánh giữa A và B. Dựa vào mối quan hệ giữa chúng, chương trình sẽ thực hiện một công việc nào đó nếu thỏa mãn, còn không chương trình sẽ thực hiện một công việc khác. Vì vậy, bình thường sau khi so sánh sẽ làm thay đổi các FLAGS, căn cứ vào trạng thái của cờ, lệnh nhảy có điều kiện sẽ quyết định có thực hiện hay không.
  - ![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/b1cff021-b473-4f0a-9bd5-1e7520a17411)
  - Trong hình trên, chúng ta thấy một ví dụ về lệnh nhảy có điều kiện là JZ. Lệnh này sẽ thực hiện nếu cờ ZF được bật. Điều này được quyết định bởi lệnh CMP trước đó. Khi hai thanh ghi EAX và EBX bằng nhau, CMP thực hiện phép trừ hai thanh ghi, kết quả sẽ bằng 0 và do đó cờ ZF được bật thành 1. Khi cờ ZF được kích hoạt thì sẽ thực hiện lệnh nhảy theo hướng mũi tên màu xanh lá cây, còn nếu hai thanh ghi khác nhau thì sẽ đi theo hướng của mũi tên đỏ.

Nếu sử dụng debugger để debug chương trình, chúng ta có thể tương tác để thay đổi kết quả trên các cờ. Tuy nhiên, điều quan trọng bây giờ cần phải nhớ có những lệnh nhảy khác nhau có thể xuất hiện trong một chương trình như hình minh họa dưới đây:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/09c9a81e-ace3-4826-b1fd-e8322d0ca57a)  
Ngoại trừ các lệnh JMP và NOP được liệt kê trong bảng, các lệnh còn lại đều là các lệnh nhảy có điều kiện. Các lệnh nhảy này đều căn cứ vào kết quả của câu lệnh so sánh trước đó.

Các lệnh nhảy Above / Below được sử dụng cho so sánh số không dấu (unsinged comparison)
Các lệnh nhảy Greater than / Less than được sử dụng cho so sánh số có dấu (singed comparison)  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/09cc2368-6eca-4cc5-8571-79dc237571a6)  
Mặc dù có nhiều lệnh nhảy có điều kiện nhưng may mắn là trong số đó có các lệnh cùng một mục đích. Ví dụ: JNE == JNZ (Nhảy nếu không bằng nhau, Nhảy nếu không bằng 0, cả hai cùng kiểm tra cờ (ZF == 0)).
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/5d551991-3d5c-4007-8c1b-323e4f56a5ea)  
bên cạnh việc so sánh sử dụng câu lệnh CMP, một câu lệnh khác cũng rất hay được sử dụng là TEST. Bản chất của lệnh TEST là tính toán logic thông qua việc AND hai toán hạng, căn cứ trên kết quả để bật cờ. Kết quả tính toán sẽ không được lưu lại.  
**Lệnh CALL và RET**  
Các lệnh tiếp theo mà tôi sẽ đề cập đến là lệnh CALL, dùng để gọi một hàm và lệnh RET, dùng để trở quay trở về lệnh tiếp theo sẽ được thực hiện sau lệnh Call.  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/8dee8fa2-cf1b-437f-9a33-302c517316e5)  
Trên hình, chúng ta thấy một ví dụ của lệnh CALL, lệnh này sẽ nhảy đến địa chỉ 0x4013d8 để thực hiện hàm tại đó (chúng ta thấy tiền tố sub_ ở phía trước của địa chỉ 0x4013D8 thông báo cho ta biết đây là một hàm).

Lệnh CALL thực hiện sẽ lưu vào đỉnh của ngăn xếp (Stack) đỉa chỉ trở về sau khi thực hiện xong hàm, tức là địa chỉ bên dưới của lệnh Call mà trong trường hợp này là 0x40123d. Sau đó, nó sẽ thay đổi địa chỉ của thanh ghi EIP bằng địa chỉ đã chỉ định trong câu lệnh. Địa chỉ đích có thể được chỉ định theo nhiều cách:

Giá trị trực tiếp (immediate value): call   0x401da8
Thanh ghi dùng chung: call eax
Vị trí bộ nhớ: call    dword ptr [0x40202c]
Ta có thể truy cập các lệnh bên trong CALL bằng cách nhấn Enter tại lệnh CALL đó:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/95bad45c-f5d7-4446-a9be-504810720df6)  
Khi kết thúc hàm, sẽ thực hiện một lệnh RET, lệnh này có nhiệm vụ lấy địa chỉ trở về được lưu tại đỉnh của stack là 0x40123d, đưa vào thanh ghi EIP và nhảy tới địa chỉ này để tiếp tục thực hiện lệnh sau CALL.  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/6eb7d228-b07a-4eea-acd2-d4882db7f6a2)  
 liên quan đến hai lệnh Call & Ret chúng ta cần biết thêm về tập quán gọi hàm (hay từ chuyên môn là calling convention), bởi vì việc gọi hàm có thể khác nhau trong mã lệnh asm. Nó bao gồm thứ tự các tham số được đẩy vào ngăn xếp hay các thanh ghi, hàm gọi (caller) hay hàm được gọi (callee) chịu trách nhiệm dọn dẹp ngăn xếp khi hoàn tất hàm. Tùy thuộc vào trình biên dịch, có hai tập quán gọi hàm hay gặp nhất là cdecl (C declaration) và và stdcall. Ngoài ra, còn rất nhiều tập quán gọi hàm khác mà các bạn có thể xem thêm tại đây: https://en.wikipedia.org/wiki/X86_calling_conventions  
 cdecl – tập quán gọi hàm phổ biến nhất:

Tham số của hàm được đẩy vào stack theo chiều từ phải qua trái.
Kết quả trả về của hàm thường lưu vào thanh ghi EAX.
Hàm gọi (caller) chịu trách nhiệm dọn dẹp stack.
Ví dụ:

_cdecl int function(int arg1, int arg2, int arg3)  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/1e5563a7-84d0-4a2e-944e-b10815131ad2)  

_**THE END**_










 




 




















              





  
