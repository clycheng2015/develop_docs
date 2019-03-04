我们传了一个A.class进去

遍历一波这个A类的所有字段，可以得到LinkedHashMap<String, BoundField>

然后遍历Json， 也会得到key-value

然后根据key拿到对应的BoundField

然后会根据当前key对应的value，按照BoundField的样子解析。

（BoundField内部有TypeAdapter，如果是基本数据类型，直接返回结果；如果不是，会进行一个递归的解析，最终得出对象，并赋值）
