## 1. 32bit 和 64bit 系统在数据类型长度上有哪些区别？
&emsp;&emsp; 就 指针、`long`有区别。



## 2. 字数据大小详情
| Signed        | Unsigned         | 32-bit | 64-bit |
| ------------- | ---------------- | ------ | ------ |
| `signed char` | `unsigned char`  | 1      | 1      |
| `short`       | `unsigned short` | 2      | 2      |
| `int`         | `unsigned`       | 4      | 4      |
| `long`        | `unsigned long`  | 4      | 8      |
| `int32_t`     | `uint32_t`       | 4      | 4      |
| `int64_t`     | `uint64_t`       | 8      | 8      |
| `char *`      |                  | 4      | 8      |
| `float`       |                  | 4      | 4      |
| `double`      |                  | 8      | 8      |