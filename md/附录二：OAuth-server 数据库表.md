> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.kancloud.cn](https://www.kancloud.cn/hanxt/springsecurity/1836919)

> HttpBasic、formLogin验证模式，RBAC权限模型，JWT、OAuth2等核心技术。验证码、图片验证码、单点登录及社交媒体登录等功能全覆盖。

官方建表 SQL 连接：[https://github.com/spring-projects/spring-security-oauth/blob/master/spring-security-oauth2/src/test/resources/schema.sql](https://github.com/spring-projects/spring-security-oauth/blob/master/spring-security-oauth2/src/test/resources/schema.sql)

oauth_client_details
====================

在项目中, 主要操作 oauth_client_details 表的类是 JdbcClientDetailsService.java, 更多的细节请参考该类. 也可以根据实际的需要, 去扩展或修改该类的实现.

<table><thead><tr><th>字段名</th><th>字段说明</th></tr></thead><tbody><tr><td>client_id</td><td>主键, 必须唯一, 不能为空. 用于唯一标识每一个客户端 (client); 在注册时必须填写 (也可由服务端自动生成). 对于不同的 grant_type, 该字段都是必须的. 在实际应用中的另一个名称叫 appKey, 与 client_id 是同一个概念.</td></tr><tr><td>resource_ids</td><td>客户端所能访问的资源 id 集合, 多个资源时用逗号 (,) 分隔, 如: “unity-resource,mobile-resource”. 该字段的值必须来源于与 security.xml 中标签‹oauth2:resource-server 的属性 resource-id 值一致. 在 security.xml 配置有几个‹oauth2:resource-server 标签, 则该字段可以使用几个该值. 在实际应用中, 我们一般将资源进行分类, 并分别配置对应的‹oauth2:resource-server, 如订单资源配置一个‹oauth2:resource-server, 用户资源又配置一个‹oauth2:resource-server. 当注册客户端时, 根据实际需要可选择资源 id, 也可根据不同的注册流程, 赋予对应的资源 id.</td></tr><tr><td>client_secret</td><td>用于指定客户端 (client) 的访问密匙; 在注册时必须填写(也可由服务端自动生成). 对于不同的 grant_type, 该字段都是必须的. 在实际应用中的另一个名称叫 appSecret, 与 client_secret 是同一个概念.</td></tr><tr><td>scope</td><td>指定客户端申请的权限范围, 可选值包括 read,write,trust; 若有多个权限范围用逗号 (,) 分隔, 如: “read,write”. scope 的值与 security.xml 中配置的‹intercept-url 的 access 属性有关系. 如‹intercept-url 的配置为‹intercept-url pattern="/m/**" access=“ROLE_MOBILE,SCOPE_READ”/&gt;则说明访问该 URL 时的客户端必须有 read 权限范围. write 的配置值为 SCOPE_WRITE, trust 的配置值为 SCOPE_TRUST. 在实际应该中, 该值一般由服务端指定, 常用的值为 read,write.</td></tr><tr><td>authorized_grant_types</td><td>指定客户端支持的 grant_type, 可选值包括 authorization_code,password,refresh_token,implicit,client_credentials, 若支持多个 grant_type 用逗号 (,) 分隔, 如: “authorization_code,password”. 在实际应用中, 当注册时, 该字段是一般由服务器端指定的, 而不是由申请者去选择的, 最常用的 grant_type 组合有: “authorization_code,refresh_token”(针对通过浏览器访问的客户端); “password,refresh_token”(针对移动设备的客户端). implicit 与 client_credentials 在实际中很少使用.</td></tr><tr><td>web_server_redirect_uri</td><td>客户端的重定向 URI, 可为空, 当 grant_type 为 authorization_code 或 implicit 时, 在 Oauth 的流程中会使用并检查与注册时填写的 redirect_uri 是否一致. 下面分别说明: 当 grant_type=authorization_code 时, 第一步 从 spring-oauth-server 获取'code’时客户端发起请求时必须有 redirect_uri 参数, 该参数的值必须与 web_server_redirect_uri 的值一致. 第二步 用 ‘code’ 换取 ‘access_token’ 时客户也必须传递相同的 redirect_uri. 在实际应用中, web_server_redirect_uri 在注册时是必须填写的, 一般用来处理服务器返回的 code, 验证 state 是否合法与通过 code 去换取 access_token 值. 在 spring-oauth-client 项目中, 可具体参考 AuthorizationCodeController.java 中的 authorizationCodeCallback 方法. 当 grant_type=implicit 时通过 redirect_uri 的 hash 值来传递 access_token 值. 如:<a href="http://localhost:7777/spring-oauth-client/implicit#access_token=dc891f4a-ac88-4ba6-8224-a2497e013865&amp;token_type=bearer&amp;expires_in=43199%E7%84%B6%E5%90%8E%E5%AE%A2%E6%88%B7%E7%AB%AF%E9%80%9A%E8%BF%87JS%E7%AD%89%E4%BB%8Ehash%E5%80%BC%E4%B8%AD%E5%8F%96%E5%88%B0access_token%E5%80%BC" target="_blank">http://localhost:7777/spring-oauth-client/implicit#access_token=dc891f4a-ac88-4ba6-8224-a2497e013865&amp;token_type=bearer&amp;expires_in=43199 然后客户端通过 JS 等从 hash 值中取到 access_token 值</a>.</td></tr><tr><td>authorities</td><td>指定客户端所拥有的 Spring Security 的权限值, 可选, 若有多个权限值, 用逗号 (,) 分隔, 如: "ROLE_</td></tr><tr><td>access_token_validity</td><td>设定客户端的 access_token 的有效时间值 (单位: 秒), 可选, 若不设定值则使用默认的有效时间值 (60 * 60 * 12, 12 小时). 在服务端获取的 access_token JSON 数据中的 expires_in 字段的值即为当前 access_token 的有效时间值. 在项目中, 可具体参考 DefaultTokenServices.java 中属性 accessTokenValiditySeconds. 在实际应用中, 该值一般是由服务端处理的, 不需要客户端自定义. refresh_token_validity 设定客户端的 refresh_token 的有效时间值 (单位: 秒), 可选, 若不设定值则使用默认的有效时间值 (60 * 60 * 24 * 30, 30 天). 若客户端的 grant_type 不包括 refresh_token, 则不用关心该字段 在项目中, 可具体参考 DefaultTokenServices.java 中属性 refreshTokenValiditySeconds. 在实际应用中, 该值一般是由服务端处理的, 不需要客户端自定义.</td></tr><tr><td>additional_information</td><td>这是一个预留的字段, 在 Oauth 的流程中没有实际的使用, 可选, 但若设置值, 必须是 JSON 格式的数据, 如:{“country”:“CN”,“country_code”:“086”} 按照 spring-security-oauth 项目中对该字段的描述 Additional information for this client, not need by the vanilla OAuth protocol but might be useful, for example,for storing descriptive information. (详见 ClientDetails.java 的 getAdditionalInformation() 方法的注释) 在实际应用中, 可以用该字段来存储关于客户端的一些其他信息, 如客户端的国家, 地区, 注册时的 IP 地址等等. create_time 数据的创建时间, 精确到秒, 由数据库在插入数据时取当前系统时间自动生成 (扩展字段)</td></tr><tr><td>archived</td><td>用于标识客户端是否已存档 (即实现逻辑删除), 默认值为’0’(即未存档). 对该字段的具体使用请参考 CustomJdbcClientDetailsService.java, 在该类中, 扩展了在查询 client_details 的 SQL 加上 archived = 0 条件 (扩展字段)</td></tr><tr><td>trusted</td><td>设置客户端是否为受信任的, 默认为’0’(即不受信任的, 1 为受信任的). 该字段只适用于 grant_type="authorization_code" 的情况, 当用户登录成功后, 若该值为 0, 则会跳转到让用户 Approve 的页面让用户同意授权, 若该字段为 1, 则在登录后不需要再让用户 Approve 同意授权 (因为是受信任的). 对该字段的具体使用请参考 OauthUserApprovalHandler.java. (扩展字段)</td></tr><tr><td>autoapprove</td><td>设置用户是否自动 Approval 操作, 默认值为 ‘false’, 可选值包括 ‘true’,‘false’, ‘read’,‘write’. 该字段只适用于 grant_type="authorization_code" 的情况, 当用户登录成功后, 若该值为’true’或支持的 scope 值, 则会跳过用户 Approve 的页面, 直接授权. 该字段与 trusted 有类似的功能, 是 spring-security-oauth2 的 2.0 版本后添加的新属性. 在项目中, 主要操作 oauth_client_details 表的类是 JdbcClientDetailsService.java, 更多的细节请参考该类. 也可以根据实际的需要, 去扩展或修改该类的实现.</td></tr></tbody></table>

oauth_client_token
==================

该表用于在客户端系统中存储从服务端获取的 token 数据. 对 oauth_client_token 表的主要操作在 JdbcClientTokenServices.java 类中, 更多的细节请参考该类.

<table><thead><tr><th>字段名</th><th>字段说明</th></tr></thead><tbody><tr><td>create_time</td><td>数据的创建时间, 精确到秒, 由数据库在插入数据时取当前系统时间自动生成 (扩展字段)</td></tr><tr><td>token_id</td><td>从服务器端获取到的 access_token 的值.</td></tr><tr><td>token</td><td>这是一个二进制的字段, 存储的数据是 OAuth2AccessToken.java 对象序列化后的二进制数据.</td></tr><tr><td>authentication_id</td><td>该字段具有唯一性, 是根据当前的 username(如果有),client_id 与 scope 通过 MD5 加密生成的. 具体实现请参考 DefaultClientKeyGenerator.java 类.</td></tr><tr><td>user_name</td><td>登录时的用户名</td></tr><tr><td>client_id</td><td></td></tr></tbody></table>

oauth_access_token
==================

在项目中, 主要操作 oauth_access_token 表的对象是 JdbcTokenStore.java. 更多的细节请参考该类.

> 结合《5.6. 认证资源服务分离》去学习使用该表

<table><thead><tr><th>字段名</th><th>字段说明</th></tr></thead><tbody><tr><td>create_time</td><td>数据的创建时间, 精确到秒, 由数据库在插入数据时取当前系统时间自动生成 (扩展字段)</td></tr><tr><td>token_id</td><td>该字段的值是将 access_token 的值通过 MD5 加密后存储的.</td></tr><tr><td>token</td><td>存储将 OAuth2AccessToken.java 对象序列化后的二进制数据, 是真实的 AccessToken 的数据值.</td></tr><tr><td>authentication_id</td><td>该字段具有唯一性, 其值是根据当前的 username(如果有),client_id 与 scope 通过 MD5 加密生成的. 具体实现请参考 DefaultAuthenticationKeyGenerator.java 类.</td></tr><tr><td>user_name</td><td>登录时的用户名, 若客户端没有用户名 (如 grant_type=“client_credentials”), 则该值等于 client_id</td></tr><tr><td>client_id</td><td></td></tr><tr><td>authentication</td><td>存储将 OAuth2Authentication.java 对象序列化后的二进制数据.</td></tr><tr><td>refresh_token</td><td>该字段的值是将 refresh_token 的值通过 MD5 加密后存储的.</td></tr></tbody></table>

oauth_refresh_token
===================

> 结合《5.6. 认证资源服务分离》去学习使用该表

在项目中, 主要操作 oauth_refresh_token 表的对象是 JdbcTokenStore.java. (与操作 oauth_access_token 表的对象一样); 更多的细节请参考该类. 如果客户端的 grant_type 不支持 refresh_token, 则不会使用该表.

<table><thead><tr><th>字段名</th><th>字段说明</th></tr></thead><tbody><tr><td>create_time</td><td>数据的创建时间, 精确到秒, 由数据库在插入数据时取当前系统时间自动生成 (扩展字段)</td></tr><tr><td>token_id</td><td>该字段的值是将 refresh_token 的值通过 MD5 加密后存储的.</td></tr><tr><td>token</td><td>存储将 OAuth2RefreshToken.java 对象序列化后的二进制数据.</td></tr><tr><td>authentication</td><td>存储将 OAuth2Authentication.java 对象序列化后的二进制数据.</td></tr></tbody></table>

oauth_code
==========

在项目中, 主要操作 oauth_code 表的对象是 JdbcAuthorizationCodeServices.java. 更多的细节请参考该类. 只有当 grant_type 为 "authorization_code" 时, 该表中才会有数据产生; 其他的 grant_type 没有使用该表.

<table><thead><tr><th>字段名</th><th>字段说明</th></tr></thead><tbody><tr><td>create_time</td><td>数据的创建时间, 精确到秒, 由数据库在插入数据时取当前系统时间自动生成 (扩展字段)</td></tr><tr><td>code</td><td>存储服务端系统生成的 code 的值 (未加密).</td></tr><tr><td>authentication</td><td>存储将 AuthorizationRequestHolder.java 对象序列化后的二进制数据.</td></tr></tbody></table>