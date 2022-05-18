# 包装类

有-128到127的缓存常量（除了Float和Double）

Byte,Short,Integer,Long 这 4 种包装类默认创建了数值 [-128，127] 的相应类型的缓存数据，
Character 创建了数值在 [0,127] 范围的缓存数据，
Boolean 直接返回 True Or False。

超过后就new对象。

