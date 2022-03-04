# get

功能: 返回表中满足条件的一条且只能有一条数据。
说明: 参数中写查询条件。
1. 如果查到多条数据，则抛异常MultipleObjectsReturned。2)查询不到数据，则抛异常：DoesNotExist。
返回值: 返回值是一个模型类对象。