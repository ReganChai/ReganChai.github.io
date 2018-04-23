## 一、开发环境  
  - Windows 7 64位  
  - Python 3.6 
  - Visual Studio 2017

## 二、说明  

 1. 关于Python开发环境搭建，可参考博主的另一篇博文---[Visual Studio 2017搭配OpenCV之Python环境](https://blog.csdn.net/u012319441/article/details/79586755)，省去其中的OpenCV配置及安装即可。另外，在做Python爬虫项目时，需要下载一些特定的Python库，在调用模块出错时，在Python环境中添加库路径即可   

 2. 在本博文中，url为：[http://www.runoob.com/python3/python3-tutorial.html](http://www.runoob.com/python3/python3-tutorial.html)   

 3. 博主提供配套源代码以及爬取到的Python3教程的 pdf 文档，有需要可在本博文末尾传送下载   

 4. 由于博主水平有限，若解读有误造成误导，敬请谅解   

## 三、准备工具

- **requests**: 用于请求网络
- **beautifulsoup**: 用于操作 html 数据
- **wkhtmltopdf**: 用来把获取到的网络的 html 文件转换为 PDF 文件； 其中所使用的 pdfkit 是 wkhtmltopdf 的Python封装包


所以，需要先安装好下面的依赖包：

    pip install requests
    pip install beautifulsoup4
    pip install pdfkit

然后需要在[wkhtmltopdf](http://wkhtmltopdf.org/downloads.html)官网下载稳定版进行安装，安装完成之后把该程序的执行路径加入到系统环境 **$PATH** 变量中（若在安装的时候将选择信息的两个选项打钩，安装过程中会自动将路径添加到环境变量）。


-----------------------------------------------------------------------
## 四、Crawler基类构建

1. 需要导入所需模块： 

		from urllib.parse import urlparse  #用于解析统一资源定位符(URL)
		import requests

2. 使用 `requests` 模块的 get 函数来获取网络响应：

        response = requests.get(url)
        	
	此时，若返回值为 `<Response [200]>` 表示对 http 的请求响应正确，若返回值为 404、503 等则表示请求错误，需要检查 URL 信息是否正确。

3. 使用 `urlparse` 函数解析URL：

		url_components = urlparse(url)

	此时会得到所解析 url 的组成部分。 url 由六部分组成：scheme、 netloc、 path、 params、 query、 fragment，具体可自行百度百科。

	对本文的 url 解析之后如下：

    (scheme='http', netloc='www.runoob.com', path='/python3/python3-tutorial.html', params='', query='', fragment='')

	然后提取所需的信息，并以元组的形式返回：

	 	url_head = url_components.scheme + "://" + url_components.netloc + url_path[0:url_path.rfind('/') + 1]
        url_domain = url_components.scheme + "://" + url_components.netloc + "/"
		return {'url_head':url_head, 'url_domain':url_domain}


--------------------------------------------------------------------------------
## 五、自定义爬虫类MyCrawler构建

1. 导入上一步构建的模块，然后让该类对基类继承：

		from CrawlerBase import Crawler
		class MyCrawler(Crawler):

2. 使用 `super` 对该类初始化，方便使用父类的方法与属性：

		def __init__(self, name, URL):
        	super().__init__(name, URL)

3. 解析目录结构, 获取所有URL目录列表:

		soup = BeautifulSoup(super().Request_Net(self.url).content, "html.parser")
		menu_tag = soup.find(class_="design")
	find 函数的参数为目录的类；在 Google Chrome 浏览器中，在左侧目录栏点击鼠标右键---“检查"，然后就会在右侧鼠标所在部分高亮显示，如图：
![](https://i.imgur.com/RVDMT5Q.png)

4. 使用同样的方法解析对应目录(不同 *url*)下的 *html* 内容(正文以及标题)：

		soup = BeautifulSoup(super().Request_Net(every_url).content, 'html.parser')
        body = soup.find_all(class_="article-intro")    # 正文
        title = soup.find_all('h1')[1].get_text()       # 标题
	需要注意的是，参数 *every_url* 为解析目录列表之后返回的 *url* 

	然后将获取到的每一个目录下的 *html* 内容保存为本地 *html* 文本，并依次排序：

        with open(html_name, 'wb') as f:
            f.write(html)
		return html_name

5. 使用 `pdfkit` 封装包将 *htmls* 文本转换为 *pdf* 文档：

		pdfkit.from_file(htmls, file_name, options = option_template)

-------------------------------------------------------
## 六、运行
1. 以命令行的形式：

		python RunoobToPdf.py

2. 或者在IED环境中直接点击图标 ![](https://i.imgur.com/ssy87tr.png) 运行


## 七、效果图
该程序是在号称宇宙最强的IED **Visual Studio 2017** 环境下编写的，其编辑界面如图：

![](https://i.imgur.com/82l1jWe.png)

爬虫运行完成之后，生成的 **PDF** 文件界面如图：

![](https://i.imgur.com/N3cFMxN.png)


## 八、Other

>Author：Regan_Chai
>
>E-Mail： regan_chai@163.com
>
>GitHub： https://github.com/ReganChai/CrawlerProject
