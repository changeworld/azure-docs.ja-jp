| **ハードウェア** | |
| --- |---|
| CPU コアの数| 8 |
| RAM| 12 GB|
| ディスクの数 | 3 <br><br> - OS ディスク<br> - プロセス サーバー キャッシュ ディスク<br> - リテンション ドライブ (フェールバック用)|
| ディスクの空き領域 (プロセス サーバー キャッシュ) | 600 GB
| ディスクの空き領域 (リテンション ディスク) | 600 GB|
| **ソフトウェア** | |
| オペレーティング システムのバージョン | Windows Server 2012 R2 <br> Windows Server 2016 |
| オペレーティング システムのロケール | 英語 (en-us)|
| VMware vSphere PowerCLI のバージョン | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0")|
| Windows Server の役割 | 次の役割を有効にしないでください。 <br> - Active Directory Domain Services <br>- インターネット インフォメーション サービス <br> - Hyper-V |
| グループ ポリシー| サーバーで次のグループ ポリシーを有効にしないでください。 <br> - コマンド プロンプトへのアクセス禁止 <br> - レジストリ編集ツールへのアクセス禁止 <br> - ファイル添付の信頼ロジック <br> - スクリプト実行の有効化 <br> **注:** これらのグループ ポリシーの詳細については[こちら](https://technet.microsoft.com/en-us/library/gg176671(v=ws.10).aspx)を参照してください。|
| インターネット インフォメーション サービス (IIS) の構成 | - 既存の Web サイトが存在しない <br> - [匿名認証](https://technet.microsoft.com/en-us/library/cc731244(v=ws.10).aspx)を有効にする <br> - [FastCGI](https://technet.microsoft.com/en-us/library/cc753077(v=ws.10).aspx) 設定を有効にする  <br> - ポート 443 でリッスンしている既存の Web サイト/アプリケーションが存在しない<br>|
| **ネットワーク** | |
| ネットワーク インターフェイス カードの種類 | VMXNET3 (VMware 仮想マシンとしてデプロイされている場合) |
| IP アドレスの種類 | 静的 |
| インターネットへのアクセス | サーバーは、直接またはプロキシ サーバーを介して次の URL にアクセスできる必要があります。 <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi (スケールアウト プロセス サーバーの場合は不要) <br> - time.nist.gov <br> - time.windows.com |
| ポート | 443 (コントロール チャネルのオーケストレーション)<br>9443 (データ転送)|
