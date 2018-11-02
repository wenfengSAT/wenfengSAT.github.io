---
layout:     post
title:      生成微信JS-SDK参数工具类SignatureUtil
subtitle:   生成微信JS-SDK参数工具类SignatureUtil
date:       2018-11-02
author:     wenfengSAT
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - 微信
---


> 生成微信JS-SDK参数工具类SignatureUtil


+ JS引入

```

wx.config({  
    debug: true, // 开启调试模式,调用的所有api的返回值会在客户端alert出来，若要查看传入的参数，可以在pc端打开，参数信息会通过log打出，仅在pc端时才会打印。  
    appId: '', // 必填，公众号的唯一标识  
    timestamp: , // 必填，生成签名的时间戳  
    nonceStr: '', // 必填，生成签名的随机串  
    signature: '',// 必填，签名，见附录1  
    jsApiList: [] // 必填，需要使用的JS接口列表，所有JS接口列表见附录2  
}); 
 
```

+ jsapi_ticket

```

生成签名之前必须先了解一下jsapi_ticket，jsapi_ticket是公众号用于调用微信JS接口的临时票据。
正常情况下，jsapi_ticket的有效期为7200秒，通过access_token来获取。由于获取jsapi_ticket的api
调用次数非常有限，频繁刷新jsapi_ticket会导致api调用受限，影响自身业务，开发者必须在自己的服务全局缓存jsapi_ticket 。

1、参考以下文档获取access_token（有效期7200秒，开发者必须在自己的服务全局缓存access_token）：

2、用第一步拿到的access_token 采用http GET方式请求获得jsapi_ticket（有效期7200秒，开发者必须在自己的服务全局缓存jsapi_ticket）：
https://api.weixin.qq.com/cgi-bin/ticket/getticket?access_token=ACCESS_TOKEN&type=jsapi

```

+ 签名算法

```

签名生成规则如下：参与签名的字段包括noncestr（随机字符串）, 有效的jsapi_ticket, timestamp（时间戳）, 
url（当前网页的URL，不包含#及其后面部分） 。对所有待签名参数按照字段名的ASCII 码从小到大排序（字典序）后，
使用URL键值对的格式（即key1=value1&key2=value2…）拼接成字符串string1。这里 需要注意的是所有参数名均为小写字符。
对string1作sha1加密，字段名和字段值都采用原始值，不进行URL 转义。


即signature=sha1(string1)。 示例：

noncestr=Wm3WZYTPz0wzccnW
jsapi_ticket=sM4AOVdWfPE4DxkXGEs8VMCPGGVi4C3VM0P37wVUCFvkVAy_90u5h9nbSlYy3-Sl-HhTdfl2fzFy1AOcHKP7qg
timestamp=1414587457
url=http://mp.weixin.qq.com?params=value
步骤1. 对所有待签名参数按照字段名的ASCII 码从小到大排序（字典序）后，使用URL键值对的格式（即key1=value1&key2=value2…）拼接成字符串string1：

jsapi_ticket=sM4AOVdWfPE4DxkXGEs8VMCPGGVi4C3VM0P37wVUCFvkVAy_90u5h9nbSlYy3-Sl-HhTdfl2fzFy1AOcHKP7qg&noncestr=Wm3WZYTPz0wzccnW&timestamp=1414587457&url=http://mp.weixin.qq.com?params=value

 

步骤2. 对string1进行sha1签名，得到signature：

0f9de62fce790f9a083d5c99e95740ceb90c27ed

 

注意事项：

1、签名用的noncestr和timestamp必须与wx.config中的nonceStr和timestamp相同。
2、签名用的url必须是调用JS接口页面的完整URL。
3、出于安全考虑，开发者必须在服务器端实现签名的逻辑。

-------------------------------------------------------------------------------
以上的东西加起来就是四步：

1、使用APPID和APPSecret获取access_token；

2、使用access_token获取jsapi_ticket ；

3、用时间戳、随机数、jsapi_ticket和要访问的url按照签名算法拼接字符串；

4、对第三步的字符串进行SHA1加密，得到签名。

```


+ 工具类代码

