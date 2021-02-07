---
title: 框架colly和goquery
tags:
  - colly
  - goquery
categories: 爬虫
copyright: true
top: 1
toc: true
sidebar: true
date: 2021-02-06 11:48:18
---
摘要:
    爬虫框架colly
    goquery功能强大
<!-- more -->
### 库colly的get和import

```
get -u github.com/gocolly/colly
import "github.com/gocolly/colly"
```

### 浏览器代理

```
"Opera/9.8(Windows NT 6.1;U;zh-cn)Presto/2.9.168 Version/11.50"

"Mozilla/5.0(Linux;Android6.0;Nexus5Build/MRA58N)AppleWebKit / 537.36(KHTML, likeGecko)Chrome / 75.0.3770.100MobileSafari / 537.36"
"Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/535.1 (KHTML, like Gecko) Chrome/14.0.835.163 Safari/535.1",
                     "Mozilla/5.0 (Windows NT 6.1; WOW64; rv:6.0) Gecko/20100101 Firefox/6.0",
                     "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/534.50 (KHTML, like Gecko) Version/5.1 Safari/534.50",
                     "Opera/9.80 (Windows NT 6.1; U; zh-cn) Presto/2.9.168 Version/11.50",
                     "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1; SV1)"
                     "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/58.0.3029.110 Safari/537.36 Edge/16.16299"

```



## 库colly的Collector结构

```
type Collector struct {
   UserAgent string
   MaxDepth int
   AllowedDomains []string
   DisallowedDomains []string
   DisallowedURLFilters []*regexp.Regexp
   URLFilters []*regexp.Regexp
   AllowURLRevisit bool
   MaxBodySize int
   CacheDir string
   IgnoreRobotsTxt bool
   Async bool
   ParseHTTPErrorResponse bool
   ID uint32
   DetectCharset bool
   RedirectHandler func(req *http.Request, via []*http.Request) error
   CheckHead         bool
   store             storage.Storage
   debugger          debug.Debugger
   robotsMap         map[string]*robotstxt.RobotsData
   htmlCallbacks     []*htmlCallbackContainer
   xmlCallbacks      []*xmlCallbackContainer
   requestCallbacks  []RequestCallback
   responseCallbacks []ResponseCallback
   errorCallbacks    []ErrorCallback
   scrapedCallbacks  []ScrapedCallback
   requestCount      uint32
   responseCount     uint32
   backend           *httpBackend
   wg                *sync.WaitGroup
   lock              *sync.RWMutex
}
```

## 库colly的其他结构

```
type RequestCallback func(*Request)
type ResponseCallback func(*Response)
type HTMLCallback func(*HTMLElement)
type XMLCallback func(*XMLElement)
type ErrorCallback func(*Response, error)
type ScrapedCallback func(*Response)
type ProxyFunc func(*http.Request) (*url.URL, error)
```



## 库colly的NewCollector函数

```
func NewCollector(options ...func(*Collector)) *Collector {
	c := &Collector{}
	c.Init()
	for _, f := range options {
		f(c)
	}
	c.parseSettingsFromEnv()
	return c
}
```

## 库colly的UserAgent函数

```
func UserAgent(ua string) func(*Collector) {
   return func(c *Collector) {
      c.UserAgent = ua
   }
}
```

## 库colly的MaxDepth函数

```
func MaxDepth(depth int) func(*Collector) {
   return func(c *Collector) {
      c.MaxDepth = depth
   }
}
```

## 库colly的AllowedDomains函数

```
func AllowedDomains(domains ...string) func(*Collector) {
   return func(c *Collector) {
      c.AllowedDomains = domains
   }
}
```

## 库colly的ParseHTTPErrorResponse函数

```
func ParseHTTPErrorResponse() func(*Collector) {
   return func(c *Collector) {
      c.ParseHTTPErrorResponse = true
   }
}
```

## 库colly的DisallowedURLFilters函数

```
func DisallowedURLFilters(filters ...*regexp.Regexp) func(*Collector) {
   return func(c *Collector) {
      c.DisallowedURLFilters = filters
   }
}
```

## 库colly的URLFilters函数

```
func URLFilters(filters ...*regexp.Regexp) func(*Collector) {
   return func(c *Collector) {
      c.URLFilters = filters
   }
}
```

## 库colly的AllowURLRevisit函数

```
func AllowURLRevisit() func(*Collector) {
   return func(c *Collector) {
      c.AllowURLRevisit = true
   }
}
```

## 库colly的MaxBodySize函数

