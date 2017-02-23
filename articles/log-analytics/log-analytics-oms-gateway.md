---
title: "OMS ゲートウェイを使って OMS にコンピューターとデバイスを接続する | Microsoft Docs"
description: "OMS で管理されたデバイスと Operations Manager で監視されたコンピューターがインターネットにアクセスできないときに、OMS ゲートウェイを使ってそれらを OMS サービスに接続してデータを送信します。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: ae9a1623-d2ba-41d3-bd97-36e65d3ca119
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 6f9974c109905f432705b85dcc8fc4d3549f16e9
ms.openlocfilehash: 445bc1259cb3fa6f02fa1cadec11b1ac4b186e78


---
# <a name="connect-computers-and-devices-to-oms-using-the-oms-gateway"></a>OMS ゲートウェイを使って OMS にコンピューターとデバイスを接続する
このドキュメントでは、OMS で管理されたデバイスと System Center Operations Manager (SCOM) で監視されたコンピューターがインターネットにアクセスできないときに OMS サービスにデータを送信する方法を説明します。 OMS ゲートウェイは、これらのデバイスやコンピューターの代わりにデータを収集して OMS サービスに送信できます。

ゲートウェイは HTTP 転送プロキシであり、HTTP CONNECT コマンドを使って HTTP トンネリングをサポートします。 ゲートウェイは、Windows を搭載する 4 コア CPU、16 GB のサーバーで実行した場合、最大 2,000 個の同時接続 OMS デバイスを処理できます。

たとえば、エンタープライズや大規模な組織のサーバーが、ネットワーク接続はあってもインターネットに接続していない場合があります。 または、直接監視する手段のない多数の POS (Point of Sale) デバイスを使っている場合があります。 さらに別の例としては、Operations Manager がプロキシ サーバーとして OMS ゲートウェイを使える場合があります。 これらの例の OMS ゲートウェイは、このようなサーバーや POS デバイスにインストールされているエージェントから OMS にデータを転送できます。

個々の各エージェントがインターネットに直接接続して OMS にデータを直接送信するのではなく、すべてのエージェント データはインターネットに接続している単一のコンピューターを通して送信されます。 そのコンピューターにゲートウェイをインストールして使います。 このシナリオでは、データを収集する任意のコンピューターにエージェントをインストールできます。 ゲートウェイは、エージェントから OMS にデータを直接転送します。ゲートウェイが転送されるデータを分析することはありません。

OMS エージェントは、ゲートウェイもインストールされているコンピューター上にインストールする必要があります。 これにより、OMS ゲートウェイの監視のほか、OMS ゲートウェイがインストールされたサーバーのパフォーマンスとイベント データの分析も可能になります。 さらに、このエージェントにより、OMS ゲートウェイが通信する必要があるサービス エンドポイントを特定できるようになります。

OMS にデータをアップロードするには、ゲートウェイはインターネットにアクセスできる必要があります。 また、各エージェントは、ゲートウェイとの間で双方向にデータを自動的に転送できるように、ゲートウェイにネットワーク接続する必要があります。 最善の結果を得るには、ドメイン コントローラーでもあるコンピューターにゲートウェイをインストールしないでください。

次の図では、直接接続されたエージェントから OMS へのデータ フローを示します。

![直接エージェントの図](./media/log-analytics-oms-gateway/direct-agent-diagram.png)

次の図では、Operations Manager から OMS へのデータ フローを示します。

![Operations Manager の図](./media/log-analytics-oms-gateway/scom-mgt-server.png)

## <a name="language-availability"></a>利用可能な言語

OMS ゲートウェイは、次の言語で利用できます。

- 中国語 (簡体字)
- 中国語 (繁体字)
- チェコ語
- オランダ語
- 英語
- フランス語
- ドイツ語
- ハンガリー語
- イタリア語
- 日本語
- 韓国語
- ポーランド語
- ポルトガル語 (ブラジル)
- ポルトガル語 (ポルトガル)
- ロシア語
- スペイン語 (インターナショナル)