```java

import java.security.MessageDigest;
import java.util.Iterator;
import java.util.Map;
import java.util.Map.Entry;
import java.util.Random;
import java.util.Set;
import java.util.SortedMap;
import java.util.TreeMap;

public class SignatureUtil {
    
    public static final String TOKEN = "3ad8id23i907o2kmli03";//用于生成数字签名

    /**
     * 创建SHA1签名
     * @param params
     * @return SHA1签名
     */
    public static String createSignature(SortedMap<String, String> params) {
        return sha1Encrypt(sortParams(params));
    }
    
    /**
     * 创建SHA1签名
     * @param timeStamp
     * @param nonce
     * @return SHA1签名
     */
    public static String createSignature(String timeStamp, String nonce) {
        SortedMap<String, String> signParams = new TreeMap<String, String>();
        signParams.put("token", TOKEN);
        signParams.put("timeStamp", timeStamp);
        signParams.put("nonce", nonce);
        return createSignature(signParams);
    }

    /**
     * 使用SHA1算法对字符串进行加密
     * @param str
     * @return
     */
    public static String sha1Encrypt(String str) {

        if (str == null || str.length() == 0) {
            return null;
        }

        char hexDigits[] = { '0', '1', '2', '3', '4', '5', '6', '7', '8', '9',
                'a', 'b', 'c', 'd', 'e', 'f' };

        try {

            MessageDigest mdTemp = MessageDigest.getInstance("SHA1");
            mdTemp.update(str.getBytes("UTF-8"));

            byte[] md = mdTemp.digest();
            int j = md.length;
            char buf[] = new char[j * 2];
            int k = 0;

            for (int i = 0; i < j; i++) {
                byte byte0 = md[i];
                buf[k++] = hexDigits[byte0 >>> 4 & 0xf];
                buf[k++] = hexDigits[byte0 & 0xf];
            }

            return new String(buf);

        } catch (Exception e) {
            return null;
        }
    }

    /**
     * 生成时间戳
     * @return
     */
    public static String getTimeStamp() {
        return String.valueOf(System.currentTimeMillis() / 1000);
    }

    /**
     * 生成6位随机字符串
     * @return
     */
    public static String getRandomStr() {
        int length = 6;
        String base = "abcdefghijklmnopqrstuvwxyz0123456789";     
        Random random = new Random();     
        StringBuffer sb = new StringBuffer();     
        for (int i = 0; i < length; i++) {     
            int number = random.nextInt(base.length());     
            sb.append(base.charAt(number));     
        }     
        return sb.toString(); 
    }
    
    /**
     * 根据参数名称对参数进行字典排序
     * @param params
     * @return
     */
    private static String sortParams(SortedMap<String, String> params) {
        StringBuffer sb = new StringBuffer();
        Set<Entry<String, String>> es = params.entrySet();
        Iterator<Entry<String, String>> it = es.iterator();
        while (it.hasNext()) {
            Map.Entry<String, String> entry = it.next();
            String k = entry.getKey();
            String v = entry.getValue();
            sb.append(k + "=" + v + "&");
        }
        return sb.substring(0, sb.lastIndexOf("&"));
    }

    public static void main(String[] args) throws Exception {
        //c4db7f5867a6290378cf24429f2803da0516a788
        String timeStamp = getTimeStamp();
        String nonce = getRandomStr();
        String sign = createSignature(timeStamp, nonce);
        System.out.println("timeStamp:"+timeStamp);
        System.out.println("nonce:"+nonce);
        System.out.println("signature:" + sign);
    }

}


```

+ 代码

