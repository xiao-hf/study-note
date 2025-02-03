### Linux环境

#### 手动安装

教程: https://min.io/download?view=aistor-custom

- 下载

  ```
  wget https://dl.min.io/server/minio/release/linux-amd64/minio
  ```

- 赋可执行权限

  ```
  chmod +x minio
  ```

- 前台启动

  ```
  MINIO_ROOT_USER=admin MINIO_ROOT_PASSWORD=password ./minio server /mnt/data --console-address ":9001"
  ```

- 后台启动

  ```
  ./minio server /mnt/data --console-address ":9001" &
  ```

- 使用命令

  ```
  minio [FLAGS] COMMAND [ARGS...]
  ```

#### docker安装

- pull镜像

```
docker pull minio/minio
```

- 启动

```
docker run -v /mnt/docker/data:/mnt/docker/data -p 9000:9000 -p 9001:9001 minio/minio server /mnt/docker/data --console-address :9001
```

### windows环境

- 下载地址

```
https://dl.min.io/server/minio/release/windows-amd64/minio.exe
```

- 启动

```
minio.exe server D:\develop\minio\data
```

- 访问后台

http://localhost:9000

### SpringBoot集成Minio

- 依赖

  ```xml
  <dependency>
      <groupId>io.minio</groupId>
      <artifactId>minio</artifactId>
      <version>8.5.9</version>
  </dependency>
  ```

- 定义MinioClient类型bean

  ```java
  @Bean
  public MinioClient minioClient() {
      return MinioClient.builder()
              .endpoint("http://192.168.80.128:9000")
              .credentials("minioadmin", "minioadmin")
              .build();
  }
  ```

- MinioClient对象没有线程安全问题

- Bucket: 存储Object的逻辑空间, 每个Bucket的数据相互隔离
- Object: 存储到MinIO的基本对象, 相当于文件

### MinioClient常用API

- bucketExists(): 检查桶是否存在
- makeBucket(): 指定新桶名称创建新桶
- listBuckets(): 列出用户有权访问的所有存储桶
- removeBucket(): 删除已存在的桶, 删除失败抛异常
- putObject(): 用于上传文件到指定的存储桶
- statObject(): 检查对象是否存在
- getPresignedObjectUrl(): 生成一个对象(文件)的url, 以便通过http访问
- getObject(): 获取文件

### MinioClient封装

- 存文件

方式1

```java
void uploadFile1(String bucketName, String dir, String objName) throws Exception {
    File file = new File(dir);
    FileInputStream fis = new FileInputStream(file);
    ObjectWriteResponse resp = minioClient.putObject(PutObjectArgs.builder().bucket(bucketName).object(objName)
            .stream(fis, file.length(), -1).build());
    fis.close();
    log.info("{}", resp);
}
```

方式2

```java
void uploadFile2(String bucketName, String dir, String fileName) throws Exception {
    ObjectWriteResponse resp = minioClient.uploadObject(UploadObjectArgs.builder()
            .bucket(bucketName)
            .object(fileName)
            .filename(dir)
            .build());
    log.info("{}", resp);
}
```



- 创建桶

  ```java
  void createBucket(String bucketName) throws Exception {
      if (!minioClient.bucketExists(BucketExistsArgs.builder().bucket(bucketName).build())) {
          minioClient.makeBucket(MakeBucketArgs.builder().bucket(bucketName).build());
          log.info("Success!");
      } else
          log.info("Already Exist!");
  }
  ```

- 删除桶

  ```java
  void removeBucket(String bucketName) throws Exception {
      minioClient.removeBucket(RemoveBucketArgs.builder().bucket(bucketName).build());
  }
  ```

- 指定文件生成下载url

  ```java
  String getObjUrl(String bucketName, String ObjName, Method method) throws Exception {
      return minioClient.getPresignedObjectUrl(GetPresignedObjectUrlArgs.builder()
              .bucket(bucketName)
              .object(ObjName)
              .expiry(3, TimeUnit.MINUTES) // 指定过期时间
              .method(method) // 指定访问方式 GET等
              .build()
      );
  }
  ```

- 指定文件获取字节数组

  ```java
  byte[] getFile(String bucketName, String objName) throws Exception {
      return minioClient.getObject(GetObjectArgs.builder()
                      .bucket(bucketName)
                      .object(objName)
              .build()).readAllBytes();
  }
  ```

- 指定文件写入指定目录

  ```java
  void transfer(String bucketName, String objName, String targetDir) throws Exception {
      minioClient.getObject(GetObjectArgs.builder()
              .bucket(bucketName)
              .object(objName)
              .build())
              .transferTo(new FileOutputStream(targetDir));
  }
  ```

- 遍历Object

  ```
  void listObjects(String bucketName) {
      Iterable<Result<Item>> res = minioClient.listObjects(ListObjectsArgs.builder().bucket(bucketName).build());
      res.forEach(itemResult -> {
          try {
              Item item = itemResult.get();
              System.out.println(item.objectName());
          } catch (Exception e) {
              throw new RuntimeException(e);
          }
      });
  }
  ```

- 删除单个文件

  ```
  void removeObject(String buckName, String objName) throws Exception {
      minioClient.removeObject(RemoveObjectArgs.builder()
              .bucket(buckName)
              .object(objName)
              .build());
  }
  ```