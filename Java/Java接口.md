### Java登录注册接口

###### 第一步：创建 entity 实体类。

根据业务逻辑创建所需要的对象和属性。
		新建 entity 文件包

- 在此文件包下面新建 User 类，来用抽象用户信息

```java
package com.dingding.entity;
		//实体类    实现封装
public class User {
    public String username;
    public int password;
    public String phone;

    public User(){}
    public User(String username, int password, String phone) {
        this.username = username;
        this.password = password;
        this.phone = phone;
    }

    public String getName(){
        return  username;
    }
    public void setName(String username){
        this.username = username;
    }
    public int getPassword(){
        return  password;
    }
    public void setPassword(int password){
        this.password = password;
    }
    public String getPhone(){
        return phone;
    }
    public void setPhone(String phone){
        this.phone= phone;
    }
}
```

- **返回给前端的数据，需要有Response类/ResponsePages类封装，也就是要带有返回码，返回消息和返回体**。

  在此文件包下面新建 Response 类，用来返回接口调用信息描述（成功与否）。

```java
package com.dingding.entity;

/**
 * Created by xpwu on 2019/6/28.
 */
public class Response {
    String msg;
    int code;
    Boolean isSuc = true;
    public Response(){}
    public Response(Boolean isSuc,String msg, int code) {
        this.msg = msg;
        this.code = code;
        this.isSuc = isSuc;
    }
    public Boolean getIsSuc() {
        return isSuc;
    }
    public void setIsSuc(Boolean isSuc) {
        this.isSuc = isSuc;
    }
    public String getMsg() {
        return msg;
    }
    public void setMsg(String msg) {
        this.msg = msg;
    }
    public int getCode() {
        return code;
    }
    public void setCode(int code) {
        this.code = code;
    }
}

```

所以 entity 下面有两个实体类，User 类和 Response 类。

###### 第二步：创建 service 服务类。

根据业务关系，编写相关业务逻辑。

提供一个注册服务与登录服务。分别为增加用户信息与根据用户名查询用户信息。

- 新建 service 文件包，在此文件包下新建 UserService 类编写接口

```java
package com.dingding.service;
import com.dingding.entity.User;
import java.util.List;
		//接口   只写方法体

public interface UserService{
    int addUser(String username,int password,String phone);//用于注册时，添加用户
    List<User> queryByUsername(String username);//用于登陆时，验证用户
}
```

- 在 service 文件包下面新建 impl 文件包，在 impl 文件包下新建 UserServiceImpl 类来实现 UserService 接口。

```java
package com.dingding.service.impl;
import com.dingding.entity.User;
import com.dingding.mapper.UserMapper;
import com.dingding.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
		//实现接口
@Service
public class UserServiceImpl implements UserService {
    @Autowired
    UserMapper userMapper;
    public int addUser(String username,int password,String phone){
        User user = new User(username,password,phone);
        int count = userMapper.addUser(user);
        return count;
    }
    public List <User> queryByUsername(String username){
        List <User> userList = userMapper.queryByUsername(username);
        return userList;
    }
}
```

###### 3、创建 mapper 类

根据 service 服务类创建对应的 mapper 类。

新建 mapper 文件包，在此文件包下新建 UserMapper 类。

```java
package com.dingding.mapper;
import com.dingding.entity.User;
import org.springframework.stereotype.Repository;
import java.util.List;

@Repository
public interface UserMapper{
   int addUser(User user);
   List<User> queryByUsername(String username);
}

```

###### 4、创建 sql 语句

在 resources 文件夹下，新建 mapper 文件夹，在此文件夹下新建相关的 xml 文件

