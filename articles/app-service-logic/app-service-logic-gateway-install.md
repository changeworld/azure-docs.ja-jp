<properties
   pageTitle="オンプレミス データ ゲートウェイへの Logic Apps のインストール | Microsoft Azure"
   description="ロジック アプリで使用するためにオンプレミス データ ゲートウェイをインストールする方法"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="07/05/2016"
   ms.author="jehollan"/>


# <a name="install-the-on-premises-data-gateway-for-logic-apps"></a>Logic Apps 向けのオンプレミス データ ゲートウェイのインストール

## <a name="installation-and-configuration"></a>インストールと構成

### <a name="prerequisites"></a>前提条件

最小:

* .NET Framework 4.5
* Windows 7 または Windows Server 2008 R2 (以降) の 64 ビット バージョン

推奨:

* 8 コア CPU
* 8 GB メモリ
* Windows 2012 R2 (以降) の 64 ビット バージョン

関連する考慮事項:

* ドメイン コント ローラーにはゲートウェイをインストールできません。
* ノート PC など、電源の切断、スリープ状態、インターネット接続の切断が発生する可能性があるコンピューターにはゲートウェイをインストールしないでください。ゲートウェイは、これらの状況では実行できないためです。 また、ワイヤレス ネットワークではゲートウェイのパフォーマンスが低下する可能性があります。

### <a name="install-a-gateway"></a>ゲートウェイのインストール

