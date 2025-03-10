# Redis

remote dictionary server 远程字典服务，是一种基于内存的KV键值对内存数据库

redis和mysql之间的关系类似于计算机组成原理里面的cache和内存，有了redis之后，要找数据会先去redis里面找，找不到了再去硬盘的mysql中找，然后再往redis里面写一份，redis和mysql是共同使用的

## 十大数据类型

help @数据类型，可以知道所有的相关操作，类似于内部的一个API文档

对于key的操作：端口号后面没有数字表示0号数据库，默认带16个库，key是区分大小写的

![image-20250308134003889](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250308134003889.png)

### String

```shell
set k1 v1xx // 设置k1的值
get k1 // 查看k1的值
set k1 v2 nx // if not exist 如果k1未存在则设置为v2
set k1 v1x xx // if exist 如果k1存在则修改其value为v1x
set k1 v1 get // 先把原先有的k1对应的value输出出来，然后把v1设置为其新值
set k1 v1 ex 10 // 设置k1的值为v1，过期时间为10秒后
ttl k1 // 查看k1所剩的有效时间
set k1 v1 px 8000 // 设置k1的值为v1，过期时间为8000毫秒后
set k1 v1 exat 1697049600 // 用unix时间戳不需要系统底层时间换算，会稍微准一点,unix时间戳需要通过System.currentTimeMillis()/1000L来查找
可以加上keepttl来确保在更改值的时候，过期时间不变继续走
```

多值批处理操作：

```shell
mset k1 v1 k2 v2 k3 v3 // OK
mget k1 k2 k3 // 1) "v1" 2) "v2" 3) "v3"
msetnx k1 v1 k4 v4 // k1存在 k4不存在 (integer) 0 类似于事务的完整性，要么一起成功要么一起失败
get k4 // (nil)
mset k5 v5 k6 v6 // (integer) 1
mget k1 k2 k3 k5 k6 // 1) "v1" 2) "v2" 3) "v3" 4) "v5" 5) "v6"
```

getrange/setrange

```shell
set k1 abcd1234 // OK
getrange k1 0 -1 // "abcd1234" 类似于substring
getrange k1 0 3 // "abcd"
getrange k1 0 4 // "abcd1"
setrange k1 1 xxyy // (integer) 8
get k1 // "axxyy234" 相当于从第1位开始后面用xxyy覆盖对应位置

```

数值增减：

```shell
set k1 100 // OK
get k1 // "100"

// 递增数字：INCR key
incr k1 // (integer) 101
incr k1 // (integer) 102
incr k1 // (integer) 103
incr k1 // (integer) 104

// 增加指定的整数：INCRBY key increment
incrby k1 3 // (intefer) 107
incrby k1 3 // (intefer) 110
incrby k1 3 // (intefer) 113
incrby k1 3 // (intefer) 116
incrby k1 3 // (intefer) 119

// 递减数值：DECR key
decr k1 // (integer) 118
decr k1 // (integer) 117
decr k1 // (integer) 116
decr k1 // (integer) 115

// 减少指定的整数：DECRBY key decrement
decrby k1 5 // (integer) 110
decrby k1 5 // (integer) 105
decrby k1 5 // (integer) 100
decrby k1 5 // (integer) 95

```

长度内容增加：

```shell
// 获取字符串长度：strlen key
set k1 abcd // OK
strlen k1 // (integer) 4

// 字符串内容追加：append key value
append k1 xxxx // (integer) 8
get k1 // "abcdxxxx"
```

分布式锁：setex 和setnx来建锁

### List

