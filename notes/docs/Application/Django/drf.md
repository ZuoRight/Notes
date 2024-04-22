# DRF

`pip install djangorestframework`

- `settings.py`

```python
INSTALLED_APPS = [
    # ...
    'rest_framework',
    # ...
]
```

- `models.py`

```python
from django.db import models

# Create your models here.
class BlogPost(models.Model):
    title = models.CharField(max_length=100)
    content = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)
```

- `serializers.py`

```python
from rest_framework import serializers
from .models import BlogPost


# 序列化器用于将模型实例转换成 JSON 格式的数据，以及将 JSON 数据解析回模型实例。
# BlogPostSerializer 类将处理 BlogPost 模型的序列化和反序列化
class BlogPostSerializer(serializers.ModelSerializer):
    class Meta:
        model = BlogPost  # 指定模型
        fields = ['id', 'title', 'content', 'created_at']  # 指定想要包含在序列化中的字段
```

- `permissions.py`

```python
from rest_framework.permissions import BasePermission

class IsOwnerOrReadOnly(BasePermission):
    """
    自定义权限，只允许对象的所有者编辑它。
    """

    def has_object_permission(self, request, view, obj):
        # 读取权限允许任何请求，
        # 所以我们总是允许 GET, HEAD 或 OPTIONS 请求。
        if request.method in SAFE_METHODS:
            return True

        # 写入权限只允许给 obj 的所有者。
        return obj.owner == request.user
```

- `views.py`

```python
from rest_framework import viewsets
from .models import BlogPost
from .serializers import BlogPostSerializer
from .permissions import IsOwnerOrReadOnly

# 视图集（ViewSet）是一种特殊类型的视图，它抽象化并组合了处理常见的 HTTP 请求（如 GET、POST、PUT、DELETE）的逻辑。
# 主要目的是简化视图的代码，使得你不需要为每种类型的 HTTP 请求单独编写视图函数。
"""
在这个例子中，BlogPostViewSet 继承自 ModelViewSet，
它自动提供了以下一些操作
    list（列出所有博客帖子）
    create（创建新博客帖子）
    retrieve（获取特定博客帖子的详情）
    update（更新博客帖子）
    destroy（删除博客帖子）
"""
class BlogPostViewSet(viewsets.ModelViewSet):
    queryset = BlogPost.objects.all()
    serializer_class = BlogPostSerializer
    permission_classes = [IsOwnerOrReadOnly]
```

- `urls.py`

```python
from django.urls import path, include
from rest_framework.routers import DefaultRouter
from .views import BlogPostViewSet

# DRF带的路由器，自动为视图集的各种操作提供了相应的URL路由
router = DefaultRouter()
router.register(r'blogposts', BlogPostViewSet)  # 所有路由以blogposts开始，比如/blogposts/{id}/

urlpatterns = [
    path('', include(router.urls)),
]
```
