## 摘要认证

```

	import sys
	import hashlib
	import ssl
    
    # 测试使用，获取输入参数
	def getURLall(url, format, token, data):
	    if format == '' or token == '' or data == '':
	        print "need format token data"
	        return ''
	    res = url + '/pmsapi?format=' + format + '&token=' + token + '&data=' + data
	    return res
	
	# 计算md5，测试时候使用，HTTPDigestAuthHandler() 函数会自己计算
	def getMD5(str):
	    hl = hashlib.md5()
	    hl.update(str.encode(encoding='utf-8'))
	    md5 = hl.hexdigest()
	    return md5
	
	username = 'cdrapi'
	password = 'cdrapi123'
	realm = 'passwd'
	url_top = 'https://192.168.93.46:8443'
	if len(sys.argv) >= 2:
	    url_top = sys.argv[1]
	#url = 'https://192.168.124.63:8443/pmsapi?format=json'
	
	
	#url = getURLall(url_top, format, token, data)
	url = url_top + "/cdrapi?fromat=xml"
	
	
	auth = urllib2.HTTPDigestAuthHandler()
	auth.add_password(realm,url_top,username,password)
	
	opener = urllib2.build_opener(auth)  
	urllib2.install_opener(opener)  
	
	#ssl._create_default_https_context = ssl._create_unverified_context #for ptyhon 2.7 or high
	res_data = urllib2.urlopen(url)  
	res = res_data.read()  
	print res 
```