# comparing

- Tóm tắt `comparing.cpp`.
    - Chia flag thành các tuple theo cặp: `{ flag[i * 2], flag[i * 2 + 1], i }` và đẩy vào priority_queue (comparator sắp xếp theo char1 + char2).
    - Mỗi lần lặp nó pop 2 tuple (gọi là A và B), rồi in 2 dòng:
    - Dòng 1: `even/odd(val1, val3, i1)` với val1 = ascii(A.first), val3 = ascii(B.first), i1 = A.index.
    - Dòng 2: `even/odd(val2, val4, i2)` với val2 = ascii(A.second), val4 = ascii(B.second), i2 = B.index.
    - `even(val1,val3,ii)` trả về chuỗi: to_string(val1) + to_string(val3) + to_string(ii) + reverse(to_string(val1) + to_string(val3)).
    - `odd(val1,val3,ii)` trả về chuỗi: to_string(val1) + to_string(val3) + to_string(ii) (hàm addend trong code = 0).

- Decode:
    - Ta chỉ cần viết 2 hàm even() và odd() để thử parse chuỗi số theo 2 cách trên. Nếu parse thành công thì thu được (val1, val3, idx) với val1 và val3 là ASCII code của ký tự.
    - Sau đó ta sắp xếp lại theo logic encode.
- Dưới đây là code giải flag của tôi.

``` python
def even(s):
    for v1_len in (2, 3):
        for v3_len in (2, 3):
            for idx_len in (1, 2):
                prefix_len = v1_len + v3_len + idx_len
                if prefix_len >= len(s):
                    continue
                v1_str, v3_str, idx_str = s[:v1_len], s[v1_len:v1_len+v3_len], s[v1_len+v3_len:prefix_len]
                if s[prefix_len:] != (v1_str + v3_str)[::-1]:
                    continue
                if v1_str.isdigit() and v3_str.isdigit() and idx_str.isdigit():
                    v1, v3, idx = map(int, (v1_str, v3_str, idx_str))
                    if 32 <= v1 <= 126 and 32 <= v3 <= 126:
                        return v1, v3, idx

def odd(s):
    for v1_len in (2, 3):
        for v3_len in (2, 3):
            for idx_len in (1, 2):
                if v1_len + v3_len + idx_len != len(s):
                    continue
                v1_str, v3_str, idx_str = s[:v1_len], s[v1_len:v1_len+v3_len], s[v1_len+v3_len:]
                if v1_str.isdigit() and v3_str.isdigit() and idx_str.isdigit():
                    v1, v3, idx = map(int, (v1_str, v3_str, idx_str))
                    if 32 <= v1 <= 126 and 32 <= v3 <= 126:
                        return v1, v3, idx

def decode(s):
    return even(s) or odd(s)

output = [
    "9548128459",
    "491095",
    "1014813",
    "561097",
    "10211614611201",
    "5748108475",
    "1171123",
    "516484615",
    "114959",
    "649969946",
    "1051160611501",
    "991021",
    "1231012101321",
    "9912515",
    "11411511",
    "1151164611511"
]

tuples = []
for a, b in zip(output[::2], output[1::2]):
    v1, v3, i1 = decode(a)
    v2, v4, i2 = decode(b)
    tuples += [(chr(v1), chr(v2), i1), (chr(v3), chr(v4), i2)]

flag_parts = {i: c1 + c2 for (c1, c2, i) in sorted(tuples, key=lambda t: (ord(t[0]) + ord(t[1]), t[2]))}
flag = "".join(flag_parts[i] for i in sorted(flag_parts))
print(flag)
```
- Tôi chạy script và đã có được flag.

<details>
<summary style="cursor: pointer">Flag</summary>

```
ictf{cu3st0m_c0mp@r@t0rs_1e8f9e}
```
</details>