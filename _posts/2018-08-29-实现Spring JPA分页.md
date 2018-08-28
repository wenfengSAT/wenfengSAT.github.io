---
layout:     post
title:      实现Spring JPA分页
subtitle:   实现Spring JPA分页
date:       2018-08-29
author:     wenfengSAT
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - JPA
---

>实现Spring JPA分页

{% raw %}
```hbs
DAO层实现：

public interface ApiRepository extends JpaRepository<Api, Long> {

	
	/**
	 * Api列表分页
	 * @param txt
	 * @param pageable
	 * @return
	 */
	@Query(value = "select * from Api where (dictid like %?1% or dictname like %?1%) and project_name like %?2%",
		    countQuery = "select count(*) from Api where (dictid like %?1% or dictname like %?1%) and project_name like %?2%",
		    nativeQuery = true)
	Page<Api> findByTxt(String txt, String projectName, Pageable pageable);
}

Controller调用：

@RequestMapping("/getApiData")
@ResponseBody
public Map<String,Object> getApiData(String txt,String projectName,int pageNumber, int pageSize) {
	HashMap<String,Object> result = new HashMap<String,Object>();
	@SuppressWarnings("deprecation")
	Pageable pageable = new PageRequest(pageNumber-1,pageSize);
	Page<Api> r = apiRepository.findByTxt(txt, projectName, pageable);
	result.put("rows", r.getContent());
	result.put("total", r.getTotalElements());
	return result;
}

```
{% endraw %}