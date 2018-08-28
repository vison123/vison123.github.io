---
layout: post
title:  MultipartFile 上传文件
date:   2017-06-07 01:08:00 +0800
header-img: "img/post-bg-rwd.jpg"
author:     "Vison"
catalog: true
tags:
    - 上传下载
---

### 后端实现

SpringMVC 用的是 的MultipartFile来进行文件上传 所以我们首先要配置MultipartResolver:用于处理表单中的file

* defaultEncoding="UTF-8" 是请求的编码格式，默认为iso-8859-1
* maxUploadSize="5400000" 是上传文件的大小，单位为字节
* uploadTempDir="fileUpload/temp" 为上传文件的临时路径

```html
<bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
        <property name="defaultEncoding" value="utf-8" />
        <property name="maxUploadSize" value="10485760" />
        <property name="maxInMemorySize" value="4096" />
        <property name="resolveLazily" value="true" />
</bean>
```

后端controller实现，仅支持POST请求，并过滤header中的content-type=multipart/*
```java
@Controller
@RequestMapping("/Api")
public class ImportExcelController {
    @Autowired
    private DataImportService dataImportService;

    @RequestMapping(
            value = "/ImportExcel/ImportSingleExcel",
            method = {RequestMethod.POST},
            headers = ("content-type=multipart/*")
    )
    @ResponseBody
    public void importSingleExcel(@RequestParam MultipartFile file) {
        dataImportService.importSingleExcel("bankTransaction", "bankTransaction", file);
    }

    @RequestMapping(
            value = "/ImportExcel/importBatchExcel",
            method = {RequestMethod.POST}
    )
    @ResponseBody
    public void importBatchExcel(@RequestParam("file") MultipartFile file) {
        dataImportService.importSingleExcel("bankTransaction", "bankTransaction", file);
    }
}
```

### 前端测试

注意要在form标签中加上enctype="multipart/form-data"表示该表单是要处理文件的,这是最基本的东西
如果需要多选，只需多个type="file"的input即可

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
  <h2>文件上传实例</h2>
  <form  method="post" action="http://192.168.64.234:9081/core/Api/ImportExcel/ImportSingleExcel" enctype="multipart/form-data">
    选择文件:<input type="file" name="file">
    <input type="submit" value="提交">
  </form>
</body>
</html>
```

### Ant Design Upload

```javascript
  <Upload
    action={baseUrl + api.groupmaintain.importExcel}
    onChange={this._uploadExcel}
    accept='multipart/form-data'
    name='excelFile'
    data={{
      smsGroupId: smsGroupId.smsGroupId,
      accessToken: userInfo && userInfo.accessToken
    }}
  >
    <Button
      type='primary'
      size='large'
    >
      导入excel
    </Button>
  </Upload>
```

### Element ui Upload

```javascript
  <el-upload
      v-if="defaultIndex === 0"
      style="display: inline-block;"
      :action="baseURL + '/car_alarm/import'"
      :on-success="handleSuccess"
      :on-error= "handleError"
      :on-progress="handleProgress"
      :with-credentials="true"
      :limit="1"
      :before-upload="handleBeforeUpload"
      ref="uploadFileMobile"
      accept="application/vnd.ms-excel,application/vnd.openxmlformats-officedocument.spreadsheetml.sheet"
      :file-list="fileList"
      :show-file-list="false"
    >
      <span class="reset-btn" style="width: 120px;" >{{ this.lang.IMPORT_CAR}}</span>
    </el-upload>
```
