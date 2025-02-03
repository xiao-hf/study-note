#### Base64

Base64编解码

```
String s = "kjdhfjksfhjskdfkj";
String base64Str = Base64.getEncoder().encodeToString(s.getBytes()); // 编码
byte[] s2 = Base64.getDecoder().decode(base64Str); // 解码
System.out.println(base64Str);
System.out.println(new String(s2));
```



#### stream()

Collectors.averagingDouble(): 平均数Double类型

```
List<Tea> list = new ArrayList<>();
for (int i = 1; i <= 10; i++)
    list.add(new Tea(i, "" + i, "" +i));
Double r1 = list.stream().collect(Collectors.averagingDouble(Tea::getId));
System.out.println(r1);
```

Collectors.joining(分隔符, 前缀, 后缀)

```
List<String> list = new ArrayList<>();
for (int i = 1; i <= 10; i++)
    list.add(Integer.toString(i));
String res = list.stream().collect(Collectors.joining(",", "(", ")"));
System.out.println(res);
```