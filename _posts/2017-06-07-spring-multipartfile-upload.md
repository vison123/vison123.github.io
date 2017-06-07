---
layout: post
title:  SpringMVC 上传文件
date:   2017-04-26 01:08:00 +0800
categories: Java
tag: 上传下载
---

＃后端
{% highlight bash %}
<bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
        <property name="defaultEncoding" value="utf-8" />
        <property name="maxUploadSize" value="10485760" />
        <property name="maxInMemorySize" value="4096" />
        <property name="resolveLazily" value="true" />
</bean>
{% endhighlight %}


{% highlight bash %}
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
    public void importBatchExcel(@RequestParam("file") MultipartFile file, @RequestBody ImportExcelBean requestBean) {
        dataImportService.importSingleExcel("bankTransaction", "bankTransaction", file);
    }
}
{% endhighlight %}

＃前端

{% highlight bash %}
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
{% endhighlight %}

