---
title: "OMS ゲートウェイを使用して Operations Management Suite にオフライン コンピューターを接続する | Microsoft Docs"
description: "Operations Management Suite で管理され、かつ System Center Operations Manager によって監視されているオフライン コンピューターは、Operations Management Suite サービスに OMS ゲートウェイで接続することにより、データを送信することができます。"
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
ms.date: 03/27/2017
ms.author: magoedte; banders
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 19bf9b3d65210458e4f018dc591e35e59287cd8e
ms.lasthandoff: 04/03/2017

---

# <a name="connect-offline-computers-to-operations-management-suite-by-using-oms-gateway"></a>OMS ゲートウェイを使用して Operations Management Suite にオフライン コンピューターを接続する

Operations Management Suite (OMS) で管理され、System Center Operations Manager (Operations Manager) によって監視されているコンピューターは、インターネットに接続されていなくても、Operations Management Suite サービスにデータを送信することができます。 OMS ゲートウェイは、HTTP CONNECT コマンドを使って HTTP トンネリングをサポートする HTTP 転送プロキシです。 OMS ゲートウェイがオフライン コンピューターに代わってデータを収集し、そのデータを Operations Management Suite サービスに送信します。  

OMS ゲートウェイは、次のシステムと連携させることができます。

* Azure Automation の Hybrid Runbook Worker。  
* Microsoft Monitoring Agent がインストールされ、なおかつ Operations Management Suite ワークスペースに直接接続されている Windows コンピューター。
* System Center Operations Manager 2012 SP1 Update Rollup (UR) 7、System Center Operations Manager 2012 R2 UR3、または System Center Operations Manager 2016 の管理グループのうち、Operations Management Suite と統合しているもの。  

IT セキュリティ ポリシーによっては、ネットワーク接続されたコンピューターをインターネットに接続することが禁止されている場合があります。 たとえば、販売時点情報管理 (POS) デバイスや IT サービスを支援するサーバーがそれに該当します。 しかしそのようなコンピューターを管理、監視するためには、Operations Management Suite に接続する必要があります。 OMS ゲートウェイと直接通信を行うようにオフライン コンピューターを設定することができます。 ゲートウェイが構成を受け取り、オフライン コンピューターに代わってデータを転送します。  

Operations Management Suite エージェントがインストールされているコンピューターを、Operations Management Suite ワークスペースに直接接続するようにセットアップすれば、すべてのコンピューターが OMS ゲートウェイを使って通信を行うようになります。 エージェントからのデータが OMS ゲートウェイによって直接 Operations Management Suite に転送されます。 OMS ゲートウェイは、転送中のデータを一切分析しません。

Operations Management Suite に Operations Manager の管理グループを統合すると、OMS ゲートウェイに接続する管理サーバーを設定することができます。 管理サーバーは構成情報を受信し、設定されているソリューションに応じて収集データを送信します。 Operations Manager エージェントは、Operations Manager アラート、構成評価、インスタンス スペース、容量データなどのデータを管理サーバーに送信します。 その他の大容量データは、OMS ゲートウェイに直接送信されます。たとえばインターネット インフォメーション サービス (IIS) のログ、パフォーマンス、セキュリティ イベントがこれに該当します。 

信頼されていないシステムを監視するために、境界ネットワーク (*DMZ*、"*非武装地帯*"、"*スクリーン サブネット*") など隔離されたネットワークにデプロイされている Operations Manager ゲートウェイ サーバーが、OMS ゲートウェイと通信することはできません。 Operations Manager ゲートウェイ サーバーは、管理サーバーに対してのみ情報を伝えることができます。 Operations Manager 管理グループが OMS ゲートウェイと通信する設定になっている場合、Azure Log Analytics に必要なデータを収集するように設定された、エージェントの管理下にあるすべてのコンピューターに対してプロキシの構成情報が自動的に配信されます。 設定が空欄になっていても、プロキシの構成情報は自動的に配信されます。    

