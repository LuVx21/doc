---
title: 面试题:Map序列化
date: 2018-09-17
tags:
- Java
---
# 题目

=== 字典序列化 ===

我们程序中用到了一个数组 a , 数组的每个元素都是一个字典(map/dict).
字典的 key/value 都是字符串, 字符串中可包含任意字符.

示例:

    a[0]["k1"] = "v1"
    a[0]["k2"] = "v2"
    a[1]["A"] = "XXX"
    ...

实际使用过程中, 我们自定义了一个基于字符串的存储结构, 数组元素之间用"换行"分割, 
字典元素之间使用"分号"分割, key/value 之间用"等号"分割.
上述数据序列化之后, 应该得到一个字符串:

    "k1=v1;k2=v2\nA=XXX"

请实现一个"保存"函数, 一个"加载"函数.

    text = store(a); //把数组保存到一个字符串中
    a = load(text);  //把字符串中的内容读取为字典数组

请考虑所有边界情况, 不要出现bug. 在满足上述需求的前提下, 可自行增加一些规则和约定.

(附加条件: 不要使用split或者其他类似现成API)

# 实现

首先实现一个方法实现:将Map组装成`k1=v1;k2=v2`格式

```Java
private static String mapAssemble(Map<String, String> map) {
    if (map == null) {
        return "";
    }
    StringBuilder sb = new StringBuilder();

    Iterator<Map.Entry<String, String>> iterator = map.entrySet().iterator();
    while (iterator.hasNext()) {
        Map.Entry<String, String> entry = iterator.next();
        sb.append(entry.getKey() + "=" + entry.getValue() + (iterator.hasNext() ? ";" : ""));
    }
    return sb.toString();
}
```

之后实现一个`store`方法:把数组保存到一个字符串中

```Java
public static String store(Map<String, String>[] array) {
    StringBuilder sb = new StringBuilder();
    for (int i = 0; i < array.length; i++) {
        sb.append(mapAssemble(array[i]) + (i != array.length - 1 ? "\n" : ""));
    }
    return sb.toString();
}
```
最后实现`load`方法:把字符串中的内容读取为字典数组

```Java
public static Map<String, String>[] load(String str) {
    if (str == null || str.length() == 0) {
        return new Map[0];
    }
    // String[] array = str.split("\\n");
    String[] array = split(str, "\\n");

    int length = array.length;
    Map<String, String>[] result = new Map[length];

    for (int i = 0; i < length; i++) {
        // String[] array1 = array[i].split(";");
        String[] array1 = split(array[i], ";");
        Map<String, String> map = new HashMap<>();
        for (int j = 0; j < array1.length; j++) {
            // map.put(array1[j].split("=")[0], array1[j].split("=")[1]);
            map.put(split(array1[j], "=")[0], split(array1[j], "=")[1]);
        }
        result[i] = map;
    }
    return result;
}
```

由于不允许使用split等方法, 只能直接动手将字符串分割.

```Java
private static String[] split(String str, String regex) {
    String[] result = null;
    if (str.length() <= regex.length()) {
        return new String[0];
    } else {
        result = new String[str.length() / (regex.length() + 1) + 1];
    }

    char[] strChars = str.toCharArray();
    char[] regexChars = (regex == "\\n") ? new char[]{'\n'} : regex.toCharArray();
    int startIndex = 0;
    int resultIndex = 0;

    for (int i = 0; i < strChars.length; i++) {
        int j = 0;
        while (i + j < strChars.length && j < regexChars.length && strChars[i + j] == regexChars[j]) {
            j++;
        }

        if (j > 0 && i != startIndex && j == regexChars.length) {
            result[resultIndex++] = new String(strChars, startIndex, i - startIndex);
            startIndex = i + regexChars.length;
        }
    }
    result[resultIndex++] = new String(strChars, startIndex, strChars.length - startIndex);
    return Arrays.copyOf(result, resultIndex);
}
```

至此, 功能基本实现, 但上述实现仍然存在问题: 由于k-v中是可以存在任意字符的, 包括汉字, 日语或者emoji等各种字符, 

于是其中就可能会存在使用的连接符`=`,`;`,`\n`. 如何将连接符和字符串中存在的连接符区分开是面临的问题, 目前没有想出好的解决方法.

# 测试

```Java
@Before
public void initMap() {
    for (int i = 0; i < 10; i++) {
        map1.put(getRandomString(), getRandomString());
        map2.put(getRandomString(), getRandomString());
        map3.put(getRandomString(), getRandomString());
    }
}

public String getRandomString() {
    int start = 0x0000;
    int end = 0x00ff;
    Random random = new Random();
    char[] chars = new char[10];
    for (int i = 0; i < 10; i++) {
        int code = random.nextInt(end - start + 1) + start;
        char c = (char) code;
        chars[i] = (c == '\n' || c == ';' || c == '=') ? ' ' : c;
        // chars[i] = c;
    }
    return new String(chars);
}

@Test
public void loadTest() {
    result = ArraySerialize.store(array);
    System.out.println(result);
    System.out.println("--------------");
    Map<String, String>[] array = null;
    array = ArraySerialize.load(result);
    for (Map<String, String> map : array)
        System.out.println(map);
}
```

可以看出连接符只有`=`,`;`,`\n`

