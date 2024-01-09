<details>
<summary>点击展开目录</summary>
<!-- TOC -->

- [Usage](#usage)
- [参考](#参考)

<!-- /TOC -->
</details>


KMP算法(一种改进的字符串匹配算法)

字符串S内查找一个词W的出现位置


## Usage

```
             1         2
m: 01234567890123456789012
S: ABC ABCDAB ABCDABCDABDE
W: ABCDABD
i: 0123456
```

| No   | 子串    | 前缀                       | 后缀                       | 前后缀最大相同长度 |
| :--- | :------ | :------------------------- | :------------------------- | :----------------- |
| 0    | A       |                            |                            | 0                  |
| 1    | AB      | A                          | B                          | 0                  |
| 2    | ABC     | A,AB                       | C,BC                       | 0                  |
| 3    | ABCD    | A,AB,ABC                   | D,CD,BCD                   | 0                  |
| 4    | ABCDA   | A,AB,ABC,ABCD              | A,DA,CDA,BCDA              | 1                  |
| 5    | ABCDAB  | A,AB,ABC,ABCD,ABCDA        | B,AB,DAB,CDAB,BCDAB        | 2                  |
| 6    | ABCDABD | A,AB,ABC,ABCD,ABCDA,ABCDAB | D,BD,ABD,DABD,CDABD,BCDABD | 0                  |

部分匹配表


```Java
/**
 * <pre>
 *              1         2
 * m: 01234567890123456789012
 * S: ABC ABCDAB ABCDABCDABDE
 * W: ABCDABD
 * i: 0123456
 * </pre>
 * <p>
 * KMP算法, 时间复杂度: O(n)
 */
public static int kmp(String s, String w) {
    // 部分匹配表
    int[] tb = new int[w.length()];
    tb = new int[]{0, 0, 0, 0, 1, 2, 0};

    int len1 = s.length(), len2 = w.length();
    int m = 0;
    while (m < len1) {
        int i = 0;
        for (; m + i < len1 && i < len2; i++) {
            if (s.charAt(m + i) != w.charAt(i)) {
                break;
            }
            if (i == len2 - 1) {
                return m;
            }
        }
        m = i - tb[i];
    }
    return -1;
}

private static int[] next(String w) {
    
}
```


## 参考

1. [克努斯-莫里斯-普拉特算法](https://zh.wikipedia.org/wiki/克努斯-莫里斯-普拉特算法)
2. [理解 KMP](http://wiki.jikexueyuan.com/project/kmp-algorithm/define.html)