```shell
lpush list1 1 2 3 4 5 // (integer) 5  依次放在最左边
rpush list2 11 22 33 44 55 // (integer) 5  依次放在最右边
type list1 // list
lrange list1 0 -1 // 全部遍历 1) "5" 2) "4" 3) "3" 4) "2" 5) "1" 从左侧开始遍历
lrange list1 0 -1 // 全部遍历 1) "11" 2) "22" 3) "33" 4) "44" 5) "55"
// 注意：这里没有 rrange !!!

lpush list1 1 2 3 4 5 // (integer) 5
lpop list1 // "5"  队列左边弹出一个
lrange list1 0 -1 // 1) "4" 2) "3" 3) "2" 4) "1"
rpop list1 // "1"
lrange list1 0 -1 // 1) "4" 2) "3" 3) "2"

// lindex 按照索引下标获得元素（从上到下）
lpush list1 1 2 3 4 5 // (integer) 5
lindex list1 0 // "5"
lindex list1 2 // "3"

// llen 获取list列表中元素的个数
lpush list1 1 2 3 4 5 // (integer) 5
llen list1 // (integer) 5

// lrem key 数字N 给定值v1, 删除N个值等于v1的元素
// 从 left 往 right 删除 2 个值等于 v1 的元素，返回值为实际删除的数量
// lrem list3 0值表示删除全部给定的值，0个就是全部值
lpush list3 v1 v1 v1 v2 v3 v3 v4 v5 // (integer) 8
lrange list3 0 -1 // 1) "v5" 2) "v4" 3) "v3" 4) "v3" 5) "v2" 6) "v1" 7) "v1" 8) "v1"
lrem list3 2 v1 // (integer) 2
lrange list3 0 -1 // 1) "v5" 2) "v4" 3) "v3" 4) "v3" 5) "v2" 
// ltrim key 开始index 结束index, 截取指定范围的值后在赋值给key,类似于substring
lpush list1 0 1 2 3 4 5 6 7 8 9 // (integer) 10
lrange list1 0 -1 // 1) "9" 2) "8" 3) "7" 4) "6" 5) "5" 6) "4" 7) "3" 8) "2" 9) "1" 10) "0"
ltrim list1 3 5 // OK
lrange list1 0 -1 // 1) "6" 2) "5" 3) "4"
6) "v1"

// rpoplpush 源列表 目的列表, 移除列表的最后一个元素，并将该元素添加到另一个列表并返回
// RPOPLPUSH 这个命令在 Redis 6.2.0 已标注为过期，推荐使用 LMOVE 命令
lpush list1 1 2 2 2 // (integer) 4
lpush list2 11 22 33 44 55 // (integer) 5
rpoplpush list1 list2 // "1"
lrange list1 0 -1 // 1) "2" 2) "2" 3) "2"
lrange list2 0 -1 // 1) "1" 2) "11" 3) "22" 4) "33" 5) "44" 6) "55"

// lset key index value, 让指定数组集合的下标位置值替换成新值
lpush list1 1 2 2 2 // (integer) 4
lrange list1 0 -1 // 1) "2" 2) "2" 3) "2" 4)"1"
lset list1 1 redis // OK
lrange list1 0 -1 // 1) "2" 2) "redis" 3) "2" 4)"1"

// linsert key before/after 已有值 插入的新值
lpush list1 1 2 2 2 // (integer) 4
lrange list1 0 -1 // 1) "2" 2) "2" 3) "2"
lset list1 1 redis // OK
lrange list1 0 -1 // 1) "2" 2) "redis" 3) "2"
linsert list1 before redis golang // (integer) 4
lrange list1 0 -1 // 1) "2" 2) "golang" 3) "redis" 4) "2"
```

### Hash

KV键值对的模式不变，但是这边的V实际上也是一个KV，也就是一个string类型的field（字段）和value（值）的映射表，hash特别适合用于存储对象

hash具体命令：

```shell
hset user:001 id 11 name z3 age 21 // (integer) 3 user:001就是键
hget user:001 id // "11"
hget user:001 name // "z3"
hmset user:001 id 12 name li4 age 26 // 同时更改多值
hmget user:001 id name age // 1) "12" 2) "li4" 3) "26"
hgetall user:001 // 1) "id: 2) "12" 3) "name" 4) "li4" 5) "age" 6) "26"
hdel user:001 age // (integer) 1 删除

// hlen 获取某个key内的全部数量
hgetall user:001 // 1) "id: 2) "12" 3) "name" 4) "li4"
hlen user:001 // (integer) 2

// hexists key 在key里面的某个值的field
hgetall user:001 // 1) "id: 2) "12" 3) "name" 4) "li4"
hexists user:001 name // (integer) 1
hexists user:001 score // (integer) 0

// hkeys key 查询出所有key对应的子key值
// hvals key 查询出所有key对应的子key的value值
hgetall user:001 // 1) "id: 2) "12" 3) "name" 4) "li4"
hkeys user:001 // 1) "id" 2) "name"
hvals user:001 // 1) "12" 2) "li4"

hgetall user:001 // 1) "id: 2) "12" 3) "name" 4) "li4"
hset user:001 age 26 score 99.5 // (integer) 2
hgetall user:001 // 1) "id: 2) "12" 3) "name" 4) "li4" 5) "age" 6) "26" 7) "score" 8) "99.5"
hincrby user:001 age 1 // (integer) 27
hincrby user:001 age 1 // (integer) 28
hincrby user:001 age 2 // (integer) 30
hgetall user:001 // 1) "id: 2) "12" 3) "name" 4) "li4" 5) "age" 6) "30" 7) "score" 8) "99.5"
hincrbyfloat user:001 score 0.5 // "100"
hincrbyfloat user:001 score 0.5 // "100.5"
hincrbyfloat user:001 score 0.5 // "101"

// hsetnx,不存在赋值，存在了无效
hsetnx user:001 email redis@163.com // (integer) 1
hsetnx user:001 email redis@163.com // (integer) 0
```

