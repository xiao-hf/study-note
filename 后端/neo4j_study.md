## CQL

#### 1.加载文件 

```
load 后缀 from "file:///xx.后缀" as 别名 ...别名[index]... 
```

**文件位置:** file=安装目录/import

**文件示例:** 

```
金蝉子,唐僧,past_life,前世
唐僧,金蝉子,now_life,今生
```

```
load csv from "file:///test.csv" as line create (:xiyouRelation {from:line[1],to:line[0]});
```

注意: 标签名不能用 别名[index]



#### 2.创建节点

```
create (a:test {a:'b'});   属性名不加引号,属性值如果为字符串需加引号 别名可不写
```



#### 3.匹配节点

```
match (n:student {name:"小明"}),(m:student {name:"小花"}) create (n)-[r:关系名]->(m) return n.name,type(r),m.name;
```



#### 4.创建关系

```
match ... create (别名)-[关系别名:关系名]->(别名);
```



#### 5.聚合函数count(*),根据label分组distinct labels(n)

```
match (n) return distinct labels(n) as labels,count(*) as count;
```



#### 6.多个标签到节点

```
create (别名:标签1:标签2:标签3...);
```



#### 7.不同标签节点之间创建关系

```
create (n:Dept),(m:aaaa),(b:test) create (n)-[r:同学 {school:'上海市高级中学'}]->(m); 
```

**注意: **执行一次只会创建一对节点的关系,非笛卡尔积



#### 8.where条件查询

**连接关键字:** OR AND XOR NOT

```
match (n:yyyy) where n.name='小红' and n.aa is null return n;
```



#### 9.delete删除节点

```
match (n:yyyy),(m:Dept) delete n,m;
```



#### 10.delete删除关系

```
match (:yyyy)-[r]->(:Dept) delete r;
```



#### 11.remove删除 属性/属性列表

```
match (n:Dept) remove n.p1,n.p2;
```



#### 12.remove删除 标签(一个节点可以有多个标签)

```
match (n:l1) remove n:l2;
```



#### 13.set添加新属性

```
match(n:l1) set n.p1='aaaa',n.p2='bbbb',n.p3='cccc';
```



#### 14.order by排序(要return出去,如果排序属性不存在为无穷大)

```
match(n:l1) return n order by n.score asc,n.p1 desc;
```



#### 15.union去重集

```
match(n:l1) return n.a as a,n.b as b union match(n:l1) return n.a as b,n.b as a;
```



#### 16.union all不去重集

```
match(n:l1) return n.a as a,n.b as b union all match(n:l2) return n.a as b,n.b as a;
```



#### 17.limit+skip实现分页,skip在前,limit在后

```
match(n:l1) return n skip (page - 1) * size limit size;
```



#### 18.merge添加节点 只会添加属性不完全重复的新节点

```
merge (:Profile {id:111,name:'小红'});
```



#### 19.in范围匹配

```
match (n:Profile) where n.id in [11,13] return n;
```



#### 20.更新节点属性

```
match (n:test) set n.name='qwq' return n;
```



#### 21.更新节点全部属性

```
match (n:test) set n = {id:1, name:'qwq', gender:'男'} return n;
```



#### 22.添加节点属性

```
match (n:学生) set n += {gender:'男', height:170} return n;
```



#### 23.添加节点标签

```
match (n) set n:节点 return n;
```



#### 24.返回节点所有标签 labels()

```
match (n:学生) return labels(n);
```



#### 25.删除标签 remove

```
match (n:学生) remove n:节点 return labels(n);
```



#### 26.删除属性

```
match (n:学生) set n.性别 = null return n;
```

```
match (n:学生) remove n.年龄 return n;
```



#### 27.关系节点detach暴力删除

```
match (n:学生) detach delete n;
```



#### 28.两跳节点未删除第一跳

```
match (:state {code:'FL'})-[:neighbor*2]-(c2) return c2; // 会导致性能大幅下降
```

```
match (:state {code:'FL'})-[:neighbor]-(c1)-[:neighbor]-(c2) return c2;
```



