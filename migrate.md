###Ҫ��
	Դ��lagou_test��,s_provinces��
	��ȥ�ظ����lagou_position���ڵĹ�˾�����к͵������������ֶη����������(lagou_company,lagou_city,lagou_position)
###����˼·��
	1.�ȸ���s_provinces������/��id��ʡ���У���/�������ݺ���id��ʡ���У�null�����ݣ�
	��union���������ݽ������ݱ����������ֵ����ݽ�������������е����ݵ����࣬
	������ϸһ����Щ��˾�ĵ�ַ��ֻ���ж�û�о�����ػ��������������Ҫ������������ݵ�
	�����
	
	2.Ȼ����lagou_test���ڽ��͹�˾��ص��ֶΣ�company_id��company_short_name��company_full_name��company_size��financestage��
	��ȡ���������һ���±�--lagou_company��

	3.��󽫴���lagou_position�������ֶΣ�pid, ����/����/��id  company_id, position, field, salary_min, salary_max, workyear, education, ptype, pnature, advantage, published_at, updated_at��
	
###����ʵ�֣�
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

	���ﴴ����lagou_city����Ҫ����������s_provinces������һ��������parentId��Ӧ��һ��������id.
	2.
	create table lagou_company as
  		select distinct  t.company_id        as cid,
                    		t.company_short_name as short_name,
                   		 t.company_full_name  as full_name,
                    		t.company_size       as size,
                   		 t.financestage
  		from lagou_test t;
	���йع�˾���ֶη����������һ���±�
	3.
	create table lagou_position as
  		select pid, c.id as city, company_id as company, position, field, salary_min, salary_max, workyear, education, ptype, pnature, advantage, published_at, updated_at
  		from (select * from lagou_test where district is null) p
        		 join lagou_city c on c.city like concat(p.city, '%') and c.district is null;
	����lagou_test���lagou_city����������ֶεı�
	insert into lagou_position
		select pid, c.id as city, company_id as company, position, field, salary_min, salary_max, workyear, education,lagou_city ptype, pnature, advantage, published_at, updated_at
		from (select * from lagou_test where district is not null) p
       		join lagou_city c on c.city like concat(p.city, '%') and c.district like concat(p.district, '%');
	�������ݡ