# Share PDF Slides with GitHub Pages

這隻程式可以幫助你將 GitHub Repository 中指定資料夾中的 PDF 檔案，透過 GitHub Pages 來呈現。透過自動產生檔案列表，讓使用者可以直接在網頁上瀏覽該檔案，快速找到想要的檔案。

主要使用到的服務與技術有：

- [GitHub Pages](https://pages.github.com/)
- [GitHub API](https://docs.github.com/en/rest)
- [Bootstrap](https://getbootstrap.com/)
- [PDF.js](https://mozilla.github.io/pdf.js/)
- JavaScript

## 取得指定 GitHub Repository 的檔案列表及檔案路徑

GitHub 提供取得指定版本庫中所有檔案的 API，只需要提供使用者名稱 `user`、版本庫名稱 `repository`、分支名稱 `branch`，並使用 HTTP GET 即可取得檔案列表，API 的 URL 如下：

```bash
https://api.github.com/repos/[user]/[repository]/git/trees/[branch]?recursive=1
```

例如要取得 `poychang/demo-share-pdf-slides` 這個版本庫 `main` 分支下的所有檔案和資料夾資訊，API 的 URL 就會是：

```bash
https://api.github.com/repos/poychang/demo-gp-share-slides/git/trees/master?recursive=1
```

回傳的結果會是 JSON 格式，其中會友 `tree` 這個陣列，裡面就是所有檔案的資訊，包含檔案名稱、路徑、大小等等，如下：

```json
{
    "sha": "7ff86a22bd5ec278ca0a5bfc8916f1e5103c6a17",
    "url": "https://api.github.com/repos/poychang/demo-gp-share-slides/git/trees/7ff86a22bd5ec278ca0a5bfc8916f1e5103c6a17",
    "tree": [
        {
            "path": "slides",
            "mode": "040000",
            "type": "tree",
            "sha": "a191896fcfda0581b95e3a63e5b5d7a06ae98bfc",
            "url": "https://api.github.com/repos/poychang/demo-gp-share-slides/git/trees/a191896fcfda0581b95e3a63e5b5d7a06ae98bfc"
        },
        {
            "path": "slides/demo-pdf.pdf",
            "mode": "100644",
            "type": "blob",
            "sha": "07cb664367e91b6e00dee93d834eefba264ec6a9",
            "size": 21309,
            "url": "https://api.github.com/repos/poychang/demo-gp-share-slides/git/blobs/07cb664367e91b6e00dee93d834eefba264ec6a9"
        },
        // 略...
    ],
    "truncated": false
}
```

這裡的重點是 `path` 相對路徑，篩選出有檔案名稱的 `path` 之後，搭配下列 URL 就可以取得該版本庫中的指定檔案：

```bash
https://raw.githubusercontent.com/[user]/[repository]/[branch]/[path]
```

## 線上顯示檔案內容

這裡使用 [PDF.js](https://mozilla.github.io/pdf.js/) 來處理 PDF 檔案，PDF.js 是 Mozilla 所開發的一套 JavaScript PDF 開源工具，可以在瀏覽器上直接顯示 PDF 檔案，不需要透過外掛程式，使用起來非常方便。
