1 请求库
import urllib2 这个是python2.7默认携带的库，用于url访问
基本用法：
urllib2.urlopen((url[, data[, timeout[, cafile[, capath[, cadefault[, context]]]]])) 打开一个url，可以是string或者一个Request对象
返回值类似一个file对象，会多三个方法：geturl, getcode, info

urllib2.Request(url[, data][, headers][, origin_req_host][, unverifiable])是 url请求的一个抽象：
可以添加头，添加post数据，添加代理等：Request.add_data(data)，Request.add_header(key, val)

urllib2.build_opener([handler, ...]) 返回一个OpenerDirector实例。

OpenerDirector：
OpenerDirector.open(url[, data][, timeout])，作用同urlopen，但可以增加handler，可以完成更高级的动作。使用这个打开，会先对request进行预处理，发送请求，然后对response进行处理。

install_opener可以创建一个全局的opener，这样调用普通urlopen的时候会默认使用这个opener，我觉得没有必要。
使用OpenerDirector可以通过添加Handlers处理很多特殊情况，譬如cookies和重定向。

例子--模拟登录：
对于需要登录的网站，可以携带cookie进行访问，关于cookie可以直接从网页获得，也可以找到网站登录的提交方法，模拟登录获取cookie
关于如何设置cookie，两种方案：
-- 获取cookie字符串，设置到header中，这种方案适合直接从网页获取cookie的情况
req = request.Request(url)
#设置cookie
req.add_header('cookie', raw_cookies)
#设置请求头
req.add_header('User-Agent', 'Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/60.0.3112.113 Safari/537.36')

-- 模拟登录获取cookie，使用Openerdirector，和HTTPCookieProcessor，模拟登录后，该opener就可以携带cookie访问了
#设置请求头
headers = {'User-agent':'Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/60.0.3112.113 Safari/537.36'}
#登录时表单提交到的地址（用开发者工具可以看到）
login_url = ' http://ssfw.xmu.edu.cn/cmstar/userPasswordValidate.portal
#构造登录请求
req = urllib.request.Request(login_url, headers = headers, data = post_data)
#构造cookie
cookie = http.cookiejar.CookieJar()
#由cookie构造opener
opener = urllib.request.build_opener(urllib.request.HTTPCookieProcessor(cookie))
#发送登录请求，此后这个opener就携带了cookie，以证明自己登录过
resp = opener.open(req)

其他详细内容：https://docs.python.org/2.7/library/urllib2.html

2 selenium 驱动浏览器进行网站访问
todo

3 解析库
-- lxml
-- beautifulsoup
-- pyquery（类似jquery语法）

4 jupyter 网页记事本