```
func MaxBodySize(sizeInBytes int) func(*Collector) {
   return func(c *Collector) {
      c.MaxBodySize = sizeInBytes
   }
}
```

## 库colly的CacheDir函数

```
func CacheDir(path string) func(*Collector) {
   return func(c *Collector) {
      c.CacheDir = path
   }
}
```

库colly的CacheDir函数

```
func IgnoreRobotsTxt() func(*Collector) {
   return func(c *Collector) {
      c.IgnoreRobotsTxt = true
   }
}
```

库colly的CacheDir函数

```
func ID(id uint32) func(*Collector) {
   return func(c *Collector) {
      c.ID = id
   }
}
```

## 库colly的Async函数

```
func Async(a bool) func(*Collector) {
   return func(c *Collector) {
      c.Async = a
   }
}
```

## 库colly的DetectCharset函数

```
func DetectCharset() func(*Collector) {
   return func(c *Collector) {
      c.DetectCharset = true
   }
}
```

## 库colly的Debugger函数

```
func Debugger(d debug.Debugger) func(*Collector) {
   return func(c *Collector) {
      d.Init()
      c.debugger = d
   }
}
```

## 库colly的HTMLElement结构和方法

```
type HTMLElement struct {
   Name       string
   Text       string
   attributes []html.Attribute
   Request *Request
   Response *Response
   DOM *goquery.Selection
   Index int
}
```

## 库colly的NewHTMLElementFromSelectionNode函数

```
func NewHTMLElementFromSelectionNode(resp *Response, s *goquery.Selection, n *html.Node, idx int) *HTMLElement {
   return &HTMLElement{
      Name:       n.Data,
      Request:    resp.Request,
      Response:   resp,
      Text:       goquery.NewDocumentFromNode(n).Text(),
      DOM:        s,
      Index:      idx,
      attributes: n.Attr,
   }
}
```

## 库colly的HTMLElement结构Attr方法

```
func (h *HTMLElement) Attr(k string) string {
   for _, a := range h.attributes {
      if a.Key == k {
         return a.Val
      }
   }
   return ""
}
```

## 库colly的HTMLElement结构ChildText方法

```
func (h *HTMLElement) ChildText(goquerySelector string) string {
   return strings.TrimSpace(h.DOM.Find(goquerySelector).Text())
}
```

## 库colly的HTMLElement结构ChildAttr方法

```
func (h *HTMLElement) ChildAttr(goquerySelector, attrName string) string {
   if attr, ok := h.DOM.Find(goquerySelector).Attr(attrName); ok {
      return strings.TrimSpace(attr)
   }
   return ""
}
```

## 库colly的HTMLElement结构ChildAttrs方法

```
func (h *HTMLElement) ChildAttrs(goquerySelector, attrName string) []string {
   var res []string
   h.DOM.Find(goquerySelector).Each(func(_ int, s *goquery.Selection) {
      if attr, ok := s.Attr(attrName); ok {
         res = append(res, strings.TrimSpace(attr))
      }
   })
   return res
}
```

## 库colly的HTMLElement结构ForEach方法

```
func (h *HTMLElement) ForEach(goquerySelector string, callback func(int, *HTMLElement)) {
   i := 0
   h.DOM.Find(goquerySelector).Each(func(_ int, s *goquery.Selection) {
      for _, n := range s.Nodes {
         callback(i, NewHTMLElementFromSelectionNode(h.Response, s, n, i))
         i++
      }
   })
}
```

## 库colly的HTMLElement结构ForEachWithBreak方法

```
func (h *HTMLElement) ForEachWithBreak(goquerySelector string, callback func(int, *HTMLElement) bool) {
   i := 0
   h.DOM.Find(goquerySelector).EachWithBreak(func(_ int, s *goquery.Selection) bool {
      for _, n := range s.Nodes {
         if callback(i, NewHTMLElementFromSelectionNode(h.Response, s, n, i)) {
            i++
            return true
         }
      }
      return false
   })
}
```



## 库colly的结构Collector的Init方法

```
func (c *Collector) Init() {
   c.UserAgent = "colly - https://github.com/gocolly/colly"
   c.MaxDepth = 0
   c.store = &storage.InMemoryStorage{}
   c.store.Init()
   c.MaxBodySize = 10 * 1024 * 1024
   c.backend = &httpBackend{}
   jar, _ := cookiejar.New(nil)
   c.backend.Init(jar)
   c.backend.Client.CheckRedirect = c.checkRedirectFunc()
   c.wg = &sync.WaitGroup{}
   c.lock = &sync.RWMutex{}
   c.robotsMap = make(map[string]*robotstxt.RobotsData)
   c.IgnoreRobotsTxt = true
   c.ID = atomic.AddUint32(&collectorCounter, 1)
}
```

