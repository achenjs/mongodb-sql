# mongodb-sql
用来记录mongodb常用的sql语句
# 启动db
/bin/mongod -f conf/mongodb.conf
# 客户端进入db
/bin/mongo 127.0.0.1:port	
# 查看数据库
show dbs  	
# 进入数据库没有的话将创建数据库
use [name]	
# 删除数据库
db.dropDatabase()	
# 检查当前数据库
db						
# 在表中插入数据
db.[collection].insert({field:value})			
# 查看所有的表
show tables
# 查看当前数据库中的包含的集合
show collections	
# find():返回所有记录
db.[collection].find()		
# 通过js循环插入多条记录
for(i=3;i<=100;i++)db.[collection].insert({"":""})		
# db自动生成的一个全局字段，值可以自己设置，但是不能重复否则错误 如：insert(_id:1)
_id			
# count():返回表中记录的数量
db.[collection].find().count()	
# skip 过滤前3条记录，limit 限制返回记录数量，sort 以什么字段排序
db.[collection].find().skip(3).limit(2).sort({"":""})		
#  remove:删除记录
db.[collection].remove(field:value)
# update({},{},[]):更新记录，至少接收2个参数，如果有第三个参数并且为true则表示会执行insert({field:value})
db.[collection].update({x:1},{x:2},[true])
# update()为了防止误操作，默认只能修改第一条记录，如果想一次修改多条记录，则需要加入第四个参数为true并且使用$set修饰符
db.[collection].update({x:1},{$set:{x:2}},false,true)   =>  将修改find({x:1})所有记录的value值
# $inc:对一个数字类型字段做 +=或者-= 操作
db.[collection].update({field: value},{$inc:{field: value}})	
# $set:重设某个字段的值
db.[collection].update({field: value},{$set:{field: value}})	
# $unset:删除记录中的某个字段
db.[collection].update({field: value],{$unset:{field: 1}})	
# $push:把value追加到field里面去，field一定要是数组类型才行，如果field不存在，会新增一个数组类型加进去。
db.[collection].update({field: value},{$push:{field: value}})   
# $pushAll:一次可以追加多个值到一个数组字段内。
db.[collection].update({field: value},{$pushAll:{field:['','',...]}})
# $addToSet:增加一个值到数组内，而且只有当这个值不在数组内才增加
db.[collection].update({field: value},{$addToSet:{field: {$each: [value1,value2,...]}}}) or 
db.[collection].update({field: value},{$addToSet:{field: [value1,value2,...]}})
# $pop:删除数组内的一个值，只能删除一个值，value只能为-1或者1,  -1删除第一个，1删除最后一个
db.[collection].update({field: value}, {$pop:{field: -1}})
# $pull:从数组field内删除一个等于value值
db.[collection].update({field: value}, {$pull:{field: value}})
# <del>$pullAll:同pull，删除数组field内所有等于value值<del>
db.[collection].update({field: value},{pullAll:{field: value}})
# $meta：写在查询条件后面可以返回返回结果的相似度
db.[collection].find({field:value},{score:{$meta:"field"}})
# remove({field: value}):删除所有value记录
db.[collection].remove({field: value})
# 删除表
db.[collection].drop()

# 索引的种类
1._id索引 2.单键索引  3.多键索引  4.复合索引  5.过期索引  6.全文索引  7.地理位置索引
# getIndexes():查看当前索引
db.[collection].getIndexes()
# ensureIndex({field: 1}):创建field索引并且正向排序，-1为逆向排序
db.[collection].ensureIndex({field: -1})  // 3.2版本后 ensureIndex 改为 createIndex
# expireAfterSeconds:设置过期时间，单位为秒
db.test.ensureIndex({time:1},{expireAfterSeconds:10})   //mongo有最小过期时间为60秒，并且有误差
# $text:{$search:"value"}: 全文检索
db.[collection].createIndex("article":"text") //先创建全文索引 <br>
db.[collection].find({$text:{$search:value}}) //找到所有值包含有value的记录 <br>
db.[collection].find({$text:{$search:"value -value1"}}) //找到必须有value,可以没有value1的记录 <br>
db.[collection].find({$text:{$search:"\"value\" \"value1\""}})  //找到必须包含value,value2的记录 <br>
# 全文索引使用限制
1.每次查询，只能指定一个$text查询<br>
2.$text查询不能出现在$nor查询中<br>
3.查询中如果包含了$text,hint不再起作用<br>

