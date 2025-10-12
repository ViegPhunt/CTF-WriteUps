# stacked

- Bài này chỉ đơn giản là lấy từng ký tự trong flag bạn đầu ra rồi đi qua từng hàm theo file chall, cứ thế 3 char được xử lý 3 lần.
- Vậy nên ta chỉ cần đi qua các hàm đó theo thứ tự ngược lại tương ứng với char được mã hóa thì ta sẽ giải được flag của bài này.
- Dưới đây là code của tôi đi ngược lại từng char qua các hàm theo thứ tự ngược lại để giải được bài này.

``` python
def eor(a1: int) -> int:
    return (a1 ^ 0x69) & 0xFF

def rtl(a1: int) -> int:
    return ((a1 << 1) & 0xFF | (a1 >> 7)) & 0xFF

def inc(a1: int) -> int:
    global globalvar, flag
    flag[globalvar] = a1 & 0xFF
    globalvar += 1
    return flag[globalvar - 1]

def off(a1: int) -> int:
    return (a1 - 15) & 0xFF

flag = [0x94, 0x7, 0xd4, 0x64, 0x7, 0x54, 0x63, 0x24, 0xad, 0x98, 0x45, 0x72, 0x35]

flag[0] = off(eor(rtl(flag[0])))
flag[1] = eor(eor(eor(flag[1])))
flag[2] = rtl(off(rtl(flag[2])))
flag[3] = rtl(rtl(eor(flag[3])))
flag[4] = eor(eor(eor(flag[4])))
flag[5] = rtl(off(rtl(flag[5])))
flag[6] = rtl(eor(rtl(flag[6])))
flag[7] = rtl(rtl(eor(flag[7])))
flag[8] = rtl(off(eor(flag[8])))
flag[9] = eor(eor(rtl(flag[9])))
flag[10] = off(off(rtl(flag[10])))
flag[11] = rtl(rtl(eor(flag[11])))
flag[12] = eor(off(rtl(flag[12])))

print("ictf{" + "".join(chr(i) for i in flag) + "}")
```
- Sau khi chạy code thì tôi đã có được flag cho bài này.

<details>
<summary style="cursor: pointer">Flag</summary>

```
ictf{1n54n3_5k1ll2}
```
</details>