```java

import java.security.MessageDigest;
import java.security.NoSuchAlgorithmException;
import java.util.HashMap;
import java.util.Map;
import java.util.Objects;
import java.util.UUID;

import javax.servlet.http.HttpServletRequest;

import org.apache.shiro.authc.AuthenticationException;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.core.ValueOperations;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RestController;

import com.alibaba.fastjson.JSONObject;
import com.asiainfo.vehicle.common.utils.CommonsService;
import com.asiainfo.vehicle.common.web.controller.BaseController;
import com.asiainfo.vehicle.modules.common.service.ApiAuthenticateService;
import com.asiainfo.vehicle.modules.common.service.GetJsonMapService;
import com.asiainfo.vehicle.modules.common.utils.HandleResult;
import com.asiainfo.vehicle.modules.common.utils.HttpClientUtil;
import com.asiainfo.vehicle.modules.common.utils.SignatureUtil;

/**
 * 
 * @Description： 微信JS-SDK参数获取
 * @author [ Wenfeng.Huang ] on [2018年11月1日上午11:13:40]
 * @Modified By： [修改人] on [修改日期] for [修改说明]
 *
 */
@RestController
public class WeixinJsSdkParamController extends BaseController {

	@Value("${appId}")
	private String appId;
	@Value("${appSecret}")
	private String appSecret;
	private static final String WEIXIN_REQUEST_TOKEN_KEY = "WEIXIN_REQUEST_TOKEN_KEY";
	private static final String ACCESS_TOKEN_URL = "https://api.weixin.qq.com/cgi-bin/token?grant_type=client_credential&appid=APP_ID&secret=APP_SECRET";
	private static final String GETCALLBACKIP_URL = "https://api.weixin.qq.com/cgi-bin/getcallbackip?access_token=accessToken";
	private static final String GETTICKET = "https://api.weixin.qq.com/cgi-bin/ticket/getticket?access_token=accessToken&type=jsapi";
	
	@Autowired
	private RedisTemplate<String, String> redisTemplate;
	@Autowired
	private GetJsonMapService getJsonMapService;
	@Autowired
	private ApiAuthenticateService authService;
	
	/**
	 * 微信JS-SDK参数获取
	 * @param request
	 * @return
	 * @throws Exception
	 */
	@PostMapping(value="/app/weixin/getJsSdkParam")
    public Map<String, Object> getJsSdkParam(HttpServletRequest request) throws  Exception{
		Map<String, Object> result = new HashMap<String, Object>();
		Map<String, String> param = new HashMap<String, String>();
		Map<String, Object> jsonMap = getJsonMapService.getJson(request);
	    String uri = request.getRequestURI().replace(webPath, "");
	    String cache = authService.authenticate(jsonMap, uri);
	    Map<?, ?> privateParam = (Map<?, ?>)jsonMap.get("private");
	    String url = (String) privateParam.get("URL");
	    if("".equals(url) || url == null) {
	    	throw new IllegalArgumentException ("URL" + "不能为空");
	    }
	    if (!Objects.isNull(cache)) {
	    	try {
				//1、获取AccessToken  
				String accessToken = getAccessToken();  
				//2、获取Ticket  
				String jsapi_ticket = getTicket(accessToken);  
				//3、随机字符串  
				String nonceStr = UUID.randomUUID().toString().replace("-", "").substring(0, 16);
				//4、时间戳 
				String timestamp = String.valueOf(System.currentTimeMillis() / 1000);
				//6、signature
				String signature =getJsSDKSignature(jsapi_ticket, nonceStr,  timestamp,  url); 
				param.put("appId", appId);
				param.put("timestamp", timestamp);
				param.put("nonceStr", nonceStr);
				param.put("signature", signature);
				param.put("url", url);
				param.put("jsapi_ticket", jsapi_ticket);
				result.put("resultCode", "0000");
			    result.put("resultDesc", "获取成功!");
			} catch (Exception e) {
				result.put("resultCode", "E003");
			    result.put("resultDesc", "获取失败!");
			}
		    result.put("resultList", param);
	    } else {
	      throw new AuthenticationException("用户未授权");
	    }
		return result;
	}
	
	/**
	 * 获取signature 
	 * @param jsapi_ticket
	 * @param noncestr
	 * @param timestamp
	 * @param url
	 * @return
	 */
	public String getJsSDKSignature(String jsapi_ticket,String noncestr, String timestamp, String url) {
        String  str = "jsapi_ticket=" + jsapi_ticket +
                "&noncestr=" + noncestr +
                "&timestamp=" + timestamp +
                "&url=" + url;
        return sha1Encrypt(str); 
	}
	
	/**
	 * 请求微信接口获取ticket
	 * @return
	 */
	public String getTicket(String token){
        String url = GETTICKET.replace("accessToken" ,token);
        JSONObject result = HttpClientUtil.doGet(url);
        return result.getString("ticket");
    }
	
	/**
	 * 从redis获取请求微信接口的token
	 * @return
	 */
	public String getWeixinToken() {
		ValueOperations<String, String> opsForValue = redisTemplate.opsForValue();
		String token = opsForValue.get(WEIXIN_REQUEST_TOKEN_KEY);
		if(!"".equals(token) && token != null) {
			boolean flag = validateAccessToken(token);
			if(!flag) {
				String accessToken = getAccessToken();
				opsForValue.set(WEIXIN_REQUEST_TOKEN_KEY, accessToken);
			}
		}else {
			String accessToken = getAccessToken();
			opsForValue.set(WEIXIN_REQUEST_TOKEN_KEY, accessToken);
		}
		return opsForValue.get(WEIXIN_REQUEST_TOKEN_KEY);
	}
	
	/**
	 * 请求微信接口获取请求微信接口的token
	 * @return
	 */
	public String getAccessToken(){
        String url = ACCESS_TOKEN_URL.replace("APP_ID" ,appId).replace("APP_SECRET",appSecret);
        JSONObject result = HttpClientUtil.doGet(url);
        return result.getString("access_token");
    }
	
	/**
	 * 验证token是否有效
	 * @param token
	 * @return 
	 * 
	 * {"errcode":40001,"errmsg":"invalid credential, access_token is invalid or not latest hint: [TCmz5a03344694!]"}
	 */
	public boolean validateAccessToken(String token) {
		String url = GETCALLBACKIP_URL.replace("accessToken" ,token);
		JSONObject result = HttpClientUtil.doGet(url);
		if(result.toJSONString().contains("ip_list") && !result.toJSONString().contains("errmsg")) {
			return true;
		}else {
			return false;
		}
	}
	
	/**
	 * 算法加密
	 * @param decript
	 * @return
	 */
	public static String sha1Encrypt(String str) {
        if (str == null || str.length() == 0) {
            return null;
        }
        char hexDigits[] = { '0', '1', '2', '3', '4', '5', '6', '7', '8', '9',
                'a', 'b', 'c', 'd', 'e', 'f' };
        try {
            MessageDigest mdTemp = MessageDigest.getInstance("SHA1");
            mdTemp.update(str.getBytes("UTF-8"));
            byte[] md = mdTemp.digest();
            int j = md.length;
            char buf[] = new char[j * 2];
            int k = 0;
            for (int i = 0; i < j; i++) {
                byte byte0 = md[i];
                buf[k++] = hexDigits[byte0 >>> 4 & 0xf];
                buf[k++] = hexDigits[byte0 & 0xf];
            }
            return new String(buf);
        } catch (Exception e) {
            return null;
        }
    }
	
}


```











