> gRPC后台接口开发

1. 添加pom.xml配置并更新maven配置

   ```xml
   <properties>
           <maven.compiler.source>15</maven.compiler.source>
           <maven.compiler.target>15</maven.compiler.target>
           <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
       </properties>
   
       <dependencies>
           <!-- protobuf Java runtime -->
           <dependency>
               <groupId>com.google.protobuf</groupId>
               <artifactId>protobuf-java</artifactId>
               <version>3.15.5</version>
           </dependency>
           <dependency> <!-- necessary for Java 9+ -->
               <groupId>org.apache.tomcat</groupId>
               <artifactId>annotations-api</artifactId>
               <version>6.0.53</version>
               <scope>provided</scope>
           </dependency>
           <!-- gRPC Java runtime -->
           <dependency>
               <groupId>io.grpc</groupId>
               <artifactId>grpc-protobuf</artifactId>
               <version>1.36.0</version>
           </dependency>
           <dependency>
               <groupId>io.grpc</groupId>
               <artifactId>grpc-stub</artifactId>
               <version>1.36.0</version>
           </dependency>
           <dependency>
               <groupId>io.grpc</groupId>
               <artifactId>grpc-netty-shaded</artifactId>
               <version>1.36.0</version>
           </dependency>
           <!-- yaml parser -->
           <dependency>
               <groupId>com.esotericsoftware.yamlbeans</groupId>
               <artifactId>yamlbeans</artifactId>
               <version>1.15</version>
           </dependency>
           <!-- Json -->
           <dependency>
               <groupId>com.alibaba</groupId>
               <artifactId>fastjson</artifactId>
               <version>1.2.75</version>
           </dependency>
           <!-- logger -->
           <dependency>
               <groupId>org.apache.logging.log4j</groupId>
               <artifactId>log4j-slf4j-impl</artifactId>
               <version>2.14.1</version>
           </dependency>
           <!-- database drivers -->
           <dependency>
               <groupId>org.postgresql</groupId>
               <artifactId>postgresql</artifactId>
               <version>42.2.19</version>
           </dependency>
           <!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
           <dependency>
               <groupId>mysql</groupId>
               <artifactId>mysql-connector-java</artifactId>
               <version>8.0.19</version>
           </dependency>
           <!-- unit test -->
           <dependency>
               <groupId>org.junit.jupiter</groupId>
               <artifactId>junit-jupiter</artifactId>
               <version>5.8.0-M1</version>
               <scope>test</scope>
           </dependency>
       </dependencies>
   
       <build>
           <extensions>
               <extension>
                   <groupId>kr.motd.maven</groupId>
                   <artifactId>os-maven-plugin</artifactId>
                   <version>1.7.0</version>
               </extension>
           </extensions>
           <plugins>
               <plugin>
                   <groupId>org.xolstice.maven.plugins</groupId>
                   <artifactId>protobuf-maven-plugin</artifactId>
                   <version>0.6.1</version>
                   <configuration>
                       <protocArtifact>com.google.protobuf:protoc:3.15.5:exe:${os.detected.classifier}</protocArtifact>
                       <pluginId>grpc-java</pluginId>
                       <pluginArtifact>io.grpc:protoc-gen-grpc-java:1.36.0:exe:${os.detected.classifier}</pluginArtifact>
                       <includes>
                           <include>health/*.proto</include>
                           <include>sessions/*.proto</include>
                       </includes>
                   </configuration>
                   <executions>
                       <execution>
                           <goals>
                               <goal>compile</goal>
                               <goal>compile-custom</goal>
                           </goals>
                       </execution>
                   </executions>
               </plugin>
               <plugin>
                   <groupId>org.apache.maven.plugins</groupId>
                   <artifactId>maven-surefire-plugin</artifactId>
                   <version>3.0.0-M5</version>
               </plugin>
               <plugin>
                   <groupId>org.apache.maven.plugins</groupId>
                   <artifactId>maven-shade-plugin</artifactId>
                   <version>3.2.4</version>
                   <executions>
                       <execution>
                           <phase>package</phase>
                           <goals>
                               <goal>shade</goal>
                           </goals>
                           <configuration>
                               <transformers>
                                   <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                                       <mainClass>com.smart3dmap.service.Runner</mainClass>
                                       <manifestEntries>
                                           <Multi-Release>true</Multi-Release>
                                       </manifestEntries>
                                   </transformer>
                               </transformers>
                           </configuration>
                       </execution>
                   </executions>
               </plugin>
           </plugins>
       </build>
   
   ```

   

