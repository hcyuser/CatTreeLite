# CatTree Lite

1. 目前只有實作一棵樹狀圖，但是它未來可以擴充成兩三棵樹
2. 目前提供三種輸入模式
    1. 輸入檢索頁面的 Cat 資料
    ```gherkin=
    <script>
        var DocuSkySearchData = [
        {cue:'土地/-',cnt:'100', 'Mark': '124'},
        {cue:'宗教/嘗會/-',cnt:'12'}]
        //目前沒有實作 'Mark' 這個 porperty ，
        //在此只是展現未來的擴充性
    </script>
    ``` 
    2. 輸入一個 Tree JSON 檔案
    ```gherkin=
    <script>
        var TreeStructureSample = [ 
        {'id': '0','parent': '#', 'text': '淡新檔案客事件分類', 'onclickDate': '666'},
        {'id': '1', 'parent': '0', 'text': '土地', 'Mark': 'Test'},
        {'id': '2', 'parent': '1', 'text': '-', 'Mark':'123'},
        {'id': '3', 'parent': '2', 'text': '-'}
                             ];
        // 'id' 為主識別
        // 'parent' 是用來對應父結點
        // 'parent': '#' 表示根目錄
        // 'text' 是顯示在節點上的文字
        // 目前沒有實作 'Mark' 以及 'onclickDate' 這些porperty ，
        // 在此只是展現未來的擴充性
    </script>
    ``` 
    3. 同時輸入檢索頁面的 Cat 資料以及一個 Tree JSON 檔案
    在此模式下，會以 Tree JSON 檔案為底圖，把檢索頁面 Cat 資料嘗試對應到 Tree JSON，並填入數目。例如：接收 ```{cue:'宗教/嘗會/-',cnt:'12'}``` 此筆 Cat 資料，就會在相對應的節點顯示件數，在此為 12 件
    
3. 提供 onClick Callback Function 讓使用者定義點擊觸發後的工作
```gherkin=
<script>
    var onclick = function(data){
        console.log('Test: '+ JSON.stringify(data));
        window.open('http://140.112.114.10/DocuSky/webApi/webpage-search-3in1.php?db=淡新檔案&corpus=淡新檔案&queryBase='+data.text);
      }
</script>
``` 

4. postMessage 傳送資料為一個陣列，陣列每個 item 為一棵樹
Example:
```gherkin=
<script>
    // postMessage 最後要傳送 CatTreeMessage
    var CatTreeMessage = [];
    // 輸入一個 Tree JSON 檔案
    var TreeStructureSample = [ 
        {'id': '0','parent': '#', 'text': '淡新檔案客事件分類', 'onclickDate': '666'},
        {'id': '1', 'parent': '0', 'text': '土地', 'Mark': 'Test'},
        {'id': '2', 'parent': '1', 'text': '-', 'Mark':'123'},
        {'id': '3', 'parent': '2', 'text': '-'}];
    // 輸入檢索頁面的 Cat 資料 
    var DocuSkySearchData = [{cue:'土地/-',cnt:'100', 'Mark': '124'},{cue:'宗教/嘗會/-',cnt:'12'},
                              {cue:'宗教/廟宇/1', cnt:'122'},{cue:'宗教/廟宇/故事', cnt:'124'},{cue:'宗教/廟1/故事', cnt:'125'},{cue:'N/N/N', cnt:'124'},{cue:'-/1/-', cnt:'124'}];
    // onclick callback
    var onclick = function(data){
        console.log('Test: '+ JSON.stringify(data));
        window.open('http://140.112.114.10/DocuSky/webApi/webpage-search-3in1.php?db=淡新檔案&corpus=淡新檔案&queryBase='+data.text);
      }
      
    // Example 1
    CatTreeMessage[0] = {TreeStructure: TreeStructureSample,onclick: encodeURI(onclick.toString())}
    // Example 2
    CatTreeMessage[0] = {TreeStructure: TreeStructureSample}
    // Example 3
    CatTreeMessage[0] = {DocuSkySearch: DocuSkySearchData,onclick: encodeURI(onclick.toString())}
    // Example 4
    CatTreeMessage[0] = {DocuSkySearch: DocuSkySearchData}
    // Example 5
    CatTreeMessage[0] = {TreeStructure: TreeStructureSample,DocuSkySearch: DocuSkySearchData,onclick: encodeURI(onclick.toString())}
    // Example 6
    CatTreeMessage[0] = {TreeStructure: TreeStructureSample,DocuSkySearch: DocuSkySearchData}
      
</script>
``` 

5. 未來工作：
    1. 製作 CSV 轉 Tree JSON 的轉換工具，應該使用現在的 code 進行改寫即可
    2. onclick callback 這裡可以進行小修正，讓他更有彈性


