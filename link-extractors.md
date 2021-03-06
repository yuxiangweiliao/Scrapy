# Link Extractors

Link Extractors 是用于从网页(`scrapy.http.Response`)中抽取会被 follow 的链接的对象。

Scrapy 默认提供 2 种可用的 Link Extractor, 但你通过实现一个简单的接口创建自己定制的 Link Extractor 来满足需求｡ Scrapy 提供了 `scrapy.contrib.linkextractors import LinkExtractor`， 不过您也可以通过实现一个简单的接口来创建您自己的 Link Extractor，满足需求。

每个 LinkExtractor 有唯一的公共方法是 `extract_links`，其接收 一个 Response 对象， 并返回 `scrapy.link.Link` 对象｡ Link Extractors 只实例化一次，其 `extract_links` 方法会根据不同的 response 被调用多次来提取链接｡

Link Extractors 在 CrawlSpider 类(在 Scrapy 可用)中使用, 通过一套规则,但你也可以用它在你的 Spider 中,即使你不是从 CrawlSpider 继承的子类, 因为它的目的很简单: 提取链接｡

## 内置 Link Extractor 参考

Scrapy 自带的 Link Extractors 类在 scrapy.contrib.linkextractors 模块提供｡

默认的 link extractor 是 LinkExtractor ，其实就是 LxmlLinkExtractor:

```
from scrapy.contrib.linkextractors import LinkExtractor
```

在以前版本的 Scrapy 版本中提供了其他的 link extractor，不过都已经被废弃了。

### LxmlLinkExtractor

#### class scrapy.contrib.linkextractors.lxmlhtml.LxmlLinkExtractor(allow=(), deny=(), allow_domains=(), deny_domains=(), deny_extensions=None, restrict_xpaths=(), tags=('a', 'area'), attrs=('href', ), canonicalize=True, unique=True, process_value=None)

LxmlLinkExtractor is the recommended link extractor with handy filtering options. It is implemented using lxml’s robust HTMLParser.

**参数:**    

- allow (a regular expression (or list of)) – a single regular expression (or list of regular expressions) that the (absolute) urls must match in order to be extracted. If not given (or empty), it will match all links.
- deny (a regular expression (or list of)) – a single regular expression (or list of regular expressions) that the (absolute) urls must match in order to be excluded (ie. not extracted). It has precedence over the allow parameter. If not given (or empty) it won’t exclude any links.
- allow_domains (str or list) – a single value or a list of string containing domains which will be considered for extracting the links
- deny_domains (str or list) – a single value or a list of strings containing domains which won’t be considered for extracting the links
- deny_extensions (list) – a single value or list of strings containing extensions that should be ignored when extracting links. If not given, it will default to the IGNORED_EXTENSIONS list defined in the scrapy.linkextractor module.
- restrict_xpaths (str or list) – is a XPath (or list of XPath’s) which defines regions inside the response where links should be extracted from. If given, only the text selected by those XPath will be scanned for links. See examples below.
- tags (str or list) – a tag or a list of tags to consider when extracting links. Defaults to ('a', 'area').
- attrs (list) – an attribute or list of attributes which should be considered when looking for links to extract (only for those tags specified in the tags parameter). Defaults to ('href',)
- canonicalize (boolean) – canonicalize each extracted url (using scrapy.utils.url.canonicalize_url). Defaults to True.
- unique (boolean) – whether duplicate filtering should be applied to extracted links.
- process_value (callable) –

它接收来自扫描标签和属性提取每个值, 可以修改该值, 并返回一个新的，或返回 None 完全忽略链接的功能。如果没有给出，process_value 默认是 lambda x: x。

例如，从这段代码中提取链接:

```
<a href="javascript:goToPage('../other/page.html'); return false">Link text</a>
```

你可以使用下面的这个 `process_value` 函数:

```
def process_value(value):
    m = re.search("javascript:goToPage\('(.*?)'", value)
    if m:
        return m.group(1)
```