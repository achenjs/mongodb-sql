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
全文索引使用限制<br>
1.每次查询，只能指定一个$text查询<br>
2.$text查询不能出现在$nor查询中<br>
3.查询中如果包含了$text,hint不再起作用<br>
