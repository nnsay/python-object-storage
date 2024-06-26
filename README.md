# 1. 包说明

python-object-storage 包是针对对象存储操作的抽象化包, 主要通过 ObjectStorage 类来操作对象存储.

# 2. 使用

该包通过实例化 ObjectStorage 获取一个对象, 使用该对象来操作对象存储. 快速测试请参考如下代码:

```python
bucket = 'nnsay-cn'
provider = os.environ.get("OBJECT_STORAGE_PROVIDER", "OSS")
config = {
    "access_key_id": os.environ.get("OBJECT_STORAGE_AK"),
    "access_key_secret": os.environ.get("OBJECT_STORAGE_SK"),
    "region": os.environ.get("OBJECT_STORAGE_REGION"),
    "endpoint": os.environ.get("OBJECT_STORAGE_ENDPOINT")
}
object_storage = ObjectStorage(provider, config)

# 上传操作
with open('hello.log', 'rb') as data:
    etag = object_storage.put_object(bucket, 'hello.log', data)
    print(f"etag: {etag}")

# 下载操作
object_storage.download_file('download-hello-minio.log', bucket, 'hello.log')

# 获取对象
bytes = object_storage.get_object(bucket, 'hello.log')
data = bytes.decode("utf-8")
print(f"get data: {data}")

# 获取下载地址
url = object_storage.get_object_signed_url(bucket, 'hello.log')
print(f"download signed url: {url}")
```

包对象和方法详细说明请参考一下细节描述.

## 2.1 实例化

```python
# aws s3
object_storage = ObjectStorage("S3")

# aliyun oss
object_storage = ObjectStorage("OSS", {access_key_id: "ak", access_key_secret:"sk", region: "oss-cn-hangzhou"})

# minio
object_storage = ObjectStorage("OSS", {access_key_id: "ak", access_key_secret:"sk", endpoint: "http://localhost:9000"})
```

## 2.2 下载文件: download_file

下载对象存储到本地文件系统

```python
object_storage.download_file(local_file: str, bucket: str, key: "对象存储Key名称")
```

- 参数说明
  - local_file: 本地文件路径
  - bucket: 桶名称
  - key: 对象存储 Key 名称
- 返回值: 无

## 2.2 上传文件: put_object

```python
object_storage.put_object(bucket: str, key: str, body: Union[bytes, str])
```

- 参数说明
  - bucket: 桶名称
  - key: 对象存储 Key 名称
  - body: 上传对象存储数据
- 返回值: str, 文件的 etag

## 2.3 获取文件: get_object

```python
object_storage.get_object(bucket: str, key: str)
```

- 参数说明
  - bucket: 桶名称
  - key: 对象存储 Key 名称
- 返回值: bytes, 对象存储字节流

## 2.4 文件下载链接: get_object_signed_url

```python
object_storage.get_object_signed_url(bucket: str, key: str, expires: int = 3600)
```

- 参数说明
  - bucket: 桶名称
  - key: 对象存储 Key 名称
  - expires: 过期秒数, 默认 1 个小时
- 返回值: str, 下载对象的签名链接

## 2.5 文件上传链接: get_object_signed_url

```python
object_storage.put_object_signed_url(bucket: str, key: str, expires: int = 3600)
```

- 参数说明
  - bucket: 桶名称
  - key: 对象存储 Key 名称
  - expires: 过期秒数, 默认 1 个小时
- 返回值: str, 上传对象的签名链接
