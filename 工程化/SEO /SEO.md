| 落地专项                                                  | 特征                                                         | 优化方向                                                     | 优先级 |
| --------------------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------ |
| Sitemap 配置                                              | 周期长，主要用于时效性较低且相对稳定页面的录入               | 目的：**确保录入** **url** **的正确性**完善本地 url 检测机制；完成 sitemap 每次上线的飞书提示功能； | P1     |
| Index Api                                                 | 周期短，对于存在很高热点和时效性的页面使用 Index Api 进行录入 | 目的：将时效性高的热点页面快速录入从而能够被检索Google api sdk 的调研与引入 | P0     |
| [Url query 管理](https://ahrefs.com/blog/url-parameters/) | 每一个网页可能都会携带 query，但是某些 query 对于页面内容并无影响，所以应该舍弃 | 统一对重点页面的 query进行梳理，在 GCS 中完成配置            | P1     |
| 已录入 Url 管理                                           | 已收录 Url 中可能会存在已经废弃的死链                        | 盘点已经废弃的 404 链接，从索引中删除或者在 TLB 层配置重定向 | P2     |
| 视频/图片资源管理                                         | CC 较多页面以图片与视频为主，但是目前这块多媒体的搜索能力较低 | 优化视频/图片等多媒体索引方案完善图片/视频相关标签属性配置   | P0     |

Meta 配置：

<meta name="keywords" content="ad,tiktok,creator,market,marketplace,tiktok creator,TCM,tiktok marketplace,marketing,advertising"/>

<meta name="description" content="The official platform for brand and creator collaborations on TikTok.Meet the most popular creators in TikTok.Tap into TikTok’s exclusive first-party insights on audience demos, growth trends, best-performing videos and much more."/>

