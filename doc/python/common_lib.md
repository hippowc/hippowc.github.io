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
#构造登录请求，其中注意这个header是一个dict，而data是一个字符串，并且不是简单的json字符串，是使用&连接的参数字符串
req = urllib.request.Request(login_url, headers = headers, data = post_data)
#构造cookie 在python 2.7中 为cookielib模块
cookie = http.cookiejar.CookieJar()
#由cookie构造opener
opener = urllib.request.build_opener(urllib.request.HTTPCookieProcessor(cookie))
#发送登录请求，此后这个opener就携带了cookie，以证明自己登录过
resp = opener.open(req)

另一个urlib
import urllib
urlretrieve(url, filename, reporthook) 下载文件

其他详细内容：https://docs.python.org/2.7/library/urllib2.html

另外请求库可以使用requests这样的三方库，不过自带的已经可以解决所有问题了，可能三方库用起来更方便一点

2 selenium 驱动浏览器进行网站访问
使用pip安装，pip install selenium，
然后根据你要使用的浏览器下载相应的驱动，对于mac linux，下载后将驱动放在PATH下，例如/usr/bin下
from selenium import webdriver
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.common.action_chains import ActionChains
#使用谷歌浏览器访问
driver = webdriver.Chrome()
driver.get('http://www.baidu.com')
driver.quit() 退出
driver.switch_to_window（） #转换到某个窗口
#查找元素，几个例子
ele = driver.find_element_by_name('wd')
driver.find_element_by_link_text('登录').click()  #点击登录按钮  
driver.find_element_by_id('inputuin').clear()   #清除用户名文本框  
driver.find_element_by_id('inputuin').send_keys('li.yan@munrocn.com')  #向用户名中输入内容  
#使用元素
ele.text, ele.size, ele.get_attribute('')获取元素
ele.click() 点击事件
ele.clear() 清除内容
ele.send_keys() 输入内容
#鼠标操作
perform()   执行所有ActionChains中存储的行为
context_click()  右击
double_click()   双击
drag_and_drop(source,target)  拖拽：其中source为拖动的原元素，target为释放的目标元素
move_to_element() 鼠标悬浮
ActionChains.context_click(element).perform()
#一些连接到已打开浏览器的尝试
http://www.spiderpy.cn/blog/detail/36


3 解析库
import re   #正则表达式
一般网页的解析都使用正则匹配
re 模块主要这几个方法：match，search，findall，finditer,split ,sub：有两个参数：前一个为模式（也是一个字符串），后一个为要进行处理的字符串。
也可以通过先编译再匹配的方式：p = re.compile("pstr"), p.match, p.findall ...
match：用于查看该字符串是否符合该正则表达式，从最开始开始匹配， 用于判断字符串开头或整个字符串是否匹配,速度快.
search：查找这个字符串是否有匹配这个模式的部分
findall：查找这个字符串所有匹配模式的部分
split：按照模式分割字符串

--正则相关：
^ $: 开始和结尾
*：0个或多个前面的字符
+：1个或多个前面的字符
？：0个或1个前面的字符，如果跟在*或+后，表示非贪婪匹配，匹配尽量少字符
{}： 匹配指定次数 {1}. {1,3}

[]: 内部任一字符
[^]: 非内部任一字符
\d: 匹配数字
\D: 非数字
\w: 数字字符下划线
\W: 非数字字符下划线

三方库：
-- lxml
-- beautifulsoup
-- pyquery（类似jquery语法）

4 jupyter 网页记事本

5 python标准库，可以去网站直接查询：https://docs.python.org/2.7/library/index.html

6 PIL python image lib
用于处理图片的python库






