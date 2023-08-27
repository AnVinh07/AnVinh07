_**BA BÀI TIẾP THEO 9,10,11 TRONG CHUỖI BÀI REVERSING CUA KIENMANOWAR**_  

1.Để tìm hiểu các tính năng của IDA, ta sẽ tiếp tục thực hành qua các ví dụ rất đơn giản. Trong phần 9 này là một crackme nhỏ nhẹ, được biên dịch bằng Visual Studio 2015.   
Để thực thi crackme này có thể bạn cần phải cài đặt phiên bản mới nhất của Visual Studio 2015 C++ runtimes ( https://www.microsoft.com/en-us/download/details.aspx?id=48145: The Visual C++ Redistributable packages install the runtime components to execute compiled C++ Visual Studio 2015 programs).  
- Sau khi cài đặt xong VC++ runtimes, chạy thử crackme (https://mega.nz/#!LHAGkKRD!h9PJX9kftBW4z3Ykf3Gwpa3LW6H_FCjIv7FZZnGux6s) là HOLA_REVERSER.exe:
- ![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/cdddeb15-43d0-4ae7-8f28-ee926ebba08f)
- Khi nhập vào một số bất kì, crackme sẽ thông báo bạn là một “good” hay “bad” reverser, juajua:
- ![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/40200fb9-6b64-4f2e-9864-44c5f2178a1c)
- Có thể thấy crackme hoạt động rất đơn giản. Ta sẽ mở nó trong IDA để phân tích.
_Bên lề: trong trường hợp của thầy Ricardo Narvaja (không phải của tôi & bạn), sau khi IDA tiến hành phân tích xong, hàm main() của crackme xuất hiện giữa các hàm tại cửa sổ Functions. Có thể dễ dàng tìm kiếm bằng tổ hợp phím CTRL + F tại cửa sổ đó. Điều này xảy ra bởi vì ông ấy là chủ của crackme này, khi biên dịch bằng Visual Studio thì trình biên dịch đã tạo ra thêm một tập tin symbol là .pdb (https://docs.microsoft.com/en-us/windows/desktop/debug/symbol-files). Dựa vào file này, IDA có thể nhận diện và nạp tên của các hàm và các biến mà Ricardo Narvaja sử dụng khi lập trình, ví dụ như trong hàm main() gọi tới hàm printf():_
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/bb57cebd-488f-4046-a69f-57ee50c93dbf)
Giờ hãy xem những gì sẽ xảy ra đối với IDA của chúng ta khi phân tích xong crackme.

Tại màn hình IDA của bạn, bạn sẽ không thấy tên hàm main xuất hiện tại cửa sổ Functions bởi IDA không có tập tin .pdb để load cùng. Điều này hoàn toàn bình thường, bởi không coder nào lại cung cấp một chương trình kèm theo symbols cả :).  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/5241b6a5-6894-463c-8a4d-383b43559d0e)  
Thông thường, chúng ta chỉ có được các tập tin symbols của các modules hệ thống (kernel32.dll, ntdll.dll, …) chứ không thể có của các chương trình, ngoại trừ những trường hợp rất hiếm hoi. Trong ví dụ này, Ricardo Narvaja có symbols file là bởi vì ông là người code ra crackme này. Tuy nhiên, để đúng như thực tế, ta sẽ phân tích crackme mà không có symbol đi kèm giống như sẽ gặp với rất nhiều các chương trình khác.

Khi load file không có symbol ta sẽ dừng tại đây:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/18a28e8d-2616-4d47-aa5e-e4646c24c53e)  
Như trên hình, code tại đây không cung cấp nhiều thông tin, do vậy ta phải tìm kiếm thông tin tại cửa sổ Strings:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/6ad19d06-54bd-4c0e-9f84-f143532169cb)  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/608f36ea-e4cf-4a1e-9ca6-8f575d9ebac3)  
Tại cửa sổ Strings, ta có được thông tin các strings được sử dụng bởi crackme. Qua các strings này ta thấy crackme sẽ hiển thị thông báo nếu như chúng ta nhập vào một số không hợp lệ. Nhấp đúp chuột tại chuỗi “Pone un numerito\n”, chuỗi này trong tiếng anh có nghĩa là “Enter a serial”:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/8cfa3f5f-d5ae-4341-88aa-3f4273b37f9e)  
Như kết quả trên hình, ta thấy rằng địa chỉ 0x402108 là con trỏ đến chuỗ. Bên cạnh địa chỉ có một tag mà IDA đặt cho địa chỉ này, bắt đầu bằng “sz” (ở máy tôi) hay “a” (trên máy bạn), hàm ý đây là một chuỗi ASCII và phần còn lại tương ứng với nội dung của chuỗi. Với thông tin này sẽ giúp ta dễ dàng để nhận ra đó là một chuỗi ASCII “szPoneUnNumerit”, từ khóa db ở bên cạnh hàm ý đây là một chuỗi byte.

Nếu nhấn phím “D” chúng ta sẽ chuyển chuỗi này thành các bytes như hình:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/03e8b406-f05c-456b-b1d4-3e4ed5bf3cb8)  
Để khôi phục lại chuỗi ban đầu, nhấn phím “A”. Di chuyển con chuột tới biểu tượng mũi tên nhỏ “↑”, IDA sẽ hiển thị thông tin về đoạn code sử dụng tới chuỗi này:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/a64fb7c1-15d2-490e-a5c8-b8752aee293c)  
Tuy nhiên, tốt hơn nên sử dụng phím “X” để tìm danh sách tất các các vùng code tham chiếu tới chuỗi:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/e3244ae0-7282-4910-8d4c-21c7d65c31aa)  
Kết quả có được như trên hình, nhấn OK ta sẽ tới hàm chính (main), nhưng ở đây hàm không được đặt tên là main như các bạn đã thấy khi load file có kèm .pdb. Ở đây, ta thấy có môt buffer được IDA đặt một cái tên khá chung chung là Buf:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/d38cf463-b529-4be6-8d9b-37db2874850c)  
Chúng ta không biết mã nguồn của crackme này, nhưng giả sử cung cấp một đoạn của mã nguồn như sau:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/0147a53e-f61c-474b-bcc4-1abf5968ab2e)  
Các bạn có thể thấy trình biên dịch đã tối ưu hóa một số biến đã khai báo trong chương trình, ví dụ như các biến cookie không được sử dụng sẽ bị loại bỏ và max đã được thay thế bằng hằng số. Bên cạnh đó ta có thông tin về vùng buffer trong mã nguồn của crackme có độ dài là 120 bytes. Buffer là một vùng nhớ dành riêng được sử dụng cho việc lưu trữ dữ liệu. Vậy khi chúng ta không có mã nguồn, làm thế nào trong IDA chúng ta có thể biết được kích thước của một buffer trên stack?  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/7af9b0cd-58b5-49b9-a738-f10750342037)  
Như trên hình, tại phần đầu của mỗi hàm chúng ta sẽ thấy một danh sách các biến và các tham số của hàm. Nhấp đúp chuột vào bất kỳ một cấu phần nào cũng sẽ đưa chúng ta đến chế độ xem tĩnh của Stack, nơi chứa thông tin về vị trí của các biến, các tham số và buffer, … kèm theo đó là khoảng cách giữa chúng:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/a88ffa7d-c442-4086-8890-f8a1180609ea)  
Tại cửa số Stack của hàm, chúng ta thấy biến Buf nhưng nó đang được định nghĩa là các byte “db”. Để thay đổi nó thành mảng các ký tự hoặc buffer như khai báo trong mã nguồn, chúng ta nhấn chuột phải tại Buf và chọn tùy chọn Array:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/59b3ef3d-28d6-4740-8b85-6f9778ebfefb)  
Ta thấy rằng IDA đã tự động nhận biết được mảng này tối đa gồm 120 phần tử (khoảng cách của Buf so với biến tiếp theo), mỗi phần tử trong mảng có kích thước là 1 byte. Nhấn OK để chấp nhận thông tin chuyển đổi mà IDA cung cấp, kết quả có được như hình:

