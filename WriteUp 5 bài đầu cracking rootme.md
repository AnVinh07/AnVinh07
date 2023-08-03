_**WriteUp 5 Bài Cracking đầu tiên trên rootme**_  

**1.Bài ELF x86 - 0 protection:**    
- Copy link bài.
- Mở terminal, gõ wget + link bài.  
- Tiếp theo, mình chạy file xem có gì. Gõ lệnh chmod +x tên file, gõ tiếp ./tên file.
- Ta thấy file bảo chúng ta phải nhập pass word. Nhập đại chuỗi kí tự nào đó.
- Ta thử kiểm tra chuỗi kí tự trong file xem có gì bằng lệnh: strings + tên file | grep password. thêm grep password để nó tìm luôn pass.
- Có pass, chúng ta chạy lại file và nhập pass thử xem.
- đúng rồi
flag:

**2.Bài ELF x86 - Basic**  
- Copy link bài.
- Mở terminal, gõ wget + link bài.
- Tiếp theo, mình chạy file xem có gì. Gõ lệnh chmod +x tên file, gõ tiếp ./tên file.
- Ta thấy file bảo chúng ta phải nhập pass word. Nhập đại chuỗi kí tự nào đó.
- Ta thử kiểm tra chuỗi kí tự trong file xem có gì bằng lệnh: strings + tên file | grep password. thêm grep password để nó tìm luôn pass
- Có pass, chúng ta chạy lại file và nhập pass thử xem.
- đúng rồi
flag:
**3.PE x86 - 0 protection**
- Copy link bài.
- Mở terminal, gõ wget + link bài.
- Tiếp theo, mình chạy file xem có gì. Gõ lệnh chmod +x tên file, gõ tiếp ./tên file.
- Ta thấy file bảo chúng ta phải nhập pass word. Nhập đại chuỗi kí tự nào đó.
- Ta thử kiểm tra chuỗi kí tự trong file xem có gì bằng lệnh: strings + tên file | grep password. thêm grep password để nó tìm luôn pass.
- Không ra pass, ta cho file vào IDA để nó phân ra.
- Ta dùng chức seach text trong IDA để tìm đến vị trí pass.
- Tới vị trí pass, ta bấm phím f5 để chuyển sang chế độ code C, để dễ đọc.
- Sau khi đọc code, ta thấy pass nhập vào sẽ được đem đi so sánh với hàm sub_40126. Ta nghĩ ngay nó là hàm này sẽ chưa pass.
- Tìm vào hàm này và chuyển nó sang code C, ta thấy có chứa một câu lệnh if dùng để so sánh từng kí tự của chuỗi vừa nhập và pass đúng, nên đây chắc chắn là pass.
- Ta chuyển nó sang kiểu char để lấy pass.
- Ta chạy lại file và nhập pass vừa tìm. Và kết quả.
flag:

  
