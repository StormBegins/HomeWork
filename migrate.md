###要求：
	源表：lagou_test表,s_provinces表。
	把去重复后的lagou_position表内的公司、城市和地区、及其他字段分离成三个表(lagou_company,lagou_city,lagou_position)
###大致思路：
	1.先根据s_provinces表获得县/区id，省，市，县/区的数据和市id，省，市，null的数据；
	用union将其获得数据建成数据表。这里获得两种的数据结果，看起来是有点数据的冗余，
	但是仔细一想有些公司的地址是只有市而没有具体的县或区，所以这里就要获得这两种数据的
	结果。
	
	2.然后在lagou_test表内将和公司相关的字段（company_id、company_short_name、company_full_name、company_size、financestage）
	提取出来分离出一个新表--lagou_company。

	3.最后将创建lagou_position表。表内字段（pid, （县/区）/市id  company_id, position, field, salary_min, salary_max, workyear, education, ptype, pnature, advantage, published_at, updated_at）
	
###具体实现：
	1.
	create table lagou_city as
  		select d.id, p.cityName as province, c.cityName as city, d.cityName as district
 		 from (select * from s_provinces where depth = 3) d
        		 join s_provinces c on d.parentId = c.id and c.depth = 2
         		join s_provinces p on c.parentId = p.id and p.depth = 1
  		union
  		select c.id, p.cityName as province, c.cityName as city, null as district
  		from (select * from s_provinces where depth = 2) c
         		join s_provinces p on c.parentId = p.id and p.depth = 1;

	这里创建出lagou_city表主要就是运用了s_provinces表中上一级地区的parentId对应下一级地区的id.
	2.
	create table lagou_company as
  		select distinct  t.company_id        as cid,
                    		t.company_short_name as short_name,
                   		 t.company_full_name  as full_name,
                    		t.company_size       as size,
                   		 t.financestage
  		from lagou_test t;
	把有关公司的字段分离出来建立一个新表
	3.
	create table lagou_position as
  		select pid, c.id as city, company_id as company, position, field, salary_min, salary_max, workyear, education, ptype, pnature, advantage, published_at, updated_at
  		from (select * from lagou_test where district is null) p
        		 join lagou_city c on c.city like concat(p.city, '%') and c.district is null;
	根据lagou_test表和lagou_city创建出相关字段的表。
	insert into lagou_position
		select pid, c.id as city, company_id as company, position, field, salary_min, salary_max, workyear, education,lagou_city ptype, pnature, advantage, published_at, updated_at
		from (select * from lagou_test where district is not null) p
       		join lagou_city c on c.city like concat(p.city, '%') and c.district like concat(p.district, '%');
	插入数据。