2. 定义proto文件

   ```protobuf
   syntax = "proto3";
   
   package smart3dmap.v1;
   
   import "google/protobuf/timestamp.proto";
   import "google/protobuf/wrappers.proto";
   import "common/pagination.proto";
   
   option java_multiple_files = true;
   option java_package = "com.smart3dmap.userManager";
   option java_outer_classname = "UserManagerProto";
   
   service UserManager {
     // 生成一张票据。
     rpc NewTicket(NewTicketRequest) returns (Ticket);
     // 验证一张票据：（1）检查票据是否在有效期内，（2）票据是否真的由本服务器签发。
     rpc VerifyTicket(Ticket) returns (google.protobuf.BoolValue);
   
     // 邀请用户注册。新邀请的用户处于未激活状态。激活后的用户账号才可使用。
     rpc InviteUser(InviteUserRequest) returns (UserInvitation);
     // 根据Token查询邀请用户的记录。
     rpc GetUserInvitation(GetUserInvitationRequest) returns (UserInvitation);
     // 激活用户账号。
     rpc ActivateUser(ActiveUserRequest) returns (User);
   
     // 依据ID或者Email查询用户。
     rpc GetUser(GetUserRequest) returns (User);
     // 分页查询用户
     rpc ListUsers(ListUsersRequest) returns (ListUsersResponse);
     //修改用户信息，账号状态等
     rpc UpdateUser(UpdateUserRequest) returns (google.protobuf.BoolValue);
     // 重置用户密码
     rpc ResetUserPassword(ResetUserPasswordRequest) returns (google.protobuf.BoolValue);
     // 根据ID删除用户
     rpc DeleteUser(DeleteUserRequest) returns (google.protobuf.BoolValue);
     // 检查用户是否为管理员。
     rpc IsAdminUser(IsAdminUserRequest) returns (google.protobuf.BoolValue);
   
     //添加部门
     rpc AddTeam(Team) returns (google.protobuf.BoolValue);
     //修改部门
     rpc UpdateTeam(Team) returns (google.protobuf.BoolValue);
     //根据id删除
     rpc DeleteTeam(DeleteTeamRequest) returns (google.protobuf.BoolValue);
     //查询所有
     rpc ListTeams(ListTeamsRequest) returns (ListTeamsResponse);
     //查询单个
     rpc GetTeam(GetTeamRequest) returns (Team);
   
     //查询小组内成员
     rpc GetTeamUser(GetTeamUserRequest) returns (stream User);
     //添加小组成员
     rpc AddTeamUser(TeamUser) returns (google.protobuf.BoolValue);
     //删除小组内成员
     rpc DeleteTeamUser(TeamUser) returns (google.protobuf.BoolValue);
   }
   
   //小组内成员
   message TeamUser {
     int32 teamId = 1;
     int32 userId = 2;
   }
   
   //用户统一通用参数
   message User {
     int32 id = 1;
     string name = 2;
     string email = 3;
     string phone = 4;
     string password = 5;
     enum State {
       UNDEFINED = 0;
       // 未激活。
       DEACTIVATED = 1;
       // 已激活，可正常使用。
       ACTIVATED = 2;
       // 已被禁用。
       DISABLED = 3;
     }
     State state = 6;
   }
   
   message Team {
     int32 id = 1;
     string name = 2;
   }
   
   message Ticket {
     string value = 1;
   }
   
   message UserInvitation {
     // 邀请的唯一标识符。
     string token = 1;
     // 被邀请的用户的信息。
     User user = 2;
     google.protobuf.Timestamp createdAt = 3;
   }
   
   message InviteUserRequest {
     // 名字
     string name = 1;
     // Email地址。拟作为用户的登录账号名，不可与已有记录重复。
     string email = 2;
     // 电话。可选。
     string phone = 3;
   }
   
   message ActiveUserRequest {
     // 用户的ID。
     int32 user_id = 1;
     // 用户拟设置的密码。
     string password = 2;
     // 激活用户账号所依赖的Token。
     string invitation_token = 3;
   }
   
   // 分页查询用户列表的参数。
   message ListUsersRequest {
     // 分页参数。
     Pagination pagination = 1;
   }
   
   message ListTeamsRequest {
     Pagination pagination = 1;
   }
   
   message NewTicketRequest {
     // 以秒为单位的有效期。暂不考虑允许请求者指定有效期。
     // int32 ttl = 1;
   }
   
   message GetUserInvitationRequest {
     // 标识邀请的Token。
     string token = 1;
   }
   
   message GetUserRequest {
     // 用户ID。
     optional int32 id = 1;
     // 用户Email。
     optional string email = 2;
   }
   
   message UpdateUserRequest {
     int32 id = 1;
     optional string name = 2;
     optional string phone = 3;
     optional User.State state = 4;
   }
   
   message ResetUserPasswordRequest {
     int32 id = 1;
     string old_password = 2;
     string new_password = 3;
   }
   
   message DeleteUserRequest {
     int32 id = 1;
   }
   
   message DeleteTeamRequest {
     int32 id = 1;
   }
   
   message GetTeamRequest {
     int32 id = 1;
   }
   
   message GetTeamUserRequest {
     int32 team_id = 1;
   }
   
   message IsAdminUserRequest {
     int32 user_id = 1;
   }
   
   message ListUsersResponse{
       repeated User users = 1;
       int32 user_count = 2;
   }
   
   message ListTeamsResponse{
       repeated Team teams = 1;
       int32 team_count = 2;
   }
   
   ```

   

3. 使用mvn compile进行proto文件编译

   1. 首先需要确保已经配置好项目的maven环境
   2. 其中如果使用maven编译proto文件，那么需要默认将proto文件放在 src/main/proto/ 路径下。
   3. 编译后生成的java文件在 target/generated-sources/ 路径下。
   4. 将 java 文件拷贝到 src/main/java/ 路径下。

   

4. 在代码编写过程中继承proto文件中编译好的接口时，若发现找不到，要将target/generated-sources/protobuf 路径下grpc-java和Java文件夹设置为Generated sorces Root。



> 附：
>
> 1. 由于各种限制，我们可能无法在单个请求中发送大文件。因此，我们需要将它们作为小块异步发送。在这种情况下，我们最终会一次又一次地发送上述请求类型。这将使我们一次又一次地发送不需要的元数据。这就是 protobuf [oneof](https://developers.google.com/protocol-buffers/docs/proto3#oneof)可以提供帮助的地方。

##### proto文件定义

- repeated：前置repeated关键词，声明该字段为**数组**类型。

- proto3不支持proto2中的required和optional关键字。

##### grpc测试问题

1. Error: 13 INTERNAL: Received RST_STREAM with error code 2

   可能出现的原因是输出多条数据，但是用的是simple rpc，改为返回结果为  stream就可以了