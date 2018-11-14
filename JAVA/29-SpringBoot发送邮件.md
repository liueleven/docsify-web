# Springboot发送邮件

### 描述

发送发：163

接受方：163，阿里云邮箱

### 导入依赖

```
<!--邮件-->
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-mail</artifactId>
</dependency>
```
### 配置
```
# 邮箱
spring.mail.host=smtp.163.com
# 发送发的邮箱账号
spring.mail.username=liuziqing20180821@163.com
# 接收方
# liuziqing2018@163.com
# 这里是smtp自定义的授权码，而不是邮箱的登录密码，别搞错了！！！
spring.mail.password=Liuziqing1997
spring.mail.properties.mail.smtp.auth=true
spring.mail.properties.mail.smtp.starttls.required=true
spring.mail.properties.mail.smtp.starttls.enable=true
```
### 代码
```
@Service
public class MailService {

    @Autowired
    private UserMapper userMapper;

    /**
     * todo gvaud
     * 缓存，最大容量值是100，默认是15min过期时间，如果过期了，就把这个key删除掉
     */
    private final Cache<String,String> registerCache
            = CacheBuilder.newBuilder().maximumSize(100)
            .expireAfterAccess(15, TimeUnit.MINUTES).removalListener(new RemovalListener<String, String>() {
                @Override
                public void onRemoval(RemovalNotification<String, String> notification) {
                    userMapper.delete(notification.getKey());
                }
            }).build();

    @Autowired
    private JavaMailSender javaMailSender;

    @Value("${spring.mail.username}")
    private String from;

    @Value("${domain.port}")
    private int port;

    @Value("${domain.name}")
    private String domain;

    public void sendMail(String title,String url, String email) {
        SimpleMailMessage mailMessage = new SimpleMailMessage();
        mailMessage.setFrom(from);
        mailMessage.setTo(email);
        mailMessage.setText(url);
        javaMailSender.send(mailMessage);

    }

    /**
     * 异步发送邮件
     * @param email
     */
    @Async
    public void registerNotify(String email) {
        // 随机生成的key
        String randomKey = RandomStringUtils.randomAlphabetic(10);
        registerCache.put(randomKey,email);
        String url = "http://" + domain + port +  "/accounts/verify?key=" + randomKey;
        sendMail("好房网，账号激活",url,email);
    }
}

@SpringBootApplication
@EnableAsync    // 使用异步注解
public class Application {

	public static void main(String[] args) {
		SpringApplication.run(Application.class, args);
	}
}
```