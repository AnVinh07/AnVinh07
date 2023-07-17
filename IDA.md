_**CÁCH THỨC HOẠT ĐỘNG CỦA IDA**_

**#CÁC THAO TÁC CƠ BẢN**

_- Khi nạp file vào IDA_  
```
Khi bạn nạp một file vào IDA, IDA sẽ có gắng nhận diện định dạng của file đó   
(ví dụ: IDA đã nhận biết chính xác crackme là một PE file) cùng với kiểu kiến trúc vi xử lý.  
Quá trình này cho ta thấy IDA hành xử giống với Windows loader.   
Mặc định, IDA sẽ không load kèm PE header hay resource section trong quá trình phân rã code của nó.  
Nếu bạn tích chọn Manual load, IDA sẽ hỏi bạn khi bạn muốn nạp từng section, kèm theo PE header.

Giữ nguyên các thiết lập mặc định và nhấn OK để IDA tiến hành phân tích file.  
Nếu chúng ta đồng ý hiển thị chế độ Proximity View (có từ phiên bản IDA v6.2),  
một màn hình graph sẽ xuất hiện cung cấp thông tin về các hàm mà IDA nhận diện được trong Crackme theo mô hình cây như bên dưới.  
Tóm lại, ở chế độ proximity view này, địa chỉ hiện tại,  
hàm gọi (caller) và hàm được gọi (callee) được biểu diễn dưới dạng tập hợp các node có liên kết với nhau:
```
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/e0232123-405a-4a0a-97f0-230ecbad425e)

_- Thao tác chuyển qua lại giữa chế độ đồ hoạ (Graph) hoặc các lệnh ở chế độ Text bình thường_  
```
Để chuyển qua lại giữa chế độ đồ hoạ (Graph) hoặc các lệnh ở chế độ Text bình thường, ta sử dụng phím tắt là spacebar(phím cách trên bàn phím).
```
  
_- Khi phân tích một tệp tin thực thi_  
```
Đầu tiên IDA mở ở chế độ Disassembler (hay có thể được gọi là Loader) và sẽ không thực thi chương trình.
IDA lúc này chỉ phân tích tĩnh(static) để phục vụ cho mục đích reversing và tạo tệp tin ".idb" (cơ sở dữ liệu) để lưu toàn bộ quá trình phân tích của chúng ta.
```
 ![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/4b0ba74e-4d5c-48ce-8ad6-25db44380d0a)  

_- IDA cung cấp rất nhiều cửa sổ được xem như là các tab và khi truy cập View-Open Subview, chúng ta có thể mở các tab này theo mục đích sử dụng riêng của từng người._  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/b1f22139-d7b0-4d1b-b1be-ece77bc93a03) 

_- Tại màn hình graph của IDA sẽ thấy xuất hiện nhiều chỗ có cùng một địa chỉ.  
Ví dụ như tại điểm bắt đầu của một hàm, các địa chỉ được lặp lại nhiều lần.   
Điều này xảy ra là bình thường vì có rất nhiều thông tin từ địa chỉ đó và nó không đủ để biểu diễn / hiển thị trên cùng một dòng._  
![image-15](https://github.com/AnVinh07/AnVinh07/assets/131764804/ec79658a-281e-48eb-a674-37ad9b6813ff)
_- Mẹo nhỏ là tìm điểm kết thúc của cùng một địa chỉ được lặp lại, tại đó chúng ta sẽ thấy nơi bắt đầu của lệnh được disassembly.  
Trong trường hợp này, lệnh tương ứng với địa chỉ 401000 là push 0._  
_- Trong IDA, ta có thể độc lập tùy chỉnh giao diện mặc định cho Loader và Debugger.  
Tại màn hình Loader, khi đã thiết lập các cửa sổ và các tab mà chúng ta sử dụng nhiều nhất theo ý thích của mình,  
ta có thể lưu lại bằng cách vào Windows-Save Desktop và tích chọn Default để lưu vào cấu hình mặc định.  
Tương tự, trong chế độ Debugger, ta cũng có thể tùy chỉnh theo ý muốn và lưu lại thành cấu hình mặc định._  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/84a1bf36-276f-4e3d-b6d1-a409a6f6a802)  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/f1239fc1-3835-421d-863e-5988e5e7211d) 

_- Chúng ta có thể tìm kiếm tại các cửa sổ này bằng cách nhấn CTRL + F, khung tìm kiếm nhỏ sẽ xuất hiện bên dưới, cho phép lọc theo những gì chúng ta nhập vào.  
Trong View-Open Subview-Strings như ở ví dụ bên dưới, IDA sẽ hiển thị cho ta những chuỗi có chứa “try”:_  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/488d9acc-c0d0-4d8c-ac2b-a51c0be2d8f3)  
_- Ngoài ra, nếu chọn View-Open Subview-Disassembly, thì có thể mở một cửa sổ disassembly thứ hai,  
qua đó có thể truy xuất tới các hàm khác với cửa sổ đầu tiên và như vậy ta có thể phân tích được nhiều hàm tại cùng một thời điểm._  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/2073bf46-9495-4585-9a94-fe32498902b9)  
_IDA cũng cung cấp một cửa sổ hiển thị dạng hexadecimal(hex), cửa sổ này có tên là Hex View_  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/168d6451-49ba-4b7a-9e10-c0aaa64e007d)  
_- Nếu bạn không thấy cửa sổ này, có thể hiển thị nó qua View –> Open Subview -> Hex dump.  
Cũng từ Open Subview, ta có thể truy xuất thông tin về các hàm được chương trình import thông qua Imports tab như hình dưới đây:_  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/38ec1622-08dc-46cd-a14f-cbbd3080fd75)

