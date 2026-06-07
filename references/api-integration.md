# 第三方API集成模式

基于数字安徽（金蝶+招采+SSO）、徽商（银行接口+Odoo）、安徽建工（群杰+浪潮+正孚）、合规院（金蝶云）、安徽国资（用友YONBIP+友空间）等项目的实战总结。

## 通用HTTP客户端封装

### 模式1：OkHttp（推荐，V10.0SP1+）
```java
public class HttpClientUtil {
    private static final OkHttpClient client = new OkHttpClient.Builder()
        .connectTimeout(30, TimeUnit.SECONDS)
        .readTimeout(30, TimeUnit.SECONDS)
        .build();

    public static String postJson(String url, String json, Map<String, String> headers) {
        RequestBody body = RequestBody.create(json, MediaType.parse("application/json"));
        Request.Builder builder = new Request.Builder().url(url).post(body);
        if (headers != null) headers.forEach(builder::addHeader);
        try (Response response = client.newCall(builder.build()).execute()) {
            return response.body().string();
        }
    }
}
```

### 模式2：Apache HttpClient（兼容V8.x/V9.0SP1）
```java
public class HttpKit {
    private static final PoolingHttpClientConnectionManager cm =
        new PoolingHttpClientConnectionManager();
    static { cm.setMaxTotal(200); cm.setDefaultMaxPerRoute(20); }

    public static String post(String url, Map<String, String> headers, String body) {
        CloseableHttpClient client = HttpClients.custom()
            .setConnectionManager(cm).build();
        // ...execute and return
    }

    public static String postJson(String url, String json) {
        // POST application/json
    }
}
```

### 模式3：HttpURLConnection（轻量兼容）
```java
public class HttpKit {
    private static final int CONNECT_TIMEOUT = 10000;
    private static final int READ_TIMEOUT = 10000;

    public static InputStream post(String urlStr, String postData) {
        URL url = new URL(urlStr);
        HttpURLConnection conn = (HttpURLConnection) url.openConnection();
        conn.setConnectTimeout(CONNECT_TIMEOUT);
        conn.setReadTimeout(READ_TIMEOUT);
        // ...
    }
}
```

## Token管理模式

### 双重检查锁缓存（推荐）
```java
public class KingdeeClient {
    private volatile String token;
    private volatile long tokenExpireTime;
    private final ReentrantLock lock = new ReentrantLock();

    public String getToken() {
        if (token == null || System.currentTimeMillis() > tokenExpireTime) {
            lock.lock();
            try {
                if (token == null || System.currentTimeMillis() > tokenExpireTime) {
                    Map<String, Object> resp = fetchToken();
                    token = (String) resp.get("access_token");
                    tokenExpireTime = System.currentTimeMillis() +
                        ((Number) resp.get("expires_in")).longValue() * 1000 - 60000;
                }
            } finally { lock.unlock(); }
        }
        return token;
    }
}
```

### Hutool TimedCache（数字安徽模式）
```java
private static final TimedCache<String, String> tokenCache =
    new TimedCache<>(30 * 60 * 1000);  // 30分钟过期

public static String getToken() {
    String token = tokenCache.get("kingdee_token");
    if (token == null) {
        token = fetchNewToken();
        tokenCache.put("kingdee_token", token);
    }
    return token;
}
```

## 签名与加密

### 国密SM4/SM3（数字安徽招采集成）
```java
public class Sm4Utils {
    // SM4加密请求Body
    public static String encrypt(String plainText, String key) { ... }
    // SM3签名
    public static String sign(String data, String key) { ... }
}
```

### AES加密（徽商SSO）
```java
public class TicketUtils {
    private static final String AES_KEY = "A1B2C3D4E5F6G7H8";  // 建议移入pluginProperties.xml
    // 票据格式: loginName|timestamp|specialKey
    public static String encrypt(String loginName) { ... }
    public static String[] decrypt(String ticket) { ... }
}
```

### MD5签名（安徽国资友空间）
```java
public class MD5SignUtil {
    public static String sign(Map<String, String> params, String secret) {
        // 1. 参数排序
        // 2. 拼接key=value&key=value
        // 3. MD5加密
    }
}
```

## 外部系统接口调用模式

### XML接口（徽商银行银企直连）
```java
public class BackPayServiceImpl {
    public BackPayResponse SinglePayment(SinglePaymentPo po) {
        String xml = buildXml(po);  // 组装XML
        String result = OkHttp.postXml(BANK_URL, xml);
        return parseXmlResponse(result);  // 解析retCode/retMsg/billCode
    }

    private String buildXml(SinglePaymentPo po) {
        return "<root><head><tr_code>BY0001</tr_code>" +
               "<erp_syscode>AHHSWCZYOA</erp_syscode>" +
               "<cust_no>301508920</cust_no></head>" +
               "<body>" + buildBodyXml(po) + "</body></root>";
    }
}
```

