# Spring Boot | Upload File

这是一个简单的API

当你向路径/uploadFile上传一个文件成功后，它会返回一个文件地址

### FileController

用MultipartFile类来接收文件，然后将它转发到Service中

```java
@Controller
public class FileController {
    @Autowired
    private FileService fileService;

    @ResponseBody
    @RequestMapping(value = "/uploadFile", method = RequestMethod.POST)
    public String uploadFile(@RequestParam(name = "file") MultipartFile file) {
        return fileService.uploadFile(file);
    }
}
```

### FileService

```java
@Service
public class FileService {
    public String uploadFile(MultipartFile file) {
        String fileName = file.getOriginalFilename(); // 获取文件名
        if (fileName == null) {
            return "file is empty";
        }
        String suffixName = fileName.substring(fileName.lastIndexOf(".")); // 获取文件后缀名
        String filePath = "D:/files/img/"; // 储存在服务器上的地址，Linux和Windosw的路径不太一样，这里得注意
        fileName = UUID.randomUUID() + suffixName; // 随机给予一个文件名.后缀
        File theFile = new File(filePath + fileName); 
        if (!theFile.getParentFile().exists())
            theFile.getParentFile().mkdirs();
        try {
            file.transferTo(theFile); // 将用户传输的文件数据转到新建的文件上
        } catch (IOException e) {
            e.printStackTrace();
        }
        return "files/img/" + fileName; // 返回拼接的地址
    }
}
```

### MyWebMvcConfigurer

这里是配置本地资源映射路径

例如当向服务器请求`localhost:1225/files/img/**`时

服务器就会找到本地文件资源对应的路径下的文件并返回它

```java
@Configuration
public class MyWebMvcConfigurer implements WebMvcConfigurer {
    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("/files/img/**").addResourceLocations("file:D:/files/img/");
    }
}
```

现在向接口发送一个文件

![image-20200729130631105](UploadFile.assets/image-20200729130631105.png)



通过`http://localhost:1225/files/img/9fd9efdc-648d-460a-9257-8f76164862eb.jpg`即可访问该文件