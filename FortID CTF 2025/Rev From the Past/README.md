# Rev From the Past

- Tôi tiến hành mở file bằng IDA để có thể đọc code của bài này.
- Vì đây là file 16-bit DOS .COM binary nên ta sẽ chỉ có thể đọc được code asm của nó.

``` ASM
public start
proc near
cli
mov     ax, cs
mov     ds, ax
mov     es, ax
assume es:seg000
mov     ss, ax
assume ss:seg000
mov     sp, 0FFFEh
sti
call    sub_10160
jb      short loc_10154
mov     ax, word_10254
mov     byte_10252, al
mov     byte_10253, ah
call    sub_101E1
mov     al, byte_10252
xor     al, 0A5h
mov     bl, al
mov     si, 37Eh
mov     di, 37Eh
mov     cx, 21h ; '!'
```
- Ta sẽ men theo hàm start để có thể khai thác chương trình.
- Đầu tiên là hàm `sub_10160`.
- Sau khi phân tích thì tôi có thể giải thích hàm này như sau:
    - Ta sẽ có flag nằm trong khoảng 80 ký tự.
    - Flag sẽ được so sánh với ký tự có sẵn là `FortID{`.
    - Sau đó đọc phần bên trong flag cho đến ký tự `}`.
- Lấy `word_10254` đưa vào `byte_10252` và `byte_10253`.
- Tiếp đến là hàm `sub_101E1`.
    - Đầu tiên là khởi tạo một mảng từ 0-255.
    - `word_10256` là seed biến động hoán vị của mảng.
    - Sau đó khởi tạo một vòng lặp 0xFF (256).
    - Với mỗi lần sẽ:
        - Shift phải 1 bit với giá trị tương ứng trong `word_10256`.
        - Nếu bit thấp = 1, XOR với `0xB400`.
        - Cập nhật `word_10256` tạo seed mới cho bước tiếp theo.
        ``` ASM
        mov ax, word_10256
        xor dx, dx
        mov bx, cx
        inc bx
        div bx
        mov bx, dx
        ```
        - Tiếp đến chia seed hiện tại cho (cx + 1), remainder (dx) = vị trí hoán vị.
        - bx = remainder, đây là index swap trong bảng.
        - Sau đó `Swap (table[cx], table[bx])`.
    - Từ đó mảng 0–255 được trộn ngẫu nhiên dựa trên seed 0xB4C1.
    - `sub_101E1` thực chất là một pseudo-random permutation generator, dùng để tạo "key table" cho block flag.
- Sau đó lấy `byte_10252`, XOR với 0xA5, bl được dùng làm key.
- Dùng `lodsb`, `xor al, bl`, `stosb` lặp 0x21 (33) lần, đây là quá trình giải mã một chuỗi dài 33 byte.
- Tiếp tục dùng `xlat` với một bảng, so sánh, nếu mọi thứ khớp thì in thông báo thành công.
- Dưới đây tôi có viết một script python để reverse lại logic đó và giải ra flag.

``` python
def sub_101E1(seed):
    lst = list(range(256))
    
    for i in range(255, 0, -1):
        if seed & 1:
            seed = (seed >> 1) ^ 0xB400
        else:
            seed = seed >> 1
        j = seed % (i + 1)
        lst[i], lst[j] = lst[j], lst[i]
    return lst


encrypted_data = [
    0x2A, 0x35, 0xA9, 0x11, 0xE3, 0x6F, 0x17, 0x79, 0x11, 0xE3, 0x79,
    0x88, 0x94, 0xB2, 0x01, 0xFD, 0x68, 0x11, 0x6F, 0x01, 0xB7, 0x11,
    0xAC, 0x6F, 0x53, 0x01, 0xCE, 0xE2, 0x11, 0x84, 0x35, 0x35, 0x51
]

word_10254 = 0xB4C1
byte_10252 = (word_10254 & 0xFF) ^ 0xA5

xor_decrypted = []
for byte in encrypted_data:
    xor_decrypted.append(byte ^ byte_10252)

sub_table = sub_101E1(word_10254)

reverse_sub_table = [0] * 256
for i in range(256):
    reverse_sub_table[sub_table[i]] = i

flag = ""
for byte in xor_decrypted:
    flag += chr(reverse_sub_table[byte])

flag = "FortID{" + flag + "}"
print(flag)
```
- Sau khi chạy script thì tôi đã có được flag cho bài này.

<details>
<summary style="cursor: pointer">Flag</summary>

```
FortID{N0w_S4v3_S3t71ng5_4nd_L4unch_D00M}
```
</details>