### Set

单key多value，且无重复,无序，set的命令基本都可以看成s再连接上其他的英文

```shell
// SADD key member [member ...] 添加元素，可以多次向同一个key中设置不同值，不会覆盖之前的值
sadd set1 1 1 1 2 2 2 2 3 3 4 4 4 4 4 4 4 4 5 // (integer) 5
// SMEMBERS key 遍历集合中的所有元素
smembers set1 // 1) "1" 2) "2" 3) "3" 4) "4" 5) "5"
// SISMEMBER key member 判断元素是否在集合中
sismember set1 6 // (integer) 0
sismember set1 1 // (integer) 1
sismember set1 5 // (integer) 1
// SREM key member [member ...] 删除元素 删除成功返回1失败返回0
srem set1 7 // (integer) 0
srem set1 1 // (integer) 1
smembers set1 // 1) "2" 2) "3" 3) "4" 4) "5"
// scard 获取集合里面的元素个数
scard set1 // (integer) 4

sadd set1 1 1 1 2 2 2 2 3 3 4 4 4 4 4 4 4 4 5 6 6 7 8 8 8 // (integer) 8
smembers set1 // 1) "1" 2) "2" 3) "3" 4) "4" 5) "5" 6) "6" 7) "7" 8) "8"
// SRANDMEMBER key [数字] 从集合中随机展现[设置的数字个数]元素，元素不删除
srandmember set1 1 // "3"
srandmember set1 3 // 1) "7" 2) "2" 3) "6"
smembers set1 // 1) "1" 2) "2" 3) "3" 4) "4" 5) "5" 6) "6" 7) "7" 8) "8"
// SPOP key [数字] 从集合中随机[弹出]一个元素，出一个删除一个
spop set1 1 // "8"
spop set1 1 // "2"
spop set1 2 // 1) "6" 2) "4"
smembers set1 // 1) "1" 2) "3" 3) "5" 4) "7"

smembers set1 // 1) "1" 2) "3" 3) "5" 4) "7"
// smove key1 key2 member 将key1里已存在的某个值member赋给key2
sadd set2 a b c // (integer) 3
smove set1 set2 7 // （integer) 1
smembers set1 // 1) "1" 2) "3" 3) "5"
smembers set2 // 1) "b" 2) "a" 3) "7" 4) "c"

// 集合运算
sadd set1 a b c 1 2 // (integer) 5
sadd set2 1 2 3 a x // (integer) 5
// 集合的差集运算 A - B 属于A但是不属于B的元素构成的集合 SDIFF key [key ...],可以计算多个元素的差集
sdiff set1 set2 // 1) "b" 2) "c"
sdiff set2 set1 // 1) "3" 2) "x"
// 集合的并集运算 A ∪ B 属于A或者属于B的元素构成的集合 SUNION key [key ...]
sunion set1 set2 // 1) "x" 2) "a" 3) "b" 4) "1" 5) "2" 6) "3" 7) "c"
// 集合的交集运算 A ∩ B 属于A同时也属于B的共同拥有的元素构成的集合 SINTER key [key ...]
sinter set1 set2 // 1) "a" 2) "1" 3) "2"

// SINTERCARD numkeys key [key ...][LIMIT limit]
// numkeys 的具体值由输入的key个数决定
// SINTERCARD 为 redis7 新命令，它不返回结果集，而是返回结果的基数。返回由所有给定集合的交集产生的集合的基数
// 基数的词语解释: 用于表示事物个数的数
sadd set1 a b c 1 2 // (integer) 5
sadd set2 1 2 3 a x // (integer) 5
sinter set1 set2 // 1) "a" 2) "1" 3) "2"
sintercard 2 set1 set2 // (integer) 3
sintercard 2 set1 set2 limit 1 // (integer) 1
sintercard 2 set1 set2 limit 2 // (integer) 2
sintercard 2 set1 set2 limit 3 // (integer) 3
sintercard 2 set1 set2 limit 4 // (integer) 3
```



### Zset（SortedSet）

