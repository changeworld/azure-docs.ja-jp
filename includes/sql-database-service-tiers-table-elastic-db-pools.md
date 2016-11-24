
### <a name="basic-elastic-pool-limits"></a>Basic エラスティック プールの制限

|   |  |
|---|:---:|
| プールあたりの最大 eDTU 数 | &nbsp;100 &nbsp;&nbsp;&nbsp; 200 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp;&nbsp;&nbsp; 1200 |
| プールあたりの最大ストレージ容量 (GB)*| &nbsp;&nbsp;&nbsp;&nbsp;10 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;20 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;39 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;73 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;117 |
| プールあたりのデータベースの最大数 | &nbsp;&nbsp;&nbsp;200 &nbsp;&nbsp;&nbsp;&nbsp;400 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;400 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;400 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;400 |
| プールあたりの最大インメモリ OLTP ストレージ容量 (GB)| 該当なし |
| プールあたりの最大同時実行ワーカー数 | &nbsp;&nbsp;&nbsp;200 &nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp;&nbsp; 1600 &nbsp;&nbsp;&nbsp;&nbsp;2400 |
| プールあたりの最大同時ログイン数 | &nbsp;&nbsp;&nbsp;200 &nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp;&nbsp; 1600 &nbsp;&nbsp;&nbsp;&nbsp;2400 |
| プールあたりの最大同時セッション数 | 4800 &nbsp;9600 &nbsp; 19200 &nbsp; 28800 &nbsp; 28800 |
| データベースあたりの最大 eDTU 数* | 5 |
| データベースあたりの最小 eDTU 数* | 0.5 |
| データベースあたりの最大ストレージ (GB)** | 2 |
| ポイントインタイム リストア | 過去 7 日間の任意のポイント |
| 障害復旧 | アクティブ geo レプリケーションを選択するとき |
|||

* データベースあたりの eDTU の最大数と最小数は、一覧に表示されている値であればどれにでも設定することができます。ただし、プールの DTU サイズとして選択した値が、少なくとも DB あたりの最大 eDTU 数と同じになっている必要があります。 

** エラスティック データベースではプール ストレージが共有されるため、データベース ストレージは、残りのプール ストレージとデータベースあたりの最大ストレージのどちらか小さい方に制限されます。


### <a name="standard-elastic-pool-limits"></a>Standard エラスティック プールの制限

|   |  |
|---|:---:|
| プールあたりの最大 eDTU 数 | &nbsp;100 &nbsp;&nbsp;&nbsp; 200 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp;&nbsp;&nbsp; 1200 |
| プールあたりの最大ストレージ容量 (GB)*| &nbsp;100 &nbsp;&nbsp;&nbsp; 200 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp;&nbsp;&nbsp; 1200 |
| プールあたりのデータベースの最大数 | &nbsp;200 &nbsp;&nbsp;&nbsp;&nbsp;400 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;400 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;400 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;400 |
| プールあたりの最大インメモリ OLTP ストレージ容量 (GB)| 該当なし |
| プールあたりの最大同時実行ワーカー数 | &nbsp;&nbsp;200 &nbsp;&nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp; 1600 &nbsp;&nbsp;&nbsp; 2400 |
| プールあたりの最大同時ログイン数 | &nbsp;&nbsp;200 &nbsp;&nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp; 1600 &nbsp;&nbsp;&nbsp; 2400 |
| プールあたりの最大同時セッション数 | 4800 &nbsp; 9600 &nbsp;19200 &nbsp;28800 &nbsp;&nbsp; 28800 |
| データベースあたりの最大 eDTU 数* | 10、20、50、100 |
| データベースあたりの最小 eDTU 数* | 0、10、20、50、100 |
| データベースあたりの最大ストレージ (GB)** | 250 |
| ポイントインタイム リストア | 過去 35 日間の任意のポイント |
| 障害復旧 | アクティブ geo レプリケーションを選択するとき |
|||

* データベースあたりの eDTU の最大数と最小数は、一覧に表示されている値であればどれにでも設定することができます。ただし、プールの DTU サイズとして選択した値が、少なくとも DB あたりの最大 eDTU 数と同じになっている必要があります。 

** エラスティック データベースではプール ストレージが共有されるため、データベース ストレージは、残りのプール ストレージとデータベースあたりの最大ストレージのどちらか小さい方に制限されます。

### <a name="premium-elastic-pool-limits"></a>Premium エラスティック プールの制限

|   |  |
|---|:---:|
| プールあたりの最大 eDTU 数 | 125 &nbsp;&nbsp;&nbsp; 250 &nbsp;&nbsp;&nbsp; 500 &nbsp;&nbsp;&nbsp; 1000 &nbsp;&nbsp;&nbsp; &nbsp;1500 |
| プールあたりの最大ストレージ容量 (GB)*| 250 &nbsp;&nbsp;&nbsp; 500 &nbsp;&nbsp;&nbsp; 750 &nbsp;&nbsp;&nbsp;&nbsp; 750 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 750 |
| プールあたりのデータベースの最大数 | 50 |
| プールあたりの最大インメモリ OLTP ストレージ容量 (GB)| &nbsp;&nbsp; 1 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 2 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 4 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 8 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;12 |
| プールあたりの最大同時実行ワーカー数 | &nbsp;&nbsp;200 &nbsp;&nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp; 1600 &nbsp;&nbsp;&nbsp; 2400 |
| プールあたりの最大同時ログイン数 | &nbsp;&nbsp;200 &nbsp;&nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp; 1600 &nbsp;&nbsp;&nbsp; 2400 |
| プールあたりの最大同時セッション数 | 4800 &nbsp; 9600 &nbsp;19200 &nbsp;28800 &nbsp;&nbsp; 28800 |
| データベースあたりの最大 eDTU 数* | 125、250、500、1,000 |
| データベースあたりの最小 eDTU 数* | 0、125、250、500、1,000 |
| データベースあたりの最大ストレージ (GB)** | 500 |
| ポイントインタイム リストア | 過去 35 日間の任意のポイント |
| 障害復旧 | アクティブ geo レプリケーションを選択するとき |
|||

* データベースあたりの eDTU の最大数と最小数は、一覧に表示されている値であればどれにでも設定することができます。ただし、プールの DTU サイズとして選択した値が、少なくとも DB あたりの最大 eDTU 数と同じになっている必要があります。 

** エラスティック データベースではプール ストレージが共有されるため、データベース ストレージは、残りのプール ストレージとデータベースあたりの最大ストレージのどちらか小さい方に制限されます。


<!--HONumber=Nov16_HO2-->