![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/f6b01fc1-9b1b-49fd-a4e4-277318af5417)  
Như vậy, tôi vừa tạo được một buffer gồm 120 bytes trong IDA, hoàn toàn phù hợp với thông tin khai báo trong mã nguồn. Từ khóa “dup” có nghĩa là lặp lại các dữ liệu trong ngoặc 120 lần (Nó tương đương với việc viết ?,?,?,?, … (120 lần)). Vì giá trị là chưa được xác định nên nó chỉ là một buffer rỗng.  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/bc2a7460-0576-481a-8f84-f90b4106a8a9)  
Các tham số của hàm sẽ được truyền vào thông qua lệnh PUSH, sau đó sử dụng một lệnh CALL để gọi hàm, lưu return address (r), và phía trên s là vùng dành cho các biến cục bộ khai báo trong hàm.  
Do các tham số được truyền vào ngăn xếp trước khi lưu địa chỉ trở về nên chúng sẽ nằm ở dưới và ở phía trên địa chỉ trở về là thanh EBP được lưu (tạo ra bởi lệnh PUSH EBP – thường là câu lệnh đầu tiên của hàm). Sau đó, ở trên là không gian dành cho các biến cục bộ. Chúng ta sẽ quan sát chi tiết hơn ở những phần tiếp sau.

Để xem nơi nào gọi tới hàm này ta nhấn “X”:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/94618fca-b71a-4508-a079-ca9530653598)  
Nhấn OK sẽ đưa ta tới nơi gọi hàm call  sub_401040. Trước lời gọi hàm ta thấy có một số lệnh PUSH truyền tham số cho hàm. Tuy nhiên, nếu chưa từng lập trình thì ta sẽ không rõ thông tin về các tham số này:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/3c0b466c-35f3-432c-86c6-910ece688a85)  
Trong trường hợp nạp crackme kèm theo symbol thì IDA nhận diện được đầy đủ các tham số truyền cho hàm như hình dưới đây:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/9c222437-f238-4942-88f0-7d8966f33d9c)  
IDA phát hiện ra rằng các tham số này không bao giờ được sử dụng, chúng là các tham số argc, argv và envp mà theo mặc định là các tham số của hàm main(). Nhưng vì không có bất kỳ tham chiếu hoặc sử dụng nào trong hàm, do vậy chức năng IDA sẽ loại bỏ chúng để tối ưu:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/0c1bbf44-ec1d-4dfb-9cd5-edca210efe5d)  
Bên cạnh đó, như các bạn thấy trong mã nguồn của crackme, thầy Ricardo cũng không sử dụng/ khai báo các tham số này cho hàm main(), nhưng khi load cùng với symbol thì chúng được sử dụng một cách mặc định. Quay trở lại với crackme, khi chúng ta muốn xem nơi mà một biến được truy cập, chúng ta chọn biến đó và nhấn “X”. Ví dụ, tôi chọn var_4:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/2563bd63-14e4-4e3f-8c73-3a789d82018e)  
Các bạn thấy rằng, biến var_4 sẽ được sử dụng ở hai vị trí.

_Bên lề: thông tin bổ sung thêm dành cho những ai chưa biết, đó là về giá trị cookie mà trong code chương trình không hề có. Đây là cơ chế bảo vệ của chương trình tránh khỏi lỗi stack overflow hay còn được gọi theo thuật ngữ chuyên ngành là Stack Canary. Giá trị này được lưu ở đầu của mỗi hàm và sẽ được kiểm tra trước khi thoát khỏi hàm. Chúng ta sẽ đặt tên cho nó là SECURITY_COOKIE:_  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/e3e460dc-0c91-4149-ad7f-0a67d9f12f65)  
Khi bạn chạy crackme, bạn để ý nó sẽ in các chuỗi ra màn hình. Điều này có nghĩa khi in các chuỗi, nó gọi tới một lệnh CALL như hình dưới. Nếu bạn đi sâu vào lệnh call này bạn sẽ thấy tác giả không lập trình ra chúng, nhưng chắc chắn cuối cùng nó cũng sẽ gọi tới hàm printf() để in các chuỗi ra màn hình.  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/07f3fb3d-ab68-49bc-bf97-88af8ebff909)  
Nếu load crackme cùng với symbols, thì IDA sẽ nhận diện được đó là hàm printf() như hình:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/9cb6f31e-8eb2-4fa1-93dd-c7cee545047b)  
Khi đào sâu vào bên trong lệnh CALL, ta sẽ thấy rằng các tham số là các chuỗi sẽ được in ra màn hình console thì ta hoàn toàn có thể suy luận rằng nó là hàm printf().  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/ce700485-9277-41cf-bb1e-ebba0a2f6c6b)  
Chúng ta thấy bên trong hàm kết thúc bằng hàm vfprintf, do đó đổi lại tên hàm như sau:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/807ac7f1-431a-43bd-a455-fb6a36bb046b)  
Như đã phân tích ở trên, chương trình có mảng Buf với kích thước 120-bytes, hãy phân tích xem code sẽ làm gì với Buf này. Ta thấy rằng nó được truyền vào cho hàm gets_s(), đó là hàm nhận những gì chúng ta nhập vào từ màn hình console.  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/f032c59e-4988-4d43-9100-db8db6858aa8)  
Hàm này nhận hai tham số truyền vào:

