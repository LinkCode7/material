# 文本重构2023
## 背景
1. 文本内存过大导致无法打开大文档
2. 打开文档过程中文本排版耗时过长（多次排版）
>文本样式重复占用内存，在组件实例场景下尤其明显


## 核心类图
```mermaid

classDiagram

%% 单个文本的主要成员
PxRichTextContent o-- "1"PxTextDocumentStyle
PxRichTextContent o-- "1"PxTextSegmentStyles

PxTextSegmentStyles --> "n"PxParagraphStyle
PxTextSegmentStyles --> "n"PxCharStyle
PxCharStyle --> StyleData

class PxRichTextContent {
    PxString m_textContent;
    PxTextDocumentStyle m_documentStyle;
    PxTextSegmentStyles m_styles;
}

class PxTextDocumentStyle {
    float m_paragraphSpacing;
    float m_paragraphIndent;

    TextHorizontalAlign m_horizontalAlign;
    TextVerticalAlign   m_verticalAlign;
    TextLayoutMode      m_layoutMode;
}

class PxTextSegmentStyles {
    PxCharStyle      m_endCharStyle;
    PxParagraphStyle m_endParagraphStyle;

    RangedStyles<PxCharStyle>      m_charStyles; // PxRBTree
    RangedStyles<PxParagraphStyle> m_paragraphStyles;
}

class PxCharStyle {
    SharedData m_data;
}
class StyleData {
    std::string   m_fontFamilyName;
    std::string   m_fontStyleName;
    float         m_fontSize;
    LetterCase    m_letterCase;
    LineHeight    m_lineHeight;
    LetterSpacing m_letterSpacing;
}

class PxParagraphStyle {
    int empty;
}

```


## 参考资料
>[红黑树实现细节](https://www.cnblogs.com/skywang12345/p/3245399.html)
>[红黑树实现细节2](https://www.cnblogs.com/skywang12345/p/3624291.html)


### 讨论
>2023-01-30
>1. PxParagraphStyle没有和文档的kiwi结构对应，意味着刷新文档后，里面的数据将丢失
>2. 在整个文档范围内共享文本样式，大部分情况下样式都相同