有序集合，和set一样也是string类型元素的集合，无重复，唯一，不同之处在于每个元素会关联一个double类型的分数，通过这个分数来进行排序，分数不唯一

```shell
// 添加元素
ZADD zset1 60 v1 70 v2 80 v3 90 V4 100 V5 // (integer) 5
// 按照元素分数从小到大的顺序返回索引从start到stop之间的所有元素
ZRANGE zset1 0 -1 // 1) "v1" 2) "v2" 3) "v3" 4) "v4" 5) "v5"
// 按照元素分数从小到大的顺序返回索引从start到stop之间的所有元素【带分数遍历】
ZRANGE zset1 0 -1 withscores // 1) "v1" 2) "60" 3) "v2" 4) "70" 5) "v3" 6) "80" 7) "v4" 8) "90" 9) "v5" 10) "100"
ZRANGE zset1 0 2 withscores // 1) "v1" 2) "60" 3) "v2" 4) "70" 5) "v3" 6) "80"
// 反转集合，按照元素分数从大到小的顺序返回索引从start到stop之间的所有元素
ZREVRANGE zset1 0 -1 withscores // 1) "v5" 2) "100" 3) "v4" 4) "90" 5) "v3" 6) "80" 7) "v2" 8) "70" 9) "v1" 10) "60"

// ZRANGEBYSCORE key min max [WITHSCORES] [LIMIT offset count]
// 获取指定分数范围的元素，可以在min和max前面加个(，表示不包含
// limit作用是【返回限制】，limit开始下标步，一共多少步,当limit max大于元素个数时，显示符合条件的所有元素组合
ZRANGEBYSCORE zset1 60 90 withscores // 1) "v1" 2) "60" 3) "v2" 4) "70" 5) "v3" 6) "80" 7) "v4" 8) "90" 【60 <= score <= 90】
ZRANGEBYSCORE zset1 (60 90 withscores // 1) "v2" 2) "70" 3) "v3" 4) "80" 5) "v4" 6) "90" 【60 < score <= 90】
ZRANGEBYSCORE zset1 (60 90 withscores limit 0 1 // 1) "v2" 2) "70" 分数大于60，小于等于90的第一个元素组合
ZRANGEBYSCORE zset1 (60 90 withscores limit 0 2 // 这里的limit后的两个数字，第一个表示跳过返回结果的个数，第二个表示取几个 因此结果为：1) "v2" 2) "70" 3) "v3" 4) "80"

// ZSCORE key member
// 获取元素的分数
zscore zset1 v5 // "100"
zscore zset1 v4 // "90"

// ZCARD key
// 获取集合中元素的数量
zcard zset1 // (integer) 5

// zrem key member [member ...]
// 某个score对应的value值，作用是删除元素
zrem zset1 v5 // (integer) 1
zrem zset1 v5 // (integer) 0
zrange zset1 0 -1 withscores // 1) "v1" 2) "60" 3) "v2" 4) "70" 5) "v3" 6) "80" 7) "v4" 8) "90"

// ZINCRBY key increment member
// 增加某个元素的分数
zincrby zset1 3 v1 // "63"=60+3
zrange zset1 0 -1 withscores // 1) "v1" 2) "63" 3) "v2" 4) "70" 5) "v3" 6) "80" 7) "v4" 8) "90"

// ZCOUNT key min max
// 获得指定分数内的元素个数
zcount zset1 60 100 // (integer) 4
zcount zset1 65 70 // (integer) 1

// ZMPOP numkeys key [key ...] MIN|MAX [COUNT count]
// 从键名列表中的第一个非空排序集中弹出一个或多个元素，它们是成员分数对
zrange zset1 0 -1 withscores // 1) "v1" 2) "63" 3) "v2" 4) "70" 5) "v3" 6) "80" 7) "v4" 8) "90"
zmpop 1 zset1 min count 1
// 1) "zset1"
// 2) 1) 1) "v1"
//       2) "63"
// ZMPOP 是 ZPOPMIN 和 ZPOPMAX 的扩展版本，允许从多个有序集合中弹出元素
// 1 表示要从多少个有序集合中弹出元素。在这里，1 表示只从 zset1 这一个有序集合中弹出元素
// zset1 是有序集合的名称。命令将从 zset1 这个有序集合中弹出元素。
// MIN 指定弹出元素的方向。MIN 表示弹出分数最低的元素（即最小的元素）。如果使用 MAX，则会弹出分数最高的元素。
// COUNT 1 指定要弹出的元素数量。COUNT 1 表示只弹出一个元素。你可以根据需要调整这个数字，以弹出多个元素。

// zrank key member [withscore] 通过memnber对应的value获得排名值
zrange zset1 0 -1 // 1) "v1" 2) "v2" 3) "v3"
zrank zset v2 // (integer) 0
// zrevrank key member [withscore] 通过子value逆序获得下标值
zrevrank zset v2 // (integer) 2

```



