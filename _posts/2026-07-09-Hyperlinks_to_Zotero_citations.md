---
title: "Adding internal hyperlinks to Zotero author–year citations in Word"
categories:
  - Zotero
ai_generated: true
---

这篇文章记录一个用于 Word 和 Zotero 的 VBA 宏。它的目标是为正文中的 Zotero **作者–年份** 引用自动添加内部超链接，使读者点击文中引用时，可以跳转到文末参考文献列表中对应的条目。

这个需求主要来自学术论文写作和排版过程。Zotero 可以自动管理文献引用和参考文献列表，但在 Word 中，**作者–年份**格式的正文引用通常只是普通的 Zotero 字段结果，并不会自动链接到文末 bibliography。对于较长论文而言，如果读者需要从正文引用快速定位到参考文献条目，手动查找会比较低效。因此，这个宏的作用是自动识别 Zotero 引用字段、读取其中的文献标题、在参考文献列表中创建书签，并将正文引用指向对应书签。

## 1. 使用场景

这个宏适用于以下情况：

- 文档使用 Zotero 插件插入引用；
- 引用格式为作者–年份格式，例如 `(Smith, 2020)` 或 `(Smith, 2020; Zhang and Li, 2021)`；
- 文末已经生成 Zotero bibliography；
- 希望正文引用可以点击跳转到文末参考文献；
- 希望正文引用的字体显示样式高亮，如变蓝（如果不需要高亮，或者需要显示为其他颜色，可以修改代码 `.Color = wdColorBlue` ，默认颜色改为 `.Color = oldColor` 或者红色 `.Color = wdColorRed`）。

它不适用于已经将 Zotero 字段完全转为纯文本的文档。因为宏需要读取 Zotero 字段代码中的文献信息，如果字段已经被取消链接，宏就无法从 field code 中提取对应的 title。

## 2. 基本原理

Word 中的 Zotero 引用本质上是一个特殊字段。正文中的引用通常包含 `ADDIN ZOTERO_ITEM`，文末参考文献列表通常包含 `ADDIN ZOTERO_BIBL`。宏的基本逻辑是：

1. 在当前 Word 文档中查找 Zotero bibliography；
2. 遍历全文字段，识别所有 Zotero in-text citation；
3. 从 Zotero citation 的 field code 中提取文献标题；
4. 在 bibliography 中搜索对应标题；
5. 给 bibliography 中的参考文献条目添加书签；
6. 给正文中的作者–年份引用添加内部超链接；
7. 保持正文引用的原始样式，避免出现蓝色或下划线。

这个过程并不改变 Zotero 的引用数据本身，而是在 Word 文档层面增加 bookmark 和 hyperlink。因此，它更接近排版增强工具，而不是 Zotero 引用样式修改工具。

## 3. 与数字引用格式宏的区别

很多已有的 Zotero 超链接宏是为数字引用格式设计的，例如：

```text
[3]
[8]–[10]
[2], [4], [5]
```

这类宏通常会在 citation 的 `plainCitation` 中查找方括号和数字，然后把数字部分链接到参考文献条目。

但是作者–年份格式并不遵循这种结构。例如：

```text
(Smith, 2020)
(Smith and Jones, 2021)
(Smith, 2020; Zhang and Li, 2021)
```

因此，原本依赖 `[ ]` 和数字编号的逻辑不能直接使用。作者–年份格式下，更合理的做法是从 Zotero 字段代码中读取每条文献的 `title`，再用 `title` 去 bibliography 中定位对应参考文献条目。

## 4. 宏代码

代码语言是 VBA，即 Visual Basic for Applications。它需要放在 Word 的 VBA 编辑器中运行，而不是在 Python、JavaScript 或普通 Markdown 环境中运行。

{% include code-header.html %}

