_**Các lệnh ASM cơ bản**_  
  - Assembly là ngôn ngữ của “Reverse Engineering”

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
```





  
