[Previous](./[14]-Static-And-Media-Files.md) | [Table of Contents](./[0]-Introduction-to-Django.md) | [Next](./[16]-Testing-Django-Applications.md)

*APIs*

# Lesson 15 - Django REST Framework

---

## 15.1 Serializers

### What is an API, and why DRF?

So far your views returned HTML pages. Many applications also need an **API** (Application Programming Interface): URLs that return **data**, usually as **JSON**, for mobile apps, JavaScript front ends, or other services. A **REST** API uses standard HTTP methods on resource URLs:

| HTTP method | Typical meaning | Example |
|---|---|---|
| `GET` | Read | `GET /api/posts/` (list), `GET /api/posts/5/` (one) |
| `POST` | Create | `POST /api/posts/` |
| `PUT` | Replace | `PUT /api/posts/5/` |
| `PATCH` | Partially update | `PATCH /api/posts/5/` |
| `DELETE` | Remove | `DELETE /api/posts/5/` |

You could build this with plain Django views and `JsonResponse`, but you would have to write conversion, validation, authentication, pagination, and error handling yourself. **Django REST Framework (DRF)** is a third-party package that provides all of it.

### Installation

```bash
pip install djangorestframework
```

```python
# settings.py
INSTALLED_APPS = [
    ...
    "rest_framework",
    "blog",
]
```

### What is a serializer?

A **serializer** translates between complex Python objects (model instances, QuerySets) and plain data types such as dictionaries that can be rendered to JSON. It works in both directions:

- **Serialization:** model instance to JSON (for responses).
- **Deserialization:** JSON to validated Python data, optionally saved as a model (for requests).

Serializers also **validate** incoming data, much like Django forms (Lesson 10).

### ModelSerializer

For models, `ModelSerializer` generates the fields for you:

```python
# blog/serializers.py
from rest_framework import serializers
from .models import Post


class PostSerializer(serializers.ModelSerializer):
    class Meta:
        model = Post
        fields = ["id", "title", "slug", "body", "status", "created_at"]
        read_only_fields = ["id", "created_at"]
```

As with ModelForms, **list fields explicitly** instead of using `"__all__"`.

### Serializing data (output)

```python
post = Post.objects.get(pk=1)
serializer = PostSerializer(post)
serializer.data
# {'id': 1, 'title': 'Hello', 'slug': 'hello', 'body': '...', 'status': 'draft', 'created_at': '2026-03-14T10:30:00Z'}

serializer = PostSerializer(Post.objects.all(), many=True)     # many=True for a collection
serializer.data                                                 # a list of dictionaries
```

### Deserializing data (input)

```python
data = {"title": "New post", "slug": "new-post", "body": "Text", "status": "draft"}

serializer = PostSerializer(data=data)
if serializer.is_valid():
    post = serializer.save()           # calls create() and inserts the row
else:
    print(serializer.errors)           # {'title': ['This field is required.']}
```

To update, pass the existing instance:

```python
serializer = PostSerializer(post, data={"title": "Changed"}, partial=True)    # partial=True for PATCH
serializer.is_valid(raise_exception=True)       # raises a ValidationError DRF turns into a 400 response
serializer.save()                               # calls update()
```

### Explicit serializer fields

```python
class PostSerializer(serializers.ModelSerializer):
    author_name = serializers.CharField(source="author.name", read_only=True)
    comment_count = serializers.SerializerMethodField()

    class Meta:
        model = Post
        fields = ["id", "title", "author_name", "comment_count"]

    def get_comment_count(self, obj):
        return obj.comments.count()
```

| Field option | Meaning |
|---|---|
| `read_only=True` | Included in output, ignored in input |
| `write_only=True` | Accepted in input, never output (for example, passwords) |
| `required=False` | Optional in input |
| `source="..."` | Read the value from another attribute, possibly across a relation |
| `SerializerMethodField` | A read-only value computed by a `get_<field>` method |

### Validation