### GEO

经纬度，存储地理位置信息，GEO实际上也是zset，可以用zset相关命令对其遍历

```shell
// GEOADD key longitude latitude member [longitude latitude member]
// 添加经纬度坐标
// 多个经度(longitude)、纬度(latitude)、位置名称(member)添加到指定的key中
GEOADD city 116.403963 39.915119 "天安门" 116.403414 39.924091 "故宫" 116.024067 40.362639 "长城" // (integer) 3
// geo类型实际上是zset，可以使用zset相关的命令对其进行遍历
type city // zset
ZRANGE city 0-1 // 
1) "\xe5\xa4\xa9\xe5\xae\x89\xe9\x97\xa8" 
2) "\xe6\x95\x85\xe5\xae\xab" 
3) "\xe9\x95\xbf\xe5\x9f\x8e"
// 如果遍历出现中文乱码可以使用如下命令：quit 之后，输入：redis-cli --raw

// GEOPOS key member [member]
// 从键里面返回所有指定名称(member )元素的位置（经度和纬度），不存在返回nil
GEOPOS city 天安门 故宫 长城
// 116.40396326780319214
// 39.91511970338637383
// 116.40341609716415405
// 39.92409008156928252
// 116.02406591176986694
// 40.36263993239462167

// GEOHASH 返回一个或多个位置元素的GEOhash表示
// geohash 算法生成的base32编码值，3维变2维变1维
// 主要分为三步：将三维的地球变为二维的坐标在将二维的坐标转换为一维的点块最后将一维的点块转换为二进制再通过base32编码
// Redis GEO 使用 geohash 来保存地理位置的坐标。
// geohash 用于获取一个或多个位置元素的 geohash 值。
// GEOHASH key member [member ...]
GEOHASH city 天安门 故宫 长城
// wx4g0f6f2v0
// wx4g0gfqsj0
// wx4t85y1kt0

// GEODIST key member1 member2 [M|KM|FT|MI]
// 返回两个给定位置之间的距离 m-米 km-千米 ft-英寸 mi-英里
GE0DIST city 天安门 长城 km // 59.3390
GE0DIST city 天安门 长城 m  // 59338.9814
GEODIST city 天安门 故宫 km // 0.9988
GEODIST city 天安门 故宫 m  // 998.8332

// GEORADIUS key longitude latitude radius M|KM|FT|MI [WITHCOORD] [WITHDIST] [WITHHASH] [COUNT count [ANY]
// 以给定的经纬度为中心，返回与中心的距离不超过给定最大距离的所有元素位置
GEORADIUS city 116.418017 39.914402 10 km withdist withcoord count 10 withhash desc
GEORADIUS city 116.418017 39.914402 10 km withdist withcoord withhash count 10 desc
// 故宫
// 1.6470
// 4069885568908290
// 116.40341609716415405
// 39.92409008156928252
// 天安门
// 1.2016
// 4069885555089531
// 116.40396326780319214
// 39.91511970338637383
// WITHDIST：在返回位置元素的同时，将位置元素与中心之间的距离也一并返回。距离的单位和用户给定的范围单位保持一致。WITHCOORD：将位置元素的经度和维度也一并返回。
// WITHHASH：以 52 位有符号整数的形式，返回位置元素经过原始 geohash 编码的有序集合分值。这个选项主要用于底层应用或者调试，实际中的作用并不大。
// COUNT：限定返回的记录数。

// GEORADIUSBYMEMBER 跟GEORADIUS类似，找出位于指定范围内的元素，中心点是由给定的位置元素决定
GEORADIUSBYMEMBER city 天安门 10 km withdist withcoord count 10 withhash
// 天安门
// 0.0000
// 4069885555089531
// 116.40396326780319214
// 39.91511970338637383
// 故宫
// 0.9988
// 4069885568908290
// 116.40341609716415405
// 39.92409008156928252

```



### HyperLogLog

基数（去除重复后的数据）统计，在应对访问量这种数据量很大的情况下有好处，因为这个数据类型一个key就可以对应2的64次个不同元素的基数，只统计数量，不记录数据