**#Chuyển đổi giữa các hệ số trong IDA**  

_- Quan sát tại giao diện của IDA, nếu như bạn đã cài đặt chuẩn thì ở dưới cùng sẽ thấy có một thanh dùng để thực hiện các câu lệnh Python.  
Điều này sẽ hỗ trợ chúng ta chuyển đổi giữa các hệ số một cách rất dễ dàng:_  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/e8e20faf-c163-4481-afb9-6e6045c77b87)  
_- Ví dụ 0x45, giá trị này được diễn giải như là một số thập lục phân bởi vì có tiền tố 0x ở phía trước.  
Chúng ta có thể chuyển đổi từ thập lục phân sang thập phân chỉ bằng cách nhấn Enter.  
Kết quả có được là:_  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/9bfa77d8-c61e-4b3f-beba-6bdf03282b31)  
_- Ta nhận được số 69, đó chính là 0x45 ở hệ thập lục phân chuyển đổi sang hệ thập phân. Nếu muốn chuyển đổi ngược lại, chúng ta phải sử dụng hàm hex():_  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/8cd39db2-fe3f-4cda-9fff-9d113986c1b4)  
_- Tương tự hệ nhị phân, dùng bin(), 0b..., để chuyển đổi._  
_- Có thể chuyển đổi nhanh bảng mã ASCII trong IDA thông qua IDA calculator.  
Nó sẽ xem xét các biểu thức và hiển thị các kí tự tương ứng như chúng ta đã thấy trong trường hợp 0x45 là kí tự E._
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/408d9c0c-7352-45a0-9d5d-32f03cc6566e)  
_- Trong cửa sổ Hex Dump của IDA chúng ta cũng có một cột để hiển thị các kí tự tương ứng:_  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/f6a92179-bd67-4992-b7b7-d4d7bd97bf6d)

**#Chức năng tìm kiếm trong IDA**

_- IDA sẽ cung cấp cho chúng ta nhiều lựa chọn tìm kiếm rất dễ hiểu.  
Khi chuyển sang các tab khác (trừ tab Hex-View) thì các tùy chọn của Search sẽ được lược bớt._  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/7f133183-4208-4356-9f29-9bbc5f52b1a7)

**- Các chức năng trong Search**

1.Next Code  
_Tùy chọn này sẽ tìm kiếm lệnh kế tiếp đã được hiểu là Code. Nếu như không được nhận diện là Code, nó sẽ bỏ qua._  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/d9ecd60e-2fee-42ef-ad4c-12a9dc60d3d9)  
2.Next Data  
_Tùy chọn này sẽ tìm kiếm các địa chỉ tiếp theo, nơi được IDA hiểu là Data (đã được định nghĩa) hoặc là xử lý dữ liệu tại bất kỳ section nào._  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/656144a9-19fb-4936-9a65-c1ec46aa0522)  
3.Search Explored và Unexplored  
_Tính năng đầu tiên sẽ tìm code hoặc data đã được định nghĩa (lệnh hoặc dữ liệu)  
Và tính năng thứ hai áp dụng với các vùng không được phát hiện là lệnh hoặc dữ liệu hợp lệ:_  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/61a02746-7f13-4dfb-82a7-0a242f6075ed)  
4.Search Immediate Value – Search Next Immediate Value  
_Lệnh này tìm kiếm lệnh đầu tiên hoặc byte dữ liệu có chứa giá trị hằng số được chỉ định.  
Quá trình tìm kiếm tương đối chậm (nhưng nhanh hơn nhiều so với tìm kiếm text):_  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/c7075cad-b7ad-4e30-9ece-8da56a761435)  
_Nếu chọn Find all occurrences, IDA sẽ tìm tất cả. Còn nếu không chọn, IDA sẽ tìm kiếm từng lần một,  
với trường hợp này, để lặp lại việc tìm kiếm, ta sẽ sử dụng Search next immediate value._  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/41ae65fa-f17a-4a1a-863a-f607913353a2)  
5.Search Text – Search Next Text  
_Tìm kiếm các chuỗi mà chúng ta nhập vào, bao gồm cả biểu thức chính quy nếu chúng ta muốn._  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/d2eb1817-a838-467a-b65c-bff00a64f447)  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/bd0959fb-c170-4d0a-952d-72b0bd33a5e0)  
_Nếu chúng ta chỉ tìm kiếm một lần, để tìm tiếp thì sử dụng Search Next Text._  
6.Search Sequence Of Bytes  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/918094d0-ac24-4182-875c-5dd095b9170d)  
_Tùy chọn này cho phép ta tìm kiếm các chuỗi các bytes ở hệ thập lục phân:_  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/02b1e146-01a2-414b-8124-895972b15fbc)  
_Nếu nhấp đúp vào kết quả đầu tiên và tại tùy chọn của IDA,  
ta thiết lập giá trị 6 tại Number of opcode bytes để hiển thị tối đa 6 bytes tương ứng với mỗi lệnh._  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/80256aab-a29d-4edc-973b-2f1e0a1e1963)  
7.Search Not Function  
_Chức năng này cho phép tìm kiếm byte đầu tiên không thuộc về bất kỳ hàm nào:_  
Search completed. Found at 004013D7.  
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/d7534a18-9f26-43cc-adaa-3e4453473228)  





