Tham số thứ nhất là một con trỏ trỏ đến buffer.
Tham số thứ hai là kích thước tối đa cho phép chúng ta gõ.
Hãy xem trong trường hợp ví dụ của chúng ta.  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/2bb10fd7-0b60-4d91-aba8-ea99f0e40576)  
Lệnh LEA được sử dụng để lấy ra địa chỉ của một biến, trong trường hợp này là con trỏ trỏ tới Buf, và buffer được truyền cho hàm thông qua lệnh PUSH EAX. Tiếp theo là PUSH 0x14 để quy định số lượng kí tự tối đa được gõ trên màn hình:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/fab6e20c-ada1-4dfa-8042-72b30b387b09)  
Chúng ta thấy trong mã nguồn của crackme, thầy Ricardo gọi hàm gets_s() với hai đối số là buf và kích thước tối đa mà thầy đã khai báo thông qua một biến được gọi là max, có giá trị 20. Tuy nhiên, trình biên dịch, để tiết kiệm không gian đã sử dụng luôn số 20 (ở hệ hexa là 0x14) như là một tham số cho hàm vì sau đó code không còn sử dụng đến biến max nữa.

Vì vậy, không cần phải thực thi crackme, tôi hoàn toàn biết được buffer của tôi sẽ dùng để chứa các ký tự mà tôi gõ tại màn hình console. Sau đó, ở đoạn code bên dưới, ta thấy cùng một con trỏ tới bộ đệm được truyền là một tham số cho hàm atoi():  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/e942756d-5c82-4514-bc5c-45b425fa9e7b)  
Thông tin hàm như sau:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/f3e85e23-fe90-467b-8a81-9c29e7448069)  
Hàm atoi() thực hiện chuyển đổi một string (kiểu char*) thành số nguyên int. Hàm sẽ trả về 0 nếu convert không thành công. Do vậy, ý tưởng ở đây là những gì bạn nhập trên màn hình sẽ được chuyển đổi sang số. Ví dụ nếu bạn nhập 41424344, nó sẽ chuyển đổi sang dạng hexa và lưu kết quả chuyển đổi vào thanh ghi EAX.  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/a8453816-e17c-4adc-92d5-2438049d387b)  
Như trên hình, hàm atoi() sau khi thực hiện sẽ lưu kết quả trả về trong thanh ghi EAX, tiếp theo gán vào thanh ghi ESI và sau khi được in ra màn hình sẽ tới đoạn code thực hiện so sánh giá trị của ESI với một số mặc định 0x124578.

Tóm lại, số ta gõ được chuyển đổi từ một chuỗi số thập phân sang số ở hệ thập lục phân và so sánh với hằng số trên. Nếu so sánh không bằng nhau (JNZ), ta sẽ phải nhận thông báo “Bad Reverser”, còn ngược lại nếu bằng thì sẽ nhận thông báo “Good Reverser”. Sử dụng Python bar của IDA để chuyển đổi số 0x124578 về dạng thập phân:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/fb92937a-3789-4a12-b835-9b7405770177)  
Kết quả sau khi chuyển đổi ta được một số thập phân là 1197432:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/e3b42700-490b-4ff0-9c2e-829d0c97ca09)  
Thực thi crackme và nhập số vừa tìm được:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/29fd98a5-17f3-4ddd-bedc-f9bbacd06cb6)  
**Tiếp theo, chúng ta sẽ tìm hiểu về tính năng Debugger của IDA. Bản thân IDA hỗ trợ nhiều kiểu Debuggers, để xem cách hoạt động của tính năng này chúng ta mở Crackme Cruehead gốc trong IDA.**  
_Bên lề: Trường hợp IDA hiển thị như hình dưới có nghĩa là đã có một file database trước đó được lưu rồi, ta chọn Overwrite nếu muốn tạo ra một cơ sở dữ liệu mới và ghi đè lên file cũ, lúc đó IDA sẽ tiến hành phân tích toàn bộ file lại từ đầu và tạo một file .idb mới._  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/89b1156c-768d-45c7-b4d5-c61f966f2c89)  
Tại màn hình Load a new file… của IDA bỏ chọn Manual load, nếu xuất hiện các cửa sổ khác thì cứ nhấn OK cho đến khi IDA load xong file. Sau khi IDA phân tích xong crackme, lựa chọn Debuggers bằng cách truy cập menu Debugger > Select Debugger như hình:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/90f09bb1-3c12-4697-a036-b5bccd87924b)  
Các bạn sẽ thấy có nhiều tùy chọn Debugger khác nhau, ta sẽ tìm hiểu dần dần khi có điều kiện. Ở bài viết này chúng ta sẽ lựa chọn Local Win32 debugger để bắt đầu (có thể lựa chọn trình debugger mặc định mỗi khi load một file mới bằng các tích chọn Set as default debugger).  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/4771d12c-90db-4f51-af96-1bc652fdc9c5)  
Qua đây, các bạn có thể thấy rằng, IDA không chỉ là một Loader với khả năng static disassembler rất tốt, cung cấp khả năng tương tác mạnh mẽ, mang lại hiệu qủa trong quá trình dịch ngược, mà còn hỗ trợ thêm cả việc debug nhằm hoàn thiện hơn. Tuy nhiên, cách hoạt động của debugger trong IDA sẽ khác một chút so với những gì các bạn đã làm quen với OllyDbg/ x64dbg.  
Trong Debuggers > Debuggers options chúng ta có các thiết lập như sau:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/ca078371-eca8-42b9-9fe6-110b374c7f53)  
Lựa chọn Suspend on process entry point để trình debugger dừng lại tại entry point của crackme. Chúng ta sẽ thực hiện những thay đổi như đã thực hiện trong phần trước. Trước tiên ta sẽ đổi màu các khối và đặt lại tên cho các sub_ như sau:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/0bbafeaf-faed-40ef-a4ae-d58b79794fd1)  
Sau đó, tại địa chỉ 0x401243 chúng ta đặt một Breakpoint tại lệnh nhảy (JZ) bằng cách nhấn F2 tại địa chỉ này. Tiếp theo, ta chuyển tới một lệnh nhảy khác mà chúng ta cũng đã phân tích trong phần trước, đặt một breakpoint tại lệnh nhảy này đồng thời đổi màu các khối tương tự như hình dưới đây:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/a8590f1c-4602-4443-962b-6b58e39fabd9)  
Sau khi thiết lập xong, ta thực hiện debug bằng cách chọn Debugger > Start Process hoặc nhấn phím tắt là F9, cửa sổ cảnh báo sẽ xuất hiện như hình:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/08493783-5153-43c0-9c97-23a30bc5b217)  
Cửa sổ cảnh báo trên sẽ luôn luôn xuất hiện khi chúng ta thực hiện debug một tệp thực thi trên máy local của mình. Bởi khi phân tích file bằng Loader của IDA, file đó sẽ không bao giờ được thực thi trên máy chúng ta, nhưng bây giờ nếu file được chạy để phục vụ debug, thì IDA sẽ đưa ra cảnh báo nhắc nhở phải cẩn thận khi thực hiện, vì có khả năng là đó một Virus hay cái gì đó có có thể gây nguy hiểm tới máy tính của chúng ta. Khi đó, ta cần sử dụng tính năng Remote Debugger và thực thi file trong một môi trường ảo hóa hoặc debug hoàn toàn bằng máy ảo. Việc sử dụng tính năng remote debugging như thế nào tôi sẽ cố gắng đề cập trong một bài viết khác.

