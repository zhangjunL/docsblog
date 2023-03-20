# OSS前端直传+后端签名

## 一、服务端签名后前端直传
   首先安装阿里云SDK Aliyun.OSS.SDK.NetCore

```cs
        public static string accessKeyId = "你的accessKeyId";
        public static string accessKeySecret = "你的accessKeySecret";
        public static string bucketName = "你的桶名称";
        public static string endpoint = "oss-cn-beijing.aliyuncs.com";
        public static int expireTime = 30;
        public Dictionary<string, string> GetPolicy(string fileName)
        {
            var dir = DateTime.Now.ToString("yyyyMMdd") + "/";
            // 构造OssClient实例。 endpoint 格式:https://oss-cn-beijing.aliyuncs.com
            var ossClient = new OssClient("https://" + endpoint, accessKeyId, accessKeySecret);
            var config = new PolicyConditions();
            config.AddConditionItem(PolicyConditions.CondContentLengthRange, 1, 1024L * 1024 * 1024 * 5);// 文件大小范围：单位byte
            config.AddConditionItem(MatchMode.StartWith, PolicyConditions.CondKey, dir);
            var expire = DateTimeOffset.Now.AddMinutes(30);// 过期时间
            // 生成 Policy，并进行 Base64 编码
            var policy = ossClient.GeneratePostPolicy(expire.LocalDateTime, config);
            var policyBase64 = Convert.ToBase64String(Encoding.UTF8.GetBytes(policy));

            // 计算签名
            var hmac = new HMACSHA1(Encoding.UTF8.GetBytes(accessKeySecret));
            var bytes = hmac.ComputeHash(Encoding.UTF8.GetBytes(policyBase64));
            var sign = Convert.ToBase64String(bytes);
            // 将签名和回调的内容，返回给前端
            var host = $"https://{bucketName}.{endpoint}";
            var key = $"{dir}{Guid.NewGuid()}/{fileName}";
            var fullUrl = $"https://{bucketName}.{endpoint}/{key}";
            var rt = new Dictionary<string, string>
            {
                { "OSSAccessKeyId",accessKeyId},
                { "Host",host },
                { "key",key},
                { "policy",policyBase64},
                { "Signature",sign},
                { "success_action_status","200"},
                { "fullUrl",fullUrl },
                {"expire",expire.ToString() }
            };

            return rt;
        }
```
前端首先访问后端获取签名,获取签名后使用FromData的形式上传文件
```js
async startUpload() {
      // 获取后端签名和上传地址
      const res = await axios.get("http://localhost:5152/api/OSS/GetPolicy", {
        params: {
          name: this.file.name
        }
      });
      var formData = new FormData();
      formData.append("name", this.file.name);
      formData.append("OSSAccessKeyId", res.data.OSSAccessKeyId);
      formData.append("key", res.data.key);
      formData.append("policy", res.data.policy);
      formData.append("signature", res.data.Signature);
      formData.append("success_action_status", res.data.success_action_status);
      formData.append("file", this.file);
      axios
        .post(res.data.Host, formData, {
          headers: {
            "Content-Type": "multipart/form-data"
          },
          withCredentials: false
        })
        .then(res => {
          console.log(res);
        });
    }
```

## 二、服务端STS签名前端分片上传+断点续传
当文件过大时，考虑使用分片上传和断点续传的方式来上传文件到oss，这时我们就不能直接使用accesskeyId和accessKeySecret的方式来在前端上传，以免暴露我们的密钥，当然也不能直接使用第一种的方式进行签名（或许可以，没有找到示例，也没有研究出来），所以我们采用STStoken的方式签名，然后在前端使用阿里云提供的SDK进行文件上传。

断点续传的思路是在每个分片上传的时候存储当前文件的上传进度，如果中间因为各种原因无法继续上传时，当用户重新上传同一个文件的时候，获取文件的上传进度，继续上传没有上传完的部分，而不是重新上传整个文件。为了确保断点续传前后上传的是同一个文件，我们使用md5作为存储进度的key值，如果是同一个文件，则续传，如果不是同一个文件，则从0开始上传。

首先登录阿里云开通sts账户和权限。

安装 aliyun-net-sdk-core和aliyun-net-sdk-sts sdk

