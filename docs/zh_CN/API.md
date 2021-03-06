# JavaScript Client API参考文档 [![Slack](https://slack.min.io/slack?type=svg)](https://slack.min.io)

## 初使化MinIO Client对象.

## MinIO

```js
var Minio = require('minio')

var minioClient = new Minio.Client({
    endPoint: 'play.minio.io',
    port: 9000,
  	useSSL: true,
    accessKey: 'Q3AM3UQ867SPQQA43P2F',
    secretKey: 'zuf+tfteSlswRu7BJ86wekitnifILbZam1KYY3TG'
});
```

## AWS S3 

```js
var Minio = require('minio')

var s3Client = new Minio.Client({
    endPoint:  's3.amazonaws.com',
	accessKey: 'YOUR-ACCESSKEYID',
	secretKey: 'YOUR-SECRETACCESSKEY'
})
```
| 操作存储桶       | 操作对象      | Presigned操作 | 存储桶策略/通知  |
| ------------- |-------------| -----| ----- |
| [`makeBucket`](#makeBucket)    | [`getObject`](#getObject) | [`presignedUrl`](#presignedUrl) | [`getBucketNotification`](#getBucketNotification) |
| [`listBuckets`](#listBuckets)  | [`getPartialObject`](#getPartialObject)    |   [`presignedGetObject`](#presignedGetObject) | [`setBucketNotification`](#setBucketNotification) |
| [`bucketExists`](#bucketExists) | [`fGetObject`](#fGetObject)    |    [`presignedPutObject`](#presignedPutObject) | [`removeAllBucketNotification`](#removeAllBucketNotification) |
| [`removeBucket`](#removeBucket)      | [`putObject`](#putObject) |    [`presignedPostPolicy`](#presignedPostPolicy) | [`getBucketPolicy`](#getBucketPolicy) |  |
| [`listObjects`](#listObjects) | [`fPutObject`](#fPutObject)   |   |   [`setBucketPolicy`](#setBucketPolicy)
| [`listObjectsV2`](#listObjectsV2) | [`copyObject`](#copyObject) | | [`listenBucketNotification`](#listenBucketNotification)|
| [`listIncompleteUploads`](#listIncompleteUploads) |  [`statObject`](#statObject) |
|     |  [`removeObject`](#removeObject)    |
|     |  [`removeObjects`](#removeObjects)    |
|  | [`removeIncompleteUpload`](#removeIncompleteUpload)  |
  


## 1.  构造函数

<a name="MinioClient_endpoint"></a>
###  new Minio.Client ({endPoint, port, useSSL, accessKey, secretKey})

|     |
| ---- |
|``new Minio.Client ({endPoint, port, useSSL, accessKey, secretKey})``|
|初使化一个新的client对象。|

__参数__

| 参数| 类型 | 描述 |
|---|---|---|
| `endPoint`  |  _string_ | endPoint是一个主机名或者IP地址。 |
| `port` | _number_  | TCP/IP端口号。可选，默认值是，如果是http,则默认80端口，如果是https,则默认是443端口。 |
|`useSSL`    | _bool_    |如果是true，则用的是https而不是http,默认值是true。 |
| `accessKey`   | _string_   |accessKey类似于用户ID，用于唯一标识你的账户。 |
|`secretKey`  |  _string_   | secretKey是你账户的密码。|
|`region`    | _string_  |设置该值以覆盖自动发现存储桶region。（可选）|
|`transport`    | _string_  |设置此值以传递自定义传输。(可选)|
|`sessionToken`    | _string_  |设置此值以提供x-amz-security-token (AWS S3特定). (可选)|


__示例__

## 创建MinIO客户端

```js
var Minio = require('minio')

var minioClient = new Minio.Client({
    endPoint: 'play.minio.io',
    port: 9000,
    useSSL: true,
    accessKey: 'Q3AM3UQ867SPQQA43P2F',
    secretKey: 'zuf+tfteSlswRu7BJ86wekitnifILbZam1KYY3TG'
});
```

## 创建AWS S3客户端


```js
var Minio = require('minio')

var s3Client = new Minio.Client({
    endPoint:  's3.amazonaws.com',
    accessKey: 'YOUR-ACCESSKEYID',
    secretKey: 'YOUR-SECRETACCESSKEY'
})
```


## 2. 操作存储桶
<a name="makeBucket"></a>

### makeBucket(bucketName, region[, callback])

创建一个新的存储桶。

__参数__

| 参数| 类型 | 描述 |
|---|---|---|
|`bucketName`  | _string_  | 存储桶名称。 |
| `region`  |  _string_ | 存储桶被创建的region(地区)。这个参数是可选的。默认值是us-east-1(美国东一区)|
|`callback(err)`  |_function_   | 回调函数，`err`做为错误信息参数。如果成功创建存储桶则`err`为null。如果没有传callback的话，则返回一个`Promise`对象。 |


__示例__


```js
minioClient.makeBucket('mybucket', 'us-east-1', function(err) {
  if (err) return console.log('Error creating bucket.', err)
  console.log('Bucket created successfully in "us-east-1".')
})
```

<a name="listBuckets"></a>
### listBuckets([callback])

列出所有存储桶。


__参数__


| 参数| 类型 | 描述 |
|---|---|---|
|`callback(err, bucketStream) `  | _function_  | 回调函数，第一个参数是错误信息。`bucketStream`是带有存储桶信息的流。如果没有传callback的话，则返回一个`Promise`对象。|

bucketStream发出对象的格式如下:-

| 参数| 类型 | 描述 |
|---|---|---|
|`bucket.name`  | _string_ |存储桶名称 |
|`bucket.creationDate`| _Date_ |存储桶创建时间。  |



__示例__


```js
minioClient.listBuckets(function(err, buckets) {
  if (err) return console.log(err)
  console.log('buckets :', buckets)
})
```

<a name="bucketExists"></a>
#### bucketExists(bucketName[, callback])

验证存储桶是否存在。


__参数__


| 参数| 类型 | 描述 |
|---|---|---|
| `bucketName`  |  _string_ | 存储桶名称。  |
| `callback(err，exists)`  | _function_  | `exists`是一个指示`bucketName`是否存在的布尔变量。当操作期间发生错误时，`err`会被设置为true|

__示例__

```js
minioClient.bucketExists('mybucket', function(err, exists) {
  if (err) {
    return console.log(err)
  }
  if (exists) {
    return console.log('Bucket exists.')
  }
})
```

<a name="removeBucket"></a>
### removeBucket(bucketName[, callback])

删除存储桶。

__参数__


| 参数  |  类型 | 描述  |
|---|---|---|
| `bucketName`  | _string_  | 存储桶名称。  |
| `callback(err)`  | _function_  |  如果存储桶删除成功则`err`为`null`。如果没有传callback的话，则返回一个`Promise`对象。 |

__示例__


```js
minioClient.removeBucket('mybucket', function(err) {
  if (err) return console.log('unable to remove bucket.')
  console.log('Bucket removed successfully.')
})
```

<a name="listObjects"></a>
### listObjects(bucketName, prefix, recursive)

列出存储桶中所有对象。

__参数__


| 参数  |  类型 | 描述  |
| ---- | ---- | ---- |
| `bucketName` | _string_ | 存储桶名称。 |
| `prefix`  | _string_  |  要列出的对象的前缀 (可选，默认值是`''`)。 |
| `recursive`  | _bool_  | `true`代表递归查找，`false`代表类似文件夹查找，以'/'分隔，不查子文件夹。（可选，默认值是`false`）  |


__返回值__


| 参数  |  类型 | 描述  |
| ---- | ---- | ---- |
| `stream` | _Stream_ | 存储桶中对象信息的流。 |

对象的格式如下：

| 参数  |  类型 | 描述  |
| ---- | ---- | ---- |
| `obj.name` | _string_ | 对象名称。 |
| `obj.prefix` | _string_ | 对象名称的前缀。 |
| `obj.size` | _number_ | 对象的大小。 |
| `obj.etag` | _string_ |对象的etag值。 |
| `obj.lastModified` | _Date_ | 最后修改时间。 |

__示例__


```js
var stream = minioClient.listObjects('mybucket','', true)
stream.on('data', function(obj) { console.log(obj) } )
stream.on('error', function(err) { console.log(err) } )
```

<a name="listObjectsV2"></a>
### listObjectsV2(bucketName, prefix, recursive，startAfter)

使用S3列表对象V2版本API列出存储桶中的所有对象。

__参数__


| 参数  |  类型 | 描述  |
| ---- | ---- | ---- |
| `bucketName` | _string_ | 存储桶名称。 |
| `prefix`  | _string_  |  要列出的对象的前缀。（可选，默认值是`''`） |
| `recursive`  | _bool_  | `true`代表递归查找，`false`代表类似文件夹查找，以'/'分隔，不查子文件夹。（可选，默认值是`false`）  |
| `startAfter`  | _string_  |  指定在列出存储桶中对象后要启动的对象名称 (可选, 默认值为`''`). |

__返回值__

| 参数  |  类型 | 描述  |
| ---- | ---- | ---- |
| `stream` | _Stream_ | 存储桶中对象信息的流。 |

对象的格式如下：

| 参数  |  类型 | 描述  |
| ---- | ---- | ---- |
| `obj.name` | _string_ | 对象名称。 |
| `obj.prefix` | _string_ | 对象名称的前缀。 |
| `obj.size` | _number_ | 对象的大小。 |
| `obj.etag` | _string_ |对象的etag值。 |
| `obj.lastModified` | _Date_ | 最后修改时间。 |


__示例__


```js
var stream = minioClient.listObjectsV2('mybucket','', true)
stream.on('data', function(obj) { console.log(obj) } )
stream.on('error', function(err) { console.log(err) } )
```


<a name="listIncompleteUploads"></a>
### listIncompleteUploads(bucketName, prefix, recursive)

列出存储桶中未完整上传的对象。

__参数__


| 参数  |  类型 | 描述  |
| ---| ---|---|
| `bucketname`  | _string_  |  存储桶名称。 |
| `prefix`  | _string_  | 未完整上传的对象的前缀。（可选，默认值是`''`）。  |
| `recursive`  | _bool_  | `true`代表递归查找，`false`代表类似文件夹查找，以'/'分隔，不查子文件夹。（可选，默认值是`false`）  |


__返回值__


| 参数  |  类型 | 描述  |
| ---| ---|---|
| `stream`  | _Stream_  |  对象格式如下：|

| 参数  |  类型 | 描述  |
| ---| ---|---|
| `part.key`  | _string_  | 对象名称。|
| `part.uploadId`  | _string_  | 对象的上传ID。|
| `part.size`  | _Integer_  | 未完整上传的对象的大小。|

__示例__


```js
var Stream = minioClient.listIncompleteUploads('mybucket', '', true)
Stream.on('data', function(obj) {
  console.log(obj)
})
Stream.on('end', function() {
  console.log('End')
})
Stream.on('error', function(err) {
  console.log(err)
})
```

## 3.  操作对象

<a name="getObject"></a>
### getObject(bucketName, objectName[, callback])

下载流式对象。

__参数__


| 参数  |  类型 | 描述  |
|---|---|---|
|`bucketName` | _string_ | 存储桶名称。 |
|`objectName` | _string_ | 对象名称。 |
|`callback(err, stream)` | _function_ | 回调函数，第一个参数是错误信息。`stream`是对象的内容。如果没有传callback的话，则返回一个`Promise`对象。 |

__示例__


```js
var size = 0
minioClient.getObject('mybucket', 'photo.jpg', function(err, dataStream) {
  if (err) {
    return console.log(err)
  }
  dataStream.on('data', function(chunk) {
    size += chunk.length
  })
  dataStream.on('end', function() {
    console.log('End. Total size = ' + size)
  })
  dataStream.on('error', function(err) {
    console.log(err)
  })
})
```
<a name="getPartialObject"></a>
### getPartialObject(bucketName, objectName, offset, length[, callback])

将对象指定范围的字节作为流进行下载。

__参数__


| 参数  |  类型 | 描述  |
|---|---|---|
|  `bucketName` | _string_  | 存储桶名称。  |
| `objectName`   | _string_  | 对象名称。  |
| `offset`   | _number_  | `offset`是从第几个字节始  |
| `length`  | _number_  | `length`是要下载的字节数组长度（可选值，如果为空的话则代表从offset一直到文件的末尾）。  |
|`callback(err, stream)` | _function_  | 回调函数，第一个参数是错误信息。`stream`是对象的内容。如果没有传callback的话，则返回一个`Promise`对象。 |

__示例__


```js
var size = 0
// reads 30 bytes from the offset 10.
minioClient.getPartialObject('mybucket', 'photo.jpg', 10, 30, function(err, dataStream) {
  if (err) {
    return console.log(err)
  }
  dataStream.on('data', function(chunk) {
    size += chunk.length
  })
  dataStream.on('end', function() {
    console.log('End. Total size = ' + size)
  })
  dataStream.on('error', function(err) {
    console.log(err)
  })
})
```

<a name="fGetObject"></a>
### fGetObject(bucketName, objectName, filePath[, callback])

下载并将对象保存成本地文件。

__参数__

| 参数  |  类型 | 描述  |
|---|---|---|
| `bucketName`  | _string_   | 存储桶名称。  |
| `objectName`  |_string_   | 对象名称。  |
| `filePath`  |  _string_ | 要写入的本地文件路径。  |
| `callback(err)`  | _function_  | 如果报错的话，则会调用回调函数，传入`err`参数。 如果没有传callback的话，则返回一个`Promise`对象。 |


__示例__


```js
var size = 0
minioClient.fGetObject('mybucket', 'photo.jpg', '/tmp/photo.jpg', function(err) {
  if (err) {
    return console.log(err)
  }
  console.log('success')
})
```
<a name="putObject"></a>
### putObject(bucketName, objectName, stream, size, metaData[, callback])

从一个stream/Buffer中上传一个对象。

##### 从stream中上传

__参数__


| 参数  |  类型 | 描述  |
|---|---|---|
| `bucketName`  |_string_   | 存储桶名称。  |
| `objectName`  |_string_   | 对象名称。  |
| `stream`  | _Stream_  |可以读的流。   |
|`size`   | _number_  | 对象的大小（可选）。  |
|`metaData`   | _Javascript Object_  | 对象的元数据（可选）。|
| `callback(err, etag)` | _function_ | 如果有错误，则用非null调用Callback函数。`etag` _string_是上传的对象的etag值。如果没有传callback的话，则返回一个`Promise`对象。 |


__示例__

单个对象的最大大小限制在5TB。putObject在对象大于5MiB时，自动使用multiple parts方式上传。这样的话，当上传失败的时候，客户端只需要上传未成功的部分即可（类似断点上传）。上传的对象使用MD5SUM签名进行完整性验证。

```js
var Fs = require('fs')
var file = '/tmp/40mbfile'
var fileStream = Fs.createReadStream(file)
var fileStat = Fs.stat(file, function(err, stats) {
  if (err) {
    return console.log(err)
  }
  minioClient.putObject('mybucket', '40mbfile', fileStream, stats.size, function(err, etag) {
    return console.log(err, etag) // err should be null
  })
})
```

##### 从"Buffer"或者"string"上传

__参数__


| 参数  |  类型 | 描述  |
|---|---|---|
| `bucketName`  |_string_   | 存储桶名称。  |
| `objectName`  |_string_   | 对象名称。  |
|`string or Buffer`   | _Stream_ or _Buffer_  |字符串可者缓冲区   |
| `metaData`  | _Javascript Object_  | 对象的元数据（可选）。 |
| `callback(err, etag)`  | _function_  |如果有错误，则用非null调用Callback函数。`etag` _string_是上传的对象的etag值。 |


__示例__


```js
var buffer = 'Hello World'
minioClient.putObject('mybucket', 'hello-file', buffer, function(err, etag) {
  return console.log(err, etag) // err should be null
})
```
<a name="fPutObject"></a>
### fPutObject(bucketName, objectName, filePath, metaData[, callback])

上传文件内容到objectName。

__参数__


| 参数  |  类型 | 描述  |
|---|---|---|
| `bucketName`  | _string_  | 存储桶名称。  |
|`objectName`   |_string_   | 对象名称。  |
| `filePath`  | _string_  | 要上传的文件路径。  |
| `metaData`  | _Javascript Object_  | 对象的元数据。  |
| `callback(err, etag)`  |  _function_ | 如果有错误，则用非null调用Callback函数。`etag` _string_是上传的对象的etag值。如果没有传callback的话，则返回一个`Promise`对象。 |

__示例__

单个对象的最大大小限制在5TB。putObject在对象大于5MiB时，自动使用multiple parts方式上传。这样的话，当上传失败的时候，客户端只需要上传未成功的部分即可（类似断点上传）。上传的对象使用MD5SUM签名进行完整性验证。

```js
var file = '/tmp/40mbfile'
var metaData = {
  'Content-Type': 'text/html',
  'Content-Language': 123,
  'X-Amz-Meta-Testing': 1234,
  'example': 5678
}
minioClient.fPutObject('mybucket', '40mbfile', file, metaData, function(err, etag) {
  return console.log(err, etag) // err should be null
})
```

<a name="copyObject"></a>
### copyObject(bucketName, objectName, sourceObject, conditions[, callback])

将源对象拷贝到指定存储桶的新对象中。

__参数__


| 参数  |  类型 | 描述  |
|---|---|---|
| `bucketName`  | _string_  | 存储桶名称。  |
|`objectName`   |_string_   | 对象名称。  |
| `sourceObject`  | _string_  | 源对象的名称  |
| `conditions`  | _CopyConditions_  | 允许拷贝需要满足的条件。  |
| `callback(err, {etag, lastModified})`  |  _function_ | 如果有错误，则用非null调用Callback函数。`etag` _string_是上传的对象的etag值，lastModified _Date_是新拷贝对象的最后修改时间。如果没有传callback的话，则返回一个`Promise`对象。 |

__示例__

```js
var conds = new Minio.CopyConditions()
conds.setMatchETag('bd891862ea3e22c93ed53a098218791d')
minioClient.copyObject('mybucket', 'newobject', '/mybucket/srcobject', conds, function(e, data) {
  if (e) {
    return console.log(e)
  }
  console.log("Successfully copied the object:")
  console.log("etag = " + data.etag + ", lastModified = " + data.lastModified)
})
```


<a name="statObject"></a>
### statObject(bucketName, objectName[, callback])

获取对象的元数据。

__参数__


| 参数  |  类型 | 描述  |
|---|---|---|
| `bucketName`  | _string_  | 存储桶名称。  |
| `objectName`  | _string_  | 对象名称。  |
| `callback(err, stat)`  | _function_  |如果有错误，则用非null调用Callback函数。`stat`含有对象的元数据信息，格式如下所示。如果没有传callback的话，则返回一个`Promise`对象。 |



| 参数  |  类型 | 描述  |
|---|---|---|
| `stat.size`  | _number_  | 对象的大小。  |
| `stat.etag`  | _string_  | 对象的etag值。  |
| `stat.metaData`  | _Javascript Object_ | 对象的元数据。|
| `stat.lastModified`  | _string_  | Last 最后修改时间。|


__示例__


```js
minioClient.statObject('mybucket', 'photo.jpg', function(err, stat) {
  if (err) {
    return console.log(err)
  }
  console.log(stat)
})
```

<a name="removeObject"></a>
### removeObject(bucketName, objectName[, callback])

删除一个对象。

__参数__


| 参数  |  类型 | 描述  |
|---|---|---|
|`bucketName`   |  _string_ | 存储桶名称。  |
| objectName  |  _string_ | 对象名称。  |
| `callback(err)`  | _function_  | 如果有错误，则用非null调用Callback函数。如果没有传callback的话，则返回一个`Promise`对象。 |


__示例__


```js
minioClient.removeObject('mybucket', 'photo.jpg', function(err) {
  if (err) {
    return console.log('Unable to remove object', err)
  }
  console.log('Removed the object')
})
```

<a name="removeObjects"></a>
### removeObjects(bucketName, objectsList[, callback])

删除对象列表中的所有对象。

__Parameters__


| 参数 | 类型 | 描述 |
| ---- | ---- | ---- |
| `bucketName` | _string_ | 存储桶的名称。 |
| `objectsList`  | _object_  |  要移除存储桶中的对象列表。  |
| `callback(err)`  | _function_  | 如果有错误，则使用非null调用回调函数。 |


__Example__


```js

var objectsList = []

// List all object paths in bucket my-bucketname.
var objectsStream = s3Client.listObjects('my-bucketname', 'my-prefixname', true)

objectsStream.on('data', function(obj) {
  objectsList.push(obj.name);
})

objectsStream.on('error', function(e) {
  console.log(e);
})

objectsStream.on('end', function() {

  s3Client.removeObjects('my-bucketname',objectsList, function(e) {
    if (e) {
        return console.log('Unable to remove Objects ',e)
    }
    console.log('Removed the objects successfully')
  })

})


```


<a name="removeIncompleteUpload"></a>
### removeIncompleteUpload(bucketName, objectName[, callback])

删除一个未完整上传的对象。

__参数__


| 参数  |  类型 | 描述  |
|---|---|---|
| `bucketName`  |_string_   | 存储桶名称。  |
| `objectName`  | _string_  | 对象名称。  |
| `callback(err)`  | _function_  |如果有错误，则用非null调用Callback函数。如果没有传callback的话，则返回一个`Promise`对象。  |


__示例__


```js
minioClient.removeIncompleteUpload('mybucket', 'photo.jpg', function(err) {
  if (err) {
    return console.log('Unable to remove incomplete object', err)
  }
  console.log('Incomplete object removed successfully.')
})
```

## 4. Presigned操作

Presigned URLs用于对私有对象提供临时的上传/下载功能。

<a name="presignedUrl"></a>
### presignedUrl(httpMethod, bucketName, objectName[, expiry, reqParams, requestDate, cb])

生成一个给指定HTTP方法（'httpMethod'）请求用的presigned URL。浏览器/移动端的客户端可以用这个URL直接进行下载，即使其所在的存储桶是私有的。这个presigned URL可以设置一个以秒为单位的相关失效时间，之后URL不再有效。默认值是7天。


__参数__



|参数   | 类型	  | 描述  |
|---|---|---|
|`httpMethod` | _string_ | http方法，put、get等。 |
|`bucketName` | _string_ | 存储桶名称。 |
|`objectName` | _string_ | 对象名称。 |
|`expiry`     | _number_ | 失效时间（以秒为单位），默认是7天，不得大于七天。（可选） |
|`reqParams`  | _object_ | 请求参数。（可选） |
|`requestDate`  | _Date_ | 一个日期对象，url将会被发送到。 默认值为现在。 (可选) |
|`callback(err, presignedUrl)` | _function_ | 如果有错误，则用非null调用Callback函数。`presignedUrl`就是可临时上传/下载文件的URL。如果没有传callback的话，则返回一个`Promise`对象。 |


__示例1__


```js
// presigned url for 'getObject' method.
// expires in a day.
minioClient.presignedUrl('GET', 'mybucket', 'hello.txt', 24*60*60, function(err, presignedUrl) {
  if (err) return console.log(err)
  console.log(presignedUrl)
})
```


__示例2__


```js
// presigned url for 'listObject' method.
// Lists objects in 'myBucket' with prefix 'data'.
// Lists max 1000 of them.
minioClient.presignedUrl('GET', 'mybucket', '', 1000, {'prefix': 'data', 'max-keys': 1000}, function(err, presignedUrl) {
  if (err) return console.log(err)
  console.log(presignedUrl)
})
```

<a name="presignedGetObject"></a>
### presignedGetObject(bucketName, objectName[, expiry, respHeaders, requestDate, cb])

生成一个给HTTP GET请求用的presigned URL。浏览器/移动端的客户端可以用这个URL直接下载，即使其所在的存储桶是私有的。这个presigned URL可以设置一个以秒为单位的相关失效时间，之后URL不再有效。默认值是7天。


__参数__



| 参数  |  类型 | 描述  |
|---|---|---|
|`bucketName` | _string_ | 存储桶名称。 |
|`objectName` | _string_ | 对象名称。 |
|`expiry`     | _number_ | 失效时间（以秒为单位），默认是7天，不得大于七天。 |
|`respHeaders`  | _object_ | 要覆盖的响应表头 (可选) |
|`requestDate`  | _Date_ | A date object, the url will be issued at. 默认值是现在。(可选) |
|`callback(err, presignedUrl)` | _function_ | 如果有错误，则用非null调用Callback函数。`presignedUrl`就是可用于通过GET请求下载对象的URL。 如果没有传callback的话，则返回一个`Promise`对象。 |


__示例__


```js
// expires in a day.
minioClient.presignedGetObject('mybucket', 'hello.txt', 24*60*60, function(err, presignedUrl) {
  if (err) return console.log(err)
  console.log(presignedUrl)
})
```

<a name="presignedPutObject"></a>
### presignedPutObject(bucketName, objectName, expiry[, callback])

生成一个给HTTP PUT请求用的presigned URL。浏览器/移动端的客户端可以用这个URL进行上传，即使其所在的存储桶是私有的。这个presigned URL可以设置一个以秒为单位的相关失效时间，之后URL不再有效。默认值是7天。


__参数__


| 参数  |  类型 | 描述  |
|---|---|---|
|`bucketName` | _string_ | 存储桶名称。 |
|`objectName` | _string_ | 对象名称。 |
|`expiry`     | _number_ | 失效时间（以秒为单位），默认是7天，不得大于七天。 |
|`callback(err, presignedUrl)` | _function_ | 如果有错误，则用非null调用Callback函数。`presignedUrl`用于使用PUT请求进行上传。如果没有传callback的话，则返回一个`Promise`对象。 |


__示例__


```js
// expires in a day.
minioClient.presignedPutObject('mybucket', 'hello.txt', 24*60*60, function(err, presignedUrl) {
  if (err) return console.log(err)
  console.log(presignedUrl)
})
```

<a name="presignedPostPolicy"></a>
### presignedPostPolicy(policy[, callback])

允许给POST请求的presigned URL设置条件策略。比如接收上传的存储桶名称、名称前缀、过期策略。

__参数__


| 参数  |  类型 | 描述  |
|---|---|---|
| `policy`  | _object_  | 通过minioClient.newPostPolicy()创建的Policy对象。 |
| `callback(err, {postURL, formData})`  | _function_  |如果有错误，则用非null调用Callback函数。`postURL`用于使用post请求上传。`formData`是POST请求体中的键值对对象。如果没有传callback的话，则返回一个`Promise`对象。 |


创建策略：


```js
var policy = minioClient.newPostPolicy()
```

设置上传策略：

```js
// Policy restricted only for bucket 'mybucket'.
policy.setBucket('mybucket')

// Policy restricted only for hello.txt object.
policy.setKey('hello.txt')
```
或者

```js
// Policy restricted for incoming objects with keyPrefix.
policy.setKeyStartsWith('keyPrefix')

var expires = new Date
expires.setSeconds(24 * 60 * 60 * 10)
// Policy expires in 10 days.
policy.setExpires(expires)

// Only allow 'text'.
policy.setContentType('text/plain')

// Only allow content size in range 1KB to 1MB.
policy.setContentLengthRange(1024, 1024*1024)
```

使用`superagent`通过浏览器POST你的数据：


```js
minioClient.presignedPostPolicy(policy, function(err, data) {
  if (err) return console.log(err)

  var req = superagent.post(data.postURL)
  _.each(data.formData, function(value, key) {
    req.field(key, value)
  })

  // file contents.
  req.attach('file', '/path/to/hello.txt', 'hello.txt')

  req.end(function(err, res) {
    if (err) {
      return console.log(err.toString())
    }
    console.log('Upload successful.')
  })
})
```

## 5. 存储桶策略和通知操作

存储桶可以配置在指定事件类型和相应路径上触发通知。

<a name="getBucketNotification"></a>
### getBucketNotification(bucketName[, cb])

获取存储在S3提供商中的通知配置，该配置属于指定的存储桶名称。

__参数__



| 参数  |  类型 | 描述  |
|---|---|---|
| `bucketName`  | _string_  | 存储桶名称。  |
| `callback(err, bucketNotificationConfig)`  | _function_  | 如果有错误，则用非null调用Callback函数。`bucketNotificationConfig`是相应存储桶上的通知配置对象。如果没有传callback的话，则返回一个`Promise`对象。 |


__示例__


```js
minioClient.getBucketNotification('mybucket', function(err, bucketNotificationConfig) {
  if (err) return console.log(err)
  console.log(bucketNotificationConfig)
})
```

<a name="setBucketNotification"></a>
### setBucketNotification(bucketName, bucketNotificationConfig[, callback])

上传一个用户创建的通知配置，并绑定到指定的存储桶上。


__参数__


| 参数  |  类型 | 描述  |
|---|---|---|
| `bucketName`  | _string_  | 存储桶名称。  |
| `bucketNotificationConfig`  | _BucketNotification_   | 包含通知配置的Javascript对象。 |
| `callback(err)`  | _function_  | 如果有错误，则用非null调用Callback函数。如果没有传callback的话，则返回一个`Promise`对象。 |


__示例__

```js
// Create a new notification object
var bucketNotification = new Minio.NotificationConfig();

// Setup a new topic configuration
var arn = Minio.buildARN('aws', 'sns', 'us-west-2', '408065449417', 'TestTopic')
var topic = new Minio.TopicConfig(arn)
topic.addFilterSuffix('.jpg')
topic.addFilterPrefix('myphotos/')
topic.addEvent(Minio.ObjectReducedRedundancyLostObject)
topic.addEvent(Minio.ObjectCreatedAll)

// Add the topic to the overall notification object
bucketNotification.add(topic)

minioClient.setBucketNotification('mybucket', bucketNotification, function(err) {
  if (err) return console.log(err)
  console.log('Success')
})
```

<a name="removeAllBucketNotification"></a>
### removeAllBucketNotification(bucketName[, callback])

删除绑定在指定存储桶上的通知配置。

__参数__


| 参数  |  类型 | 描述  |
|---|---|---|
| `bucketName`  | _string_  | 存储桶名称。 |
| `callback(err)`  | _function_  | 如果有错误，则用非null调用Callback函数。如果没有传callback的话，则返回一个`Promise`对象。 |


```js
minioClient.removeAllBucketNotification('my-bucketname', function(e) {
  if (e) {
    return console.log(e)
  }
  console.log("True")
})
```

<a name="listenBucketNotification"></a>
### listenBucketNotification(bucketName, prefix, suffix, events)

可监听存储桶上的通知。此外，还可以通过前缀、后缀和事件类型进行过滤。使用本API并不需要预先设置存储桶通知。这是MinIO的一个扩展API，服务端基于进来的请求使用唯一ID自动注册或者取消注册。

返回一个`EventEmitter`对象，它可以广播一个携有记录的`通知`事件。

为了停止监听，可调用`EventEmitter`的`stop()`方法。

__参数__

| 参数  |  类型 | 描述  |
|---|---|---|
| `bucketName`  | _string_  | 存储桶名称。 |
| `prefix`  | _string_  | 用于过滤通知的对象名称前缀。 |
| `suffix`  | _string_  | 用于过滤通知的对象名称后缀。 |
| `events`  | _Array_ | 在指定事件类型上开启通知。 |

这里是你要的[完整示例](https://github.com/minio/minio-js/blob/master/examples/minio/listen-bucket-notification.js)，拿走不谢。

```js
var listener = minioClient.listenBucketNotification('my-bucketname', 'photos/', '.jpg', ['s3:ObjectCreated:*'])
listener.on('notification', function(record) {
  // For example: 's3:ObjectCreated:Put event occurred (2016-08-23T18:26:07.214Z)'
  console.log('%s event occurred (%s)', record.eventName, record.eventTime)
  listener.stop()
})
```

<a name="getBucketPolicy"></a>
### getBucketPolicy(bucketName [, callback])

获取绑定在指定存储桶上的访问策略，如果`objectPrefix`不为空，则会根据对象的权限过滤存储桶策略。

__参数__


| 参数  |  类型 | 描述  |
|---|---|---|
| `bucketName`  | _string_  | 存储桶名称。 |
| `callback(err, policy)`  | _function_  | 如果有错误，则用非null调用Callback函数。`policy`是[存储桶策略](https://docs.aws.amazon.com/AmazonS3/latest/dev/example-bucket-policies.html)。如果没有传callback的话，则返回一个`Promise`对象。 |


```js
// Retrieve bucket policy of 'my-bucketname' that applies to all objects that
// start with 'img-'.
minioClient.getBucketPolicy('my-bucketname', 'img-', function(err, policy) {
  if (err) throw err

  console.log(`Bucket policy: ${policy}`)
})
```

<a name="setBucketPolicy"></a>
### setBucketPolicy(bucketName, bucketPolicy[, callback])

设置指定存储桶的策略。这里是详细的[存储桶策略](https://docs.aws.amazon.com/AmazonS3/latest/dev/example-bucket-policies.html)。

__参数__


| 参数  |  类型 | 描述  |
|---|---|---|
| `bucketName`  | _string_  | 存储桶名称。 |
| `bucketPolicy` | _string_ | 存储桶策略。|
| `callback(err)`  | _function_  | 如果有错误，则用非null调用Callback函数。如果没有传callback的话，则返回一个`Promise`对象。 |


```js
// Set the bucket policy of `my-bucketname`
minioClient.setBucketPolicy('my-bucketname', JSON.stringify(policy), function(err) {
  if (err) throw err

  console.log('Bucket policy set')
})
```

## 6. HTTP请求选项
### setRequestOptions(选项)

设置HTTP/HTTPS请求选项。支持的选项是`agent`([http.Agent(https://nodejs.org/api/http.html#http_class_http_agent)])和与tls相关的选项('agent', 'ca', 'cert', 'ciphers', 'clientCertEngine', 'crl', 'dhparam', 'ecdhCurve', 'honorCipherOrder', 'key', 'passphrase', 'pfx', 'rejectUnauthorized', 'secureOptions', 'secureProtocol', 'servername', 'sessionIdContext')。与tls相关选项的参考文献在[这里](https://nodejs.org/api/tls.html#tls_tls_createsecurecontext_options)

```js
// Do not reject self signed certificates.
minioClient.setRequestOptions({rejectUnauthorized: false})
```

## 7. 了解更多


- [创建属于你的购物APP示例](https://github.com/minio/minio-js-store-app)