Do ta đã biết Crackne này hoàn toàn sạch nên nhấn YES để tiếp tục quá trình debug:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/80a3ac85-0d5d-45a1-8245-04ab37118c0b)  
Vì chúng ta lựa chọn để dừng lại tại Entry Point, do vậy debugger đã dừng lại tại EP của crackme, tại địa chỉ 0x401000. Nếu bạn nhấn space bar, IDA sẽ chuyển qua chế độ đồ họa như ta đã làm việc ở LOADER. Tiếp theo, các bạn có thể bố trí lại các cửa sổ theo ý thích, thu hẹp lại cửa sổ Output Window, mở rộng cửa số Stack view, thiết lập cửa sổ General registers để quan sát được các thanh ghi cũng như trạng thái của các cờ như hình dưới:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/d3a172ac-1a9f-43aa-b091-3168b5245b55)  
Khi có được một màn hình bố trí hợp lý và phù hợp, ta sẽ lưu nó lại như một thiết lập mặc định. Tại IDA, vào Windows > Save Desktop, tích chọn Default như hình dưới. Làm như thế này, bất cứ khi nào ta thực hiện Debugger, IDA sẽ luôn nạp các thiết lập ta đã đặt mặc định và nếu chúng ta muốn thay đổi nó một lần nữa, ta có thể làm lại mà không có vấn đề gì:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/2a7038ae-60da-4bd8-a4fb-b46ac464a070)  
Bên dưới cửa sổ thanh ghi là cửa sổ chứa thông tin về Stack của chương trình:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/ee8f3349-ce17-4e5e-9227-3ce8cdf1970f)  
Ta có màn hình IDA-View EIP là nơi hiển thị toàn bộ các lệnh của chương trình và bên dưới là cửa sổ Hex View hay Hex Dump, hiển thị thông tin của bộ nhớ ở chế độ xem hexa:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/cdad0dda-9fc1-40c8-9d20-8f23d50c0b95)  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/453012bd-7ff6-44d2-aaab-664c50e9b0ad)  
Quan sát tại phần dưới của màn hinh disassembly chúng ta thấy các thông tin sau:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/7d950bfa-6be9-4e5b-bea0-25f214315358)  
Đây là các thông tin về File Offset (Offset của file thực thi khi lưu trên disk) và địa chỉ bộ nhớ (Virtual Address khi file được load vào memory). Nếu chúng ta mở crackme bằng một trình HexEditor, ví dụ như HxD, ta sẽ thấy tại File Offset là 0x600 có cùng opcode như đã thấy tại màn hình disassembly:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/6e9be629-6c27-46a3-b86e-bfa0453f46a0)  
Chúng ta đã biết rằng, phím tắt G được sử dụng để đi đến một địa chỉ bộ nhớ bất kỳ, nếu bạn ấn G và nhập vào địa chỉ 0x401389:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/b96fb466-3909-410a-90cb-74d8dd1fd5a0)  
Ta sẽ đi đến chỗ đã thiết lập breakpoint. Vào Options > General, cấu hình lại Number of opcode bytes (graph) là 0:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/03b69da4-2c7a-4bd3-824a-4f23f8a3b9a5)  
Trong cửa sổ View > Open Subview > Segments, chúng ta thấy có ba segments được nạp bởi Loader, trong đó CODE segment được nạp vào 0x401000, tiếp theo là DATA segment và .idata segment:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/75fd8a86-8605-4eaf-8d4e-d2ca380a3b31)  
Bất kỳ sự thay đổi nào chúng ta thực hiện với ba segments này đều được lưu lại vì chúng được nạp bởi IDA Loader, nhưng các thay đổi bên ngoài ba segments này sẽ bị mất bởi vì chúng chỉ là các mô-đun nạp bởi Debugger và sẽ không được lưu vào cơ sở dữ liệu của IDA. Như vậy, các mô-đun mà chúng ta đang phân tích và muốn debug phải nằm trong Loader hoặc có xuất hiện chữ L ở đó, rõ ràng chúng cũng sẽ được tải trong Debugger, nhưng có L đồng nghĩa là chúng sẽ ở trong cả hai chế độ và ta vừa có thể phân tích tĩnh tại Loader và vừa debug được bằng Debugger mà không bị mất thông tin.

