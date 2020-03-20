# Design note

如何给语言添加IO功能

-  [Adding a function call in my IR code in llvm](https://stackoverflow.com/questions/28168815/adding-a-function-call-in-my-ir-code-in-llvm) 
-  [llvm - How to implement print function in my language?](https://stackoverflow.com/questions/35526075/llvm-how-to-implement-print-function-in-my-language) 
-  [How To Call @printf in LLVM through the module builder system](https://stackoverflow.com/questions/30234027/how-to-call-printf-in-llvm-through-the-module-builder-system) 

```java
private char skipWhitespace(char ch) {
    while (true) {
        if (ch == ' ' || ch == '\t' || ch == '\n')
            ch = buffer.next();
        else break;
    }
    return ch;
}
```
改写`buffer.peek()`

```java
public boolean peek(char ch) {
     if(peek() == ch) {
         next();
         return true;
     }
     return false;
}
// To
public boolean peek(char... chs) {
    char cur_ch = peek();
    for(char ch : chs) {
        if (cur_ch == ch) {
            next();
            return true;
        }
    }
    return false;
}
```

```java
private void skipWhitespace() {
    while (buffer.peek(' ', '\t', '\n', '\r')) buffer.next();
}
```
