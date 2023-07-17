**BÀI TẬP CODE ASM**  
_BÀI 1: NHẬP CHUỖI, IN RA CHUỖI VỪA NHẬP_
```
        .model small  
        .stack 100  
        .data  
            tb1 DB 10,13, 'chuoi da nhap la: $';10 xuong dong , 13 lui dau dong*   
            str DB 100 dup('$') ;  
        .code  
          main proc  
            mov ax, @data  
            mov ds,ax ;khoi tao thanh ghi ds   
              
           ; nhap chuoi ky tu
            mov ah,10 ;    10=0ah
            lea dx,str ; tro den dia chi dau str
            int 21h 
            
            mov  ah,9 ;in mot xau ky tu   
            lea dx,tb1 ;
            int 21h   ;in ra
                
            ;dua dx chi ve phan tu thu 2 cua mang la  
            ; ky tu dau tien duoc nhap vao
            lea dx,str +2 ;256,5,h,e,l,l,o      
            int 21h  ;in na
            
            mov ah,4CH
            int 21h    
        main endp
     end
``` 
_BÀI 2: NHẬP CHUỖI, IN RA CHUỖI HOA_
```
.Model Small  
.Stack 100h  
.Data  
str DB 256 dup('$') ;khoi tao chuoi str  
tb2 DB 10, 13, 'Chuyen sang chuoi in hoa: $'    
.Code  
    main proc  
        MOV AX, @Data  
        MOV DS, AX ;khoi tao thanh ghi ds  
         
        ;Nhap chuoi:
        LEA DX, str  ;tro den dia chi chuoi str
        MOV AH, 10  ; nhap xau ngat 10
        INT 21H 

        ;In chuoi in hoa:
        MOV AH, 9
        LEA DX, tb2 ;hien thong bao tb2
        INT 21H
        CALL inhoa  
        
        MOV AH, 4ch
        INT 21H
    main endp 
    inhoa proc
        LEA SI, str+2
        Lap2:
            MOV DL, [SI]
            CMP DL, 'a'
            JL In2
            CMP DL, 'z'
            JG In2
            SUB DL, 32  ;chuyen ky tu thuong thanh ky tu hoa
        In2:
            MOV AH, 2
            INT 21H
            INC SI
            CMP [SI], '$'
            JNE Lap2
        RET
    inhoa endp
    end
``` 
_BÀI 3: NHẬP CHUỖI, IN RA ĐỘ DÀI CHUỖI VỪA NHẬP_
```
.MODEL small  
.STACK 100  
.DATA  
    tb1 DB "Nhap vao 1 chuoi: $"  
    tb2 DB 10,13,"Tong chieu dai cua chuoi: $"  
    s DB 100 dup("$")  ;chuoi nhap vao  
    ;byte 0: chua so ki tu toi da cua xau nhap vao   
    ;byte 1: chua chieu dai cua xau vd :123456789 => 9  
    ;byte 2: Chua dia chi cua ky tu dau tien  
.CODE  
    MAIN:  
        MOV AX, @DATA  
        MOV DS,AX ;khoi tao thanh ghi ds  
    
        LEA DX,tb1 ;xuat chuoi tb1
        MOV AH,9
        INT 21h 
        
        LEA DX,s 
        MOV AH,10 ;nhap chuoi s
        INT 21h 
        
        LEA DX,tb2
        MOV AH,9 ;xuat chuoi tb2
        INT 21h  
        
        ;Tinh chieu dai chuoi
        mov ax,0
        MOV AL,s+1  ;Chuyen chieu dai chuoi vao ax  al=9
        MOV CX,0    ;Khoi tao bien dem cx=0(count)
        MOV BX,10 ;khoi tao so chia
        ;123 push 3 2 1 =>pop 1 2 3
    LapDem1:; day cac so vao ngan xep
        MOV DX,0  ;khoi tao phan du bang 0
        DIV BX ; chia 10
        PUSH DX ; lay dx la phan du day vao ngan xep
        INC CX ;tang cx len 1 don vi
        CMP AX,0 ;so sanh ax phan thuong  khac 0 thi tiep tuc vong lap
        JNZ LapDem1   ;jump not zezo
        ;xuat chieu dai chuoi
       
    LapDem2: ; lay cac so tu ngan xep ra
        POP DX ;Chua so du trong phep chia
        ADD DX,'0' ;chuyen chu so -> ky tu so  
        MOV AH,2
        INT 21H
        LOOP LapDem2  
        
        MOV AH,4ch
        INT 21h
    END MAIN  
    End
```  
_BÀI 4: NHẬP CHUỖI, IN RA CHUỖI ĐẢO_
```
.model small  
.stack 100  
.data   
     str DB 50 dup('$') ;str gom 50 bytes chua 50 gia tri khoi dau   
     tb1 db 10,13,'Chuoi da duoc dao nguoc: $'   
.code  
    main proc  
        mov ax,@data  
        mov ds,ax  ;khoi tao thanh ghi ds  
        
        ;nhap xau ki tu 
        lea dx,str
        mov ah,10;0Ah
        int 21h  
        
        ;in chuoi tb2 ra man hinh
        lea dx, tb1
        mov ah,9
        int 21h
        
        ;vd 123456789 =>len=9   256,9,1,2,3....
        mov cl,[str + 1] ;chuyen chieu dai chuoi vua nhap vao cl
        lea si,[str + 2] ;dua si chi ve phan tu thu 2 cua mang la ky tu dau tien duoc nhap vao
        Lap:;day cac ky tu vao ngan xep
            push [si];dua phan tu ma si dang chi den vao dau ngan xep
            inc si  ;increase :tang gi tri cua si len 1 
            loop Lap
        
        mov cl, [str + 1];chuyen chieu dai chuoi vua nhap vao cl        
        Lap2: ;lay du lieu tu ngan xep
            pop dx  ;lay gia tri tren dinh ngan xep dua vao dx  
            mov ah,2 ;in ki tu vua lay ra man hinh
            int 21h
            Loop Lap2
        
        ; tro ve DOS dùng hàm 4 CH cua INT 21H
		MOV AH, 4CH
		INT 21H
    main endp
    end
```

**HẾT NHÉ**
