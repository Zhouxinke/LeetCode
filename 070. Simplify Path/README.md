这是个好题目，很有实际需求。

    /a/./b/../../c/
      ^^                (1)
    /a/b/../../c/
       ^^^^             (2)
    /a/../c/
     ^^^^               (2)
    //c/
    ^^                  (3)
    /c/
      ^                 (4)
    /c

我们只需要认真分析题目给出的第二个例子，就能发现几种变换方式：

1. `.` 代表当前目录，可忽略
2. `..` 代表上一级目录，应退到前一个 `/` 处
3. `//` 合并为 `/`
4. 末尾`/`删除

显然，我们的进退应该按照 `/` 来区分，这是**关键**。我们应该对路径字符串进行以 `/` 的切割。
然后一段一段的入栈出栈。接下来，我们倒过来写，看看 `/a/./b/../../c/` 入栈情况：

可分割为以下部分：

    a | . | b | .. | .. | c

| character | action | stack  |
| :-------: | :----: | :----: |
|   a       |   push |   a    |
|   .       | ignore |   a    |
|   b       |   push |   a b  |
|   ..      |   pop  |   a    |
|   ..      |   pop  |        |
|   c       |   push |   c    |

最后将结果补充上 `/` 即可：`/c`.

-----

整理一下逻辑:

1. 遇到 `..` ，若 stack 不为空的话，pop 操作
2. 继续，遇到 `.`, `..`, ` ` 这三种字符，无操作，continue.
3. 出此之外，一律 push 操作。

注：分割后每一段字符串设为 `tmp`.

```cpp
if (tmp == ".." && !stack.empty()) stack.pop_back();
else if (tmp == "." || tmp == ".." || tmp == "") continue;
else stack.push_back(tmp);
```

然后将栈里面的字符串连接即可：

```cpp
for (auto str : stack) { ret += "/" + str; }
```

这里提示 C++ 中字符串分割的基本技巧：**使用 `istringstream`, 配合 `getline(iss, tmp, '/')`**

不多说，不超过十行。AC