## <a name="download-the-oms-gateway"></a>OMS ゲートウェイをダウンロードする

OMS ゲートウェイのセットアップ ファイルの入手方法は&3; とおりあります。

### <a name="microsoft-download-center"></a>Microsoft ダウンロード センター

- [Microsoft ダウンロード センター](http://download.microsoft.com/download/2/5/C/25CF992A-0347-4765-BD7D-D45D5B27F92C/OMS%20Gateway.msi)から最新バージョンの OMS ゲートウェイをダウンロードします。

### <a name="oms-portal"></a>OMS ポータル

1.    OMS ワークスペースにログインします。
2.    **[設定]** > **[Connected Sources (接続されたソース)]** > **[Windows Servers (Windows Server)]** の順に選択します。
3.    **[Download OMS Gateway (OMS ゲートウェイのダウンロード)]** をクリックします。


### <a name="azure-portal"></a>Azure ポータル

1. [Azure Portal](https://portal.azure.com) に移動してサインインし、サービスの一覧から **[Log Analytics]** を選択します。
2. ワークスペースを選択します。
3. **[全般]** のワークスペース ブレードで、**[クイック スタート]** をクリックします。
4. **[Choose a data source to connect to the workspace (ワークスペースに接続するデータ ソースの選択)]** で、**[Computers (コンピューター)]** をクリックします。
4. **[Direct Agent (直接エージェント)]** ブレードで、**[Download OMS Gateway (OMS ゲートウェイのダウンロード)]** をクリックします。  
    ![OMS ゲートウェイのダウンロード](./media/log-analytics-oms-gateway/download-gateway.png)


## <a name="install-the-oms-gateway"></a>OMS ゲートウェイをインストールする
このゲートウェイをインストールすると、インストールされていた前のバージョンのゲートウェイ (Log Analytics フォワーダー) が置き換えられます。

前提条件: .Net Framework 4.5、Windows Server 2012 R2 SP1 以降


1. インストールを始めるには、**OMS Gateway.msi** をダブルクリックします。
2. [ようこそ] ページで **[次へ]** をクリックします。  
    ![ゲートウェイ セットアップ ウィザード](./media/log-analytics-oms-gateway/gateway-wizard01.png)
3. 使用許諾契約書ページで、**[使用許諾契約書に同意します]** を選んで使用許諾契約書に同意し、**[次へ]** をクリックします。
4. ポートとプロキシ アドレスのページで次のようにします。
   1. ゲートウェイに使う TCP ポートの番号を入力します。 セットアップは Windows ファイアウォールからこのポート番号を開きます。 既定値は 8080 です。
      有効なポート番号の範囲は、1 ～ 65535 です。 この範囲の値を入力しないと、エラー メッセージが表示されます。
   2. ゲートウェイがインストールされているサーバーでプロキシを使う必要がある場合は、ゲートウェイが接続する必要のあるプロキシ アドレスを入力します。 たとえば、`http://myorgname.corp.contoso.com:80` などです。ブランクにすると、ゲートウェイはインターネットに直接接続を試みます。 指定すると、ゲートウェイはプロキシに接続します。 プロキシ サーバーで認証が必要な場合は、ユーザー名とパスワードを入力します。  
       ![ゲートウェイ ウィザードのプロキシの構成](./media/log-analytics-oms-gateway/gateway-wizard02.png)  
   3. **[次へ]**
5. Microsoft Update が有効になっていない場合は、Microsoft Update のページが表示され、Microsoft Update を有効にできます。 選択を行い、**[次へ]** をクリックします。 使用する場合は、次の手順に進みます。
6. [インストール先のフォルダー] ページでは、既定のフォルダー c:\ProgramFiles\OMS Gateway のままにするか、またはゲートウェイをインストールする場所を入力して、**[次へ]** をクリックします。
7. [インストールの準備完了] ページで **[インストール]** をクリックします。 [ユーザー アカウント制御] が表示され、インストールのためのアクセス許可が要求される場合があります。 その場合は、**[はい]** をクリックします。
8. セットアップが完了した後、**[完了]** をクリックします。 services.msc スナップインを開いて **[OMS Gateway]** (OMS ゲートウェイ) がサービスの一覧に表示されることを確認することで、サービスが実行していることを確認できます。  
    ![サービス – OMS ゲートウェイ](./media/log-analytics-oms-gateway/gateway-service.png)

## <a name="install-an-agent-on-devices"></a>デバイスにエージェントをインストールする
必要な場合は、「[Windows コンピューターを Log Analytics に接続する](log-analytics-windows-agents.md)」で直接接続されるエージェントをインストールする方法をご覧ください。 セットアップ ウィザードまたはコマンド ラインを使ってエージェントをインストールする方法が説明されています。

## <a name="configure-oms-agents"></a>OMS エージェントを構成する
プロキシ サーバー (この場合はゲートウェイ) を使うようにエージェントを構成する方法については、「[Microsoft Monitoring Agent でプロキシとファイアウォールの設定を構成する](log-analytics-proxy-firewall.md)」をご覧ください。

Operations Manager エージェントは、Operations Manager アラート、構成評価、インスタンス スペース、容量データなどの一部のデータを管理サーバー経由で送信します。 IIS ログ、パフォーマンス、セキュリティなどの他の大容量データは、OMS ゲートウェイに直接送信されます。 各チャネルを介して送信されるデータの完全な一覧については、「[ソリューション ギャラリーから Log Analytics ソリューションを追加する](log-analytics-add-solutions.md)」をご覧ください。

> [!NOTE]
> ネットワーク負荷分散と共にゲートウェイを使う場合は、「[ネットワーク負荷分散を構成する](#optionally-configure-network-load-balancing)」をご覧ください。
>
>

## <a name="configure-a-scom-proxy-server"></a>SCOM プロキシ サーバーを構成する
プロキシ サーバーとして機能するゲートウェイを追加するように Operations Manager を構成します。 プロキシ構成を更新すると、Operations Manager に報告するすべてのエージェントにプロキシ構成が自動的に適用されます。

ゲートウェイを使って Operations Manager をサポートするには次のことが必要です。

* Microsoft Monitoring Agent (エージェント バージョン – **8.0.10900.0** 以降) がゲートウェイ サーバーにインストールされていて、通信する OMS ワークスペース用に構成されていること。
* ゲートウェイがインターネットに接続されていること、またはインターネットに接続できるプロキシ サーバーに接続されていること。

### <a name="to-configure-scom-for-the-gateway"></a>ゲートウェイ用に SCOM を構成するには
1. Operations Manager コンソールを開き、**[Operations Management Suite]** で **[接続]** をクリックして、**[プロキシ サーバーの構成]** をクリックします。  
    ![Operations Manager – プロキシ サーバーの構成](./media/log-analytics-oms-gateway/scom01.png)
2. **[Operations Management Suite へのアクセスにプロキシ サーバーを使用する]** を選び、OMS ゲートウェイ サーバーの IP アドレスを入力します。 `http://` プレフィックスを省略しないで入力します。  
    ![Operations Manager – プロキシ サーバーのアドレス](./media/log-analytics-oms-gateway/scom02.png)
3. **[完了]**をクリックします。 Operations Manager サーバーが OMS ワークスペースに接続されます。

## <a name="configure-network-load-balancing"></a>ネットワーク負荷分散を構成する
クラスターを作成することにより、ネットワーク負荷分散を使って高可用性用にゲートウェイを構成することができます。 クラスターは、Microsoft Monitoring Agent からの接続要求を複数のノードにリダイレクトすることによって、エージェントからのトラフィックを管理します。 1 台のゲートウェイ サーバーで障害が発生した場合、トラフィックは他のノードにリダイレクトされます。

1. ネットワーク負荷分散マネージャーを開き、クラスターを作成します。
2. ゲートウェイを追加する前に、クラスターを右クリックして **[クラスターのプロパティ]** を選びます。 クラスターに専用の IP アドレスを構成します。  
    ![ネットワーク負荷分散マネージャー – クラスター IP アドレス](./media/log-analytics-oms-gateway/nlb01.png)
3. Microsoft Monitoring Agent がインストールされている OMS ゲートウェイ サーバーを接続するには、**[ホストをクラスターに追加]** をクリックします。  
    ![ネットワーク負荷分散マネージャー – ホストをクラスターに追加](./media/log-analytics-oms-gateway/nlb02.png)
4. 接続するゲートウェイ サーバーの IP アドレスを入力します。  
    ![ネットワーク負荷分散マネージャー – ホストをクラスターに追加: 接続](./media/log-analytics-oms-gateway/nlb03.png)
5. インターネットに接続していないコンピューターでは、**[Microsoft Monitoring Agent のプロパティ]** 構成するときに、クラスターの IP アドレスを使います。  
    ![Microsoft Monitoring Agent のプロパティ – プロキシの設定](./media/log-analytics-oms-gateway/nlb04.png)

## <a name="configure-for-automation-hybrid-workers"></a>Automation ハイブリッド worker 用に構成する
環境内に Automation ハイブリッド worker がある場合は、次の手順で、それらをサポートするようにゲートウェイを構成するための手動で一時的な回避策を設定します。

次の手順では、Automation アカウントが存在する Azure リージョンがわかっている必要があります。 場所を調べるには次のようにします。

1. [Azure ポータル](https://portal.azure.com/)にサインインします。
2. Azure Automation サービスを選びます。
3. 適切な Azure Automation アカウントを選びます。
4. **[場所]** でリージョンを確認します。  
    ![Azure Portal – Automation アカウントの場所](./media/log-analytics-oms-gateway/location.png)

次の表を使って、各場所の URL を確認します。

**ジョブ実行時データ サービスの URL**

| **location** | **URL** |
| --- | --- |
| 米国中北部 |ncus-jobruntimedata-prod-su1.azure-automation.net |
| 西ヨーロッパ |we-jobruntimedata-prod-su1.azure-automation.net |
| 米国中南部 |scus-jobruntimedata-prod-su1.azure-automation.net |
| 米国東部 2 |eus2-jobruntimedata-prod-su1.azure-automation.net |
| カナダ中部 |cc-jobruntimedata-prod-su1.azure-automation.net |
| 北ヨーロッパ |ne-jobruntimedata-prod-su1.azure-automation.net |
| 東南アジア |sea-jobruntimedata-prod-su1.azure-automation.net |
| インド中部 |cid-jobruntimedata-prod-su1.azure-automation.net |
| 日本 |jpe-jobruntimedata-prod-su1.azure-automation.net |
| オーストラリア |ase-jobruntimedata-prod-su1.azure-automation.net |

**Agent のサービス URL**

| **location** | **URL** |
| --- | --- |
| 米国中北部 |ncus-agentservice-prod-1.azure-automation.net |
| 西ヨーロッパ |we-agentservice-prod-1.azure-automation.net |
| 米国中南部 |scus-agentservice-prod-1.azure-automation.net |
| 米国東部 2 |eus2-agentservice-prod-1.azure-automation.net |
| カナダ中部 |cc-agentservice-prod-1.azure-automation.net |
| 北ヨーロッパ |ne-agentservice-prod-1.azure-automation.net |
| 東南アジア |sea-agentservice-prod-1.azure-automation.net |
| インド中部 |cid-agentservice-prod-1.azure-automation.net |
| 日本 |jpe-agentservice-prod-1.azure-automation.net |
| オーストラリア |ase-agentservice-prod-1.azure-automation.net |

更新の管理ソリューションを使う修正プログラムのためにコンピューターがハイブリッド worker として自動的に登録される場合は、次の手順を使います。

1. OMS ゲートウェイの許可ホスト一覧に、ジョブ ランタイム データ サービスの URL を追加します。 次に例を示します。 `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
2. 次の PowerShell コマンドレットを使って、OMS ゲートウェイ サービスを開始し直します。`Restart-Service OMSGatewayService`

コンピューターがハイブリッド worker 登録コマンドレットを使って Azure Automation にオンボードされている場合は、次の手順を使います。

1. OMS ゲートウェイの許可ホスト一覧に、エージェント サービス登録 URL を追加します。 次に例を示します。`Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
2. OMS ゲートウェイの許可ホスト一覧に、ジョブ ランタイム データ サービスの URL を追加します。 次に例を示します。 `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
3. OMS ゲートウェイ サービスを開始し直します。
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>便利な PowerShell コマンドレット
OMS ゲートウェイの構成設定の更新に必要なタスクを実行するには、コマンドレットが役に立ちます。 コマンドレットを使う前に次のことを行います。

1. OMS ゲートウェイ (MSI) をインストールします。
2. PowerShell ウィンドウを開きます。
3. モジュールをインポートするには、「`Import-Module OMSGateway`」コマンドを入力します。
4. 前の手順でエラーが発生しなかった場合は、モジュールが正常にインポートされたので、コマンドレットを使うことができます。 「`Get-Module OMSGateway`」と入力します。
5. コマンドレットを使って変更を行った後は、ゲートウェイ サービスを必ず開始し直します。

手順 3 でエラーが発生した場合は、モジュールはインポートされませんでした。 PowerShell がモジュールを発見できない場合、エラーが発生することがあります。 ゲートウェイのインストール パス C:\Program Files\Microsoft OMS Gateway\PowerShell にあります。

| **コマンドレット** | **パラメーター** | **説明** | **例** |
| --- | --- | --- | --- |
| `Set-OMSGatewayConfig` |キー (必須) <br> 値 |サービスの構成を変更します |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |
| `Get-OMSGatewayConfig` |キー |サービスの構成を取得します |`Get-OMSGatewayConfig` <br> <br> `Get-OMSGatewayConfig -Name ListenPort` |
| `Set-OMSGatewayRelayProxy` |Address <br> ユーザー名 <br> パスワード |リレー (アップストリーム) プロキシのアドレス (および資格情報) を設定します |1.リレー プロキシと資格情報を設定します: `Set-OMSGatewayRelayProxy -Address http://www.myproxy.com:8080 -Username user1 -Password 123` <br> <br> 2.認証を必要としないリレー プロキシを設定します: `Set-OMSGatewayRelayProxy -Address http://www.myproxy.com:8080` <br> <br> 3.リレー プロキシの設定をクリアします。つまり、リレー プロキシは必要ありません: `Set-OMSGatewayRelayProxy -Address ""` |
| `Get-OMSGatewayRelayProxy` | |リレー (アップストリーム) プロキシのアドレスを取得します |`Get-OMSGatewayRelayProxy` |
| `Add-OMSGatewayAllowedHost` |ホスト (必須) |許可リストにホストを追加します |`Add-OMSGatewayAllowedHost -Host www.test.com` |
| `Remove-OMSGatewayAllowedHost` |ホスト (必須) |許可リストからホストを削除します |`Remove-OMSGatewayAllowedHost -Host www.test.com` |
| `Get-OMSGatewayAllowedHost` | |現在許可されているホストを取得します (ローカルに構成されている許可ホストだけであり、自動的にダウンロードされた許可ホストは含みません) |`Get-OMSGatewayAllowedHost` |
| `Add-OMSGatewayAllowedClientCertificate` |サブジェクト (必須) |クライアント証明書のサブジェクトを許可リストに追加します |`Add-OMSGatewayAllowedClientCertificate -Subject mycert` |
| `Remove-OMSGatewayAllowedClientCertificate` |サブジェクト (必須) |クライアント証明書のサブジェクトを許可リストから削除します |`Remove- OMSGatewayAllowedClientCertificate -Subject mycert` |
| `Get-OMSGatewayAllowedClientCertificate` | |現在許可されているクライアント証明書のサブジェクトを取得します (ローカルに構成されている許可サブジェクトだけであり、自動的にダウンロードされた許可サブジェクトは含みません) |`Get-OMSGatewayAllowedClientCertificate` |

## <a name="troubleshoot"></a>トラブルシューティング
ゲートウェイがインストールされているコンピューターに OMS エージェントをインストールする必要があります。 そうすれば、エージェントを使って、ゲートウェイによってログに記録されるイベントを収集できます。

![イベント ビューアー – OMS ゲートウェイ ログ](./media/log-analytics-oms-gateway/event-viewer.png)

**OMS ゲートウェイのイベント ID と説明**

次の表では、OMS ゲートウェイ ログ イベントのイベント ID と説明を示します。

| **ID** | **説明** |
| --- | --- |
| 400 |特定の ID がないすべてのアプリケーション エラー |
| 401 |正しくない構成。 例: listenPort が整数ではなくテキスト |
| 402 |TLS ハンドシェイク メッセージ解析中の例外 |
| 403 |ネットワーク エラー。 例: ターゲット サーバーに接続できません |
| 100 |一般情報 |
| 101 |サービスが開始しました |
| 102 |サービスが停止しました |
| 103 |クライアントから HTTP CONNECT コマンドを受信しました |
| 104 |HTTP CONNECT コマンドではありません |
| 105 |接続先サーバーが許可リストにないか、または接続先ポートがセキュリティで保護されたポート (443) ではありません <br> <br> ゲートウェイ サーバー上の MMA エージェントと、ゲートウェイと接続しているエージェントが、同じ Log Analytics ワークスペースに接続されていることを確認します。 |
| 105 |エラー TcpConnection – 無効なクライアント証明書: CN=Gateway <br><br> 次のことを確認します。 <br>    <br> &#149; バージョン番号 1.0.395.0 以降のゲートウェイを使っていること。 <br> &#149; ゲートウェイ サーバー上の MMA エージェントと、ゲートウェイと接続しているエージェントが、同じ Log Analytics ワークスペースに接続されていること。 |
| 106 |TLS セッションが疑わしくて拒否されたすべての理由 |
| 107 |TLS セッションが確認されました |

**収集されるパフォーマンス カウンター**

次の表では、OMS ゲートウェイに使えるパフォーマンス カウンターを示します。 パフォーマンス モニターを使ってカウンターを追加できます。

| **名前** | **説明** |
| --- | --- |
| OMS Gateway/Active Client Connection |アクティブなクライアント ネットワーク (TCP) 接続の数 |
| OMS Gateway/Error Count |エラーの数 |
| OMS Gateway/Connected Client |接続されているクライアントの数 |
| OMS Gateway/Rejection Count |TLS 検証エラーによる拒否の回数 |

![OMS ゲートウェイのパフォーマンス カウンター](./media/log-analytics-oms-gateway/counters.png)

## <a name="get-assistance"></a>サポートを受ける
Azure Portal にサインインすると、OMS ゲートウェイまたは他の Azure サービスやサービスの機能についてのサポート要求を作成できます。
サポートを要求するには、ポータルの右上隅にある疑問符記号をクリックして、**[新しいサポート要求]** をクリックします。 その後、新しいサポート要求フォームを作成します。

![新しいサポート要求](./media/log-analytics-oms-gateway/support.png)

また、[Microsoft Azure フィードバック フォーラム](https://feedback.azure.com/forums/267889)では、OMS や Log Analytics についてフィードバックすることもできます。

## <a name="next-steps"></a>次のステップ
* [データ ソースを追加](log-analytics-data-sources.md)し、OMS ワークスペース内の接続されたソースからデータを収集して OMS リポジトリに格納します。



<!--HONumber=Feb17_HO2-->


