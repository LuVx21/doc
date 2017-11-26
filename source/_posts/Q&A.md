---
title: Q&A
date: 2017-11-06
---

1. 将前3个commit合并为1个commit怎么处理?

2. git push 时, 别人先于你push了代码怎么处理?

3. push的代码中有你修改的怎么处理?没有又怎么处理?

4.
    ```python
    def selfAdd(a):
        a = a + a

    a_list = [1, 2]
    selfAdd(a_list)
    print(a_list) # [1, 2]
    ```

    为什么没有改变?

5. shell提取文件的信息, 如时间, 作者等?

6. 遍历文件夹下所有文件

    ```
    find . -print
    ```

7. 获取一个文件的全路径, 相对路径?
8. 处理路径, 获取目录, 文件名, 文件扩展名?

    ```shell
    # 获取目录
    $(dirname <filepath>)
    # 获取文件名, 含扩展名
    $(basename <filepath>)
    # 获取文件名, 不含扩展名
    $(basename <filepath> .java)
    # 获取文件名, 不含扩展名, better

    ```

9. 抽象类和接口接口的区别, 分别在什么场合下使用它们?