### Odoo JSON API（徽商合同）
```java
public class ContractHttpKit {
    // 1. 登录获取session
    public String login(String url, String db, String user, String pwd) { ... }
    // 2. POST JSON创建记录
    public String postJson(String url, String json) { ... }
    // 3. GET JSON查询
    public String getJson(String url) { ... }
}
```

### 金蝶云星空API（合规院）
```java
public class JDUtils {
    // 路由URL: api.kingdee.com
    // 1. 鉴权获取appToken
    public String authenticate(String clientId, String clientSecret) { ... }
    // 2. 获取accessToken
    public String getAccessToken(String appToken, String outerInstanceId) { ... }
    // 3. 调用业务接口
    public String saveVoucher(String token, Map<String, Object> data) {
        // POST /jdy/v2/fi/virtual
    }
}
```

### 用友YONBIP集成（安徽国资）
```java
// BIP SSO
public class BipSSOController extends BaseController {
    public ModelAndView sso(HttpServletRequest req, HttpServletResponse res) {
        String token = generateToken(user);
        String bipUrl = BIP_BASE_URL + "/service/sso?token=" + token;
        return redirectModelAndView(bipUrl);
    }
}

// NCC待办查询（直连Oracle数据库）
public class NccSection extends BaseSectionImpl {
    public BaseSectionTemplete projection(Map<String, String> params) {
        // JDBC直连BIP数据库查询待办
        String sql = "SELECT * FROM zyoa.v_yonbip_user WHERE ...";
        // 返回HTML内容
    }
}
```

## SSO单点登录集成

### 握手模式（SSOLoginHandshakeAbstract）
```java
public class SaSSOLoginHandshake extends SSOLoginHandshakeAbstract {
    @Override
    public LoginUser doLogin(String ticket, SSOLoginInfo info) {
        // 1. 验证ticket（调用第三方验证接口）
        // 2. 返回LoginUser
    }
}
```

### Controller跳转模式
```java
public class SaSSOController extends BaseController {
    @NeedlessCheckLogin
    public ModelAndView sso(HttpServletRequest req, HttpServletResponse res) {
        String ticket = req.getParameter("ticket");
        String loginName = TicketValidator.validate(ticket);
        String desLoginName = DES.encrypt(loginName);
        return redirectModelAndView("/login/sso?from=sasso&ticket=" + desLoginName);
    }
}
```

## 异步推送与补偿

### 事件驱动推送 + 补偿任务
```
@ListenEvent → 创建同步任务(状态=PENDING) → 推送执行
  ├── 成功 → SUCCESS
  ├── 可重试失败 → FAILED_RETRYABLE → 定时补偿扫描
  └── 不可重试失败 → FAILED_FINAL → 人工干预
```

### 补偿定时任务
```java
public class PendingRetryJob extends Thread {
    private volatile boolean running = true;
    private static final long SCAN_INTERVAL = 60 * 1000;  // 60秒

    @Override
    public void run() {
        while (running) {
            try {
                List<PendingSyncTask> failedTasks = taskDao.findRetryable();
                for (PendingSyncTask task : failedTasks) {
                    // 退避策略：根据重试次数延长间隔
                    dispatchService.retry(task);
                }
                Thread.sleep(SCAN_INTERVAL);
            } catch (InterruptedException e) { break; }
        }
    }
}
```

### 并发控制
```java
// 原子状态更新，防止重复推送
public boolean markSending(String taskId) {
    String sql = "UPDATE hs_pending_sync_task SET status='SENDING' " +
                 "WHERE id=? AND status='PENDING'";
    int updated = jdbc.executeUpdate(sql, new Object[]{taskId});
    return updated > 0;
}
```

## 返回值模式

### 超级节点返回值
```java
SuperNodeResponse(1)  // WAIT — 成功，等待继续流转
SuperNodeResponse(2)  // BACK — 失败，回退节点
SuperNodeResponse(0)  // 暂存
```

### Manager/Service通用响应
```java
public class Response {
    private int returnCode;  // 1=成功, 2=回退, 0=暂存
    private String message;
    private Object data;
}
```

### REST接口响应
```java
// 标准成功响应
{ "code": 0, "message": "success", "data": {...} }
// 标准失败响应
{ "code": -1, "message": "错误描述", "data": null }
```
