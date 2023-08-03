_**WriteUp 5 Bài Cracking đầu tiên trên rootme**_  

**1.Bài ELF x86 - 0 protection:**    
- Copy link bài.
- Mở terminal, gõ wget + link bài.  
- Tiếp theo, mình chạy file xem có gì. Gõ lệnh chmod +x tên file, gõ tiếp ./tên file.
- Ta thấy file bảo chúng ta phải nhập pass word. Nhập đại chuỗi kí tự nào đó.
- Ta thử kiểm tra chuỗi kí tự trong file xem có gì bằng lệnh: strings + tên file.
![bài 1 rootme](https://github.com/AnVinh07/AnVinh07/assets/131764804/4c053752-59a3-4a5c-8fc9-34b2eba90f14)  
- Có pass, chúng ta chạy lại file và nhập pass thử xem.
![bài 1](https://github.com/AnVinh07/AnVinh07/assets/131764804/f5351f45-9e80-4201-a8bf-8fe784456cfa)    
- đúng rồi  
flag: 123456789

**2.Bài ELF x86 - Basic**  
- Copy link bài.
- Mở terminal, gõ wget + link bài.
- Tiếp theo, mình chạy file xem có gì. Gõ lệnh chmod +x tên file, gõ tiếp ./tên file.
- Ta thấy file bảo chúng ta phải nhập username và pass word. Nhập đại chuỗi kí tự nào đó.
- Ta cho file lên IDA 32bit để phân tích.
- Ta chuyển thành code C cho dễ đọc.
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/c7714645-8652-410b-acbe-6e612bd0839c)  
- Có pass, chúng ta chạy lại file và nhập pass thử xem.
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/0c827af7-774c-47ff-a8ae-f7955e2a99d2)  
- đúng rồi
flag: 987654321
**3.PE x86 - 0 protection**
- Copy link bài.
- Mở terminal, gõ wget + link bài.
- Tiếp theo, mình chạy file xem có gì. Gõ lệnh wine + tên file + password
![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/f83ce7c8-0157-4b76-90de-84bbc79bcd61)  
- Ta cho file vào IDA để nó phân ra.
- Ta dùng chức seach text trong IDA để tìm đến vị trí pass.
- Tới vị trí pass
- ![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/60dc2480-f5ce-48b5-9724-5d7ac4f1a81f)
- Ta thấy in ra các dòng wrongpass và greatman, kế bên nó có chú thích hàm.
- ![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/b35a6aca-0e04-4538-9f7c-beea1161b125)
-  ta thấy pass nhập vào sẽ được đem đi so sánh với hàm sub_40126. Ta nghĩ ngay nó là hàm này sẽ chứa pass.
- Tìm vào hàm này và chuyển nó sang code C, ta thấy có chứa một câu lệnh if dùng để so sánh từng kí tự của chuỗi vừa nhập và pass đúng, nên đây chắc chắn là pass.
- ![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/27745800-b4ec-4621-870e-260efbd437b0)

- Ta chuyển nó sang kiểu char để lấy pass.
- Ta chạy lại file và nhập pass vừa tìm. Và kết quả.
- ![image](https://github.com/AnVinh07/AnVinh07/assets/131764804/e19185e2-f49f-4950-9d93-f9f4bb528a7c)

flag: SPaCIoS

  
