pom
```xml
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-mail</artifactId>
</dependency>

<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```
这里的密码password不是密码  

是授权码 一般都在邮箱中 设置-账户-SMTP服务

点击开启即可获取

```properties
spring.mail.host=smtp.exmail.qq.com
spring.mail.username=me@Therainisme.com
spring.mail.password=XXXXXXX
spring.mail.protocol=smtp
spring.mail.default-encoding=UTF-8
```

之后就可以直接发送邮件了  

Text里面可以是Html格式

```java
@SpringBootTest                                             
class ForecastSystemServerApplicationTests {                
    @Autowired                                              
    private JavaMailSender javaMailSender;                  
                                                            
    @Test                                                   
    public void testSend() throws MessagingException {      
        SimpleMailMessage message = new SimpleMailMessage();
        message.setFrom("me@Therainisme.com");               
        message.setTo("me@VisualDust.com");                 
        message.setSubject("This is a title");                     
        message.setText("This is a content");                       
        javaMailSender.send(message);                       
    }                                                       
}                                                               
```
