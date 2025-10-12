# [baseball](https://dreamhack.io/wargame/challenges/105)

- Tôi tiến hành mở file `baseball` của bài cho để xem nó hoạt động như nào.
- Sau khi xem qua hàm main thì tôi chỉ thấy là có nhập vào file chứa table ký tự base64 và một file ký tự muốn encode.
- Dựa vào file txt được bài cho thì tôi thấy file `text_in.txt` và file `text_out.txt` chính là gợi ý của bài này để có thể giải ra được table và từ đó decode dữ liệu trong file `flag_out.txt`.
- Tôi tiến hành viết script python để tìm table từ 2 file `text_in.txt` và `text_out.txt`, sau đó dùng table đó để decode flag luôn.

``` python
import base64

text_in = open("text_in.txt", "r").read().strip()
text_out = open("text_out.txt", "r").read().strip()

standard_table = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/"
standard_encoded = base64.b64encode(text_in.encode()).decode()

mapping = {}
for i in range(min(len(standard_encoded), len(text_out))):
    std_char = standard_encoded[i]
    custom_char = text_out[i]
    if std_char not in mapping:
        mapping[std_char] = custom_char

reverse_mapping = {custom_char: std_char for std_char, custom_char in mapping.items() if std_char in standard_table}

custom_table = ['?'] * 64
for custom_char, std_char in reverse_mapping.items():
    index = standard_table.index(std_char)
    custom_table[index] = custom_char

custom_table_str = ''.join(custom_table)
found_chars = sum(1 for c in custom_table if c != '?')

print(f"Found characters: {found_chars}/64")
print(f"Custom table: {custom_table_str}")

def decode(encoded_text):
    result = ""
    for char in encoded_text:
        result += reverse_mapping.get(char, char)
    return base64.b64decode(result).decode()

flag = decode(open("flag_out.txt", "r").read().strip())
print(f"Flag: DH{{{flag}}}")
```
- Tuy là chưa thể tìm được hết toàn bộ ký tự của table nhưng từng ấy dữ liệu đã đủ để ta tìm được flag của bài này.

<details>
<summary style="cursor: pointer">Flag</summary>

```
DH{Did you know how base64 works}
```
</details>