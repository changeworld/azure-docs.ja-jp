<properties 
	pageTitle="データ移動のための Data Management Gateway | Microsoft Azure"
	description="オンプレミスとクラウドの間でデータを移動するためのデータ ゲートウェイを設定します。Azure Data Factory で Data Management Gateway を使用してデータを移動します。" 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/08/2016" 
	ms.author="spelluru"/>

# Data Management Gateway
Data Management Gateway は、クライアントと、[コピー アクティビティでサポートされている](data-factory-data-movement-activities.md##supported-data-stores)オンプレミスのデータ ストアとの間でデータを移動できるようにするために、オンプレミスの環境にインストールする必要があるクライアント エージェントです。

この記事の内容は、「[Data Management Gateway を使用してオンプレミスのソースとクラウドの間でデータを移動する](data-factory-move-data-between-onprem-and-cloud.md)」の記事を補完します。その記事では、ゲートウェイを使ってオンプレミスの SQL Server データベースから Azure BLOB にデータを移動する Data Factory パイプラインの作成に関するチュートリアルが示されています。この記事では、Data Management Gateway について詳しく取り上げます。

## Data Management Gateway の機能
Data Management Gateway には次の機能があります。

- 同じ Data Factory 内にオンプレミスのデータ ソースとクラウドのデータ ソースをモデル化して、データを移動します。
- Data Factory ブレードにゲートウェイの状態を表示して、一元的に監視および管理を行います。
- オンプレミスのデータ ソースへの安全なアクセスを管理します。
	- 企業のファイアウォールを変更する必要はありません。Gateway は、インターネットを開くために送信 HTTP ベースの接続のみを行います。
	- 証明書でオンプレミスのデータ ストアの資格情報を暗号化します。
- データを効率的に移動します。データは並列に転送されます。自動再試行ロジックにより、断続的なネットワークの問題にも対応できます。

## ゲートウェイを使用する場合のデータ フロー
データ パイプラインでコピー アクティビティを使用してオンプレミスのデータを処理のためにクラウドに取り込むとき、またはクラウドの結果データをオンプレミスのデータ ストアにエクスポートして戻すときは、コピー アクティビティは内部的にゲートウェイを使用してオンプレミスのデータ ソースからクラウドに、またはその逆に、データを転送します。

データ ゲートウェイでのコピーのデータ フローと手順の概要を次に示します。![ゲートウェイを使用したデータ フロー](./media/data-factory-data-management-gateway/data-flow-using-gateway.png)

1.	データの開発者は、[Azure ポータル](https://portal.azure.com)または [PowerShell コマンドレット](https://msdn.microsoft.com/library/dn820234.aspx)を使用して Azure Data Factory 用の新しいゲートウェイを作成します。
2.	データ開発者は、ゲートウェイを指定することで、オンプレミスのデータ ストアに使用するリンクされたサービスを作成します。リンクされたサービスの設定の一部として、データ開発者は、資格情報の設定アプリケーションを使用して認証の種類と資格情報を指定します。資格情報の設定アプリケーションのダイアログは、データ ストアと通信して接続をテストし、ゲートウェイと通信して資格情報を保存します。
3. ゲートウェイは、資格情報をクラウドに保存する前に、(開発者によって提供された) ゲートウェイと関連付けられた証明書で資格情報を暗号化します。
4. Data Factory サービスは、共有 Azure Service Bus キューを使用する制御チャネルを介して、ジョブのスケジューリングと管理のためにゲートウェイと通信します。コピー アクティビティ ジョブを開始する必要がある場合、Data Factory はリクエストと資格情報をキューに入れます。ゲートウェイは、キューをポーリングした後でジョブを開始します。
5.	ゲートウェイは、同じ証明書で資格情報を復号化し、適切な認証の種類と資格情報を使用してオンプレミスのデータ ストアに接続します。
6.	ゲートウェイは、データ パイプラインでのコピー アクティビティの構成方法に応じて、オンプレミスのストアからクラウドのストレージに、またはクラウドのストレージからオンプレミスのデータ ストアに、データをコピーします。注: この手順では、ゲートウェイは、セキュリティで保護された (HTTPS) チャネルを使用して、クラウド ベースのストレージ サービス (例: Azure BLOB、Azure SQL など) と直接通信します。

## ゲートウェイの使用に関する注意点
- Data Management Gateway の 1 つのインスタンスを複数のオンプレミス データ ソースに使用できますが、**1 つの Gateway インスタンスはただ 1 つの Azure Data Factory に関連付けられており**、別の Data Factory と共有することはできないことに注意してください。
- 1 台のコンピューターには、**Data Management Gateway のインスタンスを 1 つだけ**インストールできます。例えば、オンプレミスのデータ ソースにアクセスする必要があるデータ ファクトリが 2 つあるとすると、オンプレミスのコンピューター 2 台にゲートウェイをインストールし、各ゲートウェイを別々のデータ ファクトリに結び付ける必要があります。
- **ゲートウェイはデータ ソースと同じコンピューター上に存在する必要はありませんが**、データ ソースの近くにあると、ゲートウェイがデータ ソースに接続する際の時間が短縮されます。ゲートウェイとデータ ソースの間でリソースの競合が発生しないように、オンプレミス データ ソースをホストするコンピューターとは異なるコンピューターにゲートウェイをインストールすることをお勧めします。
- **同じオンプレミス データ ソースに接続する異なるコンピューターで複数のゲートウェイを使用**できます。たとえば、2 つのデータ ファクトリを提供する 2 つのゲートウェイがあり、どちらのデータ ファクトリにも同じオンプレミス データ ソースが登録されている場合があります。
- **Power BI** のシナリオを提供するゲートウェイがコンピューターに既にインストールされている場合は、**Azure Data Factory 用のゲートウェイ**を別のコンピューターにインストールしてください。
- **ExpressRoute を使用する場合でも、Gateway を使用する**必要があります。
- **ExpressRoute** を使用する場合であっても (ファイアウォールの背後にある) オンプレミスのデータ ソースとしてデータ ソースを取り扱い、**Gateway を使用して**サービスとデータ ソースの間の接続を確立する必要があります。
- データ ストアがクラウドの **Azure IaaS VM** に存在する場合でも、**ゲートウェイを使用する**必要があります。

## Data Management Gateway のインストール

### ゲートウェイのインストール - 前提条件
- サポートされている**オペレーティング システム**のバージョンは、Windows 7、Windows 8/8.1、Windows 10、Windows Server 2008 R2、Windows Server 2012、Windows Server 2012 R2 です。ドメイン コントローラーへの Data Management Gateway のインストールは現在サポートされていません。
- ゲートウェイ コンピューターの推奨される最小限の**構成**は、2 GHz、4 コア、8 GB の RAM、80 GB のディスクです。
- ホスト コンピューターが休止状態の場合、ゲートウェイはデータ要求に応答できません。そのため、ゲートウェイをインストールする前に、コンピューターの適切な**電源プラン**を構成します。コンピューターが休止状態に構成されている場合、ゲートウェイのインストール時にメッセージが表示されます。
- Data Management Gateway を正常にインストールして構成するには、コンピューターの管理者である必要があります。他のユーザーをローカル Windows グループの **Data Management Gateway Users** に追加できます。このグループのメンバーは、Data Management Gateway 構成マネージャー ツールを使用して、ゲートウェイを構成できます。

コピー アクティビティは特定の頻度で実行するので、コンピューターのリソース (CPU、メモリ) の使用も同じピークとアイドルのパターンに従います。リソース使用率はまた、移動されるデータの量に大きく依存します。複数のコピー ジョブが進行中のとき、ピーク時にはリソース使用率が上昇するのがわかります。上記は最小限の構成ですが、データ移動の特定の負荷に応じて、最小構成より多くのリソースを構成することを常にお勧めします。

### インストール
Data Management Gateway は、次の方法でインストールすることができます。

- [Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=39717)から MSI セットアップ パッケージをダウンロードします。MSI は、すべての設定を保持しながら、既存の Data Management Gateway を最新のバージョンにアップグレードするためにも使用できます。
- 手動セットアップで **[データ ゲートウェイのダウンロードとインストール]** のリンクをクリックするか、高速セットアップで **[このコンピューターに直接インストールします]** をクリックします。高速セットアップを使う場合の詳細な手順については、[オンプレミスとクラウドの間でのデータ移動](data-factory-move-data-between-onprem-and-cloud.md)に関する記事を参照してください。手動セットアップでは、ダウンロード センターに移動します。ダウンロード センターからゲートウェイをダウンロードしてインストールする方法については次のセクションを参照してください。


### インストールのベスト プラクティス:
1.	コンピューターが休止状態にならないように、ゲートウェイ用のホスト コンピューターの電源プランを構成します。ホスト コンピューターが休止状態の場合、ゲートウェイはデータ要求に応答できません。
2.	ゲートウェイに関連付けられている証明書をバックアップする必要があります。

### ダウンロード センターからゲートウェイをインストールする
1. [Microsoft Data Management Gateway のダウンロード ページ](https://www.microsoft.com/download/details.aspx?id=39717)に移動します。
2. **[ダウンロード]** をクリックし、適切なバージョン (**32 ビット** または **64 ビット**) を選択して、**[次へ]** をクリックします。
3. **MSI** を直接実行するか、ハード ディスクに保存してから実行します。
4. **[ようこそ]** ページで **[言語]** を選択し、**[次へ]** をクリックします。
5. 使用許諾契約に**同意**し、**[次へ]** をクリックします。
6. ゲートウェイをインストールする**フォルダー**を選択し、**[次へ]** をクリックします。
7. **[インストールの準備完了]** ページで **[インストール]** をクリックします。
8. **[完了]** をクリックしてインストールを完了します。
9. Azure ポータルでキーを取得します。手順の詳細については、次のセクションを参照してください。
10. コンピューターで実行している **Data Management Gateway 構成マネージャー**の **[ゲートウェイの登録]** ページで、テキストにキーを貼り付けます。オプションとして、キー文字列を表示する場合は **[ゲートウェイのキーを表示する]** チェック ボックスをオンにし、**[登録]** をクリックします。

### キーを取得する

#### ポータルに論理ゲートウェイをまだ作成していない場合
[オンプレミスとクラウドの間でのデータ移動](data-factory-move-data-between-onprem-and-cloud.md)に関する記事に示されているチュートリアルの手順に従って、ポータルに新しいゲートウェイを作成し、**[構成]** ブレードからキーを取得します。

#### ポータルに論理ゲートウェイを既に作成している場合
1. Azure ポータルで **[Data Factory]** ブレードに移動し、**[リンクされたサービス]** タイルをクリックします。

	![[Data Factory] ブレード](media/data-factory-data-management-gateway/data-factory-blade.png)
2. **[リンクされたサービス]** ブレードで、ポータルに作成した論理**ゲートウェイ**を選択します。

	![論理ゲートウェイ](media/data-factory-data-management-gateway/data-factory-select-gateway.png)
2. **[データ ゲートウェイ]** ブレードで **[データ ゲートウェイをダウンロードしてインストールする]** をクリックします。

	![ポータルのダウンロード リンク](media/data-factory-data-management-gateway/download-and-install-link-on-portal.png)
3. **[構成]** ブレードで、**[キーの再作成]** をクリックします。警告メッセージを注意して読んでから、[はい] をクリックします。

	![キーの再作成](media/data-factory-data-management-gateway/recreate-key-button.png)
4. キーの横にある [コピー] ボタンをクリックして、キーをクリップボードにコピーします。
	
	![キーのコピー](media/data-factory-data-management-gateway/copy-gateway-key.png)

## Data Management Gateway 構成マネージャー 
ゲートウェイをインストールした後、次のどれかの方法で Data Management Gateway 構成マネージャーを起動できます。

- **[検索]** ウィンドウに、このユーティリティにアクセスする **Data Management Gateway** を入力します。
- **C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\Shared** フォルダーの実行可能ファイル **ConfigManager.exe** を実行します。
 
### ホーム ページ
ホーム ページでは、次の操作を行うことができます。

- ゲートウェイの状態を確認する (ゲートウェイがクラウド サービスに接続されているかどうか)。
- ポータルから取得したキーを使って**登録**する。
- ゲートウェイ コンピューターで **Data Management Gateway ホスト サービス**を**停止**および開始する。
- **更新のスケジュール**を特定の時刻に設定する。
- ゲートウェイの**最終更新日時**を確認する。

### [設定] ページ
[設定] ページでは、次の操作を行うことができます。

- ゲートウェイで使われる**証明書**を表示、変更、エクスポートする。この証明書は、データ ソースの資格情報の暗号化に使用されます。
- エンドポイントの **HTTPS ポート**を変更する。ゲートウェイでデータ ソースの資格情報を設定するためにポートが開かれます。
- エンドポイントの**状態**
- **SSL 証明書**を表示する。この証明書は、データ ソースの資格情報を設定するためにポータルとゲートウェイ間の SSL 通信に使用されます。

### [診断] ページ
[診断] ページでは、次の操作を行うことができます。

- 詳細**ログ**の有効化、イベント ビューアーでのログの表示、障害発生時の Microsoft へのログ送信。
- データ ソースへの**接続をテスト**する。

### Help page
[ヘルプ] ページには次の情報が表示されます。

- ゲートウェイの簡単な説明
- バージョン番号
- オンライン ヘルプ、プライバシーに関する声明、使用許諾契約書へのリンク。

## システム トレイ アイコン/通知
次の図は、表示されるトレイ アイコンの一部を示します。

![システム トレイのアイコン](./media/data-factory-data-management-gateway/gateway-tray-icons.png)

システム トレイ アイコン/通知メッセージの上にカーソルを移動すると、ゲートウェイ/更新操作の状態に関する詳細がポップアップ ウィンドウに表示されます。

## Data Management Gateway の更新
既定では、Data Management Gateway の新しいバージョンが利用可能になると、自動的に更新されます。ゲートウェイは、すべてのスケジュールされたタスクが完了するまで、更新されません。更新操作が完了するまで、ゲートウェイは追加のタスクを処理しません。更新が失敗した場合、ゲートウェイは古いバージョンにロールバックします。

更新時刻のスケジュールは、ポータルの [ゲートウェイ プロパティ] ブレード、Data Management Gateway 構成マネージャーのホーム ページ、およびシステム トレイ通知メッセージに表示されます。

Data Management Gateway 構成マネージャーの [ホーム] タブには、更新スケジュールと、ゲートウェイが最後にインストール/更新された時刻が表示されます。

![更新のスケジュール](media/data-factory-data-management-gateway/UpdateSection.png)

今すぐ更新をインストールするオプションと、スケジュールされた時刻にゲートウェイが自動的に更新されるまで待つオプションがあります。たとえば、次のスクリーンショットは Data Management Gateway 構成マネージャーに表示される通知メッセージと、すぐに更新をインストールする場合にクリックする [更新] ボタンを示しています。

![DMG 構成マネージャーの更新](./media/data-factory-data-management-gateway/gateway-auto-update-config-manager.png)

システム トレイの通知メッセージは、次のようになります。

![システム トレイのメッセージ](./media/data-factory-data-management-gateway/gateway-auto-update-tray-message.png)

システム トレイに更新操作 (手動または自動) の状態が表示されます。次に Data Management Gateway 構成マネージャーを開くと、通知バーにゲートウェイが更新されたことを示すメッセージと、[最新情報のトピック](data-factory-gateway-release-notes.md)へのリンクが表示されます。

### 自動更新機能を無効/有効にするには
次の手順で、自動更新機能を無効/有効にすることができます。

1. ゲートウェイ コンピューターで Windows PowerShell を起動します。
2. C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\PowerShellScript フォルダーに切り替えます。
3. 自動更新機能をオフ (無効) にするには、次のコマンドを実行します。

		.\GatewayAutoUpdateToggle.ps1  -off

4. オンに戻すには、次のコマンドを実行します。
	
		.\GatewayAutoUpdateToggle.ps1  -on  

## ポートとセキュリティに関する考慮事項
考慮する必要がある 2 つのファイアウォールがあります。組織の中央ルーターで実行している**企業ファイアウォール**と、ゲートウェイがインストールされているローカル コンピューター上のデーモンとして構成されている **Windows ファイアウォール**です。

![ファイアウォール](./media/data-factory-data-management-gateway/firewalls.png)

## クラウド サービスとゲートウェイの接続
Azure Data Factory とその他のクラウド サービスとのゲートウェイの接続を維持するには、**TCP** ポート **80** と **443** の送信規則が構成されていることを確認する必要があります。任意でポート **9350** から **9354** を有効にします。これらのポートは Microsoft Azure Service Bus が Azure Data Factory と Data Management Gateway の間の接続を確立し、その間の通信のパフォーマンスを向上させるために使用することができます。

企業ファイアウォール レベルで、次のドメインと送信ポートを構成する必要があります。

| ドメイン名 | ポート | 説明 |
| ------ | --------- | ------------ |
| *.servicebus.windows.net | 443、80 | TCP 経由での Service Bus Relay のリスナー (Access Control トークンの取得には 443 が必要) | 
| *.servicebus.windows.net | 9350 ～ 9354 | TCP 経由でのオプションの Service Bus Relay | 
| *.core.windows.net | 443 | HTTPS | 
| *.clouddatahub.net | 443 | HTTPS | 
| graph.windows.net | 443 | HTTPS |
| login.windows.net | 443 | HTTPS | 

Windows のファイアウォール レベルでは、通常これらの送信ポートが有効になっています。有効でない場合は、ゲートウェイ コンピューターに応じたドメインとポートを構成することができます。

## ゲートウェイの資格情報の設定
Azure ポータルにオンプレミスでリンクされたサービスをセットアップする場合、ゲートウェイに資格情報をリレーするために、**[資格情報の設定]** アプリケーションが受信ポート **8050** を使用します (記事の後半で説明します)。既定では、ゲートウェイのセットアップ中に、Data Management Gateway のインストールによってこのポートがゲートウェイ コンピューターで開きます。
 
サードパーティ製のファイアウォールを使用する場合は、ポート 8050 を手動で開くことができます。ゲートウェイのセットアップ中にファイアウォールの問題が発生した場合は、次のコマンドを使用して、ファイアウォールを構成せずにゲートウェイをインストールすることができます。

	msiexec /q /i DataManagementGateway.msi NOFIREWALL=1

ゲートウェイ コンピューター上でポート 8050 を開かず、オンプレミスでリンクされたサービスをセットアップする場合は、**[資格情報の設定]** アプリケーション以外のメカニズムを使用して、データ ストア資格情報を構成する必要があります。たとえば、[New-AzureRmDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) PowerShell コマンドレットを使用できます。データ ストア資格情報を設定する方法については、「[資格情報とセキュリティの設定](#set-credentials-and-securityy)」セクションを参照してください。

**ソース データ ストアからシンク データ ストアにデータをコピーするには:**

ファイアウォール ルールが企業ファイアウォール上で、Windows ファイアウォールがゲートウェイ コンピューター上で適切に有効化されていること、およびデータ ストア自体が適切に有効化されていることを確認する必要があります。これにより、ゲートウェイはソースとシンクの両方に正常に接続されます。コピー操作に関連するデータ ストアごとにルールを有効にする必要があります。

たとえば、**Azure SQL Database シンクまたは Azure SQL Data Warehouse シンクにオンプレミスのデータ ストア**からコピーするために、送信 **TCP** 通信を Windows ファイアウォールと企業ファイアウォールの両方に対してポート **1433** 上で許可する必要があり、許可された IP アドレスの一覧にゲートウェイ コンピューターの IP アドレスを追加するために、Azure SQL サーバーのファイアウォール設定を構成する必要があります。

### プロキシ サーバーに関する考慮事項
既定では、Data Management Gateway は、Internet Explorer からプロキシ設定を利用し、アクセスするために既定の資格情報を使用します。このケースに合わない場合は、ゲートウェイが Azure Data Factory に接続できることを確認するために、次に示すように**プロキシ サーバー設定**をさらに構成できます。

1.	ファイル エクスプ ローラーで、Data Management Gateway をインストールした後に、"C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\Shared\\diahost.exe.config" の安全なコピーを作成し、元のファイルをバックアップします。
2.	管理者として Notepad.exe を起動し、テキスト ファイル "C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\Shared\\diahost.exe.config" を開きます。次のように system.net に対して既定のタグが表示されます。

			<system.net>
				<defaultProxy useDefaultCredentials="true" />
			</system.net>	

	その後、プロキシ サーバーの詳細 (例: その親タグ内のプロキシ アドレス) を追加できます。例:

			<system.net>
			      <defaultProxy enabled="true">
			            <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
			      </defaultProxy>
			</system.net>

	追加のプロパティは、プロキシ タグ内で scriptLocation のように必要な設定を指定することが許可されます。構文の「[proxy 要素 (ネットワーク設定)](https://msdn.microsoft.com/library/sa91de1e.aspx)」を参照してください。

			<proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>

3. 構成ファイルを元の場所に保存し、 Data Management Gateway サービスを再起動し、変更を取得します。これを行うには、**[スタート]**、**[Services.msc]** から、または **[Data Management Gateway 構成マネージャー]**、**[サービスの停止]** ボタンの順にクリックしてから、**[サービスの開始]** をクリックします。サービスが開始しない場合は、不正確な XML タグ構文が編集済みのアプリケーション構成ファイルに追加されている可能性があります。

上記の点に加えて、Microsoft Azure が会社のホワイトリストにあることを確認する必要もあります。有効な Microsoft Azure の IP アドレスの一覧は、[Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=41653)からダウンロードできます。

### ファイアウォールとプロキシ サーバー関連の問題で発生する可能性がある症状:
次のようなエラーが発生した場合は、ファイアウォールまたはプロキシ サーバーの不適切な構成により、Data Management Gateway が自身を認証するための Azure Data Factory への接続からブロックされる可能性があります。ファイアウォールとプロキシ サーバーが正しく構成されていることを確認するには、前のセクションを参照してください。

1.	ゲートウェイを登録しようとすると次のエラーが発生します。「ゲートウェイのキーを登録できませんでした。ゲートウェイ キーの登録を再試行する前に、Data Management Gateway が接続状態で、Data Management Gateway Host Service が起動していることを確認してください。」
2.	構成マネージャーを開くと、ステータスは「切断」または「接続」と表示されます。[イベント ビューアー]、[アプリケーションとサービス ログ]、[Data Management Gateway] で Windows イベント ログを表示すると、「リモート サーバーに接続できません」や「Data Management Gateway のコンポーネントが応答しなくなり、自動的に再起動されます。コンポーネント名: Gateway」といったエラー メッセージが表示されます。

## ゲートウェイに関する問題のトラブルシューティング

- 詳細な情報は、Windows イベント ログのゲートウェイ ログで確認できます。Windows の **[イベント ビューアー]**、**[アプリケーションとサービス ログ]**、**[Data Management Gateway]** を使用してログを確認できます。ゲートウェイ関連の問題のトラブルシューティングでは、イベント ビューアーでエラー レベルのイベントを調べてください。
- **証明書を変更**した後にゲートウェイが動作しなくなった場合は、Microsoft Data Management Gateway 構成マネージャー ツールまたはコントロール パネルの [サービス] アプレットを使って、**Data Management Gateway Service** を再起動 (停止して開始) します。エラーが表示された場合は、Data Management Gateway サービスのユーザーが証明書マネージャー (certmgr.msc) で証明書にアクセスするための明示的なアクセス許可を付与する必要がある場合があります。サービスの既定のユーザー アカウントは **NT Service\\DIAHostService** です。
- Data Factory エディターで [暗号化] ボタンをクリックしたときに、**資格情報マネージャー** アプリケーションで資格情報を**暗号化**できなかった場合は、ゲートウェイをインストールしたコンピューターでこのアプリケーションを実行していることを確認してください。実行されていない場合は、ゲートウェイ コンピューターでアプリケーションを実行してから、資格情報の暗号化を試みます。
- データ ストア接続やドライバーに関連したエラーが表示された場合は、ゲートウェイ コンピューターで **Data Management Gateway 構成マネージャー**を起動し、**[診断]** タブに切り替えて、**[このゲートウェイを使用してオンプレミスのデータ ソースへの接続をテストします]** グループのフィールドで適切な値を選択または入力した後、**[接続テスト]** をクリックすると、その接続情報と資格情報を使用してゲートウェイ コンピューターからオンプレミスのデータ ソースに接続できるかどうかがわかります。ドライバーのインストール後も接続テストが失敗する場合は、最新の変更を認識できるようにゲートウェイを再起動します。

	![接続テスト](./media/data-factory-data-management-gateway/TestConnection.png)

### ゲートウェイ ログを Microsoft に送信する
ゲートウェイに問題があり、Microsoft サポートに連絡する必要がある場合は、ゲートウェイ ログを共有するように求められる場合があります。ゲートウェイのリリース版では、ゲートウェイ構成マネージャーでボタンを 2 回クリックして、求められたゲートウェイ ログを簡単に共有することができます。

1. ゲートウェイ構成マネージャーの **[診断]** タブに切り替えます。
 
	![Data Managemetn Gateway - [診断] タブ](media/data-factory-data-management-gateway/data-management-gateway-diagnostics-tab.png)
2. **[ログの送信]** リンクをクリックして次のダイアログ ボックスを表示します。

	![Data Management Gateway - ログの送信](media/data-factory-data-management-gateway/data-management-gateway-send-logs-dialog.png)
3. (省略可能) **[ログの表示]** をクリックしてイベント ビューアーでログを確認します。
4. (省略可能) **[プライバシー]** をクリックして Microsoft オンライン サービスのプライバシーに関する声明を表示します。
3. アップロードする情報に問題がなければ、**[ログの送信]** をクリックすると、トラブルシューティングのために過去 7 日間のログが Microsoft に実際に送信されます。ログ送信操作の状態が次のように表示されます。

	![Data Management Gateway - ログの送信の状態](media/data-factory-data-management-gateway/data-management-gateway-send-logs-status.png)
4. 操作が完了すると、次のようなダイアログ ボックスが表示されます。
	
	![Data Management Gateway - ログの送信の状態](media/data-factory-data-management-gateway/data-management-gateway-send-logs-result.png)
5. **レポート ID** を書き留めておき、Microsoft サポートに知らせてください。レポート ID は、トラブルシューティング用にアップロードしたゲートウェイ ログを特定するために使われます。レポート ID は参照用にイベント ビューアーにも保存されます。レポート ID を探すときは、イベント ID "25" を見つけて日付と時刻を確認します。
	
	![Data Management Gateway - ログの送信のレポート ID](media/data-factory-data-management-gateway/data-management-gateway-send-logs-report-id.png)

### ゲートウェイ ホスト コンピューターでゲートウェイ ログをアーカイブする
ゲートウェイに問題が発生し、ゲートウェイ ログを直接共有できない場合として、次のようないくつかのシナリオがあります。

- ユーザーが手動でゲートウェイをインストールしてゲートウェイを登録する場合
- ユーザーが構成マネージャーで再生成されたキーを使ってゲートウェイを登録しようとしている場合
- ユーザーがログを送信しようとして、ゲートウェイ ホスト サービスに接続できない場合

このような場合は、ゲートウェイ ログを zip ファイルとして保存し、後で Microsoft サポートに連絡するときにそれを共有できます。たとえば、ゲートウェイを登録するときに次のようなエラーが表示された場合:

![Data Management Gateway - 登録エラー](media/data-factory-data-management-gateway/data-management-gateway-registration-error.png)

**[ゲートウェイ ログのアーカイブ]** リンクをクリックしてログをアーカイブして保存し、zip ファイルを Microsoft サポートと共有します。

![Data Management Gateway - ログのアーカイブ](media/data-factory-data-management-gateway/data-management-gateway-archive-logs.png)

 
## コンピューターから別のコンピューターへのゲートウェイの移動
このセクションでは、1 台のコンピューターから別のコンピューターにゲートウェイ クライアントを移動する手順を説明します。

2. ポータルで **Data Factory ホーム ページ**に移動し、**[リンクされたサービス]** タイルをクリックします。

	![データ ゲートウェイ リンク](./media/data-factory-data-management-gateway/DataGatewaysLink.png)
3. **[リンクされたサービス]** ブレードの **[データ ゲートウェイ]** セクションでゲートウェイを選択します。
	
	![ゲートウェイが選択された状態の [リンクされたサービス] ブレード](./media/data-factory-data-management-gateway/LinkedServiceBladeWithGateway.png)
4. **[データ ゲートウェイ]** ブレードで **[データ ゲートウェイをダウンロードしてインストールする]** をクリックします。
	
	![ダウンロード ゲートウェイ リンク](./media/data-factory-data-management-gateway/DownloadGatewayLink.png)
5. **[構成]** ブレードで **[データ ゲートウェイをダウンロードしてインストールする]** をクリックし、指示に従ってコンピューターにデータ ゲートウェイをインストールします。

	![[構成] ブレード](./media/data-factory-data-management-gateway/ConfigureBlade.png)
6. **Microsoft Data Management Gateway Configuration Manager** を開いたままにします。
 
	![構成マネージャー](./media/data-factory-data-management-gateway/ConfigurationManager.png)
7. ポータルの **[構成]** ブレードでコマンド バーの **[キーの再作成]** をクリックし、警告メッセージで **[はい]** をクリックします。キー文字列の横にある **[コピー]** ボタンをクリックして、キーをクリップボードにコピーします。古いコンピューターのゲートウェイは、キーが再作成されるとすぐに機能を停止することに注意してください。
	
	![キーの再作成](./media/data-factory-data-management-gateway/RecreateKey.png)
	 
8. **キー**をコンピューター上の **Data Management Gateway Configuration Manager** の **[ゲートウェイの登録]** ページにあるテキスト ボックスに貼り付けます。(省略可能) **[ゲートウェイのキーを表示する]** チェック ボックスをオンにしてキー文字列を表示します。
 
	![キーのコピーと登録](./media/data-factory-data-management-gateway/CopyKeyAndRegister.png)
9. **[登録]** をクリックして、ゲートウェイをクラウド サービスに登録します。
10. **[設定]** タブで **[変更]** をクリックして以前のゲートウェイで使用されていたのと同じ証明書を選択し、**[パスワード]** を入力して **[完了]** をクリックします。
 
	![証明書の指定](./media/data-factory-data-management-gateway/SpecifyCertificate.png)

	古いゲートウェイから証明書をエクスポートするには、次の手順に従います。古いコンピューターで Data Management Gateway Configuration Manager を起動し、**[証明書]** タブに切り替え、**[エクスポート]** をクリックし、指示に従います。
10. ゲートウェイの登録が成功したら、Gateway Configuration Manager のホーム ページで、**[登録]** が **[登録済み]** に、**[状態]** が **[開始]** に設定されていることを確認する必要があります。

## 資格情報とセキュリティの設定
Data Factory エディターで資格情報を暗号化するには、次の操作を行います。

1. **ゲートウェイ コンピューター**上で Web ブラウザーを起動し、[Azure ポータル](http://portal.azure.com)に移動します。必要に応じてデータ ファクトリを検索して **[Data Factory]** ブレードで開き、**[作成およびデプロイ]** をクリックして Data Factory エディターを起動します。
1. ツリー ビューの既存の**リンクされたサービス**をクリックしてその JSON 定義を参照するか、Data Management Gateway を必要とするリンクされたサービスを新規に作成します (例: SQL Server や Oracle)。
2. JSON エディターで、**gatewayName** プロパティにゲートウェイの名前を入力します。
3. **connectionString** の **[データ ソース]** プロパティにサーバー名を入力します。
4. **connectionString** の **[初期カタログ]** プロパティにデータベース名を入力します。
5. コマンド バーの **[暗号化]** ボタンをクリックして、ClickOnce **資格情報マネージャー** アプリケーションを起動します。**[資格情報の設定]** ダイアログ ボックスが表示されます。![[資格情報の設定] ダイアログ](./media/data-factory-data-management-gateway/setting-credentials-dialog.png)
6. **[資格情報の設定]** ダイアログ ボックスで、次の手順を実行します。
	1.	Data Factory サービスがデータベースへの接続に使用する**認証**を選択します。
	2.	**[ユーザー名]** の設定に、データベースへのアクセス権を持つユーザーの名前を入力します。
	3.	**[パスワード]** の設定に、ユーザーのパスワードを入力します。
	4.	**[OK]** をクリックして、資格情報を暗号化し、ダイアログ ボックスを閉じます。
5.	これで、**connectionString** 内に **encryptedCredential** プロパティが表示されます。
		
			{
	    		"name": "SqlServerLinkedService",
		    	"properties": {
		        	"type": "OnPremisesSqlServer",
			        "description": "",
		    	    "typeProperties": {
		    	        "connectionString": "data source=myserver;initial catalog=mydatabase;Integrated Security=False;EncryptedCredential=eyJDb25uZWN0aW9uU3R",
		            	"gatewayName": "adftutorialgateway"
		        	}
		    	}
			}

ゲートウェイ コンピューターとは異なるコンピューターからポータルにアクセスする場合は、資格情報マネージャー アプリケーションがゲートウェイ コンピューターに接続できることを確認する必要があります。アプリケーションがゲートウェイ コンピューターにアクセスできない場合、データ ソースの資格情報を設定したり、データ ソースへの接続をテストしたりすることはできません。

Azure ポータルから起動した **[資格情報の設定]** アプリケーションを使用してオンプレミスのデータ ソースの資格情報を設定すると、ポータルは、ユーザーがゲートウェイ コンピューターの **Data Management Gateway 構成マネージャー**の **[証明書]** タブで指定した証明書で資格情報を暗号化します。

API を使用して資格情報を暗号化した方がよい場合は、[New-AzureDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) PowerShell コマンドレットを使用して資格情報を暗号化できます。コマンドレットはゲートウェイに構成されている証明書を使用して資格情報を暗号化します。このコマンドレットによって返される資格情報を、[New-AzureRmDataFactoryLinkedService](https://msdn.microsoft.com/library/mt603647.aspx) コマンドレットで使用する JSON ファイルの **connectionString** の **EncryptedCredential** 要素や、ポータルの Data Factory エディターで JSON スニペットに追加できます。

	"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",

Data Factory エディターを使用して資格情報を設定するもう 1 つの方法があります。エディターを使用して SQL Server のリンクされたサービスを作成し、プレーン テキストで資格情報を入力した場合、資格情報は、ゲートウェイが使用するように構成されている証明書ではなく、Data Factory が所有する証明書で暗号化されます。この方法は、場合によっては少し高速ですが、セキュリティは低下します。したがって、この方法は開発/テストの目的のみに使用することをお勧めします。


## PowerShell コマンドレット 
このセクションでは、Azure PowerShell コマンドレットを使用して、ゲートウェイを作成し登録する方法について説明します。

1. 管理者モードで **Azure PowerShell** を起動します。
2. 次のコマンドを実行して Azure 資格情報を入力することにより、Azure アカウントにログインします。

	Login-AzureRmAccount
2. **New-AzureRmDataFactoryGateway** コマンドレットを使用して、次のように論理ゲートウェイを作成します。

		$MyDMG = New-AzureRmDataFactoryGateway -Name <gatewayName> -DataFactoryName <dataFactoryName> -ResourceGroupName ADF –Description <desc>

	**コマンドと出力の例**:


		PS C:\> $MyDMG = New-AzureRmDataFactoryGateway -Name MyGateway -DataFactoryName $df -ResourceGroupName ADF –Description “gateway for walkthrough”

		Name              : MyGateway
		Description       : gateway for walkthrough
		Version           :
		Status            : NeedRegistration
		VersionStatus     : None
		CreateTime        : 9/28/2014 10:58:22
		RegisterTime      :
		LastConnectTime   :
		ExpiryTime        :
		ProvisioningState : Succeeded
		Key               : ADF#00000000-0000-4fb8-a867-947877aef6cb@fda06d87-f446-43b1-9485-78af26b8bab0@4707262b-dc25-4fe5-881c-c8a7c3c569fe@wu#nfU4aBlq/heRyYFZ2Xt/CD+7i73PEO521Sj2AFOCmiI

	
4. Azure PowerShell で **C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\PowerShellScript** フォルダーに移動します。次のコマンドに示すように、ローカル変数 **$Key** に関連付けられた **RegisterGateway.ps1** スクリプトを実行して、コンピューターにインストールされているクライアント エージェントを、前に作成した論理ゲートウェイに登録します。

		PS C:\> .\RegisterGateway.ps1 $MyDMG.Key
		
		Agent registration is successful!

	IsRegisterOnRemoteMachine パラメーターを使用することで、リモート マシンにゲートウェイを登録できます。例:
		
		.\RegisterGateway.ps1 $MyDMG.Key -IsRegisterOnRemoteMachine true

5. **Get-AzureRmDataFactoryGateway** コマンドレットを使用して、データ ファクトリ内のゲートウェイの一覧を取得できます。**[状態]** に **[オンライン]** と表示されている場合、ゲートウェイをいつでも使用できることを意味します。

		Get-AzureRmDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF

**Remove-AzureRmDataFactoryGateway** コマンドレットを使用して、ゲートウェイを削除できます。また、**Set-AzureRmDataFactoryGateway** コマンドレットを使用して、ゲートウェイの記述を更新できます。これらのコマンドレットの文法やその他の詳細については「Data Factory Cmdlet Reference (Data Factory コマンドレット リファレンス)」を参照してください。

### PowerShell を使用したゲートウェイの一覧表示

	Get-AzureRmDataFactoryGateway -DataFactoryName jasoncopyusingstoredprocedure -ResourceGroupName ADF_ResourceGroup

### PowerShell を使用したゲートウェイの削除
	
	Remove-AzureRmDataFactoryGateway -Name JasonHDMG_byPSRemote -ResourceGroupName ADF_ResourceGroup -DataFactoryName jasoncopyusingstoredprocedure -Force 


## 次のステップ
- ゲートウェイの詳細については、「[Data Management Gateway](data-factory-data-management-gateway.md)」の記事をご覧ください。

<!---HONumber=AcomDC_0727_2016-->