# 索引的属性
1.名字,name指定:<br>
db.[collection].createIndex({"key":"value"},{"name": "aa"})  //自定义索引的name为aa <br>
2.唯一性,unique指定:<br>
db.[collection].createIndex({},{unique:true/false})<br>  // 指定唯一索引后，不能插入相同的记录
3.稀疏性，sparse指定:<br>
db.[collection].createIndex({},{sparse:true/false})<br>   // 强制使用稀疏索引时，只是在包含稀疏索引的集合中查找
4.是否定时删除，expireAfterSeconds指定:<br>
TTL,过期索引<br>

# 地理位置索引
概念：将一些点的位置存储在MongoDB中，创建索引后，可以按照位置来查找其他店 <br>
子分类：2d索引,用于存储和查找平面上的点。 2dsphere索引，用于存储和查找球面上的点<br>
查找方式:<br>
1.查找距离某个点一定距离内的点<br>
2.查找包含在某个区域内的点<br>                  //   例如打车软件，外卖配送服务等等

# 2d索引
创建方式：db.[collection].createIndex({x:"2d"})<br>
位置表示方式：经纬度【经度，纬度】<br>取值范围：经度【-180,180】 纬度【-90,90】<br>
db.[collection].insert({x:[180,100]})<br>
<br>
查询方式：<br>
(1)$near查询：查询距离某个点最近的点.<br>
db.[collection].find({x:{$near:[1,1]}})                    会返回100个距离最近的记录 <br>
db.[collection].find({x:{$near:[1,1],$maxDistance:10}})    筛选出范围10以内的记录 <br>
<br>
(2)$geoWithin查询：查询某个形状内的点.<br>
形状的表示：<br>
1.$box：矩形，使用
{$box:[[x1,y1],[x2,y2]]} 表示 <br>
db.[collection].find({x:{$geoWithin:{$box:[1,1],[2,2]}}})
2.$center：圆形，使用
${$center:[[x1,y1],r]} 表示 x1,y1代表圆心的位置，r代表半径<br>
3.$polygon：多边形，使用
{$polygon:[[x1,y1],[x2,y2],[x3,y3]]} 表示<br>
<br>
geoNear查询：<br>
geoNear使用runCommand命令进行使用，常用使用如下：
db.runCommand({
  geoNear:<collection>,
  near:[x,y],
  minDistance:
  maxDistance:
  num:              // 限制返回记录个数
})
# 2dsphere索引：
球面地理位置索引概念：球面地理位置索引<br>
创建方式：db.[collection].createIndex({x:"2dsphere"})<br>
位置表示方式：<br>
GeoJSON：描述一个点，一条直线，多边形等形状。<br>
格式：<br>
{type:"",coordinates:[coordinates]} <br>
查询方式与2d索引查询方式类似，支持$minDistance与$maxDistance
<br>
# 索引构建情况分析
如何评判当前索引构建情况：<br>
1.mongostat工具介绍.        //bin目录下的  mongostat可以查看mongodb的状态    <br>
2.profile集合介绍.          <br>      
3.日志介绍.<br>
4.explain分析.    db.[collection].find({}).explain('executionStats') 可以用来查看find有没有用到索引查询等信息 <br>
http://tieba.baidu.com/p/3941931560  详细说明地址

# Mongodb安全概览
1.最安全的是物理隔离：不现实 <br>
2.网络隔离其次 <br>
3.防火墙再其次 <br>
4.用户名密码在最后 <br>

# 使用用户名密码登录  配置mongo.conf
1.auth=true <br>
2.keyfile开启

# Mongodb创建用户
1.创建语法：createUser(2.6之前为addUser) <br>
2.{
  user: "name",
  pwd: "password",
  customData: {},
  roles: [{role: "角色类型", db: "database"}]
} <br>
3.角色类型：内建类型(read,readWrite,dbAdmin,dbOwner,userAdmin...) http://blog.csdn.net/chen88358323/article/details/50206651 <br>

# Mongodb用户角色详解
1.数据库角色(read,readWrite,dbAdmin,dbOwner,userAdmin) <br>
2.集群角色(clusterAdmin, clusterManager...) <br>
3.备份角色(backup, restore...) <br>
4.其他特殊权限(DBAdminAnyDatabase...)