```shell
pfadd hll01 1 3 5 7 9  // (integer) 1
pfadd hll02 1 2 4 4 4 4 5 5 5 9 9 10 // (integer) 1
pfcount hll01 // (integer) 5
pfcount hll02 // (integer) 6,只有6个不一样的
pfmerge hllresult hll01 hll02 // OK 去重的融合
pfcount hllresult // (integer) 8
type hll01 // string
```



### Bitmap

通过String类型来统计二值状态的数据类型，一个key对应的数据最大是有2的32次位

```
// setbit key offset value 会返回原来的值
// setbit 键偏移位 只能零或者1
// 【Bitmap的偏移量从零开始计算的】
setbit k1 1 1 // (integer) 0(这个0就是旧值)
setbit k1 2 1 // (integer) 0
setbit k1 3 1 // (integer) 0
setbit k1 3 7 // (error) ERR bit is not an integer or out of range
setbit k1 3 0 // (integer) 1
type k1 // string

// getbit key offset 获取键偏移位的值
getbit k1 0 // (integer) 0
getbit k1 1 // (integer) 1
getbit k1 2 // (integer) 1
getbit k1 3 // (integer) 0
getbit k1 4 // (integer) 0
getbit k1 365 // (integer) 0

// strlen key 统计【字节数】占用多少
// 不是字符串长度而是【占据几个字节】，超过8位后自己按照8位一组一byte再扩容
setbit k2 0 1 // (integer) 0
setbit k2 7 1 // (integer) 0
strlen k2 // (integer) 1 1byte=8bit
setbit k2 8 1 // (integer) 0
strlen k2 // (integer) 2

// bitcount key [start end [byte|bit]]
// 全部键里面包含有1的有多少个
setbit uid:login123 1 1 // (integer) 0
setbit uid:login123 2 1 // (integer) 0
setbit uid:login123 3 1 // (integer) 0
bitcount uid:login123 // (integer) 3

// bitop operation(AND|OR|XOR|NOT) destkey key [key ...]
// 用于对多个字符串键进行位操作，并将结果存储到目标键中。它支持四种位操作：AND、OR、XOR 和 NOT。
// 案例：连续2天都签到的用户数量
// 假如某个网站或者系统，它的用户有1000W，我们可以使用redis的HASH结构和bitmap结构做个用户id和位置的映射
hset uid:map 0 uid-092iok-z_j // (integer) 1
hset uid:map 1 uid-738exd-mhm // (integer) 1
hgetall uid:map // 1) "0" 2) "uid-092iok-z_j" 3) "1" 4) "uid-738exd-mhm"
setbit 20250212 0 1 // (integer) 0 表示0号用户也就是uid-092iok-z_j在2025年2月12日登陆过
setbit 20250212 1 1 // (integer) 0 表示1号用户也就是uid-738exd-mhm在2025年2月12日登陆过
setbit 20250212 2 1 // (integer) 0 表示2号用户在2025年2月12日登陆过
setbit 20250212 3 1 // (integer) 0 表示3号用户在2025年2月12日登陆过
getbit 20250212 0 // (integer) 1
setbit 20250213 0 1 // (integer) 0
setbit 20250213 2 1 // (integer) 0
bitcount 20250212 // (integer) 4
bitcount 20250213 // (integer) 2
BITOP AND k3 20250212 20250213 // (integer) 1 操作成功
bitcount k3 // (integer) 2

```

### Bitfield

一次可以操作多个比特位