```cs
public Dictionary<string, string> GetSTSToken()
        {
            //此处使用sts账户的id和secret
            var AccessKeyID = "***";
            var AccessKeySecret = "***";
            string bucketName = "***";
            // ststoken
            IClientProfile profile = DefaultProfile.GetProfile("oss-cn-beijing", AccessKeyID, AccessKeySecret);
            DefaultAcsClient client = new DefaultAcsClient(profile);
            var request = new AssumeRoleRequest();
            request.RoleArn = "***";
            request.RoleSessionName = "xxx";//这里的名字随便写
            request.DurationSeconds = 3600;//过期时间
            var response = client.GetAcsResponse(request);

            var result = new Dictionary<string, string>
            {
                {"AccessKeyId", response.Credentials.AccessKeyId},
                {"AccessKeySecret",response.Credentials.AccessKeySecret },
                {"SecurityToken",response.Credentials.SecurityToken },
                {"Expiration",response.Credentials.Expiration },
                {"BucketName",bucketName }
            };

            return result;
        }
```
签名完成后，安装阿里云oss sdk
```shell
npm install ali-oss;
npm install spark-md5;
```
```js 
<template>
  <div class="hello">
    <div>
      <input type="file" @change="fileChange" />
      <div>{{ progress }}</div>
    </div>
  </div>
</template>
#自行导入包，自行定义变量
async fileChange(e) {
      this.file = e.target.files[0];
      this.uploadFile(this.file);
    },
async uploadFile(file) {
      const objectKey = "xxx" + "/file/" + this.file.name;
      // 初始化 OSS 客户端 SDK
      await this.initOSSClient();
      this.resumeUpload(objectKey, file);
    }
# 首先初始化oss 对象
    async initOSSClient() {
      const res = await axios.get("http://localhost:5152/api/OSS/GetSTSToken");
      console.log(res);
      const {
        AccessKeyId,
        AccessKeySecret,
        SecurityToken,
        BucketName
      } = res.data;
      this.bucketName = BucketName;

      this.client = new OSS({
        region: "oss-cn-beijing",
        accessKeyId: AccessKeyId,
        accessKeySecret: AccessKeySecret,
        stsToken: SecurityToken,
        bucket: BucketName
      });
    },
    # 断点上传
    async resumeUpload(objectKey, file) {
      //使用SparkMd5计算文件的md5值
      let md5 =await this.calculateFileMD5(file);

      let checkpoint = JSON.parse(
        window.localStorage.getItem("checkpoint_" + md5)
      );
      var _this = this;
      // 重试五次。
      for (let i = 0; i < 5; i++) {
        try {
          const result = await this.client.multipartUpload(objectKey, file, {
            checkpoint,
            async progress(percentage, cpt) {
              checkpoint = cpt;
              _this.progress = parseInt(percentage * 100);
              // 将 checkpoint 保存到浏览器localstorage 中。
              window.localStorage.setItem(
                "checkpoint_" + md5,
                JSON.stringify(checkpoint)
              );
            }
          });
          // 删除本地保存的 checkpoint，如果此处不删除的话，上传成功后，用户无法再次上传同名文件
          window.localStorage.removeItem("checkpoint_" + md5);

          break; // 跳出当前循环。
        } catch (e) {
          console.log(e);
        }
      }
    },
    // 使用sparkMD5 计算文件md5
     calculateFileMD5(file, chunkSize = 2097152) {
      // chunkSize为分块大小，默认为2MB
      return new Promise((resolve, reject) => {
        const fileReader = new FileReader();
        let currentPosition = 0;
        const spark = new SparkMD5.ArrayBuffer();

        fileReader.onerror = function() {
          reject("文件读取失败！");
        };

        fileReader.onload = function() {
          spark.append(fileReader.result); // 将读取到的数据添加到MD5计算器中

          currentPosition += chunkSize;
          if (currentPosition < file.size) {
            // 文件还没读完，继续读取下一块
            loadNext();
          } else {
            // 文件读取完毕，计算MD5值并返回结果
            const hash = spark.end();
            resolve(hash);
          }
        };

        function loadNext() {
          const blob = file.slice(currentPosition, currentPosition + chunkSize);
          fileReader.readAsArrayBuffer(blob);
        }

        // 开始读取第一块
        loadNext();
      });
    }
```

如果想自己控制上传的各步骤可以使用initiateMultipartUpload  uploadPart completeMultipartUpload 等方法自行实现各步骤，大致思路就是先initiateMultipartUpload初始化一个分片上传，返回uploadid，然后将文件按一定的大小分片，之后循环上传每个分片，完成分片之后调用completeMultipartUpload方法合并文件,这种方式比较复杂。