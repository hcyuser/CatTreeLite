# CatTree Lite

1. 目前提供六種資料輸入模式
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
    4. postMessage 輸入 Excel 資料，會顯示樹狀結構還有表單可以交互操作，輸入規範是下方 Sample Code 的 ExcelMessage
    5. 透過 url 讀取 JSON 並顯示，使用方式為： https://abc.com/?url=https://www.com.tw/1.json
    JSON 規範如同下方 Sample Code 的 postMessage 資料

    6. 若不透過 postMessage 傳入資料，單獨打開 CatTree 頁面，可以手動上傳 Excel 表單，請參閱 Excel 表單範例

3. postMessage CatTreeMessage 輸入資料時，可以附帶一些選項，目前提供 title, theme, openTreeTerm, quantifierTerm, openTreeAll 等選項
    1. 提供 title，使用時機如下:
        1. 若有多棵樹，其切換按鈕名稱就是 title
        2. 若該棵樹只有輸入檢索頁面的 Cat 資料(DocuSkySearchData)，其樹根名稱就是 title。
    2. 提供 theme，讓開發者可以切換 layout，目前提供 3 種 layout，分別是 original, proton, dark
    3. openTreeTerm 可以展開特定名稱的節點，例如：`openTreeTerm : '故事'`，就會從根節點展開到 `'故事'`這個節點，若 node 同名，則同名的 node 都會展開。
    4. quantifierTerm 可以在 postMessage 輸入檢索頁面的 Cat 資料(DocuSkySearchData)時，更改量詞，例如：`quantifierterm : '隻'`，則會以此量詞來顯示數量。
    5. openTreeAll 可以展開所有節點，例如：`openTreeAll: true`
3. postMessage 以及透過 url 讀取 JSON 傳送資料為一個陣列(CatTreeMessage)或者一個物件(ExcelMessage)。CatTreeMessage 陣列每個 item 為一棵樹。ExcelMessage 物件是 Excel 表單資料。
Example CatTreeMessage:
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
    var DocuSkySearchData = [{cue:'土地/-',cnt:'100',url:'http://yahoo.com.tw'},
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
Example ExcelMessage:
```gherkin=
<script>
    // postMessage 最後要傳送 ExcelMessage
    ExcelMessage = {
        "第一棵分類樹": [
          { //第一筆資料
            // L 表示階層
            "1_id": "1",
            "L1": "民事",
            "L2": "田房",
            "L3": "霸佔",
            "3_text": "國立臺灣大學",
            "2_時間": "民國108年",
            "4_地點": "臺灣–臺北",
            "6_Url": "https://www.google.com",
            "5_RefId": "twgis_19291"
          },
          { //第二筆資料
            "1_id": "2",
            "L1": "民事",
            "L2": "田房",
            "L3": "爭界",
            "3_text": "國立清華大學",
            "2_時間": "民國109年",
            "4_地點": "臺灣–新竹",
            "6_Url": "http://140.112.114.10/",
            "5_RefId": "cbdb_196395"
          },
          {
            "1_id": "3",
            "L1": "民事",
            "L2": "田房",
            "L3": "爭界123",
            "L4": "-",
            "3_text": "國立交通大學",
            "2_時間": "民國110年",
            "4_地點": "臺灣–新竹",
            "5_RefId": "dila_A000294"
          },
          {
            "1_id": "4",
            "L1": "民事",
            "L2": "田房",
            "L4": "123",
            "3_text": "國立政治大學",
            "2_時間": "民國111年",
            "4_地點": "臺灣–臺北",
            "5_RefId": "hvd_70623"
          }
        ],
        "第二棵分類樹": [
          {
            "id": "1",
            "L1": "族群關係",
            "L2": "客家內部關係",
            "L3": "金錢糾紛",
            "text": "國立臺灣大學1",
            "時間": "民國108年",
            "地點": "臺灣–臺北",
            "IiifManifest": "https://iiif.lib.harvard.edu/manifests/ids:3098346"
          },
          {
            "id": "2",
            "L1": "民事",
            "L2": "田房",
            "L3": "爭界",
            "text": "國立清華大學1",
            "時間": "民國109年",
            "地點": "臺灣–新竹"
          },
          {
            "id": "3",
            "L1": "民事",
            "L2": "田房",
            "L3": "爭界",
            "text": "國立交通大學1",
            "時間": "民國110年",
            "地點": "臺灣–新竹"
          },
          {
            "id": "4",
            "L1": "族群關係",
            "L2": "客家內部關係",
            "L3": "金錢糾紛",
            "L4": "123",
            "text": "國立政治大學1",
            "時間": "民國111年",
            "地點": "臺灣–臺北"
          },
          {
            "id": "5",
            "L1": "社會組織",
            "L2": "拓墾組織",
            "text": "國立政治大學1",
            "時間": "民國111年",
            "地點": "臺灣–臺北"
          }
        ]
      }
</script>
```