Operations Management Suite に直接接続されているエージェントまたは Operations Management Suite とゲートウェイを介して通信する Operations Management グループは、ネットワーク負荷分散 (NLB) を使用して可用性を高めることができます。 NLB によってトラフィックがリダイレクトされて複数のゲートウェイ サーバーに分散されます。 いずれかのゲートウェイ サーバーがダウンしても、別の利用可能なノードにトラフィックがリダイレクトされます。  

OMS ゲートウェイ ソフトウェアが実行されているコンピューターには、Operations Management Suite エージェントをインストールするようお勧めします。 そうすることで OMS ゲートウェイを監視し、パフォーマンス データやイベント データを分析することができます。 また、このエージェントを通じて OMS ゲートウェイは、その通信相手のサービス エンドポイントを特定することができます。

各エージェントは、ゲートウェイとの間で双方向にデータを自動的に転送できるように、ゲートウェイにネットワーク接続する必要があります。 ドメイン コントローラーに OMS ゲートウェイをインストールすることはできません。

次の図は、ゲートウェイ サーバーを使った場合の直接エージェントから Operations Management Suite へのデータの流れを示したものです。 エージェントのプロキシ構成は、OMS ゲートウェイが Operations Management Suite との通信に使うポートと合わせる必要があります。  

![エージェントと Operations Management Suite の直接通信の図](./media/log-analytics-oms-gateway/oms-omsgateway-agentdirectconnect.png)

次の図は、Operations Manager 管理グループから Operations Management Suite へのデータの流れを示したものです。   

![Operations Manager と Operations Management Suite との通信の図](./media/log-analytics-oms-gateway/oms-omsgateway-opsmgrconnect.png)

## <a name="prerequisites"></a>前提条件

OMS ゲートウェイの実行に使用するコンピューターは、以下の要件を満たしている必要があります。

* Windows 10、Windows 8.1、Windows 7、Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2、Windows Server 2008 のいずれかのオペレーティング システム
* Microsoft .NET Framework 4.5
* 4 コア以上のプロセッサ
* 8 GB 以上のメモリ 

### <a name="language-availability"></a>利用可能な言語

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

## <a name="download-oms-gateway"></a>OMS ゲートウェイのダウンロード

最新バージョンの OMS ゲートウェイは、次の 3 とおりの方法で入手できます。

* [Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=54443)からダウンロードする。

* Operations Management Suite ポータルからダウンロードする。 Operations Management Suite ワークスペースにサインインして、以下の手順を実行します。

    1. **[設定]** > **[接続されたソース]** > **[Windows Servers]** の順に選択します。 
    2. **[OMS ゲートウェイのダウンロード]** を選択します。

