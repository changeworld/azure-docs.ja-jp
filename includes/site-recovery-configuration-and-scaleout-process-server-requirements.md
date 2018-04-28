| **コンポーネント** | **要件** |
| --- |---|
| CPU コア数| 8 |
| RAM | 16 GB|
| ディスクの数 | 3、OS ディスク、プロセス サーバーのキャッシュ ディスク、フェールバック用リテンション ドライブを含みます |
| ディスクの空き領域 (プロセス サーバー キャッシュ) | 600 GB
| ディスクの空き領域 (リテンション ディスク) | 600 GB|
| オペレーティング システム  | Windows Server 2012 R2 <br> Windows Server 2016 |
| オペレーティング システムのロケール | 英語 (en-us)|
| VMware vSphere PowerCLI のバージョン | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0")|
| Windows Server の役割 | これらの役割を有効にしないでください。 <br> - Active Directory Domain Services <br>- インターネット インフォメーション サービス <br> - Hyper-V |
| グループ ポリシー| これらのグループ ポリシーを有効にしないでください。 <br> - コマンド プロンプトへのアクセス禁止。 <br> - レジストリ編集ツールへのアクセス禁止。 <br> - ファイル添付の信頼ロジック。 <br> - スクリプト実行の有効化。 <br> [詳細情報](https://technet.microsoft.com/en-us/library/gg176671(v=ws.10).aspx)|
| IIS | - 既存の Web サイトが存在しない <br> - [匿名認証](https://technet.microsoft.com/en-us/library/cc731244(v=ws.10).aspx)を有効にする <br> - [FastCGI](https://technet.microsoft.com/en-us/library/cc753077(v=ws.10).aspx) 設定を有効にする  <br> - ポート 443 でリッスンしている既存の Web サイト/アプリケーションが存在しない<br>|
| NIC の種類 | VMXNET3 (VMware VM としてデプロイされている場合) |
| IP アドレスの種類 | 静的 |
| インターネットへのアクセス | サーバーは、次の URL にアクセスできる必要があります。 <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi (スケール アウト プロセス サーバーについては不要) <br> - time.nist.gov <br> - time.windows.com |
| ポート | 443 (コントロール チャネルのオーケストレーション)<br>9443 (データ転送)|
