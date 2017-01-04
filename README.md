# mongodb-sql
用来记录mongodb常用的sql语句



/bin/mongod -f conf/mongodb.conf    				# 启动db
/bin/mongo 127.0.0.1:port	    				# 客户端进入db
show dbs  							# 查看数据库
use [name]							# 进入数据库没有的话将创建数据库
db.dropDatabase()						# 删除数据库
db								# 检查当前数据库
db.[collection].insert({field:value})				# 在表中插入数据
show collections						# 查看所有的表
db.[collection].find()						# 返回所有记录
for(i=3;i<=100;i++)db.[collection].insert({"":""})		# 通过js循环插入多条记录
_id								#db自动生成的一个全局字段，值可以自己设置，但是不能重复否则错误 如：insert(_id:1)
db.[collection].find().count()					# 返回表中记录的数量
db.[collection].find().skip(3).limit(2).sort({"":""})		#skip 过滤前3条记录，limit 限制返回记录数量，sort 以什么字段排序
db.[collection].update({x:1},{x:2},[true])			# 更新记录，至少接收2个参数，如果有第三个参数并且为true则表示会执行insert({field:value})
db.[collection].update({field: value},{$inc:{field: value}})	# 对一个数字类型字段做 +=或者-= 操作
db.[collection].update({field: value},{$set:{field: value}})	# 重设某个字段的值
db.[collection].update({field: value],{$unset:{field: 1}})	# 删除某个字段
db.[collection].update({field: value},{$push:{field: value}})   # 把value追加到field里面去，field一定要是数组类型才行，如果field不存在，会新增一个数组类型加进去。