库colly的结构Collector的Appengine方法

```
func (c *Collector) Appengine(ctx context.Context) {
   client := urlfetch.Client(ctx)
   client.Jar = c.backend.Client.Jar
   client.CheckRedirect = c.backend.Client.CheckRedirect
   client.Timeout = c.backend.Client.Timeout

   c.backend.Client = client
}
```





```
func (c *Collector) Visit(URL string) error {
   if c.CheckHead {
      if check := c.scrape(URL, "HEAD", 1, nil, nil, nil, true); check != nil {
         return check
      }
   }
   return c.scrape(URL, "GET", 1, nil, nil, nil, true)
}
```



```
func (c *Collector) Head(URL string) error {
   return c.scrape(URL, "HEAD", 1, nil, nil, nil, false)
}
```



```
func (c *Collector) Post(URL string, requestData map[string]string) error {
   return c.scrape(URL, "POST", 1, createFormReader(requestData), nil, nil, true)
}
```



```
func (c *Collector) PostRaw(URL string, requestData []byte) error {
   return c.scrape(URL, "POST", 1, bytes.NewReader(requestData), nil, nil, true)
}
```



```
func (c *Collector) PostMultipart(URL string, requestData map[string][]byte) error {
   boundary := randomBoundary()
   hdr := http.Header{}
   hdr.Set("Content-Type", "multipart/form-data; boundary="+boundary)
   hdr.Set("User-Agent", c.UserAgent)
   return c.scrape(URL, "POST", 1, createMultipartReader(boundary, requestData), nil, hdr, true)
}
```



```
func (c *Collector) Request(method, URL string, requestData io.Reader, ctx *Context, hdr http.Header) error {
   return c.scrape(URL, method, 1, requestData, ctx, hdr, true)
}
```



```
func (c *Collector) SetDebugger(d debug.Debugger) {
   d.Init()
   c.debugger = d
}
```



```
func (c *Collector) OnResponse(f ResponseCallback) {
   c.lock.Lock()
   if c.responseCallbacks == nil {
      c.responseCallbacks = make([]ResponseCallback, 0, 4)
   }
   c.responseCallbacks = append(c.responseCallbacks, f)
   c.lock.Unlock()
}
```



```
func (c *Collector) OnHTML(goquerySelector string, f HTMLCallback) {
   c.lock.Lock()
   if c.htmlCallbacks == nil {
      c.htmlCallbacks = make([]*htmlCallbackContainer, 0, 4)
   }
   c.htmlCallbacks = append(c.htmlCallbacks, &htmlCallbackContainer{
      Selector: goquerySelector,
      Function: f,
   })
   c.lock.Unlock()
}
```



```
func (c *Collector) OnError(f ErrorCallback) {
   c.lock.Lock()
   if c.errorCallbacks == nil {
      c.errorCallbacks = make([]ErrorCallback, 0, 4)
   }
   c.errorCallbacks = append(c.errorCallbacks, f)
   c.lock.Unlock()
}
```



```
func (c *Collector) SetProxy(proxyURL string) error {
   proxyParsed, err := url.Parse(proxyURL)
   if err != nil {
      return err
   }

   c.SetProxyFunc(http.ProxyURL(proxyParsed))

   return nil
}
```



```
func (c *Collector) Clone() *Collector {
   return &Collector{
      AllowedDomains:         c.AllowedDomains,
      AllowURLRevisit:        c.AllowURLRevisit,
      CacheDir:               c.CacheDir,
      DetectCharset:          c.DetectCharset,
      DisallowedDomains:      c.DisallowedDomains,
      ID:                     atomic.AddUint32(&collectorCounter, 1),
      IgnoreRobotsTxt:        c.IgnoreRobotsTxt,
      MaxBodySize:            c.MaxBodySize,
      MaxDepth:               c.MaxDepth,
      DisallowedURLFilters:   c.DisallowedURLFilters,
      URLFilters:             c.URLFilters,
      ParseHTTPErrorResponse: c.ParseHTTPErrorResponse,
      UserAgent:              c.UserAgent,
      store:                  c.store,
      backend:                c.backend,
      debugger:               c.debugger,
      Async:                  c.Async,
      RedirectHandler:        c.RedirectHandler,
      errorCallbacks:         make([]ErrorCallback, 0, 8),
      htmlCallbacks:          make([]*htmlCallbackContainer, 0, 8),
      xmlCallbacks:           make([]*xmlCallbackContainer, 0, 8),
      scrapedCallbacks:       make([]ScrapedCallback, 0, 8),
      lock:                   c.lock,
      requestCallbacks:       make([]RequestCallback, 0, 8),
      responseCallbacks:      make([]ResponseCallback, 0, 8),
      robotsMap:              c.robotsMap,
      wg:                     &sync.WaitGroup{},
   }
}
```

