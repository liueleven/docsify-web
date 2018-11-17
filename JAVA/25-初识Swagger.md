# SpringBoot Swagger配置
> 自动生成RESTful风格api文档

### 导入maven依赖
```
<dependency>
	<groupId>io.springfox</groupId>
	<artifactId>springfox-swagger2</artifactId>
	<version>2.7.0</version>
</dependency>

<dependency>
	<groupId>io.springfox</groupId>
	<artifactId>springfox-swagger-ui</artifactId>
	<version>2.7.0</version>
</dependency>
```
### 启动类配置初始化
```
@SpringBootApplication
@EnableSwagger2
public class SmrdemoApplication {

	public static void main(String[] args) {
		SpringApplication.run(SmrdemoApplication.class, args);
	}


	@Bean
	public Docket createRestApi() {
		return new Docket(DocumentationType.SWAGGER_2)
				.apiInfo(apiInfo())
				.select()
				.paths(PathSelectors.any())
				.build();
	}

	private ApiInfo apiInfo() {
		return new ApiInfoBuilder()
				.title("Spring Boot + Swagger 测试")
				.description("测试swagger")
				.termsOfServiceUrl("http://47.100.223.103:8080")
				.version("1.0")
				.build();
	}
}
```
### 运行
浏览器输入：http://localhost:8080/swagger-ui.html 就可以看到api页面了

### 3.注解的使用

- 类注解 
> @Api(description = "用户管理") 

- 方法注解 
> @ApiOperation(value = "获取用户列表", notes ="获取所有用户信息")


- 属性注解 
    > @ApiModelProperty(hidden = true) private String password;隐藏类/方法/   
        
    > @ApiModelProperty(value = "用户名") private String username;
    
- 参数注解
> @ApiParam(value = "用户id", required = true) userid
    