# 日期。1)参数auto_now表示每次保存对象时，自动设置该字段为当前时间，用于"最后一次修改"的时间戳，它总是使用当前日期，默认为false。2) 参数auto_now_add表示当对象第一次被创建时自动设置当前时间，用于创建的时间戳，它总是使用当前日期，默认为false。3)参数auto_now_add和auto_now是相互排斥的，组合将会发生错误。

类型: DateField：([auto_now=False, auto_now_add=False])