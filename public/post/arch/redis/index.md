
之前看过Redis，最近Redis升6.0改动不小，重新review一下源码，边看边做一些注释，详见 https://github.com/qzqsmile/redis/tree/annotation-601

SDS部分 Redis用来存字符串的数据结构。这个结构被作者领出来准备做成一个公用模块了。 https://github.com/antirez/sds

我看了下源码，让我印象比较深刻的是其对内存利用真是到了极致。 不光是采用 struct attribute((packed)) 这种压缩的struct结构体的技巧。同时对于不同长度的字符串居然构建不同的struct进行存储，仅仅是为了节省存储长度的那点内存。

比如

```C
struct __attribute__ ((__packed__)) sdshdr8 {
   uint8_t len; /* used */
   uint8_t alloc; /* excluding the header and null terminator */
   unsigned char flags; /* 3 lsb of type, 5 unused bits */
   char buf[];
};

struct __attribute__ ((__packed__)) sdshdr16 {
   uint16_t len; /* used */
    uint16_t alloc; /* excluding the header and null terminator */
    unsigned char flags; /* 3 lsb of type, 5 unused bits */
    char buf[];
}
```

sdshdr8 len仅仅是8bit仅能存放256长度的字符，而sdshr16则是uint16,可以存放65536长度的字符

hashtable
redis的hash比较有特点的设计在于rehash的那部分，因为redis作为一个高性能的KV缓存。其中当hash表内存不足时，重新分配内存进行rehash的过程会造成客户端卡顿的情况. <
为了解决这个问题，redis采取了渐进式hash的处理方式。所谓渐进式hash主要是在set 以及 get操作时，判断其是否在rehash的状态，如果是的话则进行小批量的搬运操作。
渐进式hash主要在两个地方进行相应的action。在hashtable里有个rehashindex的变量。用于存储搬运的位置。
同时渐进式hash还在redis空闲时进行搬运，但是此处搬运的刻度不再是每次搬运几个slot这样，而是每次搬运多久，比如redis6里每次搬运1ms。




   