```vb
Option Explicit

Public Sub CitingHyperlink_AuthorYear()

    Dim nStart As Long, nEnd As Long
    nStart = Selection.Start
    nEnd = Selection.End

    Application.ScreenUpdating = False
    ActiveWindow.View.ShowFieldCodes = False

    Dim f As Field
    Dim bibRange As Range
    Dim idx As Long

    For Each f In ActiveDocument.Fields
        If InStr(1, f.code.Text, "ADDIN ZOTERO_BIBL", vbTextCompare) > 0 Then
            Set bibRange = f.Result.Duplicate
            Exit For
        End If
    Next f

    If bibRange Is Nothing Then
        MsgBox "未找到 Zotero bibliography。请先插入或刷新参考文献列表。", vbExclamation
        GoTo CleanExit
    End If

    For idx = ActiveDocument.Fields.Count To 1 Step -1

        Set f = ActiveDocument.Fields(idx)

        If InStr(1, f.code.Text, "ADDIN ZOTERO_ITEM", vbTextCompare) > 0 Then

            Dim titles As Collection
            Set titles = ExtractTitlesFromZoteroCode(f.code.Text)

            If titles.Count = 0 Then GoTo NextField

            Dim shownText As String
            shownText = f.Result.Text

            Dim segs As Collection
            Set segs = GetCitationSegments(shownText)

            Dim i As Long
            Dim bmName As String
            Dim linkRange As Range

            If titles.Count = 1 Or segs.Count <> titles.Count Then

                bmName = SafeBookmarkName(CStr(titles(1)))

                If EnsureTitleBookmark(ActiveDocument, bibRange, CStr(titles(1)), bmName) Then
                    Set linkRange = f.Result.Duplicate
                    AddInternalHyperlink ActiveDocument, linkRange, bmName
                End If

            Else

                For i = 1 To titles.Count

                    bmName = SafeBookmarkName(CStr(titles(i)))

                    If EnsureTitleBookmark(ActiveDocument, bibRange, CStr(titles(i)), bmName) Then
                        Set linkRange = FindTextInRange(f.Result.Duplicate, CStr(segs(i)))

                        If Not linkRange Is Nothing Then
                            AddInternalHyperlink ActiveDocument, linkRange, bmName
                        End If
                    End If

                Next i

            End If

        End If

NextField:
    Next idx

CleanExit:
    ActiveWindow.View.ShowFieldCodes = False
    Application.ScreenUpdating = True
    ActiveDocument.Range(nStart, nEnd).Select

End Sub

Private Function ExtractTitlesFromZoteroCode(ByVal codeText As String) As Collection

    Dim col As New Collection
    Dim p As Long, q As Long
    Dim title As String

    p = 1

    Do
        p = InStr(p, codeText, """title"":""")

        If p = 0 Then Exit Do

        p = p + Len("""title"":""")
        q = FindJsonStringEnd(codeText, p)

        If q > p Then
            title = Mid$(codeText, p, q - p)
            title = JsonUnescape(title)

            If Len(Trim$(title)) > 0 Then
                col.Add title
            End If

            p = q + 1
        Else
            Exit Do
        End If

    Loop

    Set ExtractTitlesFromZoteroCode = col

End Function

Private Function FindJsonStringEnd(ByVal s As String, ByVal startPos As Long) As Long

    Dim i As Long
    Dim j As Long
    Dim slashCount As Long

    For i = startPos To Len(s)

        If Mid$(s, i, 1) = """" Then

            slashCount = 0
            j = i - 1

            Do While j >= startPos And Mid$(s, j, 1) = "\"
                slashCount = slashCount + 1
                j = j - 1
            Loop

            If slashCount Mod 2 = 0 Then
                FindJsonStringEnd = i
                Exit Function
            End If

        End If

    Next i

    FindJsonStringEnd = 0

End Function

Private Function GetCitationSegments(ByVal shownText As String) As Collection

    Dim col As New Collection
    Dim t As String
    Dim inner As String
    Dim parts() As String
    Dim i As Long
    Dim part As String

    t = Trim$(shownText)

    If Len(t) >= 2 And Left$(t, 1) = "(" And Right$(t, 1) = ")" Then
        inner = Mid$(t, 2, Len(t) - 2)
    Else
        inner = t
    End If

    If InStr(1, inner, ";", vbTextCompare) > 0 Then

        parts = Split(inner, ";")

        For i = LBound(parts) To UBound(parts)
            part = Trim$(parts(i))

            If Len(part) > 0 Then
                col.Add part
            End If
        Next i

    Else

        If Len(inner) > 0 Then
            col.Add inner
        End If

    End If

    Set GetCitationSegments = col

End Function

Private Function EnsureTitleBookmark(ByVal doc As Document, ByVal bibRange As Range, ByVal title As String, ByVal bmName As String) As Boolean

    Dim r As Range

    If Len(Trim$(title)) = 0 Then Exit Function

    If doc.Bookmarks.Exists(bmName) Then
        EnsureTitleBookmark = True
        Exit Function
    End If

    Set r = bibRange.Duplicate

    With r.Find
        .ClearFormatting
        .Text = Left$(title, 250)
        .Replacement.Text = ""
        .Forward = True
        .Wrap = wdFindStop
        .Format = False
        .MatchCase = False
        .MatchWholeWord = False
        .MatchWildcards = False
        .MatchSoundsLike = False
        .MatchAllWordForms = False
    End With

    If r.Find.Execute Then
        doc.Bookmarks.Add Range:=r, Name:=bmName
        EnsureTitleBookmark = True
    Else
        EnsureTitleBookmark = False
    End If

End Function

Private Function FindTextInRange(ByVal baseRange As Range, ByVal textToFind As String) As Range

    Dim r As Range
    Set r = baseRange.Duplicate

    With r.Find
        .ClearFormatting
        .Text = Left$(textToFind, 250)
        .Replacement.Text = ""
        .Forward = True
        .Wrap = wdFindStop
        .Format = False
        .MatchCase = False
        .MatchWholeWord = False
        .MatchWildcards = False
        .MatchSoundsLike = False
        .MatchAllWordForms = False
    End With

    If r.Find.Execute Then
        Set FindTextInRange = r
    Else
        Set FindTextInRange = Nothing
    End If

End Function

Private Sub AddInternalHyperlink(ByVal doc As Document, ByVal anchorRange As Range, ByVal bmName As String)

    Dim shown As String
    Dim oldStyle As Variant
    Dim oldColor As Long
    Dim h As Hyperlink
    Dim r As Range

    shown = anchorRange.Text

    If Len(Trim$(shown)) = 0 Then Exit Sub

    oldStyle = anchorRange.style
    oldColor = anchorRange.Font.Color

    Set h = Nothing

    If anchorRange.Hyperlinks.Count > 0 Then

        Set h = anchorRange.Hyperlinks(1)

        On Error Resume Next
        h.Address = ""
        h.SubAddress = bmName
        h.ScreenTip = "Go to reference"
        Set r = h.Range
        On Error GoTo 0

    Else

        On Error Resume Next
        Set h = doc.Hyperlinks.Add(Anchor:=anchorRange, Address:="", SubAddress:=bmName, ScreenTip:="Go to reference", TextToDisplay:=shown)
        Set r = h.Range
        On Error GoTo 0

    End If

    If r Is Nothing Then Set r = anchorRange

    On Error Resume Next
    r.style = oldStyle

    With r.Font
        .Underline = wdUnderlineNone
        .Color = wdColorBlue

    End With

    On Error GoTo 0

End Sub

Private Function JsonUnescape(ByVal s As String) As String

    s = Replace(s, "\""", """")
    s = Replace(s, "\\", "\")
    s = Replace(s, "\/", "/")
    s = DecodeUnicodeEscapes(s)

    JsonUnescape = s

End Function

Private Function DecodeUnicodeEscapes(ByVal s As String) As String

    Dim p As Long
    Dim hs As String

    p = InStr(1, s, "\u", vbTextCompare)

    Do While p > 0 And p + 5 <= Len(s)

        hs = Mid$(s, p + 2, 4)

        If IsHex4(hs) Then
            s = Left$(s, p - 1) & ChrW$(CLng("&H" & hs)) & Mid$(s, p + 6)
            p = InStr(p + 1, s, "\u", vbTextCompare)
        Else
            p = InStr(p + 2, s, "\u", vbTextCompare)
        End If

    Loop

    DecodeUnicodeEscapes = s

End Function

Private Function IsHex4(ByVal s As String) As Boolean

    Dim i As Long
    Dim c As String

    If Len(s) <> 4 Then Exit Function

    For i = 1 To 4
        c = Mid$(s, i, 1)

        If InStr(1, "0123456789ABCDEFabcdef", c, vbBinaryCompare) = 0 Then
            Exit Function
        End If
    Next i

    IsHex4 = True

End Function

Private Function SafeBookmarkName(ByVal title As String) As String

    Dim base As String
    Dim h As String
    Dim maxBaseLen As Long

    h = SimpleHash(title)
    maxBaseLen = 40 - Len("Z__") - Len(h)

    If maxBaseLen < 1 Then maxBaseLen = 1

    base = Left$(MakeValidBMName_AuthorYear(title), maxBaseLen)

    If Len(base) = 0 Then base = "Ref"

    SafeBookmarkName = "Z_" & base & "_" & h

End Function

Private Function MakeValidBMName_AuthorYear(ByVal strIn As String) As String

    Dim i As Long
    Dim ch As String
    Dim code As Long
    Dim tempStr As String

    strIn = Trim$(strIn)

    For i = 1 To Len(strIn)

        ch = Mid$(strIn, i, 1)
        code = AscW(ch)

        If code < 0 Then code = code + 65536

        Select Case code
            Case 48 To 57, 65 To 90, 95, 97 To 122
                tempStr = tempStr & ch
            Case Else
                tempStr = tempStr & "_"
        End Select

    Next i

    Do While InStr(tempStr, "__") > 0
        tempStr = Replace(tempStr, "__", "_")
    Loop

    If Left$(tempStr, 1) Like "[0-9_]" Then
        tempStr = "A_" & tempStr
    End If

    MakeValidBMName_AuthorYear = tempStr

End Function

Private Function SimpleHash(ByVal s As String) As String

    Dim h As Double
    Dim i As Long
    Dim ch As Long

    h = 0

    For i = 1 To Len(s)
        ch = AscW(Mid$(s, i, 1))

        If ch < 0 Then ch = ch + 65536

        h = h * 131 + ch
        h = h - Int(h / 1000000007#) * 1000000007#
    Next i

    SimpleHash = CStr(CLng(h))

End Function
```