```shell
#bitfield可以将一个Redis字符看作是一个由二进制位组成的数组
set fieldkey hello // OK
get fieldkey // "hello"
bitfield fieldkey get i8 0 // (integer) 104
bitfield fieldkey get i8 8 // (integer) 101
bitfield fieldkey get i8 16 // (integer) 108
bitfield fieldkey get i8 24 // (integer) 108
bitfield fieldkey get i8 32 // (integer) 111
#key: 要进行位操作的键。
#GET type offset: 从指定的偏移量 offset 处读取指定位宽 type 的值。
#SET type offset value: 在指定的偏移量 offset 处设置指定位宽 type 的值为 value。
#INCRBY type offset increment: 在指定的偏移量 offset 处对指定位宽 type 的值增加 increment。
#OVERFLOW WRAP|SAT|FAIL: 设置溢出行为，WRAP 表示回绕，SAT 表示饱和，FAIL 表示失败

BITFIELD fieldkey get i8 8 // (integer) 101
BITFIELD fieldkey set i8 8 120 // 从第9个位开始，将接下来8个位用有符号数120（即字母x）替换
// (integer) 101
get fieldkey // "hxllo"

set fieldkey hello // OK
bitfield fieldkey incrby u4 2 1 // 从第3个位开始，对接下来的4位无符号数+1
// (integer) 11
bitfield fieldkey incrby u4 2 1 // (integer) 12
bitfield fieldkey incrby u4 2 1 // (integer) 13
bitfield fieldkey incrby u4 2 1 // (integer) 14
bitfield fieldkey incrby u4 2 1 // (integer) 15
bitfield fieldkey incrby u4 2 1 // 默认overflow为wrap，即循环溢出
// (integer) 0

set test a // OK
BITFIELD test get i8 0 // (integer) 97 a对应的ascii码值97
BITFIELD test set i8 0 127 // (integer) 97 i8表示有符号8位二进制，范围(-128~127)
BITFIELD test get i8 0 // (integer) 127 理论极限
get test // "\x7f"
BITFIELD test set i8 0 158 // (integer) 127 默认overflow为wrap，即循环溢出
BITFIELD test get i8 0 // (integer) -98 循环溢出是超出时直接到最小值了，例如set 128 结果是-128   set 129 结果是-127 以此类推
BITFIELD test overflow sat i8 0 128 // (integer) -98
BITFIELD test get i8 0 // (integer) 127 上溢计算的结果为最大的整数值
BITFIELD test overflow fail set i8 0 888 // (nil)
```

### Stream

主要用于消息队列，弥补了之前无法实现消息的持久化和主备复制功能，可以把这个数据类型理解为Redis版本的MQ消息中间件，消息队列看作一个个节点，每个节点中其实也是一个个键值对

一个消息链表，将所有加入的消息都串起来，每个消息都有一个唯一的 `ID` 和对应的内容

```
XADD
消息 ID 必须要比上一个 ID 大
默认用星号表示自动生成 ID
∗ 用于 XADD 命令中，让系统自动生成 ID
xadd mystream * id 11 cname z3 // "1669453947894-0"序列号
xadd mystream * id 12 cname li4 // "1669453953261-0"
xadd mystream * k1 v1 k2 v2 k3 v3 // "1669453973537-0"
type mystream // stream

XRANGE key start end [COUNT count]，用于获取消息列表（可以指定范围），忽略删除的消息，start 表示开始值，- 代表最小值，end 表示结束值，+ 代表最大值，count 表示最多获取多少个值

XREVRANGE key end start [COUNT count]，根据 ID 降序输出

XDEL key id [id ...]，删除指定 Stream 中的一个或多个消息

XLEN key，用于获取指定 Stream 中的消息数量

XTRIM key MAXLEN|MINID，用于裁剪 Stream 的长度，保留指定数量的消息或删除指定 ID 之前的消息，用于对 Stream 的长度进行截取，如超长会进行截取

XREAD [COUNT count] [BLOCK milliseconds] STREAMS key [key ...] id [id ...]，用于获取消息（阻塞/非阻塞），只会返回大于指定 id 的消息
只会返回大于指定 ID 的消息，COUNT 最多读取多少条消息；BLOCK 是否以阻塞的方式读取消息，默认不阻塞，如果 milliseconds 设置为 0，表示永远阻塞
XREAD count 2 streams mystream $ // (nil)
XREAD count 2 streams mystream 0-0//0-0表示读取全部
阻塞时：
客户端1>XREAD count 1 block 0 streams mystream $
// 当一敲下回车，就会阻塞，一直在耗着，我们新开一个客户端，往流里添加消息
客户端2>XADD mystream * k8 v8 // "1669454754263-0"
// 然后可以发现刚刚阻塞等待的客户端1有了输出
// 客户端1>1) 1) "mystream"
// 客户端1>   2) 1) 1) "1669454754263-0"
// 客户端1>         2) 1) "k8"
// 客户端1>            2) "v8"
// 客户端1>(15,45s)
阻塞和非阻塞就是非阻塞如果拿不到直接输出nil，阻塞拿不到就一直等到他拿到
```

消费组相关指令：

```
XGROUP CREATE
XGROUP CREATE key group id|$，用于创建消费者组
xgroup create mystream groupA $，$ 表示从 Stream 尾部开始消费，只消费最新消息
xgroup create mystream groupB 0，0 表示从 Stream 头部开始消费
XREADGROUP GROUP group [COUNT count] [BLOCK milliseconds] STREAMS key id
XREADGROUP GROUP groupA consumer1 streams mystream >//这里groupA就是一个消费者组，而consumer1是这个组里面的一个消费者
注意：
1.stream 中的消息一旦被消费组里的一个消费者读取了，就不能再被该消费组内的其他消费者读取了，即同一个消费组里的消费者不能消费同一条消息
2.不同消费组的消费者可以消费同一条消息
3.消费者的名称是在xreadgroup语句的时候才能执行的
```

