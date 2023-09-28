2. Jenkins更改虛擬機文件操作流程!
    1. 回報欲修改的部分與操作計畫給主管後，並取得允諾。
    2. 確認欲修改的部分對應的jenkins任務
        - 如:qa、prod，game-api、ctl...等
    3. 確認jenkins任務執行內容
        - 如:ssh執行的虛擬機與使用者、執行的指令與文件
    4. 進入終端機確認jenkins執行的文件內容，查找到欲修改文件
        - 為避免修改到文件，利用`cat`、`less`、`tail`、`head`等指令顯示文件內容後，進行文件內容的確認
        - 使用 `pwd` 確認虛擬機中當前位置是否符合jenkins欲執行文件的位置。
    5. 找到欲修改文件，進行修改
        - 使用pwd確認當前位置是否有符合欲修改文件之檔案位置
        - 修改檔案務必小心，若檔案有不確定部分，勿妄自判斷，要回報主管，確認之後再繼續動作
        - 修改後，也要再次檢查是否有正確修改，其他部分是否正常
    6. 進行測試完成後，發PR，回報主管確認。

3. GKE 503 connection_refuse問題處理
    1. 問題：game-api進行更新時，會出現短時間的503 connection_refused錯誤
    2. 處理方式：
        1. 查看紀錄檔，發現game-api的pod關閉後，loadbalancer在短時間內仍會送請求到該pod
        2. 修改game-api的helm value檔，在preStop指令加入sleep 30，讓loadbalancer有足夠的時間更新資訊
        3. 由於延遲30秒，terminationGracePeriodSeconds也要增加30秒，讓pod有足夠的時間優雅地關閉。
        4. 修改後進行更新，未再發現有503 connection_refused錯誤