```python
class PostSerializer(serializers.ModelSerializer):
    class Meta:
        model = Post
        fields = ["id", "title", "body"]

    # Field-level: validate_<fieldname>
    def validate_title(self, value):
        if len(value) < 5:
            raise serializers.ValidationError("Title must be at least 5 characters.")
        return value

    # Object-level: validate()
    def validate(self, attrs):
        if attrs.get("title") == attrs.get("body"):
            raise serializers.ValidationError("Title and body must differ.")
        return attrs
```

### Relationships and nested serializers

By default a `ForeignKey` appears as the related object's primary key. To embed the full related object, nest a serializer:

```python
class CommentSerializer(serializers.ModelSerializer):
    class Meta:
        model = Comment
        fields = ["id", "name", "text"]


class PostDetailSerializer(serializers.ModelSerializer):
    comments = CommentSerializer(many=True, read_only=True)
    author = serializers.StringRelatedField()          # uses the author's __str__()

    class Meta:
        model = Post
        fields = ["id", "title", "body", "author", "comments"]
```

Other relation fields: `PrimaryKeyRelatedField`, `SlugRelatedField`, and `HyperlinkedRelatedField`.

Nested serializers are **read-only by default**. Writing nested data requires overriding `create()` and `update()`, which is more advanced, so many APIs accept IDs for writes and use nested output only for reads.

Prefetch relations in the view (Lesson 7) to avoid N+1 queries when nesting:

```python
Post.objects.select_related("author").prefetch_related("comments")
```

### Choosing the right serializer

| Class | Use for |
|---|---|
| `serializers.Serializer` | Full manual control, non-model data |
| `serializers.ModelSerializer` | Almost every model-backed API |
| `serializers.HyperlinkedModelSerializer` | APIs that link to related resources by URL |

---

## 15.2 API views and viewsets

DRF offers several layers of views. Each one trades control for brevity.

### Layer 1: function views with @api_view

```python
# blog/views.py
from rest_framework import status
from rest_framework.decorators import api_view
from rest_framework.response import Response

from .models import Post
from .serializers import PostSerializer


@api_view(["GET", "POST"])
def post_list(request):
    if request.method == "GET":
        posts = Post.objects.all()
        serializer = PostSerializer(posts, many=True)
        return Response(serializer.data)

    serializer = PostSerializer(data=request.data)
    if serializer.is_valid():
        serializer.save()
        return Response(serializer.data, status=status.HTTP_201_CREATED)
    return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)


@api_view(["GET", "PUT", "DELETE"])
def post_detail(request, pk):
    try:
        post = Post.objects.get(pk=pk)
    except Post.DoesNotExist:
        return Response(status=status.HTTP_404_NOT_FOUND)

    if request.method == "GET":
        return Response(PostSerializer(post).data)

    if request.method == "PUT":
        serializer = PostSerializer(post, data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

    post.delete()
    return Response(status=status.HTTP_204_NO_CONTENT)
```

Key differences from plain Django views:

- `request.data` holds the parsed body (JSON, form data, and so on), replacing `request.POST`.
- `Response` renders to JSON or HTML depending on what the client asks for; DRF also provides a browsable API in the browser.
- Status codes live in `rest_framework.status`.

### Layer 2: class-based APIView

```python
from rest_framework.views import APIView
from django.shortcuts import get_object_or_404


class PostListAPIView(APIView):
    def get(self, request):
        serializer = PostSerializer(Post.objects.all(), many=True)
        return Response(serializer.data)

    def post(self, request):
        serializer = PostSerializer(data=request.data)
        serializer.is_valid(raise_exception=True)
        serializer.save()
        return Response(serializer.data, status=status.HTTP_201_CREATED)


class PostDetailAPIView(APIView):
    def get(self, request, pk):
        post = get_object_or_404(Post, pk=pk)
        return Response(PostSerializer(post).data)
```

### Layer 3: generic views

Generic views remove the repetitive parts. You provide a `queryset` and a `serializer_class`:

```python
from rest_framework import generics


class PostListCreateView(generics.ListCreateAPIView):
    queryset = Post.objects.all()
    serializer_class = PostSerializer


class PostRetrieveUpdateDestroyView(generics.RetrieveUpdateDestroyAPIView):
    queryset = Post.objects.all()
    serializer_class = PostSerializer
```