提问！基于 `Stream` 实现的消息队列，如何保证消费者在发生故障或宕机再次重启后，仍然可以读取未处理完的消息?

答：`Streams` 会自动使用内部队列（也称为 `PENDING List` ）留存消费组里每个消费者读取的消息保底措施，直到消费者使用 `XACK` 命令通知 `Streams` "消息已经处理完成”

```
XPENDING mystream groupC
// 1) (integer) 6
// 2) "1669454323961-0" 这里代表的是所有消费者读取的消息最小ID
// 3) "1669454754263-0" 这里代表的是所有消费者读取的消息最大ID
// 4) 1) 1) "consumer1" 三个消费者分别读取
//       2) "2"
//    2) 1) "consumer2" 三个消费者分别读取
//       2) "2"
//    3) 1) "consumer3" 三个消费者分别读取
//       2) "2"
XPENDING mystream groupA
// 1) (integer) 6
// 2) "1669454323961-0"
// 3) "1669454754263-0"
// 4) 1) 1) "consumer1" 一口气读6条
//       2) "6"

XACK key group id [id...]，向消息队列确认消息处理已完成
XPENDING mystream groupC - + 10 consumer2
// 1) 1) "1669454495782-0"
//    2) "consumer2"
//    3) (integer) 320571
//    4) (integer) 1
// 2) 1) "1669454547474-0"
//    2) "consumer2"
//    3) (integer) 320571
//    4) (integer) 1
XACK mystream groupC 1669454495782-0 // (integer) 1 ACK成功确认一条，返回1
XPENDING mystream groupC - + 10 consumer2
// 1) 1) "1669454547474-0"
//    2) "consumer2"
//    3) (integer) 356553 自上次传递以来的毫秒数，从消息最后一次被传递给消费者到现在的时间间隔（以毫秒为单位），这个值可以用来判断消息是否长时间未被处理（即是否处于“卡住”状态）
//    4) (integer) 1 消息被传递的次数，该消息被传递给消费者的次数，如果消息被多次传递（例如消费者处理失败后重新传递），这个值会大于 1
```

## 持久化

通过RDB（快照）或者AOF（指令复现）来实现持久化

### RDB （redis database）

rdb适合大规模的数据恢复，但是有2倍膨胀的缺点，而且可能备份不及时，在指定的时间间隔内，实现数据的快照，把所有数据都保存在硬盘上，以rdb文件格式保存，这种持久化方法是默认的

配置文件：

7之前的：

![image-20250310105328679](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250310105328679.png)

目前的：

![image-20250310105406995](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250310105406995.png)

操作分自动触发和手动触发

自动触发：

这个save可以手动设置，可以指定保存rdb文件的路径，路径中的文件夹要求已经存在，还可以修改rdb文件名

执行flushall会自动生成一个空的rdb文件，无意义，因此需要我们把启动redis的机器和备份rdb文件的机器分机，不然就会覆盖



手动触发：

调用bgsave来手动执行备份，不可以用save，因为save会阻塞当前redis服务器，而bgsave会另外fork一个子进程，和父进程一样，在这个子进程中自行备份

可以通过redis-check-rdb #rdb文件名来检查并修复指定文件

禁用快照：配置文件中直接禁用

![image-20250310112734638](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250310112734638.png)

参数优化：

![image-20250310113130194](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250310113130194.png)

### AOF （Append Only File）

以日志的形式来记录每个写操作，把所有执行过的指令都记录下来，并且这个文件只能继续追加写或者但不可以更改，AOF是默认关闭的，存下来的日志文件是.aof，这个文件里会自动压缩来保证文件不要太大下一次打开就会再执行一次aof文件中的所有命令来实现持久化

写回策略：

always 每个写命令执行完立刻同步把最新的日志写到磁盘

everysec 写命令执行完，先把东西写到aof缓存中，每隔一秒写到磁盘中一次，是默认的方式

no 写到缓存中，由操作系统决定什么写回磁盘

配置文件：

开启aof  `appendonly yes`即可

保存路径  为了和rdb区分，会在配置文件中多设置一项appenddirname，这一项会有个值，那个值会加在aof的文件路径当中