## 库goquery的Document结构

```
type Document struct {
   *Selection
   Url      *url.URL
   rootNode *html.Node
}
```

## 库goquery的Selection结构

```
type Selection struct {
   Nodes    []*html.Node
   document *Document
   prevSel  *Selection
}
```

## 库goquery的NewDocumentFromNode函数

```
func NewDocumentFromNode(root *html.Node) *Document {
   return newDocument(root, nil)
}
```

库goquery的Document结构

```
func NewDocument(url string) (*Document, error) {
   // Load the URL
   res, e := http.Get(url)
   if e != nil {
      return nil, e
   }
   return NewDocumentFromResponse(res)
}
```

库goquery的Document结构

```
func NewDocumentFromReader(r io.Reader) (*Document, error) {
   root, e := html.Parse(r)
   if e != nil {
      return nil, e
   }
   return newDocument(root, nil), nil
}
```

库goquery的Document结构

```
func NewDocumentFromResponse(res *http.Response) (*Document, error) {
   if res == nil {
      return nil, errors.New("Response is nil")
   }
   defer res.Body.Close()
   if res.Request == nil {
      return nil, errors.New("Response.Request is nil")
   }

   // Parse the HTML into nodes
   root, e := html.Parse(res.Body)
   if e != nil {
      return nil, e
   }

   // Create and fill the document
   return newDocument(root, res.Request.URL), nil
}
```

库goquery的Document结构

```
func CloneDocument(doc *Document) *Document {
   return newDocument(cloneNode(doc.rootNode), doc.Url)
}
```

库goquery的Document结构

```
func newDocument(root *html.Node, url *url.URL) *Document {
   // Create and fill the document
   d := &Document{nil, url, root}
   d.Selection = newSingleSelection(root, d)
   return d
}
```

## 库goquery的Document结构

```
func newEmptySelection(doc *Document) *Selection {
   return &Selection{nil, doc, nil}
}
```

## 库goquery的newSingleSelection函数

```
func newSingleSelection(node *html.Node, doc *Document) *Selection {
   return &Selection{[]*html.Node{node}, doc, nil}
}
```

## 库goquery的Matcher结构

```
type Matcher interface {
   Match(*html.Node) bool
   MatchAll(*html.Node) []*html.Node
   Filter([]*html.Node) []*html.Node
}
```

## 库goquery的compileMatcher函数

```
func compileMatcher(s string) Matcher {
   cs, err := cascadia.Compile(s)
   if err != nil {
      return invalidMatcher{}
   }
   return cs
}
```

## 库goquery的invalidMatcher结构

```
type invalidMatcher struct{}
func (invalidMatcher) Match(n *html.Node) bool             { return false }
func (invalidMatcher) MatchAll(n *html.Node) []*html.Node  { return nil }
func (invalidMatcher) Filter(ns []*html.Node) []*html.Node { return nil }
```

## 库goquery的常用选择器的语法

```
Find("#id")是id="id"的元素
Find(".class")是所有class="class"的元素
Find("p")所有<p>元素
Find("div[lang]")筛选lang属性的div元素
Find("div[lang=zh]")筛选lang属性为zh的div元素
Find("div[lang!=zh")筛选lang属性不等于zh的div元素
Find("div[lang|=zh")筛选lang属性为zh或zh开头的div元素
Find("div[lang*=zh")筛选lang属性包含zh的div元素
Find("div[lang~=zh")筛选lang属性包含zh这个单词的div元素,单词以空格分开
Find("div[lang$=zh")筛选lang属性以zh结尾的div元素,区分大小写
Find("div[lang^=zh")筛选lang属性以zh开头的div元素,区分大小写
```

## 库colly的回调函数

```
OnRequest 在发起请求前被调用
OnError 请求过程中如果发生错误被调用
OnResponse 收到回复后被调用
OnHTML 在OnResponse之后被调用，如果收到的内容是HTML
OnScraped 在OnHTML之后被调用
```

## 库goquery