Một trong những thanh công cụ mà tôi thấy luôn luôn hữu ích là Jump:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/d99e1404-96e1-41b0-b90e-3b713f11c622)  
IDA sẽ bổ sung thêm hai nút như hình dưới, tương tự như ta thấy ở các trình web browser. Sau khi cho hiển thị toolbar này, ta lưu lại màn hình Windows > Save Desktop.  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/f96f3c9c-31f2-4f85-9c19-e23e72195e98)  
Thanh công cụ này cho ta khả năng quay trở lại hoặc chuyển tới màn hình đã làm việc lúc trước một cách rất thuận tiện. Nhấn mũi tên  , ta sẽ quay lại màn hình entry point của crackme mà ta đã break trước đó:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/4cf946d0-06f9-4d4c-918a-8957b652c4df)  
Cũng ngay tại menu Debugger, ta có thể xem được danh sách các Breakpoints đã đặt thông qua Breakpoint list (Ctrl+Alt+B):  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/8c0abde1-8bc2-4054-8a24-9990c1876c65)  
Và đi tới bất kỳ bp nào mà ta muốn bằng cách nhấp đúp vào bp đó, ví dụ:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/a6647203-7c2e-4709-917d-321490b5d154)  
Lúc này, ta đang dừng lại tại Entry Point và có hai Breakpoints đã thiết lập trước đó, do vậy ta có thể nhấn F9 để thực thi crackme:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/a5a94a93-1439-40c2-96ff-eafc5b103314)  
Khi crackme đã thực thi hoàn toàn, vào Help > Register và nhập thông tin bất kì, ví dụ:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/21609b8a-2ced-4bb1-8d56-27c9710277a9)  
Sau đó nhấn OK, ta sẽ dừng lại ở bp tại địa chỉ 0x40138B:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/8e91e389-6b8a-4f24-84b8-3c85758c63c6)  
Mũi tên bên trái lúc này đang nhấp nháy, nó báo hiệu cho ta biết nơi vùng code tiếp theo sẽ tiếp tục thực hiện. Quan sát tại cửa sổ Registers, chúng ta thấy rằng thanh ghi EAX đang có giá trị 0x6D:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/4da05e17-9826-41f1-b9c5-6694fc67ed5d)  
Nếu tôi gõ chr (0x6D) tại thanh Python của IDA, kết quả trả về cho tôi chữ cái m trong chuỗi “manowar”.  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/73c33e09-3e26-419d-886a-26e009797ee8)  
Cũng tại thanh Python, làm tương tự như trên ta thấy với chr(0x41) sẽ cho kết quả là chữ A. Như vậy, kí tự này sẽ được so sánh xem liệu nó có thấp hơn giá trị 0x41 (‘A’) không?  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/4a1e0d6a-3b71-4864-94b0-3b97bd7e2a94)  
Bên cạnh đó, IDA cũng cung cấp cách biểu diễn khác hiển thị rõ ràng hơn, bằng cách một nhấp chuột phải vào giá trị 41h tại màn hình disassembly, trong số các tùy chọn mà chúng ta có thể lựa chọn giống hình dưới, ta thấy có xuất hiện ‘A’:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/0852bd6a-1f35-46fb-af44-4ca7ebe7d5d1)  
Làm tương tự với 0x5A, ta có được chữ cái ‘Z’. Chúng ta thấy nó so sánh giá trị 0x6D với A và Z. Tại thời điểm này, chưa phải lúc chúng ta tìm cách giải quyết hoàn toàn crackme này, nhưng các bạn thấy rằng 0x6D lớn hơn 0x41, do vậy ta sẽ không đi đến khối màu đỏ – thực hiện hiển thị thông báo lỗi. Rõ ràng, nó sẽ nhảy vào khối đỏ nếu nó thấp hơn, nhưng nó cũng có thể được đánh giá bằng cách quan sát trạng thái của các cờ.  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/a2fb8799-2cce-449e-8ff1-93fa5a10d4c0)  
Trong hình trên, chúng ta thấy lệnh nhảy JB nhảy theo mũi tên màu xanh lá cây trong IDA nếu nhỏ hơn. Khi thực hiện lệnh so sánh, cờ C (còn gọi là CF hoặc C) sẽ được kích hoạt, theo đó lệnh nhảy sẽ nhảy nếu cờ C = 1. Quan sát các cờ trong IDA:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/7415812e-17d6-4b5d-9b70-bc4c92625b77)  
Do cờ C = 0 nên lệnh nhảy sẽ không được thực hiện và nó sẽ theo hướng của mũi tên đỏ. Vậy điều kiện tính toán sẽ thế nào để cho cờ Carry Flag được kích hoạt?

Carry Flag cung cấp cho chúng ta thông tin rằng có gì đó đã sai trong một phép toán giữa các số nguyên unsigned. Nếu tôi làm phép trừ giống như cách thực hiện của lệnh CMP (không lưu lại kết quả), phép tính 0x6D-0x41 cho kết quả là 0x2C, đây là một số dương và sẽ không có vấn đề gì. Tuy nhiên, nếu ví dụ, giá trị của tôi là 0x30, bằng cách trừ đi 0x41, kết quả sẽ là -0x11:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/1817b606-72ec-4c52-89c3-92443560de76)  
Đây là một giá trị âm và không được chấp nhận là kết quả của một phép toán số dương, vì nếu bạn tiếp tục làm việc ở hệ thập lục phân:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/aa1c4cc9-1f10-4004-b0c1-89a5ea995125)  
Kết quả có được là 0xFFFFFFEF và đây sẽ là một số dương có giá trị rất lớn 4294967279 và không cách nào để thực hiện phép trừ 0x30 – 0x41 ra kết quả bằng 0xFFFFFFEF J.

Làm thế nào để chúng ta biết được cần phải quan tâm đến dấu của kết quả khi thực hiện một phép toán?

Điều này phụ thuộc vào lệnh nhảy, trong trường hợp này JB là một lệnh nhảy được sử dụng sau khi so sánh các số nguyên không dấu (unsigned). Đối với các phép toán giữa các số nguyên có dấu (signed) sẽ sử dụng lệnh JL. Ví dụ, nếu tôi so sánh số 0xFFFFFFFF với 0x40. Trong một lệnh nhảy không quan tâm đến dấu thì rõ ràng là số này lớn hơn, nhưng nếu nó là một bước nhảy nơi mà dấu cần được xem xét, lúc đó 0xFFFFFFFF sẽ là -1 và nó sẽ nhỏ hơn 0x40.

Vì vậy, để đánh giá liệu so sánh sử dụng dấu hay không, chúng ta phải xem xét lệnh nhảy tiếp theo để đưa ra quyết định.  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/d2d82607-bb1b-487d-9ba1-a24a21314376)  
Nếu lệnh nhảy là bất kỳ trong số trên, nó được xem xét là Without Sign, còn nếu thuộc danh sách trong bảng dưới thì được xem xét là With Sign.  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/dceeed9f-a659-415c-a6f6-3ed5cf6c3471)  
Các bạn thấy lệnh nhảy JE (nhảy nếu hai toán hạng bằng nhau) đều xuất hiện ở cả hai bảng vì trong trường hợp đó dấu không còn quan trọng nữa. Nếu hai toán hạng bằng nhau, nó sẽ được kích hoạt bằng cách đặt cờ ZF là 1. Chúng ta cũng thấy rằng lệnh JG (nhảy nếu lớn hơn) trong bảng tổng hợp nhảy có dấu. Cùng một mục đích nhảy như thế là lệnh JA, nhưng trong bảng tổng hợp nhảy không dấu.

Trong quá trình phân tích hàng ngày chúng ta sẽ thường xuyên phải quan sát trạng thái của các cờ, và nếu thấy một lệnh nhảy JB thì ta sẽ biết rằng đó là một so sánh giữa các số dương hay các số nguyên không dấu, và nếu toán hạng đầu tiên là nhỏ hơn thì nó sẽ nhảy.