* [Azure Portal](https://portal.azure.com) からダウンロードする。 サインインして、以下の手順を実行します。  

   1. サービスの一覧から **[Log Analytics]** を選択します。  
   2. ワークスペースを選択します。
   3. ワークスペース ブレードの **[全般]** で **[クイック スタート]** をクリックします。
   4. **[ワークスペースに接続するデータ ソースを選択する]** の **[コンピューター]** を選択します。
   5. **[ダイレクト エージェント]** ブレードで **[OMS ゲートウェイのダウンロード]** を選択します。<br><br> ![OMS ゲートウェイのダウンロード](./media/log-analytics-oms-gateway/download-gateway.png)


## <a name="install-oms-gateway"></a>OMS ゲートウェイのインストール

ゲートウェイをインストールするには、次の手順を実行します。 

> [!NOTE]
> 以前のバージョンのゲートウェイ (旧称 Log Analytics フォワーダー) がインストールされている場合、最新版の OMS ゲートウェイにアップグレードされます。
>

1. インストール先のフォルダーで **OMS Gateway.msi** をダブルクリックします。
2. **[ようこそ]** ページで **[次へ]** をクリックします。<br><br> ![ゲートウェイ セットアップ ウィザード](./media/log-analytics-oms-gateway/gateway-wizard01.png)<br> 
3. **[使用許諾契約書]** ページで、マイクロソフト ソフトウェア ライセンス条項に同意する場合は **[使用許諾契約書の条項に同意する]** を選択します。 
4. **[Port and proxy address (ポートとプロキシ アドレス)]** ページで、以下を実行します。

   1. ゲートウェイに使用する TCP ポート番号を入力します。 Windows ファイアウォールには、このポート番号に基づく受信規則がセットアップ プログラムによって構成されます。 既定値は 8080 です。
      有効なポート番号の範囲は、1 ～ 65535 です。 この範囲の値を入力しないと、エラー メッセージが表示されます。
   2. ゲートウェイがインストールされているサーバーがプロキシを介して通信する必要がある場合は、ゲートウェイが接続する必要のあるプロキシのアドレスを入力します。 たとえば、「**http://myorgname.corp.contoso.com:80**」と入力します。 プロキシ アドレス ボックスを空欄にした場合、ゲートウェイは、インターネットに直接接続を試みます。 プロキシ サーバーで認証が必要な場合は、ユーザー名とパスワードを入力します。<br><br> ![ゲートウェイ ウィザードのプロキシの構成](./media/log-analytics-oms-gateway/gateway-wizard02.png)<br>   
5. コンピューターで Microsoft Update が有効になっていない場合、**[Microsoft Update]** ページが表示されます。 有効にするかどうかをそのページで選択できます。 使用する場合は、次の手順に進みます。
6. **[インストール先のフォルダー]** ページでは、ゲートウェイのインストール先として、既定のフォルダー C:\Program Files\OMS Gateway をそのまま使用するか、目的の場所を入力します。
7. **[インストールの準備完了]** ページで **[インストール]** をクリックします。 インストールするためのアクセス許可を要求するダイアログ ボックスが表示されます。 **[はい]** を選択します。
8. **[完了]** を選択します。 サービスが実行されていることを確認するために、Services.msc スナップインを開いて **[OMS Gateway]** がサービスの一覧に表示されていることを確認します。 サービスの状態が **[実行中]** になっている必要があります。<br><br> ![OMS ゲートウェイのサービスを確認](./media/log-analytics-oms-gateway/gateway-service.png)  

## <a name="set-up-network-load-balancing"></a>ネットワーク負荷分散の設定 
OMS ゲートウェイは、NLB を使って高可用性構成にすることができます。 Windows Server のネットワーク負荷分散機能またはハードウェアベースのロード バランサーを利用することができます。 Operations Management Suite エージェントからまたは Operations Manager の管理サーバーから要求された接続は、ロード バランサーによって複数のノードにリダイレクトされ、トラフィックの負荷が分散されます。 いずれかのゲートウェイ サーバーで障害が発生しても、トラフィックは他のノードにリダイレクトされます。

Windows Server 2016 のネットワーク負荷分散クラスターの設計とデプロイの方法については、「[ネットワーク負荷分散](https://technet.microsoft.com/windows-server-docs/networking/technologies/network-load-balancing)」を参照してください。  

Windows Server ネットワーク負荷分散クラスターをセットアップするには、次の手順を実行します。  

1. ネットワーク負荷分散クラスターのメンバーとなる Windows Server に管理者アカウントでサインインします。  
2. サーバー マネージャーでネットワーク負荷分散マネージャーを開きます。
3. **[ツール]** を選択し、**[ネットワーク負荷分散マネージャー]** を選択します。
4. インストールされている Microsoft Monitoring Agent に OMS ゲートウェイ サーバーを接続するには、クラスターの IP アドレスを右クリックし、**[ホストをクラスターに追加]** をクリックします。<br><br> ![ネットワーク負荷分散マネージャー: ホストをクラスターに追加](./media/log-analytics-oms-gateway/nlb02.png)<br> 
5. 接続するゲートウェイ サーバーの IP アドレスを入力します。<br><br> ![ネットワーク負荷分散マネージャー: ホストをクラスターに追加、接続](./media/log-analytics-oms-gateway/nlb03.png) 
    
## <a name="set-up-the-operations-management-suite-agent-and-an-operations-manager-management-group"></a>Operations Management Suite エージェントと Operations Manager 管理グループの設定
このセクションでは、直接接続された Operations Management Suite エージェントと Operations Manager 管理グループを設定する方法について説明します。 OMS ゲートウェイを使って Operations Management Suite と通信を行うように Azure Automation Hybrid Runbook Worker を設定することもできます。  

Operations Management Suite に直接接続されている Windows コンピューターに Operations Management Suite エージェントをインストールする要件と手順については、[Windows コンピューターを Operations Management Suite に接続する方法](log-analytics-windows-agents.md)に関するページを参照してください。 Linux コンピューターの場合は、[Linux コンピューターを Operations Management Suite に接続する方法](log-analytics-linux-agents.md)に関するページを参照してください。 

### <a name="set-up-the-operations-management-suite-agent-and-operations-manager-to-use-the-oms-gateway-as-a-proxy-server"></a>OMS ゲートウェイをプロキシ サーバーとして使うように Operations Management Suite エージェントと Operations Manager を設定する

### <a name="set-up-a-standalone-operations-management-suite-agent"></a>スタンドアロン Operations Management Suite エージェントの設定
プロキシ サーバーを使うようにエージェントを設定する方法については、[Microsoft Monitoring Agent を使ったプロキシとファイアウォール設定の構成](log-analytics-proxy-firewall.md)に関するページを参照してください。 この場合、プロキシ サーバーがゲートウェイになります。 ネットワーク ロード バランサーの背後に複数のゲートウェイ サーバーをデプロイしている場合には、Operations Management Suite エージェントのプロキシ構成が、ネットワーク負荷分散の仮想 IP アドレスとなります。<br><br> ![Microsoft Monitoring Agent のプロパティ: プロキシの設定](./media/log-analytics-oms-gateway/nlb04.png)

### <a name="set-up-operations-manager-all-agents-use-the-same-proxy-server"></a>Operations Manager の設定 (すべてのエージェントが同じプロキシ サーバーを使用する場合)
ゲートウェイ サーバーを追加するように Operations Manager を設定します。 Operations Manager に報告するすべてのエージェントに Operations Manager のプロキシ構成が自動的に適用されます (設定が空欄であっても同じです)。

Operations Manager で OMS ゲートウェイを使うためには、次の要件を満たしている必要があります。

* Microsoft Monitoring Agent (エージェント バージョン **8.0.10900.0** 以降) がゲートウェイ サーバーにインストールされていること。 通信相手となる Operations Management Suite エージェント ワークスペース用に設定されている必要があります。
* ゲートウェイがインターネットに接続されていること、またはインターネットに接続できるプロキシ サーバーに接続されていること。

> [!NOTE]
> ゲートウェイの値を指定しなかった場合には、全部のエージェントに対して空の値がプッシュされます。

Operations Management Suite エージェント ワークスペースに Operations Manager サーバーを接続するには

1. Operations Manager コンソールで **[Operations Management Suite]** > **[接続]** > **[プロキシ サーバーの構成]** に移動します。<br><br> ![Operations Manager: プロキシ サーバーの構成](./media/log-analytics-oms-gateway/scom01.png)<br> 
2. **[Operations Management Suite へのアクセスにプロキシ サーバーを使用する]** を選択します。 OMS ゲートウェイ サーバーの IP アドレス、またはネットワーク ロード バランサーの仮想 IP アドレスを入力します。 必ず先頭に **http://** を付けて入力してください。<br><br> ![Operations Manager: プロキシ サーバーのアドレス](./media/log-analytics-oms-gateway/scom02.png)<br> 
3. **[完了]** を選択します。 

### <a name="set-up-operations-manager-specific-agents-use-the-proxy-server"></a>Operations Manager の設定 (特定のエージェントがプロキシ サーバーを使用する場合)
環境が大規模であるか、複雑な場合には、特定のサーバー (またはグループ) のみ OMS ゲートウェイ サーバーを使用する構成が必要になることも考えられます。 そのようなサーバーでは、Operations Manager エージェントを直接更新する方法は使えません。 値が、管理グループ全体に適用されるグローバルな値で上書きされてしまうからです。 そこで、これらの値をプッシュするルールを上書きします。

> [!NOTE] 
> これと同じ構成手法を使えば、ご利用の環境で複数の OMS ゲートウェイ サーバーを実行することができます。 たとえば、リージョンごとに固有の OMS ゲートウェイ サーバーの指定を必要とする場合が考えられます。

1. System Center Operations Manager コンソールで、**[作成]** ワークスペースを選択します。  
2. **[ルール]** を選択し、System Center Operations Manager ツール バーの **[スコープ]** ボタンを選択します。 このボタンが利用できない場合には、**[監視]** ウィンドウでフォルダーではなくオブジェクトを選択していることを確認してください。 **[管理パック オブジェクトのスコープ設定]** ダイアログ ボックスには、ターゲットになることが多いクラス、グループ、またはオブジェクトが一覧表示されます。 
3. **[検索]** ボックスに「**ヘルス サービス**」と入力します。 それを一覧から選択してください。 **[OK]**を選択します。  
4. Operations Manager コンソールのツール バーでルール **[Advisor Proxy Setting Rule (アドバイザーのプロキシ設定のルール)]** を検索し 、**[上書き]** をクリックしたら、**[Override the Rule\For a specific object of class: Health Service (ルールの上書き\クラスの特定のオブジェクト: ヘルス サービス)]** にカーソルを合わせ、 一覧から特定のオブジェクトを選択します。 必要があれば、この上書きを適用するサーバーのヘルス サービス オブジェクトが含まれるカスタム グループを作成し、 そのグループに対して上書きを適用することもできます。
5. **[上書きのプロパティ]** ダイアログ ボックスで、**WebProxyAddress** パラメーターの横にある **[上書き]** 列を選択します。 **[上書き値]** ボックスに、OMS ゲートウェイ サーバーの URL を入力します。 必ず先頭に **http://** を付けて入力してください。

   >[!NOTE]
   > このルールを手動で有効にする必要はありません。 このルールは、System Center Advisor Secure Reference Override 管理パックに含まれる上書きで自動的に管理されます。 この管理パックは、System Center Advisor Monitoring Server Group を対象にしています。
   > 

6. 管理パックを指定するには、次のいずれかを実行します。
    * **[目的の管理パックの選択]** リストから管理パックを選択します。
    * 新しい管理パックを最初から作成するには、**[新規]** を選択します。 
7. **[OK]**を選択します。 

### <a name="set-up-automation-hybrid-workers"></a>Automation ハイブリッド worker の設定
ご利用の環境に Azure Automation Hybrid Runbook Worker がある場合は、ゲートウェイでそれらをサポートするように、手動による一時的な回避策を作成することができます。

次の手順では、Automation アカウントが存在する Azure リージョンがわかっている必要があります。 該当する場所を調べるには次のようにします。

1. [Azure ポータル](https://portal.azure.com/)にサインインします。
2. Azure Automation サービスを選びます。
3. 該当する Azure Automation アカウントを選びます。
4. **[場所]** でリージョンを確認します。<br><br> ![Azure Portal: Automation アカウントの場所](./media/log-analytics-oms-gateway/location.png)  

次の表を使って、各場所の URL を確認します。

**ジョブ実行時データ サービスの URL**

| 場所 | URL |
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

| 場所 | URL |
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

ご使用のコンピューターが、更新の管理ソリューションを使用して修正プログラムを適用するための Hybrid Runbook Worker として自動的に登録されている場合は、次の手順に従います。

1. OMS ゲートウェイの**許可ホスト**一覧に、ジョブ ランタイム データ サービスの URL を追加します。 次に例を示します。 `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
2. 次の Azure PowerShell コマンドレットを使って、OMS ゲートウェイ サービスを再起動します。`Restart-Service OMSGatewayService`

コンピューターが Hybrid Runbook Worker 登録コマンドレットを使って Azure Automation にオンボードされている場合は、次の手順に従います。

1. OMS ゲートウェイの**許可ホスト**一覧に、エージェント サービス登録 URL を追加します。 次に例を示します。`Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
2. OMS ゲートウェイの**許可ホスト**一覧に、ジョブ ランタイム データ サービスの URL を追加します。 次に例を示します。 `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
3. OMS ゲートウェイ サービスを再起動します ( `Restart-Service OMSGatewayService`)。

## <a name="useful-powershell-cmdlets"></a>便利な PowerShell コマンドレット
OMS ゲートウェイの設定は、コマンドレットを使って更新することができます。 PowerShell コマンドレットを使う前に、必ず次の作業を行ってください。

1. OMS ゲートウェイ (MSI) をインストールします。
2. PowerShell コンソール ウィンドウを開きます。
3. モジュールをインポートするには、「`Import-Module OMSGateway`」コマンドを入力します。 エラーが発生しない場合、モジュールが正常にインポートされ、コマンドレットを使用できる状態になっています。 
4. 「`Get-Module OMSGateway`」を入力します。
5. コマンドレットを使って変更を行った後は、OMS ゲートウェイ サービスを再起動します。

手順 3 でエラーが発生した場合は、モジュールはインポートされませんでした。 PowerShell がモジュールを検出できない場合、エラーが発生することがあります。 モジュールは、ゲートウェイのインストール パス (C:\Program Files\Microsoft OMS Gateway\PowerShell) にあります。

| コマンドレット | パラメーター | Description | 例 |
| --- | --- | --- | --- |
| `Set-OMSGatewayConfig` |キー (必須) <br> 値 |サービス構成を変更します |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |
| `Get-OMSGatewayConfig` |キー |サービス構成を取得します |`Get-OMSGatewayConfig` <br> <br> `Get-OMSGatewayConfig -Name ListenPort` |
| `Set-OMSGatewayRelayProxy` |Address <br> ユーザー名 <br> パスワード |リレー (アップストリーム) プロキシのアドレス (および資格情報) を設定します |1.リレー プロキシと資格情報を設定します: `Set-OMSGatewayRelayProxy -Address http://www.myproxy.com:8080 -Username user1 -Password 123` <br> <br> 2.認証を必要としないリレー プロキシを設定します: `Set-OMSGatewayRelayProxy -Address http://www.myproxy.com:8080` <br> <br> 3.リレー プロキシの設定をクリアします。つまり、リレー プロキシは必要ありません: `Set-OMSGatewayRelayProxy -Address ""` |
| `Get-OMSGatewayRelayProxy` | |リレー (アップストリーム) プロキシのアドレスを取得します |`Get-OMSGatewayRelayProxy` |
| `Add-OMSGatewayAllowedHost` |ホスト (必須) |許可リストにホストを追加します |`Add-OMSGatewayAllowedHost -Host www.test.com` |
| `Remove-OMSGatewayAllowedHost` |ホスト (必須) |許可リストからホストを削除します |`Remove-OMSGatewayAllowedHost -Host www.test.com` |
| `Get-OMSGatewayAllowedHost` | |現在許可されているホストを取得します (ローカルに構成されている許可ホストだけであり、自動的にダウンロードされた許可ホストは含みません) |`Get-OMSGatewayAllowedHost` |
| `Add-OMSGatewayAllowedClientCertificate` |サブジェクト (必須) |クライアント証明書のサブジェクトを許可リストに追加します |`Add-OMSGatewayAllowedClientCertificate -Subject mycert` |
| `Remove-OMSGatewayAllowedClientCertificate` |サブジェクト (必須) |クライアント証明書のサブジェクトを許可リストから削除します |`Remove- OMSGatewayAllowedClientCertificate -Subject mycert` |
| `Get-OMSGatewayAllowedClientCertificate` | |現在許可されているクライアント証明書のサブジェクトを取得します (ローカルに構成されている許可サブジェクトだけであり、自動的にダウンロードされた許可サブジェクトは含みません) |`Get-OMSGatewayAllowedClientCertificate` |

## <a name="troubleshooting"></a>トラブルシューティング
ゲートウェイによってログに記録されたイベントを収集するには、Operations Management Suite エージェントをインストールしておく必要があります。<br><br> ![イベント ビューアー: OMS ゲートウェイ ログ](./media/log-analytics-oms-gateway/event-viewer.png)

**OMS ゲートウェイのイベント ID と説明**

次の表は、OMS ゲートウェイ ログ イベントのイベント ID を一覧にしたものです。

| ID | 説明 |
| --- | --- |
| 400 |特定の ID がないすべてのアプリケーション エラー |
| 401 |正しくない構成。 例: listenPort が整数ではなく "\<テキスト\>" |
| 402 |トランスポート層セキュリティ (TLS) ハンドシェイク メッセージ解析中の例外 |
| 403 |ネットワーク エラー。 例: 対象サーバーに接続できません |
| 100 |一般情報 |
| 101 |サービスが開始しました |
| 102 |サービスが停止しました |
| 103 |クライアントから HTTP CONNECT コマンドを受信しました |
| 104 |HTTP CONNECT コマンドではありません |
| 105 |接続先サーバーが許可リストにないか、または接続先ポートがセキュリティで保護されたポート (443) ではありません <br> <br> ゲートウェイ サーバー上の Microsoft Monitoring Agent エージェントと、ゲートウェイと接続しているエージェントが、同じ Log Analytics ワークスペースに接続されていることを確認します。 |
| 105 |エラー TcpConnection – 無効なクライアント証明書: CN=Gateway <br><br> 次のことを確認します。 <br>    <br> &#149; バージョン 1.0.395.0 以降の OSM ゲートウェイを使っていること。 <br> &#149; ゲートウェイ サーバー上の Microsoft Monitoring Agent エージェントと、ゲートウェイとやり取りするエージェントとが、同じ Log Analytics ワークスペースに接続されていること。 |
| 106 |TLS セッションが疑わしくて拒否されたすべての理由 |
| 107 |TLS セッションが確認されました |

**収集されるパフォーマンス カウンター**

次の表は、OMS ゲートウェイで利用できるパフォーマンス カウンターの一覧です。 Windows パフォーマンス モニターを使ってカウンターを追加できます。

| 名前 | Description |
| --- | --- |
| OMS Gateway/Active Client Connection |アクティブなクライアント ネットワーク (TCP) 接続の数 |
| OMS Gateway/Error Count |エラーの数 |
| OMS Gateway/Connected Client |接続されているクライアントの数 |
| OMS Gateway/Rejection Count |TLS 検証エラーによる拒否の回数 |

![OMS ゲートウェイのパフォーマンス カウンター](./media/log-analytics-oms-gateway/counters.png)

## <a name="get-assistance"></a>サポートを受ける
Azure Portal にサインインすると、OMS ゲートウェイのほか、あらゆる Azure サービスやサービスの機能についてのサポート要求を作成できます。
サポートを要求するには、ポータルの右上隅にある疑問符記号を選択して、**[新しいサポート要求]** を選択します。 新しいサポート要求フォームを作成します。

![新しいサポート要求](./media/log-analytics-oms-gateway/support.png)

また、[Microsoft Azure フィードバック フォーラム](https://feedback.azure.com/forums/267889)では、Operations Management Suite や Log Analytics についてフィードバックを残すこともできます。

## <a name="next-steps"></a>次のステップ
接続されているソースのデータを Operations Management Suite ワークスペースで収集するには、[データ ソースを追加](log-analytics-data-sources.md)します。 収集した日付は、Operations Management Suite リポジトリに保存することができます。