[オンプレミス データ ゲートウェイのインストーラーはここで](http://go.microsoft.com/fwlink/?LinkID=820931&clcid=0x409)入手できます。

モードとして**オンプレミス データ ゲートウェイ**を指定し、職場または学校のアカウントを使用してサインインし、新しいゲートウェイを構成します。あるいは、既存のゲートウェイの移行、復元、または引き継ぎを行います。

* ゲートウェイを構成するには、**名前**と**回復キー**を入力し、**構成**をクリックまたはタップします。

    8 文字以上の回復キーを指定します。これは安全な場所に記録しておいてください。 このキーは、ゲートウェイの移行、復元、または引き継ぎを実行する場合に必要になります。

* 既存のゲートウェイの移行、復元、または引き継ぎを行うには、ゲートウェイの作成時に指定された回復キーを指定します。

### <a name="restart-the-gateway"></a>ゲートウェイの再起動

ゲートウェイは Windows サービスとして実行されます。他の Windows サービスと同様に、開始や停止は複数の方法で行うことができます。 たとえば、ゲートウェイが実行されているコンピューターに対する管理者特権でコマンド プロンプトを開き、次のコマンドのいずれかを実行します。

* サービスを停止するには、次のコマンドを実行します。

    `net stop PBIEgwService`

* サービスを開始するには、次のコマンドを実行します。

    `net start PBIEgwService`

### <a name="configure-a-firewall-or-proxy"></a>ファイアウォールまたはプロキシの構成

ゲートウェイのプロキシ情報を指定する方法については、「[Power BI Gateway のプロキシ設定を構成する](https://powerbi.microsoft.com/en-us/documentation/powerbi-gateway-proxy/)」を参照してください。

ファイアウォールまたはプロキシが接続をブロックしているかどうかを確認できます。これには、PowerShell プロンプトから次に示すコマンドを実行します。 これによって、Azure Service Bus に対する接続がテストされます。 これはネットワーク接続のみのテストで、クラウド サーバーのサービスまたはゲートウェイに関する確認は行われません。 コンピューターが実際にインターネットに接続できるかどうかを判断するために役立ちます。

`Test-NetConnection -ComputerName watchdog.servicebus.windows.net -Port 9350`

結果は、次の例のようになります。 **TcpTestSucceeded** が true でない場合、ファイアウォールによってブロックされている可能性があります。

```
ComputerName           : watchdog.servicebus.windows.net
RemoteAddress          : 70.37.104.240
RemotePort             : 5672
InterfaceAlias         : vEthernet (Broadcom NetXtreme Gigabit Ethernet - Virtual Switch)
SourceAddress          : 10.120.60.105
PingSucceeded          : False
PingReplyDetails (RTT) : 0 ms
TcpTestSucceeded       : True
```

すべて網羅する場合は、**ComputerName** および **Port** の値を、このトピックに後述している[ポートの構成](#configure-ports) に表示されているものと置き換えます。

このファイアウォールは、Azure Service Bus から Azure データ センターへの接続もブロックしている可能性があります。 その場合は、それらのデータ センター向けの現在のリージョンの IP アドレスすべてをホワイト リストに登録してください (ブロックが解除されます)。 [ここで Azure IP アドレス](https://www.microsoft.com/download/details.aspx?id=41653)の一覧を入手できます。

### <a name="configure-ports"></a>ポートの構成

ゲートウェイは、Azure Service Bus への送信接続を作成します。 通信を行う送信ポートは、TCP 443 (既定)、5671、5672、9350 - 9354 です。 ゲートウェイでは受信ポートは必要ありません。

[ハイブリッド ソリューション](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)の詳細についてはこちらをご覧ください。

| ドメイン名 | 送信ポート | Description |
| ----- | ------ | ------ |
| *. analysis.windows.net | 使用します | HTTPS |
| *.login.windows.net | 443 | HTTPS |
| *.servicebus.windows.net |5671 - 5672 | Advanced Message Queuing Protocol (AMQP) |
| *.servicebus.windows.net | 443、9350 - 9354 | TCP 経由での Service Bus Relay のリスナー (Access Control トークンの取得には 443 が必要) |
| *. frontend.clouddatahub.net | 使用します | HTTPS |
| *.core.windows.net | 443 | HTTPS |
| login.microsoftonline.com | 443 | HTTPS |
| *. msftncsi.com | 使用します | Power BI サービスによってゲートウェイにアクセスできない場合、インターネット接続のテストに使用されます。 |

ドメインではなく IP アドレスのホワイト リストを作成する必要がある場合、「 [Microsoft Azure Datacenter IP ranges list](https://www.microsoft.com/download/details.aspx?id=41653)」(Microsoft Azure Datacenter IP 範囲リスト) をダウンロードして使用できます。 場合によっては、Azure Service Bus 接続は完全修飾ドメイン名ではなく IP アドレスを使用して行われます。

### <a name="sign-in-account"></a>サインイン アカウント

ユーザーは、職場または学校のアカウントを使用してサインインします。 これは組織アカウントです。 Office 365 サービスにサインアップして、実際の職場の電子メールを指定しなかった場合は、 jeff@contoso.onmicrosoft.com. クラウド サービス内でアカウントは Azure Active Directory (AAD) のテナントに格納されます。 ほとんどの場合、AAD アカウントの UPN が電子メール アドレスと一致します。

### <a name="windows-service-account"></a>Windows サービス アカウント

オンプレミス データ ゲートウェイは、Windows サービスのログオン資格情報に関して NT SERVICE\PBIEgwService を使用するように構成されています。 既定でサービスとしてログオンする権限を持っています。 これは、ゲートウェイをインストールしているコンピューターのコンテキストです。

これは、オンプレミス データ ソースへの接続に使用されるアカウントでも、クラウド サービスにサインインする職場または学校のアカウントでもありません。

##<a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="general"></a>全般

**質問**: ゲートウェイではどのようなデータ ソースがサポートされますか?<br/>
**回答**: この文書の作成時点では、SQL Server です。

**質問**: SQL Azure など、クラウドのデータ ソースにゲートウェイは必要ですか? <br/>
**回答**: いいえ。 ゲートウェイは、オンプレミス データ ソースのみに接続します。

**質問**: 実際の Windows サービスは何と呼ばれていますか?<br/>
**回答**: サービスでは、ゲートウェイは、Power BI エンタープライズ ゲートウェイ サービスと呼ばれています。

**質問**: は、クラウドからゲートウェイへの着信接続はありますか? <br/>
**回答**: いいえ。 ゲートウェイは、Azure Service Bus への発信接続を使用します。

**質問**: 発信接続をブロックしたらどうなりますか? 開くために何をする必要がありますか? <br/>
**回答**: ゲートウェイが使用するポートとホストを確認してください。


**質問**: ゲートウェイはデータ ソースと同じコンピューターにインストールする必要がありますか? <br/>
**回答**: いいえ。 ゲートウェイは、指定された接続情報を使用してデータ ソースに接続します。 この意味ではゲートウェイをクライアント アプリケーションと考えてください。 指定されたサーバー名に接続できることのみが必要です。


**質問**: ゲートウェイからデータ ソースにクエリを実行する際に待機時間が発生するのはなぜですか? 最適なアーキテクチャとは何ですか? <br/>
**回答**: ネットワークの待機時間を減らすには、できるだけデータ ソースの近くにゲートウェイをインストールします。 実際のデータ ソースにゲートウェイをインストールできる場合は、生成する待機時間が最小限になります。 データ センターについても検討してください。 たとえば、サービスが米国西部データ センターを使用しており、SQL Server が Azure VM でホストされている場合、Azure VM も米国西部データ センターに配置することをお勧めします。 待機時間が最小限になり、Azure VM の送信料金が回避されます。


**質問**: のネットワーク帯域幅の要件はありますか? <br/>
**回答**: ネットワーク接続のために十分なスループットを確保することをお勧めします。 すべての環境は異なっており、送信されるデータの量が結果に影響を与えます。 ExpressRoute を使用すると、オンプレミスと Azure データ センター間で一定レベルのスループットを保証するために役立ちます。

サード パーティ製のツール Azure 速度テスト アプリを使用して、スループットを測定することができます。


**質問**: ゲートウェイ Windows サービスは、Azure Active Directory アカウントを使用して実行できますか? <br/>
**回答**: いいえ。 Windows サービスには有効な Windows アカウントが必要です。 既定では、サービス SID、NT SERVICE\PBIEgwService で実行します。


**質問**: 結果はどのようにクラウドに送られますか? <br/>
**回答**: これは、Azure Service Bus を使用して行われます。 詳細については、そのしくみを参照してください。


**質問**: 自分の資格情報はどこに格納されますか? <br/>
**回答**: データ ソース用に入力した資格情報は、暗号化されてゲートウェイ クラウド サービスに格納されます。 資格情報はオンプレミス ゲートウェイで復号化されます。

### <a name="high-availability/disaster-recovery"></a>高可用性と障害復旧

**質問**: ゲートウェイで高可用性のシナリオを有効にする予定はありますか? <br/>
**回答**: 計画は存在していますが、具体的な時期は決まっていません。


**質問**: 障害復旧のためにはどのようなオプションを使用できますか? <br/>
**回答**: 回復キーを使用して、ゲートウェイを復元または移動することができます。 ゲートウェイをインストールするときに、回復キーを指定します。


**質問**: 回復キーの利点は何ですか? <br/>
**回答**: 災害発生後に、ゲートウェイの設定を移行または回復することができます。

### <a name="troubleshooting"></a>トラブルシューティング

**質問**: ゲートウェイのログはありますか? <br/>
**回答**: このトピックの後半の「ツール」を参照してください。


**質問**: オンプレミス データ ソースに送信されるクエリはどのようにして確認できますか? <br/>
**回答**: 送信されるクエリが含まれるクエリ トレースを有効にすることができます。 トラブルシューティングが完了したら元の値に戻してください。 クエリ トレースを有効のままにしておくと、ログがさらに拡大します。

クエリをトレースするためにデータ ソースに用意されているツールを使用することもできます。 たとえば、SQL Server 用の拡張イベントまたは SQL Profiler や Analysis Services を使用できます。

## <a name="how-the-gateway-works"></a>ゲートウェイのしくみ

ユーザーがオンプレミス データ ソースに接続されている要素を操作するとき:

1. クラウド サービスが、クエリと、データ ソース用の暗号化された資格情報を作成し、ゲートウェイが処理するようにクエリをキューに送信します。
1. このサービスはクエリを分析して、Azure Service Bus に要求をプッシュします。
1. オンプレミス データ ゲートウェイは、保留中の要求がないか Azure Service Bus をポーリングします。
1. ゲートウェイは、クエリを取得し、資格情報を復号化し、その資格情報によってデータ ソースに接続します。
1. ゲートウェイは、実行するためにクエリをデータ ソースに送信します。
1. 結果が、データ ソースからゲートウェイに送信され、クラウド サービスに配置されます。 サービスが結果を使用します。

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="update-to-the-latest-version"></a>最新バージョンへの更新

多くの問題は、ゲートウェイのバージョンが古い場合に現れます。  一般的な方法として、最新のバージョンかどうかを確認することをお勧めします。  1 か月またはそれ以上ゲートウェイを更新していない場合は、最新バージョンのゲートウェイをインストールしても、問題が発生するかを確認してください。

### <a name="error:-failed-to-add-user-to-group.-(-2147463168-pbiegwservice-performance-log-users-)"></a>エラー: ユーザーをグループに追加できませんでした。 (-2147463168 PBIEgwService Performance Log Users )

ドメイン コントローラー (サポート対象外) にゲートウェイをインストールしようとすると、このエラーを受け取ることがあります。 ドメイン コントローラー以外のコンピューターにゲートウェイをデプロイする必要があります。

## <a name="tools"></a>ツール

### <a name="collecting-logs-from-the-gateway-configurator"></a>ゲートウェイの構成ウィザードからのログの収集

ゲートウェイのいくつかのログを収集することができます。 必ず最初にログを確認します。

#### <a name="installer-logs"></a>インストーラー ログ

`%localappdata%\Temp\Power_BI_Gateway_–Enterprise.log`

#### <a name="configuration-logs"></a>構成ログ

`%localappdata%\Microsoft\Power BI Enterprise Gateway\GatewayConfigurator.log`

#### <a name="enterprise-gateway-service-logs"></a>エンタープライズ ゲートウェイ サービスのログ

`C:\Users\PBIEgwService\AppData\Local\Microsoft\Power BI Enterprise Gateway\EnterpriseGateway.log`

#### <a name="event-logs"></a>イベント ログ

データ管理ゲートウェイと PowerBIGateway のログは、 **[アプリケーションとサービス ログ]**にあります。

### <a name="fiddler-trace"></a>Fiddler のトレース

[Fiddler](http://www.telerik.com/fiddler) は、HTTP トラフィックを監視する Telerik の無料ツールです。  Power BI サービスとクライアント コンピューターの間のさまざまな要素を確認できます。 これによって、エラーやその他の関連する情報が明らかになることがあります。

## <a name="next-steps"></a>次のステップ
- [Logic Apps へのオンプレミス接続の作成](app-service-logic-gateway-connection.md)
- [エンタープライズ統合機能](app-service-logic-enterprise-integration-overview.md)
- [Logic Apps コネクタ](../connectors/apis-list.md)


<!--HONumber=Oct16_HO2-->


