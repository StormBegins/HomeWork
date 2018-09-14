代码如下：

	select p.cityName, c.cityName, a.cityName
	from s_provinces p,
	     s_provinces c,
	     s_provinces a
	where p.cityName = '广东省'
	  and p.id = c.parentId
	  and c.id = a.parentId

根据表的递归规律（ id 列即上级对应 parentId 即下级列）所形成的省对市对区/县加以条件进行自链接查询。
	

