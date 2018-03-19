title: 在java Web项目中处理前后端日期字段自动匹配绑定
author: jackcooper
date: 2018-03-19 17:10:26
tags:
---
在java开发中，经常会遇到前后端交互日期格式，一般我们都会用日期格式化来处理，但时间久了，你是否感觉到繁琐而且冗余呢，本文给出一种全新的解决思路。通过正则提取出日期数字，自动将前端传的日期字符串绑定到对象的日期格式属性上去，省去一切繁琐步骤。闲话少说，文归正传。

#定义BaseController

```java
public class BaseController implements ServletContextAware{
/**
     * 日志
     **/
    protected final Logger log = LoggerFactory.getLogger(BaseController.class);


    public HttpServletRequest request;
    public HttpServletResponse response;
    public HttpSession session;
    public ServletContext application;
    /**
     * 文件
     **/
    @Autowired(required = false)
    protected MultipartResolver multipartResolver;

    /**
     * @param code
     * @return
     */
    public static String respJson(CodeEnum code) {
        return respJson(code, null);
    }

    /**
     * @param code
     * @param data
     * @return
     */
    public static String respJson(CodeEnum code, Object data) {
        JSONObject jsono = new JSONObject();
        jsono.put("code", code.getCode());
        jsono.put("message", code.getMessage());
        if (data != null) {
            jsono.put("data", data);
        }
        return jsono.toJSONString();
    }

    /**
     *
     * @param code 错误码
     * @param message 内容
     * @return
     */
    public static String respJson(String code , String message ) {
        return respJson(code,message,null);
    }

    /**
     *
     * @param code
     * @param message
     * @param data
     * @return
     */
    public static String respJson(String code , String message , Object data){
        JSONObject jsono = new JSONObject();
        jsono.put("code",code);
        jsono.put("message",message);
        if (data != null) {
            jsono.put("data", data);
        }
        return JSON.toJSONString(jsono);
    }

    @ModelAttribute
    public void setReqAndRes(HttpServletRequest request, HttpServletResponse response) {
        this.request = request;
        this.response = response;
        this.session = request.getSession();
    }

    public void setServletContext(ServletContext arg0) {
        this.application = arg0;
    }

    /**
     * 获取传递过来的参数
     */
    public String getParameter(String key) {
        return request.getParameter(key);
    }

    /**
     * 分页对象
     */
    public <T> Page<T> getPage(Class<T> clazz) {
        Page<T> page = new Page<T>();
        String pageNo = getParameter("pageNo");
        String pageSize = getParameter("pageSize");
        pageNo = pageNo == null || "".equals(pageNo) ? "1" : pageNo;
        pageSize = pageSize == null || "".equals(pageSize) ? "10" : pageSize;
        page.setSearchCount(true);
        page.setSize(Integer.valueOf(pageSize));
        page.setCurrent((Integer.valueOf(pageNo)));
        return page;
    }

    protected String redirectOuter(String url) {
        return "redirect:" + url;
    }

    /**
     * binder用于bean属性的设置
     */
    @InitBinder
    public void initBinder(WebDataBinder binder) {
        binder.registerCustomEditor(Date.class, new DateEditor());
        binder.registerCustomEditor(String.class, new StringEscapeEditor());
    }

    /**
     * 获取传递过来的str参数,并给默认值
     */
    public String getStrParameter(String key, String defaultValue) {
        String str = getParameter(key);
        if (org.apache.commons.lang3.StringUtils.isEmpty(str)) {
            return defaultValue;
        }
        return str.trim();
    }

}
```
上述代码中initBinder方法上添加了一个@InitBinder注解，该方法会在请求时被调用，改方法中有个DateEditor类，日期的处理就是在这个里处理完的。

# DateEditor类

```java
class DateEditor extends PropertyEditorSupport {
    @Override
    public void setAsText(String text) throws IllegalArgumentException {
        if (StringUtils.isEmpty(text)) {
            return;
        }
        text = text.trim();
        Pattern pattern = Pattern.compile("[^0-9]");
        Matcher matcher = pattern.matcher(text);
        text = matcher.replaceAll("");

        int length = text.length();
        Date date;
        switch(length){
            case 14:
                date = DateTime.parse(text,DateTimeFormat.forPattern("yyyyMMddHHmmss")).toDate();
                break;
            case 12:
                date = DateTime.parse(text,DateTimeFormat.forPattern("yyyyMMddHHmm")).toDate();
                break;
            case 10:
                date = DateTime.parse(text,DateTimeFormat.forPattern("yyyyMMddHH")).toDate();
                break;
            case 8:
                date = DateTime.parse(text,DateTimeFormat.forPattern("yyyyMMdd")).toDate();
                break;
            case 6:
                date = DateTime.parse(text,DateTimeFormat.forPattern("yyyyMM")).toDate();
                break;
            case 4:
                date = DateTime.parse(text,DateTimeFormat.forPattern("yyyy")).toDate();
                break;
            default: return;
        }
        setValue(date);
    }
}

```

#如何使用
上面准备完成后，在需要用到的controller中继承BaseController即可！
一般网上的例子仅支持如下格式的日期：
*  yyyyMMdd HH:mm:ss
* yyyyMMdd HH:mm
* yyyyMMdd HH
* yyyyMMdd
* yyyyMM
* yyyy
本文的方法，支持包含14、12、10、8、6、4个数字的所有字符串。