#初めに
QiitaはマークダウンだけでなくHTMLも使うことができます。しかし
`<element style="style">`
のように`style`属性を設定しても無視されるので、スタイルを用いた強調などは出来ませんでした。
しかし、`class`属性や`id`属性などを使えば彩色やサイズ変更などが可能になることが分かりました。
そこでQiitaのCSSをダウンロードし、様々なスタイルを試してみました。
#スタイルの一例
<div class="markdownContent"><div class="highlight"><p class="ne">abdefg</p></div></div>
<p class="adventCalendarBreadcrumb">
<a>abcdefg</a>
</p>
<p class="fa-check-circle-o">abcdefg</p>
<p class="btn-primary"><a class="badge">abcdefg</a></p>
<p class="fa-comment-o">abcdefg</p>
<p class="editorMarkdown_tab">abcdegh</p>
<p class="sharedHeader-public">abcdegh</p>
<div class="markdownContent"><div class="highlight small"><em class="small ne newUserPageProfile_fullName">abdefg</em></div></div> 

そしてコードはこれです。

```html
<div class="markdownContent"><div class="highlight"><p class="ne">abdefg</p></div></div>
<p class="adventCalendarBreadcrumb">
<a>abcdefg</a>
</p>
<p class="fa-check-circle-o">abcdefg</p>
<p class="btn-primary"><a class="badge">abcdefg</a></p>
<p class="fa-comment-o">abcdefg</p>
<p class="editorMarkdown_tab">abcdegh</p>
<p class="sharedHeader-public">abcdegh</p>
<div class="markdownContent"><div class="highlight small"><em class="small ne newUserPageProfile_fullName">abdefg</em></div></div> 
```
