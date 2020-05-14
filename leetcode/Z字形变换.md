# Z 字形变换

## 链接
[Z字形变换-leetcode](https://leetcode-cn.com/problems/zigzag-conversion/)

## 题目

> 将一个给定字符串根据给定的行数，以从上往下、从左到右进行 Z 字形排列。
>
> 比如输入字符串为 "LEETCODEISHIRING" 行数为 3 时，排列如下：
>
> L   C   I   R
>
> E T O E S I I G
>
> E   D   H   N
>
> 之后，你的输出需要从左往右逐行读取，产生出一个新的字符
>
> 串，比如："LCIRETOESIIGEDHN"。
>
> 请你实现这个将字符串进行指定行数变换的函数：
>
> string convert(string s, int numRows);

### 示例 1:
>
> 输入: s = "LEETCODEISHIRING", numRows = 3
>
> 输出: "LCIRETOESIIGEDHN"
>

### 示例 2:
>
> 输入: s = "LEETCODEISHIRING", numRows = 4
>
> 输出: "LDREOEIIECIHNTSG"
>
> 解释:
>
> L     D     R
>
> E   O E   I I
>
> E C   I H   N
>
> T     S     G

## 思路
根据例子中的 numRows 观察，每个符合 numRows 个数的列相隔的字符数量是 numRow - 2，为什么是2呢，因为只需要减去首尾两个字符数量即可。

## 解题代码：（此方案不唯一）
```java
public String convert(String s, int numRows) {
        int length = s.length();
        if(length < 3 ){
            return s;
        }
        String[][] arr = new String[length][numRows];
        int arri = 0;
        int arrj = 0;
        int count = 0;
        int midNums = Math.max(numRows - 2, 0);
        for (int i = 0; i < length; i++) {
            arr[arri][arrj++] = String.valueOf(s.charAt(i));
            count++;
            if(count % numRows == 0) {
                for (int j = 0; j < midNums; j++) {
                    if(i + 1 + j >= length) {
                        continue;
                    }
                    arr[++arri][--arrj - 1] = String.valueOf(s.charAt(i + 1+j));
                }
                i += midNums;
                count = 0;
                arri ++;
                arrj = 0;
            }

        }
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < numRows; i++) {
            for (int j = 0; j < length; j++) {
                if(null != arr[j][i]){
                    sb.append(arr[j][i]);
                }

            }
        }
        return sb.toString();
    }
```