#### 29.两跳节点删除第一跳

```
match (:state {code:'FL'})-[:neighbor]-(c1)-[:neighbor]-(c2) where not (:state {code:'FL'})-[:neighbor]-(c2) return c2;
```



#### 30.option空值也返回

```
match (n:state {code:'AK'}) optional match (n)-[:neighbor]-(m) return n.code, m.code;
```



#### 31.聚合函数

avg()

```
match (n:学生) return avg(n.年龄);
```

count()

```
match (n:学生) return count(n.年龄);
```

max()

```
match (n:学生) return max(n.年龄);
```

min()

```
match (n:学生) return min(n.年龄);
```

sum()

```
match (n:学生) return sum(n.年龄);
```

collect(): 收集成列表

```
match (n:学生) return collect(n.年龄);
```

stDev(): 总体标准偏差

```
match (n:学生) return stDev(n.年龄);
```



#### 32.排序

默认升序

```
match (n:学生) return n.年龄 as age order by age desc;
```



#### 33.with中间操作

```
match (n:学生) with n.年龄 as age return age order by age desc;
```



#### 34.unwind将列表转为行,每行一个数据

```
match (n:学生) with collect(n.年龄) as list unwind list as c return c;
```



#### 35.profile查看查询性能

```
profile match (n:学生) return n;
```



#### 36.函数return randomUUID()

```
return randomUUID();
create (n:user {id: randomUUID()}) return n;
```



#### 37.字符串函数

upper(str): 全转为大写

```
create (n:string {name:'sS'}) return upper(n.name);
```

lower(str): 全转为小写

```
create (n:string {name:'sS'}) return lower(n.name);
```

substring(str, fromIndex, len): 截取字符串

```
create (n:string {name:'abcde'}) return substring(n.name, 2, 3); //cde
```



#### 38.关系函数

startnode():关系的开始节点

endnode():关系终止点

id():关系id

type():关系type



## GDS

#### 安装GDS

1.下载响应jar包 https://github.com/neo4j/graph-data-science

2.放在neo4j安装目录/plugins/

3.在 neo4j安装目录/conf/neo4j.conf文件 加两行

```
dbms.security.procedures.unrestricted=gds.*
dbms.security.procedures.allowlist=gds.*
```

4.重启neo4j

5.执行 return gds.version() 查看是否安装成功



#### 列出所有算法

```
call gds.list();
```



#### =~: 正则匹配gds过程

```
call gds.list() yield name, description, signature where name =~ ".*shortestPath.*" return name,description,signature;
```



#### 原生投影





#### 创建索引

对象: 节点属性或关系属性

```
create index 索引名 for 节点/关系 on 节点/关系.属性 //属性如果多个,需要(属性1,属性2,属性3)
CREATE INDEX stu_name2 for (n:学生) on n.id;
CREATE INDEX stu_name33 for (n:学生) on (n.id,n.name);
```



#### 查看索引

查看所有标签

```
SHOW INDEXES;
```

查看有 学生标签 的索引

```
show indexes where '学生' in labelsOrTypes;
```



#### 删除索引

```
drop index stu_name if exists;
```



#### dijkstra最短路

```
MATCH (source:标签)-[r:CONNECTION]->(target:标签)
RETURN gds.graph.project(
  '组名',
  source,
  target,
  { relationshipProperties: r { .距离属性名 } }
)
```

```
MATCH
  (source:标签 {起点属性}),
  (target:标签 {终点属性)
CALL gds.shortestPath.dijkstra.stream(
  '组名',
  {
    sourceNode: source,
    targetNode: target,
    relationshipWeightProperty: '距离属性名'
  }
)
YIELD
  index,
  sourceNode,
  targetNode,
  totalCost,
  path
RETURN
  index,
  gds.util.asNode(sourceNode).name AS sourceNodeName,
  gds.util.asNode(targetNode).name AS targetNodeName,
  totalCost,
  nodes(path) AS path
ORDER BY index
```