Nếu tiếp tục thực thi chương trình, bạn sẽ thấy nó liên tục dừng lại ở breakpoint đã đặt, từ đó sẽ thấy rằng ta đang ở một vòng lặp thực hiện đọc từng kí tự của chuỗi tên và so sánh với 0x41, nếu có một kí tự nhập vào thấp hơn thì crackme sẽ hiển thị thông báo lỗi. Tuy nhiên, vừa rồi tôi nhập đều là các chữ cái (manowar) trong ô Name nên sẽ không xảy ra việc hiển thị thông báo lỗi này. Nhưng thử dừng quá trình debug lại và tiến hành debug lại từ đầu, lúc này tôi nhập tên là 22ricnar và key là 98989898:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/c5cfcb34-712e-4215-b8e7-bad5d532aaaa)  
Nhấn OK, ta lại dừng lại tại breakpoint tại địa chỉ 0x40138B:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/3893c39f-7eac-4772-aa6e-f8623e4fb8f9)  
Bây giờ, ta thấy rằng kí tự đầu tiên là 0x32 tương ứng với số 2 trong chuỗi 22ricnar. Vì 0x32 nhỏ hơn 0x41, mũi tên màu xanh lá cây sẽ nhấp nháy, tức là lệnh nhảy sẽ được thực hiện, cờ C được kích hoạt bởi vì khi lấy 0x32 trừ đi 0x41 trong một phép trừ unsigned, kết quả có được là số âm, đó là một lỗi sẽ kích hoạt cờ C.

Nếu nhấn chuột phải tại cờ C, ta có thể thiết lập nó về 0:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/66017da5-565d-4273-bb59-ed9096dc3325)  
Khi thiết lập xong thì đồng thời mũi tên đỏ cũng nhấp nháy vì chúng ta vừa mới thay đổi lại điều kiện nhảy  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/b0e0d028-5144-4744-94c9-cc084340e2f1)  
Nếu chúng ta cho Run tiếp, nó sẽ lại dừng lại tại breakpoint khi kiểm tra kí tự tiếp theo cũng là 2 trong chuỗi 22ricnar, và mũi tên màu xanh lá cây sẽ lại nhấp nháy một lần nữa. Làm tương tự như trên, ta đảo ngược lại cờ CF, thiết lập nó là 0. Những lần break tiếp theo tại lệnh nhảy này tương ứng với chuỗi ricnar, các kí tự từ lúc này trở đi đều lớn hơn 0x41, do đó sau phép so sánh sẽ không kích hoạt cờ CF và crackme sẽ rẽ nhánh theo mũi tên đỏ.

Sau khi vượt qua được quá trình kiểm tra từng kí tự trong chuỗi tên chúng ta dừng lại tại lệnh nhảy thứ hai (đã đặt breakpoint):  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/7f514597-5d52-4ad8-b66d-25cc97e147af)  
Tại đây, ta thấy crackme thực hiện so sánh xem EAX và EBX có bằng nhau không? Chuyển qua cửa sổ Registers, quan sát thấy giá trị của hai thanh ghi là khác nhau, do đó mũi tên đỏ nhấp nháy để báo hiệu sẽ rẽ nhánh vào đoạn code thông báo lỗi:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/b96c5fe1-cf43-4f9a-aded-e9213f3db1b0)  
Do giá trị của hai thanh ghi EAX và EBX không bằng nhau cho nên cờ Z không được bật:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/226dfa11-e733-4a9c-b41f-c2bf621e188d)  
Nếu ta thay đổi giá trị của cờ ZF, crackme sẽ rẽ nhánh theo hướng mũi tên màu xanh lá cây để tới vùng code hiển thị Good Work. Nhấn chuột phải tại cờ ZF và chọn Increment Value:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/ff9db3b4-d592-4ac8-b186-9d0a1c750371)  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/d0579e0f-b007-481c-828f-98acac48b479)  
Nhấn F9 để thực thi, ta có được kết quả như sau:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/fa996789-479f-473a-a51e-ea6b738772cf)  
Toàn bộ quá trình thực hiện ở trên cùng đạt một mục đích tương tự như khi chúng ta thực hiện patch crackme này, chỉ khác ở chỗ là ta không thay đổi code của chương trình, hoàn toàn công việc chỉ là thay đổi trạng thái các cờ trong quá trình debug.

Ngoài ra, có một cách khác là thay đổi thanh ghi EIP để nó trỏ tới lệnh tiếp theo được thực hiện. Ví dụ, giả sử ta đang dừng tại lệnh nhảy tại địa chỉ 0x401243, lúc này lựa chọn địa chỉ 0x40124c và nhấn chuột phải và chọn Set IP (hay nhấn phím tắt là CTRL + N):  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/9d5e4d59-8aa3-4c9e-930c-9a26c1f43f76)  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/2f242c44-91e3-417d-86aa-82b765f43b71)  
Lúc đó, chương trình sẽ tiếp tục thực hiện lệnh từ địa chỉ 0x40124c giống như ta đã làm khi thực hiện thay đổi các cờ  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/5dd99d6f-4add-4a39-b964-5283cd543f9f)  

**Tiếp theo, chúng ta sẽ đi đến với kiển thức về FLAGS(các cờ trong IDA)**  

1.CARRY FLAG (CF)  :  Cờ Nhớ  
Chúng ta đã tìm hiểu hoạt động của cờ CF (cờ nhớ) trong phần trước. Cờ này được kích hoạt trong quá trình tính toán của các số unsigned. Khi kết quả là số âm như ta đã gặp ở bài trước hoặc vượt quá mức biểu diễn tối đa trong trường hợp phép cộng. Hay nói cách khác, cờ CF được thiết lập là 1 khi có nhớ từ bit msb trong phép cộng hoặc có vay vào bit msb trong phép trừ.