| Generic view | Methods supported |
|---|---|
| `ListAPIView` | GET (list) |
| `CreateAPIView` | POST |
| `RetrieveAPIView` | GET (one) |
| `UpdateAPIView` | PUT, PATCH |
| `DestroyAPIView` | DELETE |
| `ListCreateAPIView` | GET (list), POST |
| `RetrieveUpdateDestroyAPIView` | GET, PUT, PATCH, DELETE |

Customize behavior by overriding hooks:

```python
class PostListCreateView(generics.ListCreateAPIView):
    serializer_class = PostSerializer

    def get_queryset(self):
        return Post.objects.filter(status="published").select_related("author")

    def perform_create(self, serializer):
        serializer.save(author=self.request.user)      # attach the current user
```

### Layer 4: ViewSets

A **ViewSet** groups all the actions for a resource in one class, and a router (Section 15.3) generates the URLs:

```python
from rest_framework import viewsets


class PostViewSet(viewsets.ModelViewSet):
    queryset = Post.objects.all()
    serializer_class = PostSerializer
```

`ModelViewSet` provides `list`, `create`, `retrieve`, `update`, `partial_update`, and `destroy`: the complete CRUD API in four lines.

| ViewSet class | Provides |
|---|---|
| `ModelViewSet` | Full CRUD |
| `ReadOnlyModelViewSet` | `list` and `retrieve` only |
| `GenericViewSet` | No actions; mix in only those you want |

Add extra endpoints with `@action`:

```python
from rest_framework.decorators import action


class PostViewSet(viewsets.ModelViewSet):
    queryset = Post.objects.all()
    serializer_class = PostSerializer

    @action(detail=True, methods=["post"])
    def publish(self, request, pk=None):
        post = self.get_object()
        post.status = "published"
        post.save()
        return Response({"status": "published"})
```

This creates `POST /api/posts/5/publish/`.

Use different serializers for different actions:

```python
    def get_serializer_class(self):
        if self.action == "retrieve":
            return PostDetailSerializer
        return PostSerializer
```

### Filtering, searching, ordering, and pagination

Add global pagination in settings:

```python
REST_FRAMEWORK = {
    "DEFAULT_PAGINATION_CLASS": "rest_framework.pagination.PageNumberPagination",
    "PAGE_SIZE": 20,
}
```

Responses are then wrapped with `count`, `next`, `previous`, and `results`.

Built-in filter backends handle search and ordering:

```python
from rest_framework import filters


class PostViewSet(viewsets.ModelViewSet):
    queryset = Post.objects.all()
    serializer_class = PostSerializer
    filter_backends = [filters.SearchFilter, filters.OrderingFilter]
    search_fields = ["title", "body"]            # ?search=django
    ordering_fields = ["created_at", "views"]    # ?ordering=-created_at
```

For field-based filtering (`?status=published`), the `django-filter` package integrates with DRF.

### Which layer should I use?

| Need | Use |
|---|---|
| A one-off, unusual endpoint | `@api_view` or `APIView` |
| Standard list/detail behavior for a model | Generic views |
| A full CRUD resource with minimal code | `ModelViewSet` |

---

## 15.3 Routers

### The problem routers solve

With viewsets, you would still have to write URLs for every action (list, detail, plus any `@action`). A **router** generates these URL patterns automatically.

### Using a router

```python
# blog/api_urls.py
from django.urls import include, path
from rest_framework.routers import DefaultRouter

from .views import CommentViewSet, PostViewSet

router = DefaultRouter()
router.register("posts", PostViewSet, basename="post")
router.register("comments", CommentViewSet, basename="comment")

urlpatterns = [
    path("", include(router.urls)),
]
```

```python
# mysite/urls.py
urlpatterns = [
    path("admin/", admin.site.urls),
    path("api/", include("blog.api_urls")),
]
```

### The URLs generated

For `router.register("posts", PostViewSet, basename="post")` the router creates:

| URL | HTTP method | Action | URL name |
|---|---|---|---|
| `/api/posts/` | GET | `list` | `post-list` |
| `/api/posts/` | POST | `create` | `post-list` |
| `/api/posts/{pk}/` | GET | `retrieve` | `post-detail` |
| `/api/posts/{pk}/` | PUT | `update` | `post-detail` |
| `/api/posts/{pk}/` | PATCH | `partial_update` | `post-detail` |
| `/api/posts/{pk}/` | DELETE | `destroy` | `post-detail` |
| `/api/posts/{pk}/publish/` | POST | the `@action` method | `post-publish` |

Reverse them like any other URL: `reverse("post-detail", kwargs={"pk": 5})`.

### basename

`basename` is used to build URL names. It is optional when the viewset has a `queryset` attribute (DRF derives it from the model name), but **required** if the viewset defines `get_queryset()` instead of a `queryset` attribute.

### SimpleRouter vs DefaultRouter

| Router | Extra behavior |
|---|---|
| `SimpleRouter` | Just the resource URLs |
| `DefaultRouter` | Adds an **API root** page at `/api/` listing all endpoints, and optional format suffixes such as `.json` |

### Manually binding viewsets

You can also map actions to URLs by hand, without a router:

```python
post_list = PostViewSet.as_view({"get": "list", "post": "create"})
post_detail = PostViewSet.as_view({"get": "retrieve", "put": "update", "delete": "destroy"})

urlpatterns = [
    path("posts/", post_list, name="post-list"),
    path("posts/<int:pk>/", post_detail, name="post-detail"),
]
```

### Nested resources

Routers do not nest by default. The simplest approach is a filtered flat endpoint (`/api/comments/?post=5`). Alternatively, write a custom `@action` on the parent:

```python
    @action(detail=True, methods=["get"])
    def comments(self, request, pk=None):
        post = self.get_object()
        serializer = CommentSerializer(post.comments.all(), many=True)
        return Response(serializer.data)
```

### Versioning your API

Include a version in the URL so you can change the API without breaking existing clients:

```python
urlpatterns = [
    path("api/v1/", include("blog.api_urls")),
]
```

### Trying out your API

- Open `http://127.0.0.1:8000/api/` in a browser to use DRF's **browsable API**.
- Use `curl` or a tool such as Postman or HTTPie:

```bash
curl http://127.0.0.1:8000/api/posts/

curl -X POST http://127.0.0.1:8000/api/posts/ \
     -H "Content-Type: application/json" \
     -d '{"title": "From curl", "slug": "from-curl", "body": "Hi"}'
```

---

## 15.4 Authentication and permissions

An API usually needs to know **who** is calling (authentication) and **whether they may do it** (permissions).

### Global defaults

```python
# settings.py
REST_FRAMEWORK = {
    "DEFAULT_AUTHENTICATION_CLASSES": [
        "rest_framework.authentication.SessionAuthentication",
        "rest_framework.authentication.TokenAuthentication",
    ],
    "DEFAULT_PERMISSION_CLASSES": [
        "rest_framework.permissions.IsAuthenticatedOrReadOnly",
    ],
}
```

The default (when you configure nothing) is to allow **everyone**, so setting a safer default is strongly recommended.

### Authentication classes

| Class | How it works | Typical use |
|---|---|---|
| `SessionAuthentication` | Uses Django's login session; requires a CSRF token for unsafe methods | Browser front ends on the same site |
| `BasicAuthentication` | Username and password on every request | Testing only; never without HTTPS |
| `TokenAuthentication` | A key sent as `Authorization: Token <key>` | Simple mobile apps and scripts |
| JWT (third-party, `djangorestframework-simplejwt`) | Short-lived signed access tokens plus refresh tokens | Modern SPAs and mobile apps |

### Token authentication

```python
INSTALLED_APPS = [
    ...
    "rest_framework",
    "rest_framework.authtoken",
]
```

```bash
python manage.py migrate
```

```python
# mysite/urls.py
from rest_framework.authtoken.views import obtain_auth_token

urlpatterns = [
    path("api/token/", obtain_auth_token),
    ...
]
```

