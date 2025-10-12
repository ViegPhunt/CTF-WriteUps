# Intro-Rev

- Tôi tiến hành mở file bằng IDA để có thể xem code của bài này.
- Ta có thể thấy ở hàm main chính là thuật toán check flag chính của ta luôn, cùng phân tích từng phần nào.

``` C
printf("Enter flag: ");
__isoc99_scanf("%s", s);
if ( strlen(s) > 70 )
    goto LABEL_14;
``` 
- Đầu tiên, flag sẽ được nhập vào, nếu flag lớn hơn 70 ký tự thì sẽ kết thúc chương trình luôn.

``` C
v6 = 0;
for ( i = 0; i < strlen(s); ++i ) {
    v8 = 0;
    v9 = 255;
    while ( 1 ) {
        v11 = (v8 + v9) / 2;
        if ( v11 == s[i] )
            break;
        v4 = v6++;
        if ( v11 >= s[i] ) {
            s[v4 + 80] = 60;
            v9 = v11 - 1;
        }
        else {
            s[v4 + 80] = 62;
            v8 = v11 + 1;
        }
    }
    v3 = v6++;
    s[v3 + 80] = 61;
}
```
- Đây chính là hàm xử lý chính mà ta cần để ý tới.
- Mỗi ký tự trong flag sẽ được mô phỏng theo quá trình tìm kiếm nhị phân trên khoảng [0, 255].
- Các ký hiệu `<` và `>` đại diện cho so sánh với giá trị giữa (mid).
    - `<` nghĩa là mid >= ký tự, cập nhật hi = mid - 1.
    - `>` nghĩa là mid < ký tự, cập nhật lo = mid + 1.
- Khi tìm thấy ký tự đúng, ký tự `=` được thêm vào để đánh dấu kết thúc của chuỗi so sánh cho ký tự đó.

``` C
if ( v6 == 448 ) {
    for ( j = 0; (unsigned __int64)j <= 0x1BF; ++j )
    {
        if ( s[j + 80] != target[j] )
        goto LABEL_14;
    }
    puts("Correct!");
    return 0;
}
```
- Sau đó đến bước kiểm tra kết quả, flag nhập vào, sau khi đi qua vòng lặp biến đổi sẽ được so sánh với mảng `target` có sẵn của bài.
- Giờ ta chỉ cần lấy dữ liệu trong mảng target và giải ngược lại thì sẽ có được flag.
- Sau đây là code python để giải flag bài này của tôi.

``` python
target = "<><<<>>=<>>=<>>><<>=<>>><><=<><<><=<><<<><=<>>>>=<<>><=<>><<<=<<>>=<>=<><>><=<<>><<<=<>>><>=<>>><<>=<>=<><><>>=<<>><=<>><=<>><=<<>><<=<>><<>=<<>><>=<>=<<>><><=<>><>>>=<>><<><=<>=<><<>><=<<>><=<>>><<>=<>><>>>=<>=<><>><=<<>><<<=<>>><>=<>>><<>=<>=<><<<>>=<>>><>=<>><>>>=<>><<><=<<>><><=<>><>>=<<>><=<>><>>>=<<>>=<<>><><=<<>><<=<>=<><><=<<>><=<>><<<=<>>><<>=<>><<=<>><><<=<>=<><<<<=<>><>><=<>><=<<>><<<=<>>><<>=<<>><<=<<>>=<>><><<=<>><>>=<<>><>=<>>>>>="
target = target.split('=')

flag = []
for i in target:
    lo, hi = 0, 255
    for s in i:
        mid = (lo + hi) // 2
        if s == '<':
            hi = mid - 1
        elif s == '>':
            lo = mid + 1
    ch = (lo + hi) // 2
    flag.append(chr(ch))

print(''.join(flag))
```
- Sau khi chạy code thì tôi đã có được flag cho bài này.

<details>
<summary style="cursor: pointer">Flag</summary>

```
FortID{3a7_Y0ur_V3gg1e5_4nd_L3rn_Y0ur_Fund4m3n741_S3arch_Alg0r17hm5}
```
</details>