Hãy quan sát các ví dụ sau thông qua trình debugger. Thực thi crackme.exe trong IDA thông qua debugger, ta dừng lại tại Entry Point của crackme (như đã cấu hình ở phần trước). Tại đây ta tiến hành patch lệnh như sau:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/708cf3d9-456c-4d4a-bb05-a0175e992d33)  
Khi patch như trên, chế độ graph của IDA sẽ bị lỗi, IDA tự động chuyển đổi về chế độ text. Để chuyển lại về chế độ graph, ta nhấn chuột phải tại địa chỉ entry point và chọn Create Function, sau đó nhấn phím space bar để chuyển lại về chế độ graph ban đầu. Tiếp theo, đặt lại giá trị cho thanh ghi EAX = 0xffffffff bằng cách nhấn chuột phải tại thanh ghi này và chọn Modify Value (E):  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/b45f455e-4be3-42ad-bda2-3d39adfea0b6)  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/e2450863-d094-4ffc-8fa3-52ac1ab145f8)  
Sau khi thay đổi xong, nhấn F8 trace qua lệnh đã patch để thực hiện lệnh add này. Quan sát ta sẽ thấy cờ CF được kích hoạt do phép cộng bị tràn vượt quá số dương lớn nhất:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/c0d119e4-5f51-4008-b013-c7b9506708f9)  
Điều tương tự xảy ra nếu chúng ta thực hiện patch lệnh bên dưới thành SUB EAX, EDX:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/be56ace2-8c4f-4864-aed0-8eee77371fef)  
Sau đó, thay giá trị của EAX thành 0x25 và EDX thành 0x40. Nhấn F8 để thực hiện lệnh và quan sát xem liệu CF có được kích hoạt hay không? Chúng ta đã thấy rằng, nếu trừ hai số dương cho nhau và kết quả là số âm (Signed = -27 ~ 0xFFFFFFE5), cờ CF sẽ được bật lên:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/b799a618-18b5-4d0a-b60c-72c5a9d4366c)  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/f3942290-899c-415c-ae36-1b66e8240ce4)  
Như vậy là cờ CF đã được kích hoạt. Nếu tôi tiếp tục cho thực hiện phép trừ này một lần nữa bằng cách nhấn chuột phải tại lệnh SUB và chọn Set IP, nhưng lần này tôi thay giá trị của thanh ghi EAX thành 0x100, giá trị của EDX vẫn giữ nguyên.  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/29fd3f42-4178-4e15-8a42-3532a95d13d7)![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/637fdfa1-8eea-4b9b-a1f8-fc7c00935200)  
Tiếp theo F8 để trace qua lệnh:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/2c19f156-3ad3-41a4-a19d-8dd178aa991b)  
Ta thấy cờ CF không được kích hoạt.

Như vậy, các quy tắc để bật cờ CF (carry) trong phép toán nhị phân/số nguyên là:

Cờ carry được bật nếu phép cộng hai số dẫn đến bit có trọng số lớn nhất bị đẩy ra ngoài – vượt quá khả năng biểu diễn. Ví dụ: 1111 + 0001 = 0000 (CF được bật)
Cờ carry được bật nếu phép trừ hai số dẫn tới việc cần phải vay vào bit có trọng số lớn nhất để trừ. Ví dụ: 0000 – 0001 = 1111 (CF được bật)
Trường hợp ngược lại, cờ CF không được bật (bằng 0):

0111 + 0001 = 1000 (carry flag thiết lập bằng 0)
1000 – 0001 = 0111 (carry flag thiết lập bằng 0)  

2.OVERFLOW FLAG (OF) : Cờ Tràn  

Cờ OF (cờ tràn) cũng tương tự như cờ CF, nhưng đối với các tính toán liên quan đến số có dấu (signed). Cờ OF được thiết lập 1 khi xảy ra tràn, ngược lại nó bằng 0. Hiện tượng tràn gắn liền với một sự thật là phạm vi của các số biểu diễn trong máy tính có giới hạn. Ví dụ, phạm vi của các số thập phân có dấu có thể biểu diễn bằng một word 16 bit là từ -32768 đến 32767, với một byte 8 bit thì phạm vi là từ -128 đến 127. Đối với các số không dấu thì phạm vi từ 0 tới 65535 cho một word và từ 0 đến 255 cho một byte. Nếu kết quả của một phép tính nằm ngoài phạm vi thì hiện tượng tràn sẽ xảy ra và kết quả nhận được bị cắt bớt sẽ không phải là kết quả đúng. Xem xét một vài ví dụ dưới đây.  

Giờ ta thay đổi EIP về lại câu lệnh ADD EAX, 1 và đặt lại giá trị của thanh ghi EAX thành 0x7fffffff (signed: 2147483647):  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/4cf40caf-9794-4f37-b908-af785f5a9735)  
Sau đó nhấn F8 để thực hiện lệnh:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/0f9a8053-ac63-4b88-a158-63d439534182)  
Chúng ta thấy rằng cờ OF đã được kích hoạt sau khi thực hiện lệnh. Đó là vì khi cộng 1 vào số dương 0x7fffffff, nếu xem xét đây là thao tác tính toán với số có dấu (signed) thì sẽ khiến kết quả sau khi thực hiện là số âm nhỏ nhất (signed: -2147483648) và dẫn đến kết quả sai:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/5c7a45d7-9959-422e-ad0f-a9ef03aa8862)  
Nếu thực hiện phép trừ EAX cho EDX với các giá trị như trên:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/16d66eef-bd88-4c41-adf7-c92f58c7dcfe)  
Cờ OF vẫn sẽ được kích hoạt bởi vì khi lấy số âm nhỏ nhất là 0x80000000 trừ đi 0x40 cho kết quả là một giá trị dương rất lớn (0x7FFFFFC0) và khiến cho kết quả của phép toán sai. Do đó, chúng ta có thể kết luận rằng cờ OF được kích hoạt khi có lỗi xảy ra trong quá trình tính toán với dấu. OF được bật khi bit có trọng số cao nhất (được xem là bit dấu) bị thay đổi bằng cách cộng hai số có cùng dấu (hoặc trừ hai số có dấu ngược nhau). Tràn không bao giờ xảy ra khi các dấu của hai toán hạng cộng là khác nhau (hoặc dấu của hai toán hạng trừ là giống nhau).

Như vậy, một số quy tắc để bật OF (overflow) trong phép toán nhị phân/số nguyên là:

Nếu tổng của hai số với bit dấu tắt tạo ra kết quả là một số với bit dấu bật, cờ “overflow” sẽ được bật. Ví dụ: 0100 + 0100 = 1000 (OF được bật)
Nếu tổng của hai số với bit dấu bật tạo ra kết quả là một số với bit dấu tắt, cờ “overflow” sẽ được bật. Ví dụ: 1000 + 1000 = 0000 (OF được bật)  

3.SIGN FLAG (SF) : Cờ Dấu  

Cờ này khá đơn giản, nó được kích hoạt khi kết quả của việc tính toán là số âm, trong mọi trường hợp. Nó chỉ quan tâm tới kết quả của dấu mà không cần quan tâm kết quả tính toán đúng hay sai. Hay nói cách khác, cờ SF (cờ dấu) được thiết lập 1 khi bit msb của kết quả bằng 1, có nghĩa là kết quả là âm nếu ta làm việc vơi số có dấu.

