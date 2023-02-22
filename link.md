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
历史：https://www.jianshu.com/p/e464970ae18a<br>
文本相关知识：http://www.cncsto.com/article/11509<br>
有用的：https://source.chromium.org/chromium/chromium/src/+/main:third_party/blink/renderer/platform/fonts/;bpv=0;bpt=0
>谷歌开源浏览器chromium：[GitHub](https://github.com/chromium/chromium)、[GoogleGit](https://chromium.googlesource.com/chromium/src/+/HEAD/third_party/blink/)<br>
[blink-代码浏览^_^](https://source.chromium.org/chromium/chromium/src/+/main:third_party/blink/;bpv=1;bpt=0)<br>
[chromium官方文档](https://chromium.sourceforge.net/doc/)<br>
[WebKit->blink](http://devrel.zoomquiet.top/data/20190813173638/index.html)

[pixso.emoji图片来源](https://www.unicode.org/emoji/charts/full-emoji-list.html)

[UNICODE EMOJI](http://www.unicode.org/reports/tr51/#Subject_Emoji_Modifiers)<br>
[Emoji 识别与过滤](https://mupceet.com/2018/07/emoji-distinguish&filter/)<br>
[emoji 字符的识别与过滤](https://blog.csdn.net/LawssssCat/article/details/103435633)<br>
[匹配emoji的正则表达式](https://ihateregex.io/expr/emoji/)<br>
[emoji-released](http://unicode.org/emoji/charts/emoji-released.html)<br>
[emoji-test.txt](https://unicode.org/Public/emoji/11.0/)

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
不要强调问题，尝试推进问题，解决问题 & 正视问题

# 其它
## [skia编译](https://skia.org/docs/user/build/)
```
// llvm信息
C:\LLVM\bin> .\clang.exe - v
Target: x86_64-pc-windows-msvc
Thread model: posix
InstalledDir: C:\LLVM\bin

// 编译指令
bin/gn.exe gen out/share --args='is_debug=true is_official_build=false is_component_build=true skia_use_system_libjpeg_turbo=false skia_use_libwebp_encode = false skia_use_system_freetype2=false skia_use_system_libpng=false skia_use_system_libwebp=false skia_use_system_zlib=false skia_use_system_icu=false skia_use_freetype = true skia_use_system_harfbuzz=false skia_enable_fontmgr_win = false skia_enable_fontmgr_win_gdi = false skia_use_expat = true skia_use_system_expat = false skia_enable_fontmgr_android = false skia_use_dng_sdk = false skia_enable_fontmgr_custom_directory = true  clang_win = ""C:\LLVM""'
```

[CMake 入门实战](https://www.hahack.com/codes/cmake/)

#### C++招聘目标2023.02.06
| 经验要求 | 组内定位 | 备注 |
| :---:| :---: | :---: |
几何图形 | 拉伸、矢量、路径、布尔运算 | 矩阵、向量、2D、3D、平移、旋转、缩放，CAD、三维建模
大规模重构经验 | 协同管理、撤销重做、UI交互、测试代码等局部重构 | 代码设计、设计模式、设计原则、架构原则 
内存优化、性能优化 | 大文档打开，业务侧性能瓶颈突破 | 内存管理、内存优化
文本 | bug修改&重构，文本、字体底层编辑器相关，语言学相关 | 
调试工具开发、研发效率改进 | 


[简书CMake](https://www.jianshu.com/p/cbee27847638)
[CMake-Best-Practices](https://github.com/PacktPublishing/CMake-Best-Practices)
[CMake-Best-Practices翻译](https://github.com/xiaoweiChen/CMake-Best-Practices)
