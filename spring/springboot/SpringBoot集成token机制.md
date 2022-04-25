# SpringBoot集成token机制

#### 前言

使用JWT，JWT全写JSON Web Token, 是开放的行业标准RFC7591，用来实现端到端安全验证。JWT加密JSON，保存在客户端，不需要在服务端保存会话信息。可以应用在前后端分离的用户验证上，后端对前端输入的用户信息进行加密产生一个令牌字符串， 前端再次请求时附加此字符串，后端再使用算法解密。

#### 1. 导入java-jwt包（Maven）

```xml
<dependency>
    <groupId>com.auth0</groupId>
    <artifactId>java-jwt</artifactId>
    <version>3.8.2</version>
</dependency>
```

参考官方说明：https://github.com/auth0/java-jwt

#### 2. 定义token管理类

```java
import com.auth0.jwt.JWT;
import com.auth0.jwt.JWTVerifier;
import com.auth0.jwt.algorithms.Algorithm;
import com.auth0.jwt.exceptions.JWTVerificationException;
import com.lemon.inventory.bean.sys.UserDTO;

import java.util.Date;
import java.util.HashMap;
import java.util.Map;

public class TokenUtils {
    // 设置过期时间
    private static final long EXPIRE_DATE = 30 * 60 * 1000;
    // token秘钥
    private static final String TOKEN_SECRET = "popo12333Dexter9883332";

    /**
     * 生成token
     * @param user 登陆用户信息
     * @return 生成的token
     */
    public static String generateToken(UserDTO user){

        String token;
        try {
            // token过期时间
            Date date = new Date(System.currentTimeMillis() + EXPIRE_DATE);
            // 秘钥及加密算法
            Algorithm algorithm = Algorithm.HMAC256(TOKEN_SECRET);
            // JWT的头部承载两部分信息：token类型和采用的加密算法
            Map<String,Object> header = new HashMap<>();
            header.put("typ","JWT");
            header.put("alg","HS256");

            // 生成token
            token = JWT.create()
                    .withHeader(header)
                    .withClaim("id", user.getId()) // 把用户id放在Payload Claims里面
                    .withExpiresAt(date)
                    .sign(algorithm);
        }catch (Exception e){
            e.printStackTrace();
            return  null;
        }
        return token;
    }

    /**
     * 验证token是否有效
     * @param token token值
     * @return 有效返回true，否则false
     */
    public static boolean verify(String token){
        try {
            Algorithm algorithm = Algorithm.HMAC256(TOKEN_SECRET);
            JWTVerifier verifier = JWT.require(algorithm).build();
            verifier.verify(token);

            // verifier.verify(), token无效或者过期都会抛出JWTVerificationException
            return true;
        } catch (JWTVerificationException e) {
//            e.printStackTrace();
            return false;
        }
    }
}
```



#### 3. 实现拦截器

```java
import com.lemon.inventory.util.sys.TokenUtils;
import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;

public class AuthenticationInterceptor implements HandlerInterceptor {

    private final String OPTIONS = "OPTIONS";

    @Override
    public boolean preHandle(HttpServletRequest httpServletRequest, HttpServletResponse response, Object object) throws Exception {

        // 过滤掉OPTIONS请求（跨域预检）
        if(OPTIONS.equalsIgnoreCase(httpServletRequest.getMethod())) {
            return true;
        }

        // 从 http 请求头中取出 token
        String token = httpServletRequest.getHeader("token");

        if (token == null) {
            returnJson(response, "无效token，请重新登录!");
        }

        // 判断token是否有效
        if(!TokenUtils.verify(token)) {
            returnJson(response, "无效token，请重新登录!");
            return false;
        }

        return true;
    }


    /**
     * 封装一个json格式返回数据到前端
     */
    private void returnJson(HttpServletResponse response, String msg) throws Exception{
        response.setCharacterEncoding("UTF-8");
        response.setContentType("application/json");
        // 无效状态的token，返回状态为500
        response.setStatus(500);

        try (PrintWriter writer = response.getWriter()) {
            String result = "{\"status\":500,\"msg\":\"" + msg + "\"}";

            writer.print(result);

        } catch (IOException e) {
            throw new Exception("response error");
        }
    }

    @Override
    public void postHandle(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, ModelAndView modelAndView) throws Exception {

    }
    @Override
    public void afterCompletion(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, Exception e) throws Exception {

    }
}
```



#### 4. 配置javaConfig，设置哪些路由需要拦截

```java
import com.lemon.inventory.Auth.AuthenticationInterceptor;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class InterceptorConfig implements WebMvcConfigurer {
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(authenticationInterceptor())
                .addPathPatterns("/**")
                .excludePathPatterns("/login");
    }

    @Bean
    public AuthenticationInterceptor authenticationInterceptor() {
        return new AuthenticationInterceptor();// 自己写的拦截器
    }
}
```

