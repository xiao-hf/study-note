##### on duplicate key update

```
insert into tea (id, name, uniq)
values (#{id}, #{name}, #{uniq})
on duplicate key update
id = #{id}, `name` = #{name}, uniq = #{uniq}
```

只要有冲突, insert变成update



##### from_unixtime

单位: 秒

```
select from_unixtime(0, '%Y-%M-%d %H:%m:%S'); -- 起始 1970-1-01 08:01:00
```



##### xml忽略解析

```
<![CDATA[Xxx]]>
```



##### date_sub

```
select date_sub('2025-1-13', interval 30 day); -- 输出:2024-12-14
```



