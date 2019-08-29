# Redis设计与实现笔记

### 动态字符串

1. SDC结构：buf 数组中已使用字节的数量，int len 保存的字符串长度，int free 未使用字节的数量，char buf[] 字节数组，用于保存字符串
2. SDS与C字符串的区别：
   SDC获取字符串长度 O(1)；SDC杜绝缓冲区溢出；SDC
3. 