```
//官方文档
http://godoc.org/github.com/PuerkitoBio/goquery
//创建文档
d,e := goquery.NewDocumentFromReader(reader io.Reader)
d,e := goquery.NewDocument(url string)
//查找内容
ele.Find("#title") //根据id查找
ele.Find(".title") //根据class查找
ele.Find("h2").Find("a") //链式调用
//获取内容
ele.Html()
ele.Text()
//获取属性
ele.Attr("href")
ele.AttrOr("href", "")
//遍历
ele.Find(".item").Each(func(index int, ele *goquery.Selection){   
})
```

库goquery的find例子

```
//要获取用户的 BIO,首先用 Chrome 定位到对应的 HTML
//<span class="bio" title="和知乎在一起">和知乎在一起</span> 
doc.Find("span.bio")
//一个选择器对应多个结果，可以使用 First(), Last(), Eq(index //int), Slice(start, end int)这些方法进一步定位
```

```
<div class="profile-navbar clearfix">
<a class="item " href="/people/jixin/asks">提问<span class="num">1336</span></a> <a class="item " href="/people/jixin/answers">回答<span class="num">785</span></a> <a class="item " href="/people/jixin/posts">文章<span class="num">91</span></a> <a class="item " href="/people/jixin/collections">收藏<span class="num">44</span></a> <a class="item " href="/people/jixin/logs">公共编辑<span class="num">51648</span></a> </div> 
//如果要定位找到回答数，对应的 go 代码是：
doc.Find("div.profile-navbar").Find("span.num").Eq(1)
//属性操作
text := doc.Find("div.profile-navbar").Find("span.num").Eq(1).Text()    // "785"
```



```
获取属性值也很容易，有两个方法：
Attr(attrName string) (val string, exists bool): 返回属性值和该属性是否存在，类似从 map中取值
AttrOr(attrName, defaultValue string) string: 和上一个方法类似，区别在于如果属性不存在，则返回给定的默认值
```



## 库goquery 的迭代方法

```
Each(f func(int, *Selection)) *Selection: 其中函数 f 的第一个参数是当前的下标，第二个参数是当前的节点
EachWithBreak(f func(int, *Selection) bool) *Selection: 和 Each 类似，增加了中途跳出循环的能力，当 f 返回 false 时结束迭代
Map(f func(int, *Selection) string) (result []string): f 的参数与上面一样，返回一个 string 类型，最终返回 []string.
```

## 库goquery的元素的选择器

```
package main
func main() {
	html := `<body>
				<div>DIV1</div>
				<div>DIV2</div>
				<span>SPAN</span>
			</body>`
	dom, _ := goquery.NewDocumentFromReader(strings.NewReader(html))
	dom.Find("div").Each(func(i int, selection *goquery.Selection) {
		fmt.Println(selection.Text())
	})
}
```

## 库goquery的ID 选择器

```
dom.Find("#div1").Each(func(i int, selection *goquery.Selection) {
		fmt.Println(selection.Text())
	})
```

## 库goquery的Element ID 选择器

```
dom.Find("div#div1").Each(func(i int, selection *goquery.Selection) {
		fmt.Println(selection.Text())
	})
```

## 库goquery的Class选择器

```
dom.Find(".name").Each(func(i int, selection *goquery.Selection) {
		fmt.Println(selection.Text())
	})
```

## 库goquery的Element Class 选择器

```

```

## 库goquery的属性选择器

```
dom.Find("div[class]").Each(func(i int, selection *goquery.Selection) {
		fmt.Println(selection.Text())
	})
```

```
dom.Find("div[class=name]").Each(func(i int, selection *goquery.Selection) {
	fmt.Println(selection.Text())
})
```

```
package main
func main() {
	html := `<body>
				<script type="application/ld+json">JSDKK</script>
			</body>`
	dom, _ := goquery.NewDocumentFromReader(strings.NewReader(html))	dom.Find("script[type='application/ld+json']").Each(func(i int, selection *goquery.Selection) {
		fmt.Println(selection.Text())
	})
}
```

## 库goquery的parent>child选择器

```
dom.Find("body>div").Each(func(i int, selection *goquery.Selection) {
		fmt.Println(selection.Text())
	})
```

```
   dom.Find("body div").Each(func(i int, selection *goquery.Selection) {
		fmt.Println(selection.Text())
	})
```

## 库goquery的prev+next相邻选择器

```
dom.Find("div[lang=zh]+p").Each(func(i int, selection *goquery.Selection) {
    		fmt.Println(selection.Text())
```

