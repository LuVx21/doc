<details>
<summary>点击展开目录</summary>
<!-- TOC -->

- [文件上传方式](#文件上传方式)
- [文件读写方式](#文件读写方式)

<!-- /TOC -->
</details>

## 文件上传方式

文件上传的实现方式:



Spring
```Java
public RestResponseBo upload(HttpServletRequest request, @RequestParam("file") MultipartFile[] multipartFiles) {
}
public R upload(@RequestParam("file") MultipartFile multipartFile) {
    String fname = multipartFile.getOriginalFilename();
    File file = new File(CLASSPATH + fkey);
    FileCopyUtils.copy(multipartFile.getInputStream(), new FileOutputStream(file));
}
```

reseteasy
```Java
public String fileUpload(MultipartFormDataInput input) {
    Map<String, InputPart> formDataMap = input.getFormData();
    String fileName = "";
    String type = "";
    InputPart fileInputPart = null;
    File file = null;
    String fullName = "";

    try {
        type = formDataMap.get("type").getBodyAsString();
        fileName = formDataMap.get("filename").getBodyAsString();
        fileInputPart = formDataMap.get("file");

        String suffix = FILE_TYPE_SQL.equals(type) ? FILE_SUFFIX_SQL : FILE_SUFFIX_ZIP;
        fullName = fileName + suffix;

        InputStream inputStream = fileInputPart.getBody(InputStream.class, null);
        byte[] bytes = IOUtils.toByteArray(inputStream);
        file = new File(UPLOADED_FILE_PATH + fullName);
        FileUtils.writeByteArrayToFile(file, bytes);

        return interfaceDataBloodFacade.fileUpload(file, type);
    } catch (Exception e) {
        return CommonResp.getCommonFailResp();
    }
}
```


## 文件读写方式


https://blog.csdn.net/Mingyueyixi/article/details/78034054

java文件读写的工具类


