
次の制限は、Azure Backup に適用されます。

| 制限の種類 | 既定の制限 |
| --- | --- |
| 各資格情報コンテナーに登録できるサーバーやマシンの数 |Windows Server/Client/SCDPM で 50  <br/> IaaS VM で 200 |
| Azure 資格情報コンテナー ストレージに格納されるデータのデータ ソースのサイズ |最大 54400 GB<sup>1</sup> |
| 各 Azure サブスクリプションで作成できるバックアップ資格情報コンテナー数 |25 Recovery Services コンテナー (リージョンあたり) |
| 1 日にバックアップをスケジュールできる回数 |Windows Server/Client で 1 日 3 回 <br/> SCDPM で 1 日 2 回 <br/> IaaS VM で 1 日 1 回 |
| バックアップ用に Azure の仮想マシンに接続できるデータ ディスクの数 |16 |
| バックアップ用に Azure の仮想マシンに接続される個々のデータ ディスクのサイズ| 1023 GB <sup>2</sup>|

* <sup>1</sup> 54400 GB の制限は、IaaS VM のバックアップには適用されません。
* <sup>2</sup> 非管理対象ディスクを最大 4 TB サポートする[プライベート プレビュー](https://gallery.technet.microsoft.com/Instant-recovery-point-and-25fe398a?redir=0)を用意しています。 

