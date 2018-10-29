## python括号匹配

### python 括号匹配，多个括号并列排要加(?:)


	pattern = re.compile(r'((?:[0-9a-f]{1,4})(?:\:[0-9a-f]{1,4}){1,7})')                                     
	pattern = re.compile(r'(([0-9a-f]{1,4})(\:[0-9a-f]{1,4}){1,7})')
	
	line = "include = 2001:b8:1:2:20b:82ff:fe67:4bf"  
	match = pattern.findall(line)                                                                             
	print match


第一个pattern返回  
['2001:b8:1:2:20b:82ff:fe67:4bf']  
第二个pattern返回  
[('2001:b8:1:2:20b:82ff:fe67:4bf', '2001', ':4bf')]  