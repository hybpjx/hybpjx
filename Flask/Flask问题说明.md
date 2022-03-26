# Flask问题说明

## 1. 导入试图放到最后的原因

- 循环引用

```python
from flask import Blueprint

goods_bp = Blueprint('goods', __name__)

from . import views
```

如果提前导入views

```python
from . import goods_bp


@goods_bp.route('/goods')
def get_goods():
    return 'get_goods'

```

会发现view要先导入蓝图 才能创建路由



这就是循环引用



## 2. Flask Debug模式的作用

- Django Debug模式的作用
    - 支持静态文件
    - 后端出现错误，会直接显示真是的错误信息给前端
    - 修改代码后，自动重启开发服务器
- Flask Debug的作用
    - 后端出现错误，会直接显示真是的错误信息给前端
    - 修改代码后，自动重启开发服务器







## 3. 支持options请求不等价与实现了CORS跨域解决方案

options https请求方式 返回接口信息