Ví dụ như sau:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/b0cae2d8-5217-47c4-898e-5bf3894b91ad)  
Kết quả của0x8000000 cộng 0x1 vẫn nằm trong dải số âm, là 0x8000001, vì vậy SF được kích hoạt. Chúng ta cũng thấy rằng OF và CF không được kích hoạt vì không có lỗi trong quá trình tính toán của cả signed hoặc unsigned.  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/10208252-521a-41c3-b150-9cde6b1b06b2)  
Rõ ràng, bộ xử lý khi thực hiện một lệnh liên quan tới tính toán hai thanh ghi, nó không hề biết các thanh ghi này là signed hay unsigned. Còn chúng ta có thể biết được là bởi vì ta thấy các lệnh nhảy có điều kiện ở phía dưới, ngược lại bộ xử lý không biết, do đó, trong bất kỳ hoạt động nào nó cũng sẽ xem xét các lệnh như thể là signed hoặc unsigned tại cùng một thời điểm và thay đổi các cờ cần thiết.

Vì các lệnh nhảy có điều kiện phụ thuộc vào cờ, chương trình sẽ nhìn vào kết quả của các cờ CF (trong phép tính unsigned) hoặc OF (trong phép tính signed) để từ đó đưa ra quyết định nhảy. Ví dụ, nếu có một lệnh JB (lệnh này là unsigned), do đó nó sẽ chỉ nhìn vào cờ CF và không quan tâm đến cờ OF ngay cả khi cả hai cờ này đều được kích hoạt.

Như vậy, trách nhiệm thuộc về người lập trình, người có quy ước về kết quả. Nếu đang làm việc với số có dấu thì chỉ có cờ OF đáng quan tâm trong khi cờ CF có thể bỏ qua, ngược lại khi làm việc với số không dấu thì cờ quan trọng là CF chứ không phải là OF.  

4.ZERO FLAG (ZF)  

Cờ này không phụ thuộc vào dấu  
 ![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/16d3f0fc-0f0a-49c8-9096-e53838b5b4bf)  
 Nó được kích hoạt khi:

Phép so sánh (sử dụng một phép trừ) khi cả hai toán hạng đều bằng nhau.
Khi tăng hoặc giảm và kết quả là bằng không, hoặc trong một phép trừ mà kết quả có được bằng 0.
Chúng ta có thể chứng minh điều đó:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/d44ec7e2-0c53-4df0-9807-41eb6dd3bc0f)  
Ta thiết lập EAX có giá trị 0xffffffff và cộng thêm 1 vào EAX. Điều gì sẽ xảy ra?  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/5292a233-a373-425c-90db-43e391162172)  
Chúng ta thấy rằng cờ ZF được kích hoạt vì kết quả bằng 0 và nếu ta xem xét cả số unsigned thì cờ CF cũng được kích hoạt vì có tràn khi cộng 1 vào số dương lớn nhất. Trong khi đó, cờ OF không được kích hoạt bởi vì cả hai đều là số có dấu, -1 + 1 = 0 và không có lỗi. Cờ SF cũng không kích hoạt vì kết quả không phải là số âm.

Chúng ta thấy trạng thái các cờ rất quan trọng. Hãy xem liệu lệnh nhảy có điều kiện kế tiếp có xảy ra hay không? Ta patch lệnh SUB EAX, EDX như trên và bên dưới là lệnh nhảy JB 0x401018:   
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/82b51eb2-f9a3-43d9-a559-ba74b4c3ac3e)  
Sau đó gán EAX = 0x40 và EDX = 0x2, nhấn F8 để thực hiện lệnh SUB:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/ee0d8203-9a3d-4cf1-8ad2-c3e44703bdab)  
Mũi tên màu đỏ sẽ nhấp nháy vì thanh ghi EAX lớn hơn thanh ghi EDX, do đó lệnh nhảy sẽ không thực hiện. Ta quan sát các cờ.  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/b4bde26a-f2de-4428-9c96-1f2049ad740f)  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/d5792af7-efbf-4159-a459-ef474ecea02a)  
JB là một lệnh nhảy unsigned và chỉ nhảy nếu cờ CF được kích hoạt. Rõ ràng ở đây cờ CF không được kích hoạt vì quá trính tính toán là chính xác giữa hai số dương cho ra kết quả là số dương, có nghĩa số đầu tiên lớn hơn số thứ hai, như vậy sẽ không thực hiện nhảy.  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/f678ded6-c048-4377-a2ea-876c818412c8)  
Nhưng nếu chúng ta thay đổi EAX thành 0x40 và EDX thành 0x80 và thử lặp lại lệnh trừ một lần nữa:  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/f97b4f89-fb91-4ad7-aa9d-4566ce0c9a4b)  
Trong trường hợp này, vì EAX nhỏ hơn EDX, lệnh nhảy JB sẽ được thực hiện và đi theo hướng của mũi tên màu xanh lá cây.  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/07868df8-fecf-481b-b482-294039b78f05)  
Vì khi lệnh JB nhìn vào cờ CF, nó sẽ nhảy vì cờ CF đã được bật. Vì kết quả của một thao tác unsigned là số âm và đã gây ra lỗi. Cờ SF cũng được kích hoạt vì kết quả là âm, còn cờ OF không được kích hoạt.

Lệnh JB sẽ nhảy căn cứ theo trạng thái của cờ CF nhưng nếu tôi thay đổi thành lệnh JL.  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/aadc4b9e-0435-4110-a9d5-dc6e32016bbf)  
Trong trường hợp này, hướng thực hiện thay đổi và đi theo mũi tên màu xanh lá cây bởi vì toán hạng đầu tiên nhỏ hơn toán hạng thứ hai, nhưng lệnh nhảy JL căn cứ vào cờ nào?  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/140e4c15-f338-4f10-8f31-315c5376ac47)  
Chúng ta thấy rằng lệnh JL sẽ thực hiện nếu cờ SF khác 0. Trong trường hợp này cờ SF = 1, do vậy sẽ nhảy và cũng là logic vì toán hạng đầu tiên nhỏ hơn toán hạng thứ hai. Lệnh SUB tương tự cách hoạt động của lệnh so sánh CMP, chỉ khác là SUB lưu kết quả còn CMP thì không.

Kết luận của bài viết này là không nhất thiết phải nhìn vào cờ để biết điều gì sẽ xảy ra với các nhảy có điều kiện, nó thuộc về hoạt động nội bộ bên trong. Chúng ta chỉ cần nhớ rằng, nếu hai toán hạng bằng nhau, lệnh JZ sẽ thực hiện. Nếu toán hạng đầu nhỏ hơn và là unsigned, thì sẽ nhảy nếu nó là lệnh JB. Còn nếu toán hạng đầu nhỏ hơn nhưng ở kiểu signed thì sẽ nhảy nếu là lệnh JL. Như vậy, ta chỉ cần quan sát cột thứ ba trong bảng signed và unsigned là đủ.  

**_THE END_**





















  



















































































































