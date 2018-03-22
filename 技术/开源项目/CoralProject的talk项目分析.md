1. 四种用户角色：admin, moderator, staff和commenter (/talk/models/enum/user_roles.js目录下)

2. talk/locales目录是国际化目录，可以设置不同的语言

3. talk-plugin-auth插件下SignInContent和SignUpContent分别为登录和注册页面，signInContainer文件是登录注册容器文件，里面也要引入登录注册按钮跳转的方法，translations.yml文件是登录注册页面的国际化文件

4. talk/client/coral-embed-stream/src/actions/auth.js是客户端按钮跳转等方法的文件

5. talk/client/coral-embed-stream/src/constants/auth.js是客户端方法等用到的常量定义的文件

6. yarn命令不仅会安装talk项目package.json文件中的依赖，而且还会安装talk项目下enable的plugins下package.json文件中的依赖，而talk中enable的plugins在talk/plugins.default.json文件中指定

7. talk/config文件是配置各项配置参数等的文件，.env文件是设置用户参数和app参数的文件

8. talk/plugins目录下是各插件，talk-plugin-qq-auth是qq的三方登录的插件，router.js文件是路由文件，passport.js文件是passport的QQ Strategy定义文件