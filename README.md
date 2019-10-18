# CatTree Lite

1. 已實作 postMessage 傳入多棵樹，並全部顯示
2. 目前提供四種資料輸入模式
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
        var TreeStructureSample = [
        {'id': '0','parent': '#', 'text': '淡新檔案客事件分類', 'onclickDate': '666'},
        {'id': '1', 'parent': '0', 'text': '土地', 'Mark': 'Test'},
        {'id': '2', 'parent': '1', 'text': '-', 'Mark':'123'},
        {'id': '3', 'parent': '2', 'text': '-', url:'http://yahoo.com.tw'}];
        // 'id' 為主識別
        // 'parent' 是用來對應父結點
        // 'parent': '#' 表示根目錄
        // 'text' 是顯示在節點上的文字
        // 'Mark' 以及 'onclickDate' 這些 porperty 可以透過 onClick Callback Function 來取得並處理
        // 有 'url' porperty 的資料，在點擊後會自動開啟該 url
    </script>
    ```
    3. postMessage 同時輸入檢索頁面的 Cat 資料以及一個 Tree JSON 檔案
    在此模式下，會以 Tree JSON 檔案為底圖，把檢索頁面 Cat 資料嘗試對應到 Tree JSON，並填入數目。例如：接收 ```{cue:'宗教/嘗會/-',cnt:'12'}``` 此筆 Cat 資料，就會在相對應的節點顯示件數，在此為 12 件
    4. 透過 url 讀取樹狀結構的 JSON 並顯示，使用方式為： https://abc.com/?treestructurejson_url=https://www.com.tw/1.json

3. postMessage 輸入資料時，可以附帶一些選項，放在 `option` 這個物件當中，目前提供 onclick, title, theme, openTreeTerm, quantifierterm, openTreeAll 等選項

    1. 提供 onClick Callback Function 讓開發者定義點擊後的工作
    ```gherkin=
    <script>
        var onclick = function(node, TreeStructure){
            console.log('Node: '+ JSON.stringify(node));
            console.log('TreeStructure: '+ JSON.stringify(TreeStructure));
            window.open('http://140.112.114.10/DocuSky/webApi/webpage-search-3in1.php?db=淡新檔案&corpus=淡新檔案&queryBase='+node.text);
          }
          // node 為被觸發節點的資料，例如：{'id': '2', 'parent': '1', 'text': '-', 'Mark':'123'}
          // TreeStructure 為樹狀圖的 JSON，開發者可以透過此資料了解 node 在 TreeStructure 中的關係
          // 若該 node 有 url property，會優先打開該 url，若無 url，才會執行 onClick Callback Function
    </script>
    ```
    2. 提供 title，使用時機如下:
        1. 若有多棵樹，其切換按鈕名稱就是 title
        2. 若該棵樹只有輸入檢索頁面的 Cat 資料(DocuSkySearchData)，其樹根名稱就是 title。
    3. 提供 theme，讓開發者可以切換 layout，目前提供 3 種 layout，分別是 original, proton, dark
    4. openTreeTerm 可以展開特定名稱的節點，例如：`openTreeTerm : '故事'`，就會從根節點展開到 `'故事'`這個節點，若 node 同名，則同名的 node 都會展開。
    5. quantifierterm 可以在 postMessage 輸入檢索頁面的 Cat 資料(DocuSkySearchData)時，更改量詞，例如：`quantifierterm : '隻'`，則會以此量詞來顯示數量。
    6. openTreeAll 可以展開所有節點，例如：`openTreeAll: true`
4. postMessage 傳送資料為一個陣列(CatTreeMessage)，陣列每個 item 為一棵樹
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
    // onclick callback
    var onclick = function(node, TreeStructure){
        console.log('Node: '+ JSON.stringify(node));
        console.log('TreeStructure: '+ JSON.stringify(TreeStructure));
        window.open('http://140.112.114.10/DocuSky/webApi/webpage-search-3in1.php?db=淡新檔案&corpus=淡新檔案&queryBase='+node.text);
      }

    // Example 1
    CatTreeMessage[0] = {TreeStructure: TreeStructureSample,
                            option:
                            {
                                onclick: encodeURI(onclick.toString()),
                                title: '淡新檔案客家事件分類',
                                theme: 'dark'
                            }
                          }
    // Example 2
    CatTreeMessage[1] = {DocuSkySearch: DocuSkySearchData,
                            option:
                            {
                                onclick: encodeURI(onclick.toString()),
                                title: '666',
                                quantifierterm: '很多件123',
                                openTreeTerm : '故事'

                            }
                          }
    // Example 3
    CatTreeMessage[2] = {TreeStructure: TreeStructureSample,DocuSkySearch: DocuSkySearchData,onclick: encodeURI(onclick.toString())}
    // Example 4
    CatTreeMessage[3] = {TreeStructure: TreeStructureSample,DocuSkySearch: DocuSkySearchData,
                              option:
                              {
                                  onclick: encodeURI(onclick.toString()),
                                  title: '我就愛說中文',
                                  theme: 'original',
                                  quantifierterm: '很多件啊啊啊',
                                  openTreeAll: true
                              }

                          }
</script>
```

5. 未來工作：
    1. 製作 CSV to 樹狀結構 JSON Converter，透過此 Converter，學者可以上傳 CSV，並作簡單的編輯還有 priority 調整，之後產生樹狀結構的 JSON 並 postMessage 到 CatTree Lite
