米国運輸省の TranStats データ コレクションから取得した旅客機の定時運航データ (<a href="http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">On-Time</a>)。<p> </p>データセットには、2013 年 4 月から 10 月までの期間のデータが含まれます。Azure Machine Learning Studio へのアップロードの前に、データセットを次のように処理します。<ul><li>米国本土の混雑度が上位 70 位までの空港を含めるように、データセットをフィルター処理します。</li><li>キャンセルされたフライトは 15 分超の遅延として分類します。</li><li>迂回したフライトをフィルターで外します。</li><li>次の列を選択します。Year、Month、DayofMonth、DayOfWeek、Carrier、OriginAirportID、DestAirportID、CRSDepTime、DepDelay、DepDel15、CRSArrTime、ArrDelay、ArrDel15、Cancelled</li></ul>


<!--HONumber=42-->
