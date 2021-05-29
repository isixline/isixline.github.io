## 阿里云人机校验（滑动校验）
[官方文档](https://help.aliyun.com/document_detail/121893.html?spm=5176.2020520162.0.dexternal.73285fb0WkhLIZ)

### 验证流程
- 客户端 -> 阿里云服务器：获取验签所需参数
- 客户端 -> 应用服务器：验证所需参数和业务代码
- 应用服务器 -> 阿里云服务器：验签

### 配置
#### 创建AccessKey
访问密钥AccessKey（AK）相当于登录密码，只是使用场景不同。AccessKey用于程序方式调用云服务API，而登录密码用于登录控制台。
AccessKey ID用于标识用户。
AccessKey Secret是用来验证用户的密钥。AccessKey Secret必须保密。

#### 添加captcha配置
- 配置名称（后期可修改）
- 高峰期OPS（貌似随便填）
- 业务类型 + 验证方式 + 使用场景 （组成一个配置，也就是最多可以有2 * 3 * 6个配置，配置成功后不可删除）
ps：配置不可删除，不使用，不付费。
ps：自2020年9月起，阿里云人机验证服务接入集成模块已升级为新版，需使用新版代码集成。


### 前端代码集成
#### 引入js包
```html
    <script src="https://g.alicdn.com/AWSC/AWSC/awsc.js"></script>
```

#### 标识dom
```html
    <div id="nc"></div>
```
#### 初始化滑块
```js
    // 实例化nc
    AWSC.use("nc", function (state, module) {
        // state标识状态，首次实例化会加载外部js，timeout为获取超时，loaded为已加载
        if (state === 'timeout') {
            console.log('nc timeout')
        }

        // 初始化
        const nc = module.init({
            // 应用类型标识,配置管理页签可找到对应的appkey值
            appkey: "CF_APP_1",
            //使用场景标识，配置管理页签可找到对应的scene值
            scene: "register",
            // 声明滑动验证需要渲染的目标ID。
            renderTo: "nc",
            //前端滑动验证通过时会触发该回调参数。会话ID（sessionId）、签名串（sig）、请求唯一标识（token）需随业务请求一同发送至端验签
            success: function (data) {
                window.console && console.log(data.sessionId)
                window.console && console.log(data.sig)
                window.console && console.log(data.token)
            },
            // 滑动验证失败时触发该回调参数。
            fail: function (failCode) {
                 window.console && console.log(failCode)
            },
            // 验证码加载出现异常时触发该回调参数。
            error: function (errorCode) {
                 window.console && console.log(errorCode)
            }
        });

```
#### 其他参数
- language，语言，默认cn
- width，滑动条宽度，默认300
- height，滑动条高度（亲测未生效），默认34
- fontSize，字体大小，默认12
- hideErrorCode，隐藏验证时的错误码，默认false
- upLang，自定义文案
- test，用于测试验证码的不同状态（module.TEST_PASS，module.TEST_BLOCK

#### nc对象
- reset
- hide
- show

### 后端代码集成
#### 引入依赖
```gradle
    compile 'com.aliyun:aliyun-java-sdk-core:4.5.1'
    compile 'com.aliyun:aliyun-java-sdk-afs:1.0.1'
```

```java
    public class CaptchaService {
    public static final String PRODUCT = "afs";
    public static final String END_POINT = "afs.aliyuncs.com";

    @Value("${aliyun.captcha.region-id}")
    private String regionId;

    @Value("${aliyun.captcha.access-key-id}")
    private String accessKeyId;

    @Value("${aliyun.captcha.access-key-secret}")
    private String accessKeySecret;

    @Value("${captcha.enabled}")
    private boolean captchaEnabled;

    private IAcsClient client;

    public IAcsClient getClient() {
        if (client == null) {
            IClientProfile profile =
                    DefaultProfile.getProfile(regionId, accessKeyId, accessKeySecret);
            client = new DefaultAcsClient(profile);
            DefaultProfile.addEndpoint(regionId, PRODUCT, END_POINT);
        }

        return client;
    }

    public void verify(HttpServletRequest httpRequest, CaptchaVerifyRequest captchaVerify) {
        if (!captchaEnabled) {
            return;
        }

        if (null == captchaVerify) {
            throw UnauthorizedException.unauthorized("captcha_verify_empty");
        }

        AuthenticateSigRequest request = new AuthenticateSigRequest();
        request.setSessionId(captchaVerify.getSessionId()); // 会话ID。必填参数，从前端获取，不可更改。
        request.setSig(captchaVerify.getSig()); // 签名串。必填参数，从前端获取，不可更改。
        request.setToken(captchaVerify.getToken()); // 请求唯一标识。必填参数，从前端获取，不可更改。
        request.setScene(captchaVerify.getScene()); // 场景标识。必填参数，从前端获取，不可更改。
        request.setAppKey(captchaVerify.getAppKey()); // 应用类型标识。必填参数，后端填写。
        request.setRemoteIp(IpUtils.getRealIpAddress(httpRequest)); // 客户端IP。必填参数，后端填写。


        try {
            // response的code枚举：100验签通过，900验签失败
            AuthenticateSigResponse response = getClient().getAcsResponse(request);

            if (!response.getCode().equals(100)) {
                log.info(response.getMsg());
            }
        } catch (ClientException e) {
            e.printStackTrace();
        }
    }

}

```