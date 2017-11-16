+++
date = "2016-12-08T22:07:46+08:00"
title = "DWR"
draft = false
tags = ["整理","Java","DWR"]
share = true
+++

[TOC]

## 参考
- [在 Spring Web MVC 环境下使用 DWR](https://www.ibm.com/developerworks/cn/java/j-lo-springdwr/)

## 作用
- Spring 框架提供，可将 Java 组件方法直接暴露给 JavaScript 客户端
- 通过将 Spring 容易中的 Bean 转换为 JavaScript 对象

## 配置
### web.xml
```
<servlet-mapping>
    <servlet-name>springServlet</servlet-name>
    <url-pattern>/dwr/*</url-pattern>
</servlet-mapping>
```

### springmvc.xml   
```
<!-- DWR3.0配置 -->
<!-- 打开dwr的控制器 -->
   <dwr:controller id="dwrController" debug="true" >
       <dwr:config-param name="allowScriptTagRemoting" value="true" /> 
       <dwr:config-param name="crossDomainSessionSecurity" value="false" />
       <dwr:config-param name="activeReverseAjaxEnabled" value="true"/>
   </dwr:controller>
   <dwr:url-mapping/>
   <bean class="org.springframework.web.servlet.handler.SimpleUrlHandlerMapping">
    <property name="alwaysUseFullPath" value="true"/>
    <property name="mappings">
    <props>
      <prop key="/dwr/**/*">dwrController</prop>
    </props>
    </property>
</bean>
<!-- 使用 dwr的 annotation -->
<!-- 要求dwr在spring容器中检查拥有@RemoteProxy 和 @RemoteMethod注解的类。注意它不会去检查Spring容器之外的类。 -->
<dwr:annotation-config id="dwrAnnotationConfig" />
<dwr:annotation-scan base-package="com.syncsoft.ywgl.xmgl.dtmszckz.web" scanDataTransferObject="true" scanRemoteProxy="true" />
<dwr:annotation-scan base-package="com.syncsoft.dwr" scanDataTransferObject="true" scanRemoteProxy="true" />
```


### java-code
```
@RemoteProxy(creator=SpringCreator.class,name="DtmszckzPusher")    //推送给谁，推送了什么内容
public class DtmszckzPusher {
    @RemoteMethod
    public void setContrlAction(String jmr_id,String contrlAction) {
        SessionInfo sessionInfo = (SessionInfo) SecurityUtils.getSubject().getSession().getAttribute(Constants.USER_INFO_SESSION);
        final String userId = sessionInfo.getUserId();
        final String action = contrlAction;

        Browser.withAllSessionsFiltered(
            new ScriptSessionFilter() {
                public boolean match(ScriptSession scriptSession) {    //返回true的情况下才可以接收到推送信息
                    System.out.println("scriptSession in DtmszckzPusher:"+scriptSession.getId());
                    if (scriptSession.getAttribute("userId") == null){
                        return false;
                    }else{
                        return (scriptSession.getAttribute("userId")).equals(userId);
                    }
                }
            },
            new Runnable() {
                private ScriptBuffer script = new ScriptBuffer();
                public void run() {
                    script.appendCall("showMessage", action);    //showMessage为前台调用方法
                    Collection<ScriptSession> sessions = Browser.getTargetSessions();

                    for (ScriptSession scriptSession : sessions) {
                        scriptSession.addScript(script);
                    }
                }
            }
        );
    }
}

@RemoteProxy(creator=SpringCreator.class,name="DtmsjmrdPusher")    //注册，申请推送
public class DtmsjmrdPusher {

    public static final String SS_ID="DWR_ScriptSession_Id";
    @RemoteMethod
    public void clientOnPageLoad(String userId) { 
       SessionInfo sessionInfo = (SessionInfo) SecurityUtils.getSubject().getSession().getAttribute(Constants.USER_INFO_SESSION);
       userId = sessionInfo.getUserId();

       ScriptSession scriptSession = WebContextFactory.get().getScriptSession(); 
       scriptSession.setAttribute("userId", userId); 
       DwrScriptSessionManagerUtil du = new DwrScriptSessionManagerUtil();
       try {
           ScriptSessionListener li = du.init();
           du.addScriptSessionListener(li);
        } catch (ServletException e) {
            e.printStackTrace();
        }
   }
}
```


### html
```
-- import js package
    <script type='text/javascript' src='<%=request.getContextPath() %>/dwr/engine.js'></script>
    <script type='text/javascript' src='<%=request.getContextPath() %>/dwr/util.js'></script>
    <script type='text/javascript' src='<%=request.getContextPath() %>/dwr/interface/DtmszckzPusher.js'></script> 
    ///dwr/interface为固定写法，DtmszckzPusher则为推送的Java类，.js也是固定写法

-- sender
    function Button_onClick(action){
        var jmr_id = $("#userId").val();
        DtmszckzPusher.setContrlAction(jmr_id,action);
    }

-- receiver
    初始化（通知服务器你准备好，并为你创建登陆记录）
    onload="onLoadPage();dwr.engine.setActiveReverseAjax(true);dwr.engine.setNotifyServerOnPageUnload(true,true);"
    function onLoadPage(){
        var userId = '<%= sessionId %>';
        DtmsjmrdPusher.clientOnPageLoad(userId, showMessage);
    }     

-- receiver-callback
    function showMessage(action){ 
        alert(action); 
    }
```