```xml
<mapper namespace="com.dingding.mapper.UserMapper">
    <resultMap id="BaseResultMap" type="com.dingding.entity.User">
        <result column="username" jdbcType="VARCHAR" property="username" />
        <result column="password" jdbcType="INTEGER" property="password" />
        <result column="phone" jdbcType="VARCHAR" property="phone" />
    </resultMap>
    <insert id="addUser" parameterType="com.dingding.entity.User">
        INSERT INTO `user` VALUES(#{username},#{password},#{phone})
    </insert>
    <select id="queryByUsername" resultType="com.dingding.entity.User">
        SELECT * FROM `user` WHERE username = #{username}
    </select >
</mapper>

```

###### 5、调用服务，处理业务逻辑

新建 controller 文件包，在此文件包下新建 UserController 类，在此类中调用调用相关服务并处理逻辑。

注册接口的逻辑处理
			判断用户名、密码、手机号是否为空，如果为空，注册失败；
			如果都不为空，根据用户名查询是否有重复用户名，如果有，注册失败；
			如果没有重复用户名，注册成功。
		登录接口的逻辑处理
			判断用户名、密码是否为空；
			如果都不为空，根据用户名查询数据库对应的密码；
			如果密码有误，登陆失败；
			如果密码正确，登陆成功。

```java
package com.dingding.controller;
import com.dingding.entity.Response;
import com.dingding.entity.User;
import com.dingding.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RestController;
import java.util.List;
import java.util.Map;

@RestController
public class UserController {
    @Autowired
   private UserService service;
   @RequestMapping(value = "/register",method = RequestMethod.POST)
   public Response register(@RequestBody Map<String, String> person){
       String username = person.get("username");
       int password = Integer.parseInt(person.get("password"));
       String phone = person.get("phone");
       //1.判断用户名、密码、手机号是否为空
       if(username != null && password != 0 && phone != null){
           List<User> users =  service.queryByUsername(username);
            //2.判断是否有重复用户名
           if(users!=null && users.size()>0){
               return new Response(true,"注册失败，用户名重复,请更换",-1);
           }else {
               int count = service.addUser(username,password,phone);
               if(count>0){
                  //3.没有重复用户名，注册成功
                   return new Response(true,"注册成功",1);
               }else {
                   return new Response(true,"注册失败",-1);
               }
           }
       }else{
           return new Response(true,"注册失败，请检查用户名、密码、手机号是否为空",-1);
       }
   }
    @RequestMapping(value = "/login",method = RequestMethod.POST)
    public Response login(@RequestBody Map<String, String> person){
        String username = person.get("username");
        int password =Integer.parseInt(person.get("password"));      
        //1. 判断用户名、密码是否为空
        if(username != null && password != 0 ){
         List<User> users =  service.queryByUsername(username);
         //2. 判断用户名是否存在
            if(users!=null && users.size()>0){
                User user = users.get(0);
                  //3. 判断密码是否正确
                if(password == user.getPassword()){
                   //4. 密码正确，登陆成功
                    return new Response(true,"登陆成功",1);
                }else {
                    return new Response(false,"登陆失败，密码错误",-1);
                }
            }else {
                return new Response(true,"登陆失败，用户名不存在",-1);
            }
        }else {
            return new Response(true,"登陆失败，请检查用户名、密码是否为空",-1);
        }
    }
}

```



#### 总结

1. 根据业务逻辑抽象出需要的对象。

   ```
   就像你要喝茶，首先你要思考一下你需要准备哪些东西，比如你需要准备热水、杯子和茶叶，然后记录下来。
   ```

2. 根据业务关系写实现步骤。

   ```
   知道了需要哪些东西之后，就可以开始规划实现步骤，比如你需要用水和杯子泡茶叶。
   ```

3. 根据逻辑关系创建连接。

   ```
   准备工作都做好了，这时候你需要创建你的物品清单凭证（为了能拿到物品）。
   ```

4. 连接数据库，写 sql 语句。

   ```
   根据凭证，到仓库里面拿东西（水、杯子、茶叶）。
   ```

5. 调用服务类。

   ```
   可以开始喝水了。
   ```

   