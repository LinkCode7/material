# Link's document
## Text module
#### 文本不显示原因
>- 字体大小为0
>- 没有填充颜色或填充颜色与背景色相近（注意文本解析逻辑，涉及默认样式、公共样式、文本块样式覆写场景）
>- 缺失字体且没有字形路径
>- 当前字体不支持文本中的字符
>- 所有图形都不显示，浏览器系统内存占用很高，关掉全部标签页，重启浏览器



#### 字体回退列表

#### 新功能：有序、无序列表（20221101）
>- 核心逻辑：缩进排版、字符绘制，文本宽度
>- 产品需求：序列化与反序列化、协同、常用操作、显示效果（间距、对齐方式）、快捷键、格式兼容
>- 特殊情况：特殊字符的码点，缺失字体场景，字体不支持字符的场景
>- 与软件其它功能的联动：复制粘贴、alt复制、拉伸、等比拉伸、撤销重做、组件实例

#### 打开文档场景下文本不排版
>1. 确保打开文档后能正常显示文本、emoji
>2. 文本编辑前要先排版，处理相关断言
>3. 打开文档不下载字体（下载字体后会触发重新排版）
>4. 打开特殊的文档
>5. 协同
>    - 导入的文档
>    - 导入带emoji的文档
>    - 导入的缺失字体但有GeometryData的文档
>    - 导入的缺失字体且没有GeometryData的文档（通过回退字体列表显示）

#### emoji相关
历史：https://www.jianshu.com/p/e464970ae18a
文本相关知识：http://www.cncsto.com/article/11509
有用的：https://source.chromium.org/chromium/chromium/src/+/main:third_party/blink/renderer/platform/fonts/;bpv=0;bpt=0
>谷歌开源浏览器chromium：[GitHub](https://github.com/chromium/chromium)、[GoogleGit](https://chromium.googlesource.com/chromium/src/+/HEAD/third_party/blink/)、[blink](https://source.chromium.org/chromium/chromium/src/+/main:third_party/blink/;bpv=1;bpt=0)
[chromium官方文档](https://chromium.sourceforge.net/doc/)
[WebKit->blink](http://devrel.zoomquiet.top/data/20190813173638/index.html)

[pixso.emoji图片来源](https://www.unicode.org/emoji/charts/full-emoji-list.html)

[UNICODE EMOJI](http://www.unicode.org/reports/tr51/#Subject_Emoji_Modifiers)
[Emoji 识别与过滤](https://mupceet.com/2018/07/emoji-distinguish&filter/)
[emoji 字符的识别与过滤](https://blog.csdn.net/LawssssCat/article/details/103435633)
[匹配emoji的正则表达式](https://ihateregex.io/expr/emoji/)
[emoji-released](http://unicode.org/emoji/charts/emoji-released.html)

#### unicode
1个字节=8位

[字节、字符、位](https://blog.csdn.net/besmarterbestronger/article/details/97930750)
[UTF8和UTF16编码](https://blog.csdn.net/qq_51409098/article/details/126430723)
[string,wstring,u16string,u32string相互转换](https://blog.csdn.net/qican_7/article/details/96990603)
[string-u16string-u32string转换](https://www.dovov.com/stringu16stringu32string.html)
[ICU Documentation](https://unicode-org.github.io/icu/userguide/conversion/converters.html)


#### Github
[MemoryFlowAnalysis](https://github.com/emojicode/emojicode/tree/master/Compiler/MemoryFlowAnalysis)

#### [静 の 迭代记录](https://boardmix.cn/app/share?token=0Dr10iktRFI4EkWKJWRoB3685j_RmfegznIOLMzcwiqC8XAGsDZR9hA0Akgw7jf3CswK-LlwkFhdHooXbN5_vDLFvhx0O9IWR8C4hhZHwbs=&elementNodeGuid=349:3)

>- 段首输入"数字. "生成有序列表，段首输入"- "生成无序列表
>- 空文本状态下的项目符号一直都有点问题没空去处理
>- breakTextIntoLines()一直没空拆分，写的很乱

- 关键代码
```cpp
// 排版
void SkmTextWrapper::breakTextIntoLines(SkmParagraphImpl* paragrph, SkScalar maxWidth, const AddLineToParagraph& addLine, const AddPartToParagraph& addPart, const std::vector<SkmTextPartStyle>& _partStyle);
// UI
void SkmTextLine::paintBulletBlob(SkCanvas* canvas);
```
- 相关提交
[项目符号初版](https://pxgit.300624.cn/board-client/board-client-app-wasm/-/merge_requests/267/diffs)
[有序列表识别](https://pxgit.300624.cn/board-client/board-client-app-wasm/-/merge_requests/1441/diffs)
[任务列表 + UI优化](https://pxgit.300624.cn/board-client/board-client-app-wasm/-/commit/5cdaee75081805974d85838b19f34b71fd5f9133)

- BoardMix调试
>本地调就先 t 创建一个文本，然后ctrl + q、enter、backspace、delete这些键都能用
无序列表就ctrl + l
有序列表就ctrl + shift + l



## Next
不要强调问题，尝试推进问题，解决问题


## 连接线：经典场景
[init]706978736F2D6B7700020D636F6D70726573733A7A73746428B52FFD60080385120006217649D0F0AA3114AEE0ADF11FC05A9973C9F63DB0B03B31133676271000C7B2C074F2C387FB11ED732466F51EE1D3C259E99CB2F7E57D0F5F359D5FA7CF2518A405900F9E7F2F6972CB94025E005B006100762FC8EEA5EE61578B2B80110EA19E2494801303DB679BEDB3C984DE0FC3DFB020075A0E47B3A20719DC045186869BA9D1E05E7422F5EDDFDFC7EFF17D066116DBBB95C38232EC5E03BFC49F1D25221F2554787AB54BA8FDF0BE29A4943ED5A1FD5D687B27B37D941E805FA25994744BE4E03175A054A7A1D284CD5BD156CEEF95FCFE0F8FBFE3ABF053F8287C1D3FE78373EE601ACF78832F3CC25FCCE29653CCF2EA24A6A6D73038C2EED1FF065CC0041CA232883F3E6029C0148405F6C6EC30CF98ED850961D6B889D80B53ABA6D1697B6384682CC8CAB140B516D4C57912B5C4CE93A808C9C1121D4D12A505AA49F8619CBCC7D2B63F9BFAAA8795561A9ED6FFC47F33A3420AEF85629BF51353EA7F554D9EE02416B16D720631ECD572C15AAFDC8729692186EDB51D91B7DBCA5ED98CD451B0BDB20795AC11B1C25E9B8EB4B151DA5B1E91364921F606E154B206C7F6BE381D596028ED4F451C6F10ACD89F8BCD38D341B13F97A03A9E1CD9FE54325060EA40692F9206E0080D0F7B871400476ED85EDEAC06312060022243470FC5924EBF413231E0B907A43B28CAA098C02A98CB2666356EDFBF02B10797FC75637921223C42AA48C45870172523221BE78D4C4220E57D427064FA5E2D73900A13C25E190D07448C2FA520CD820C0AEDB1DA5188D3085A04AC0F1135E02493EA6DC78284C60C1DA600E1B8119E