## 库goquery的prev~next选择器

```
dom.Find("div[lang=zh]~p").Each(func(i int, selection *goquery.Selection) {
	fmt.Println(selection.Text())
})
```

## 库goquery的内容过滤器

```
dom.Find("div:contains(DIV2)").Each(func(i int, selection *goquery.Selection) {
	fmt.Println(selection.Text())
})
```

```
  dom.Find("span:has(div)").Each(func(i int, selection *goquery.Selection) {
		fmt.Println(selection.Text())
	})	
//Find(":empty")
```

## 库goquery的:first-child过滤器

```
dom.Find("div:first-child").Each(func(i int, selection *goquery.Selection) {
    		fmt.Println(selection.Html())
    	})
```

## 库goquery的:first-of-type过滤器

```
dom.Find("div:first-of-type").Each(func(i int, selection *goquery.Selection) {
    		fmt.Println(selection.Html())
    	})
```

## 库goquery的:last-child 和 :last-of-type过滤器

```
这两个正好和上面的:first-child、:first-of-type相反，表示最后一个，
```

## 库goquery的:nth-child(n) 过滤器

```
dom.Find("div:nth-child(3)").Each(func(i int, selection *goquery.Selection) {
    		fmt.Println(selection.Html())
    	})
```

## 库goquery的:nth-of-type(n) 过滤器

```
:nth-of-type(n)和 :nth-child(n)类似，只不过它表示的是同类型元素的第n个,所以:nth-of-type(1)和
:first-of-type是相等的
```

## :nth-last-child(n) 和:nth-last-of-type(n) 过滤器

```
这两个和上面的类似，只不过是倒序开始计算的，最后一个元素被当成了第一个。
```

## 库goquery的:only-child 过滤器

```
dom.Find("div:only-child").Each(func(i int, selection *goquery.Selection) {
    		fmt.Println(selection.Html())
    	})
```

## 库goquery的:only-of-type 过滤器

```
可以使用Find(":only-of-type"),因为它是它的父元素中，唯一的div元素，这就是
:only-of-type过滤器所要做的，同类型元素只要只有一个，就可以被筛选出来。
```

## 库goquery的选择器或(|)运算

筛选出`div,span`等元素怎么办？这时候可以采用多个选择器进行组合使用，并且以`逗号(,)分割`，`Find("selector1, selector2, selectorN")`表示，只要满足其中一个选择器就可以被筛选出来，也就是`选择器的或(|)运算操作`。

```
package main
func main() {
	html := `<body>
    				<div lang="zh">DIV1</div>
    				<span>
    					<div>DIV5</div>
    				</span>
    			</body>`
	dom,_:=goquery.NewDocumentFromReader(strings.NewReader(html))
	dom.Find("div,span").Each(func(i int, selection *goquery.Selection) {
		fmt.Println(selection.Html())
	})
}
```

## 库colly的爬链接

```
// 对每个具有href属性的元素调用回调
	c.OnHTML("a[href]", func(e *colly.HTMLElement) {
   link := e.Attr("href")
   fmt.Printf("Link found: %s -> %s\n", e.Text, link)
   // 在页面上找到访问链接
   // 只有那些在alloweddomain中的链接才会被访问
   //c.Visit(e.Request.AbsoluteURL(link))
})
// 在请求之前会调用该方法，打印出请求地址
c.OnRequest(func(r *colly.Request) {
   fmt.Println("Visiting", r.Body)
})
```

## 库colly的设置随机延迟

```
c.Limit(&colly.LimitRule{
   DomainGlob:  "*",
   RandomDelay: 1 * time.Second,
})
```

## 库colly的随机user agent

```
c := colly.NewCollector(func(c *colly.Collector) {
		extensions.RandomUserAgent(c) // 设置随机头
		c.Async=true
	},
//过滤url,去除不是https://movie.douban.com/top...的url
		colly.URLFilters(
			regexp.MustCompile("^(https://movie\\.douban\\.com/top250)\\?start=[0-9].*&filter="),
		),
	) 
```

## 库colly的提取页面链接

```
c.OnHTML("a[href]", func(e *colly.HTMLElement) {
		link := e.Attr("href")
		//fmt.Printf("find link: %s\n", e.Request.AbsoluteURL(link))
		c.Visit(e.Request.AbsoluteURL(link))
	})
```

## 库colly的Header获取

```
c.OnRequest(func(r *colly.Request) {
   fmt.Printf("%+v\r\n%+v\r\n", *r, *(r.Headers))
})
```