A client logs in once and reuses the token:

```bash
curl -X POST http://127.0.0.1:8000/api/token/ -d "username=ada&password=secret"
# {"token": "9944b09199c62bcf9418ad846dd0e4bbdfc6ee4b"}

curl http://127.0.0.1:8000/api/posts/ -H "Authorization: Token 9944b09199c62bcf9418ad846dd0e4bbdfc6ee4b"
```

Treat tokens like passwords: send them only over HTTPS and never commit them.

### Permission classes

| Class | Who is allowed |
|---|---|
| `AllowAny` | Everyone |
| `IsAuthenticated` | Logged-in users only |
| `IsAuthenticatedOrReadOnly` | Anyone may read; only logged-in users may write |
| `IsAdminUser` | Users with `is_staff=True` |
| `DjangoModelPermissions` | Uses Django's model permissions (Lesson 12) |

Set them per view:

```python
from rest_framework.permissions import IsAuthenticated


class PostViewSet(viewsets.ModelViewSet):
    queryset = Post.objects.all()
    serializer_class = PostSerializer
    permission_classes = [IsAuthenticated]
```

With `@api_view`:

```python
from rest_framework.decorators import api_view, permission_classes

@api_view(["GET"])
@permission_classes([IsAuthenticated])
def my_view(request):
    ...
```

Permission classes in a list are combined with **AND**. Combine with `|` (OR) and `~` (NOT) using operators:

```python
permission_classes = [IsAdminUser | IsOwner]
```

### Custom permissions

Object-level rules, such as "only the author may edit", need a custom class:

```python
# blog/permissions.py
from rest_framework import permissions


class IsAuthorOrReadOnly(permissions.BasePermission):
    def has_object_permission(self, request, view, obj):
        if request.method in permissions.SAFE_METHODS:      # GET, HEAD, OPTIONS
            return True
        return obj.author == request.user
```

```python
class PostViewSet(viewsets.ModelViewSet):
    queryset = Post.objects.all()
    serializer_class = PostSerializer
    permission_classes = [permissions.IsAuthenticatedOrReadOnly, IsAuthorOrReadOnly]
```

Two methods exist:

- `has_permission(request, view)` is checked for **every** request.
- `has_object_permission(request, view, obj)` is checked when `get_object()` is called (detail routes).

### Limiting what a user can see

Permissions decide access to an object, but you should also filter lists so users never receive objects they should not see:

```python
def get_queryset(self):
    return Post.objects.filter(author=self.request.user)
```

### Throttling (rate limiting)

Protect your API from abuse and overload:

```python
REST_FRAMEWORK = {
    "DEFAULT_THROTTLE_CLASSES": [
        "rest_framework.throttling.AnonRateThrottle",
        "rest_framework.throttling.UserRateThrottle",
    ],
    "DEFAULT_THROTTLE_RATES": {
        "anon": "100/day",
        "user": "1000/day",
    },
}
```

Exceeding the limit returns `429 Too Many Requests`.

### Common status codes in APIs

| Code | Meaning | When |
|---|---|---|
| `200` | OK | Successful read or update |
| `201` | Created | Successful create |
| `204` | No Content | Successful delete |
| `400` | Bad Request | Validation failed |
| `401` | Unauthorized | Not authenticated |
| `403` | Forbidden | Authenticated but not permitted |
| `404` | Not Found | Object does not exist |
| `429` | Too Many Requests | Throttled |

### API security checklist

- Use **HTTPS** everywhere.
- Set a restrictive `DEFAULT_PERMISSION_CLASSES`.
- Use `fields` lists (never `"__all__"`) so sensitive columns are not exposed.
- Mark sensitive fields `write_only=True` (such as passwords).
- Validate all input in serializers.
- Apply throttling.
- For browser-based JavaScript clients on other domains, configure CORS with `django-cors-headers`, and understand that session authentication needs CSRF protection (Lesson 17).

---

[Previous](./[14]-Static-And-Media-Files.md) | [Table of Contents](./[0]-Introduction-to-Django.md) | [Next](./[16]-Testing-Django-Applications.md)
