# CatTree Lite

1. 目前提供五種資料輸入模式
    1. postMessage 輸入檢索頁面的 Cat 資料
    ```gherkin=
    <script>
        var DocuSkySearchData = [
        {cue:'土地/-',cnt:'100', 'Mark': '124'},
        {cue:'宗教/嘗會/-',cnt:'12'}]
        //目前沒有實作 'Mark' 這個 porperty ，
        //在此只是展現未來的擴充性
    </script>
    ```
    2. postMessage 輸入一個 Tree JSON 檔案
    ```gherkin=
    <script>
        var TreeStructureSample = [ {'id': '0', 'parent': '#', 'text': '淡新檔案客家事件分類', 'onclickDate': '666', url:'http://yahoo.com.tw'},
                                    {'id': '1', 'parent': '0', 'text': '土地', 'Mark': 'Test'},
                                    {'id': '2', 'parent': '1', 'text': '-', 'Mark':'123'},
                                    {'id': '3', 'parent': '2', 'text': '-'},
                                    {'id': '4', 'parent': '0', 'text': '宗教'},
                                    {'id': '5', 'parent': '4', 'text': '嘗會'},
                                    {'id': '6', 'parent': '5', 'text': '-'},
                                    ];
        // 'id' 為主識別
        // 'parent' 是用來對應父結點
        // 'parent': '#' 表示根目錄
        // 'text' 是顯示在節點上的文字
        // 有 'url' porperty 的資料，在點擊後會自動開啟該 url
    </script>
    ```
    3. postMessage 同時輸入檢索頁面的 Cat 資料以及一個 Tree JSON 檔案
    在此模式下，會以 Tree JSON 檔案為底圖，把檢索頁面 Cat 資料嘗試對應到 Tree JSON，並填入數目。例如：接收 ```{cue:'宗教/嘗會/-',cnt:'12'}``` 此筆 Cat 資料，就會在相對應的節點顯示件數，在此為 12 件
    4. 透過 url 讀取 JSON 並顯示，使用方式為： https://abc.com/?url=https://www.com.tw/1.json
    JSON 規範是下方 Sample Code 的 CatTreeMessage

    5. 若不透過 postMessage 傳入資料，單獨打開 CatTree 頁面，可以手動上傳 Excel 表單，Excel 表單規範：

3. postMessage 輸入資料時，可以附帶一些選項，目前提供 title, theme, openTreeTerm, quantifierTerm, openTreeAll 等選項
    1. 提供 title，使用時機如下:
        1. 若有多棵樹，其切換按鈕名稱就是 title
        2. 若該棵樹只有輸入檢索頁面的 Cat 資料(DocuSkySearchData)，其樹根名稱就是 title。
    2. 提供 theme，讓開發者可以切換 layout，目前提供 3 種 layout，分別是 original, proton, dark
    3. openTreeTerm 可以展開特定名稱的節點，例如：`openTreeTerm : '故事'`，就會從根節點展開到 `'故事'`這個節點，若 node 同名，則同名的 node 都會展開。
    4. quantifierTerm 可以在 postMessage 輸入檢索頁面的 Cat 資料(DocuSkySearchData)時，更改量詞，例如：`quantifierterm : '隻'`，則會以此量詞來顯示數量。
    5. openTreeAll 可以展開所有節點，例如：`openTreeAll: true`
3. postMessage 以及 url 傳送資料為一個陣列(CatTreeMessage)，陣列每個 item 為一棵樹
Example:
```gherkin=
<script>
    // postMessage 最後要傳送 CatTreeMessage
    var CatTreeMessage = [];
    // 輸入一個 Tree JSON 檔案
    var TreeStructureSample = [ {'id': '0', 'parent': '#', 'text': '淡新檔案客家事件分類', 'onclickDate': '666', url:'http://yahoo.com.tw'},
                                {'id': '1', 'parent': '0', 'text': '土地', 'Mark': 'Test'},
                                {'id': '2', 'parent': '1', 'text': '-', 'Mark':'123'},
                                {'id': '3', 'parent': '2', 'text': '-'},
                                {'id': '4', 'parent': '0', 'text': '宗教'},
                                {'id': '5', 'parent': '4', 'text': '嘗會'},
                                {'id': '6', 'parent': '5', 'text': '-'}];
    // 輸入檢索頁面的 Cat 資料
    var DocuSkySearchData = [{cue:'土地/-',cnt:'100', 'Mark': '124'},
                             {cue:'宗教/嘗會/-',cnt:'12'},
                             {cue:'宗教/廟宇/1', cnt:'122'},{cue:'宗教/廟宇/故事', cnt:'124'},
                             {cue:'宗教/廟1/故事', cnt:'125'},{cue:'N/N/N', cnt:'124'},
                             {cue:'-/1/-', cnt:'124'}];
    // Example 1
    CatTreeMessage[0] = {TreeStructure: JSON.parse(JSON.stringify(TreeStructureSample)),
                         title: '淡新檔案客家事件分類',
                         theme: 'dark',
                         openTreeTerm : '-'
                         }
    // Example 2
    CatTreeMessage[1] = {DocuSkySearch: JSON.parse(JSON.stringify(DocuSkySearchData)),
                         quantifierTerm: '很多件123',
                         openTreeTerm : '故事'
                         }
    // Example 3
    CatTreeMessage[2] = {DocuSkySearch: JSON.parse(JSON.stringify(DocuSkySearchData)),
                         title: '666',
                         quantifierTerm: '很多件123',
                         }
    // Example 4
    CatTreeMessage[3] = {TreeStructure: JSON.parse(JSON.stringify(TreeStructureSample)), DocuSkySearch: JSON.parse(JSON.stringify(DocuSkySearchData)),
                         title: '我就愛說中文',
                         theme: 'original',
                         quantifierTerm: '很多件啊啊啊234',
                         openTreeAll: true,
                         openTreeTerm : '嘗會'
                         }
</script>
```

4. 未來工作：
    1. 把 CatTree Lite 當成獨立工具時，如何上傳 Excel 並解析與互動？
    2. 是否可以把 cnt==0 的節點（以及其 cnt 為零的 ancestors）隱藏起來不顯示？
