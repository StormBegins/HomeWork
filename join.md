�������£�

	select p.cityName, c.cityName, a.cityName
	from s_provinces p,
	     s_provinces c,
	     s_provinces a
	where p.cityName = '�㶫ʡ'
	  and p.id = c.parentId
	  and c.id = a.parentId

���ݱ�ĵݹ���ɣ� id �м��ϼ���Ӧ parentId ���¼��У����γɵ�ʡ���ж���/�ؼ����������������Ӳ�ѯ��
	

