---
layout:     post
title:      SpringMVC+ajaxForm实现表单提交并且上传文件
subtitle:   SpringMVC+ajaxForm实现表单提交并且上传文件
date:       2018-03-05
author:     wenfengSAT
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - SpringMVC
    - ajaxForm
---

>SpringMVC+ajaxForm实现表单提交并且上传文件

# 配置文件

{% raw %}
```hbs
<bean id="multipartResolver"    
        class="org.springframework.web.multipart.commons.CommonsMultipartResolver">    
        <!-- set the max upload size100MB -->    
        <property name="maxUploadSize">    
            <value>104857600</value>    
        </property>    
        <property name="maxInMemorySize">    
            <value>4096</value>    
        </property>   
        <property name="defaultEncoding">  
            <value>utf-8</value>  
        </property>  
    </bean>

```
{% endraw %}

# 前端JS代码
	
{% raw %}
```hbs
function addNotice(){
	var type = $("#type").combobox('getValue');
	var title = $("#title").textbox('getValue'); 
	var body = $("#body").textbox('getValue');
	console.log("type:"+type+"-----title:"+title+"-----body:"+body);
	
	$("#personnelForm").ajaxSubmit({
		type: "POST",
		url:"../../addPersonnelNotice",
		dataType: "json",
		data:{
			type:type,
			title:title,
			body:body
	    },
	    success:function(data){
			if(data.info == "success"){
				$.messager.alert('提示', '通知发布成功！', 'info');
			}else{
				$.messager.alert('错误', '数据加载失败！', 'error');
			}
	    },
	    error:function(){
	    	$.messager.alert('错误', '数据加载失败！', 'error');
	    }
	});
}
```
{% endraw %}

# 后台代码

{% raw %}
```hbs

@RequestMapping(value="/addPersonnelNotice",method = RequestMethod.POST)
	public void addPersonnelNotice(HttpServletRequest request,HttpServletResponse response,
			@RequestParam(required=false)MultipartFile file, @RequestParam("body")String body,
			@RequestParam("type")String type,@RequestParam("title")String title){
		HttpSession session = request.getSession();
		User user = (User) session.getAttribute(Constant.USER_SESSION_KEY);
		int fileType = Integer.parseInt(request.getParameter("type"));
		String createDate = sdf.format(new Date());
		String createUser = user.getUserName();
		PersonnelNotice personnelNotice = new PersonnelNotice( title, body, createUser, createDate, fileType);
		if(file != null){
			//上传附件
			personnelNotice.setAnnexFileName(file.getOriginalFilename());
			File uploadFile = new File("D:\\"+ System.currentTimeMillis()+ file.getOriginalFilename());
			personnelNotice.setAnnexFilePath(uploadFile.getPath());
			try {
				FileUtils.copyInputStreamToFile(file.getInputStream(), uploadFile);
			} catch (IOException e) {
				e.printStackTrace();
			}
		}
		JSONObject result = new JSONObject();
		if(service.addPersonnelNotice(personnelNotice)){
			result.put(Constant.INFO, Constant.SUCCESS);
		}else{
			result.put(Constant.INFO, Constant.FAIL);
		}
		writer(response,result);
	}

```
{% endraw %}	
