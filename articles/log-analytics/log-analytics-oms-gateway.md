---
title: OMS ゲートウェイを使ってコンピューターを接続する | Microsoft Docs
description: デバイスと Operations Manager で監視されたコンピューターがインターネットにアクセスできないときに、OMS ゲートウェイを使ってそれらを接続して Azure Automation および Log Analytics サービスにデータを送信します。
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ae9a1623-d2ba-41d3-bd97-36e65d3ca119
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: magoedte
ms.openlocfilehash: 66e5444f5346a44cfc8a43cf2b43dbaeacffedc9
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2018
---
# <a name="connect-computers-without-internet-access-by-using-the-oms-gateway"></a>インターネットにアクセスできないコンピューターを OMS ゲートウェイを使って接続する
このドキュメントでは、直接接続されたコンピューターまたは Operations Manager で監視されているコンピューターがインターネットにアクセスできないときに、OMS ゲートウェイを使用して Azure Automation および Log Analytics との通信を構成する方法について説明します。 OMS ゲートウェイは、HTTP CONNECT コマンドを使って HTTP トンネリングをサポートする HTTP 転送プロキシです。 インターネットにアクセスできないコンピューターの代わりに、データを収集して、Azure Automation と Log Analytics に送信できます。  

OMS ゲートウェイは、以下のものをサポートしています。

* Azure Automation の Hybrid Runbook Worker  
* Microsoft Monitoring Agent がインストールされており、同エージェントが Log Analytics ワークスペースに直接接続している Windows コンピューター
* OMS エージェント for Linux が Log Analytics ワークスペースに直接接続されている Linux コンピューター  
* System Center Operations Manager 2012 SP1 with UR7、Operations Manager 2012 R2 with UR3、Operations Manager 2016、Operations Manager Version 1801 の管理グループのうち、Log Analytics と統合しているもの  

IT セキュリティ ポリシーによりネットワーク上の特定のコンピューターがインターネットへの接続を禁止されていて (販売時点管理デバイスや IT サービスをサポートするサーバーなど)、それでもそのコンピューターを管理および監視するために Azure Automation または Log Analytics に接続する必要がある場合は、OMS ゲートウェイと直接通信するように構成できます。

 このようなコンピューターに Log Analytics ワークスペースと直接接続している OMS エージェントを構成した場合は、すべてのコンピューターが OMS ゲートウェイと通信します。 OMS ゲートウェイはエージェントからのデータをサービスに直接転送します。 転送中のデータを分析することはありません。

Log Analytics と Operations Manager 管理グループが統合している場合には、管理サーバーが OMS ゲートウェイに接続して構成情報を受信し、収集されたデータを送信するという構成が可能です。 Operations Manager エージェントは、Operations Manager アラート、構成評価、インスタンス スペース、容量データなどのデータを管理サーバーに送信します。 IIS ログ、パフォーマンス情報、セキュリティ イベントなどの他の大容量データは、OMS ゲートウェイに直接送信されます。  

信頼されていないシステムの監視のために DMZ などの分離されたネットワーク上に Operations Manager ゲートウェイ サーバーを展開している場合には、そのサーバーが OMS ゲートウェイと通信することはできません。 Operations Manager ゲートウェイ サーバーは、管理サーバーに対してのみレポートを送信できます。 

Operations Manager 管理グループが OMS ゲートウェイと通信する構成になっている場合には、エージェントが管理しており、かつ Log Analytics 用のデータを収集する構成になっているコンピューターそれぞれに対して、プロキシの構成情報が自動で配信されます (設定が空欄であっても同じです)。    

OMS ゲートウェイを経由して Log Analytics と通信する Operations Management グループまたは Log Analytics に直接接続しているグループに対して高可用性を実現するには、ネットワーク負荷分散を使って複数の OMS ゲートウェイ サーバーにトラフィックをリダイレクトし、トラフィックを分散させます。 ゲートウェイ サーバーが 1 台ダウンした場合には、トラフィックが別の利用可能なノードにリダイレクトされます。  

OMS ゲートウェイ ソフトウェアを実行しているコンピューターに OMS エージェントをインストールして OMS ゲートウェイを監視し、パフォーマンスやイベントに関するデータを分析することをお勧めします。 さらに、このエージェントにより、OMS ゲートウェイが通信する必要があるサービス エンドポイントを特定できるようになります。

すべてのエージェントは、ゲートウェイとの間で双方向にデータを自動的に転送できるように、ゲートウェイにネットワーク接続する必要があります。 ドメイン コントローラーにゲートウェイをインストールすることはお勧めしません。

次の図は、ゲートウェイ サーバーを使った場合の直接エージェントから Azure Automation および Log Analytics へのデータ フローを示したものです。 エージェントのプロキシ構成は、OMS ゲートウェイがサービスとの通信に使っているものと同じポートを使う必要があります。  

![エージェントとサービスの直接通信の図](./media/log-analytics-oms-gateway/oms-omsgateway-agentdirectconnect.png)

次の図は、Operations Manager 管理グループから Log Analytics へのデータ フローを示したものです。   

![Operations Manager と Log Analytics の通信の図](./media/log-analytics-oms-gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="prerequisites"></a>前提条件

OMS ゲートウェイを実行するコンピューターを指定するときは、次のコンポーネントがあることを確認します。

* Windows 10、Windows 8.1、Windows 7
* Windows Server 2016、Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2、Windows Server 2008
* .NET Framework 4.5
* 最低 4 コアのプロセッサと 8 GB のメモリ 

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

### <a name="supported-encryption-protocols"></a>サポート対象の暗号化プロトコル
OMS ゲートウェイは、トランスポート層セキュリティ (TLS) 1.0、1.1、1.2 のみをサポートします。  Secure Sockets Layer (SSL) はサポートされません。

### <a name="supported-number-of-agent-connections"></a>サポートされるエージェント接続の数
次の表に、ゲートウェイ サーバーと通信するエージェントがサポートされる数を示します。 このサポートは、6 秒ごとに約 200 KB のデータをアップロードするエージェントに基づいています。 テストされるエージェントごとのデータ量は、1 日あたり約 2.7 GB です。

|ゲートウェイ |サポートされるエージェントの概数|  
|--------|----------------------------------|  
|- CPU: Intel XEON CPU E5-2660 v3 @ 2.6 GHz 2 コア<br> - メモリ: 4 GB<br> - ネットワーク帯域幅: 1 Gbps| 600|  
|- CPU: Intel XEON CPU E5-2660 v3 @ 2.6 GHz 4 コア<br> - メモリ: 8 GB<br> - ネットワーク帯域幅: 1 Gbps| 1,000|  

## <a name="download-the-oms-gateway"></a>OMS ゲートウェイをダウンロードする

最新バージョンの OMS ゲートウェイのセットアップ ファイルの入手方法は 2 とおりあります。

1. [Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=54443)からダウンロードする。

2. Azure Portal からダウンロードする。 Azure Portal にサインインしてから、次の手順を実行します。  

   1. サービスの一覧を参照し、**[Log Analytics]** を選択します。  
   2. ワークスペースを選択します。
   3. ワークスペース ブレードの **[全般]** で **[クイック スタート]** をクリックします。
   4. **[ワークスペースに接続するデータ ソースを選択する]** の **[コンピューター]** を選択します。
   5. **[ダイレクト エージェント]** パネルで **[OMS ゲートウェイのダウンロード]** を選びます。
   
    ![OMS ゲートウェイのダウンロード](./media/log-analytics-oms-gateway/download-gateway.png)

-- または -- 

   1. ワークスペース ブレードの **[設定]** で、**[詳細設定]** を選びます。
   2. **[接続されたソース]** > **[Windows サーバー]** の順に選びます。 次に、**[OMS ゲートウェイのダウンロード]** を選びます。

## <a name="install-the-oms-gateway"></a>OMS ゲートウェイをインストールする

ゲートウェイをインストールするには、以下の手順を実行します。 以前のバージョン (旧称 "*Log Analytics フォワーダー*") をインストールしている場合には、今回のリリースにアップグレードされます。  

1. インストール先のフォルダーで **OMS Gateway.msi** を選びます。
2. **[ようこそ]** ページで **[次へ]** をクリックします。

 ![ゲートウェイ セットアップ ウィザード](./media/log-analytics-oms-gateway/gateway-wizard01.png)
  
3. **[使用許諾契約書]** ページで、**[使用許諾契約書に同意します]** を選びます。 次に、**[次へ]** を選択します。

4. **[Port and proxy address (ポートとプロキシ アドレス)]** ページで、以下を実行します。

   a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 ゲートウェイに使う TCP ポートの番号を入力します。 セットアップにより、Windows ファイアウォールに対してこのポート番号を使った受信ルールが構成されます。 既定値は 8080 です。 有効なポート番号の範囲は、1 ～ 65535 です。 この範囲の値を入力しないと、エラー メッセージが表示されます。

   b. ゲートウェイがインストールされているサーバーがプロキシを介して通信する必要がある場合は、ゲートウェイが接続する必要のあるプロキシを入力します。 たとえば、次のスクリーンショットでは「`http://myorgname.corp.contoso.com:80`」を入力しています。 このフィールドを空白のままにすると、ゲートウェイはインターネットに直接接続しようとします。  プロキシ サーバーで認証が必要な場合は、ユーザー名とパスワードを入力します。
   
    ![ゲートウェイ ウィザードのプロキシの構成](./media/log-analytics-oms-gateway/gateway-wizard02.png)

4. **[次へ]**を選択します。

5. Microsoft Update が有効になっていない場合は、Microsoft Update のページが表示され、有効にするかどうかを選択できます。 選択を行い、**[次へ]** を選びます。 使用する場合は、次の手順に進みます。

6. **[インストール先のフォルダー]** ページでは、既定のフォルダー **C:\Program Files\OMS Gateway** をそのまま使うか、OMS ゲートウェイをインストールする場所を入力します。 次に、**[次へ]** を選択します。

7. **[インストールの準備完了]** ページで **[インストール]** をクリックします。 [ユーザー アカウント制御] が表示され、インストールのためのアクセス許可が要求されることがあります。 アクセス許可の要求が表示された場合は、**[はい]** を選びます。

8. セットアップが完了した後、**[完了]** を選びます。 サービスが実行中であることを確認するには、services.msc スナップインを開きます。そこでサービスの一覧に **[OMS ゲートウェイ]** が表示されており、状態が **[実行中]** であれば、サービスは正常に実行されています。

    ![サービス – OMS ゲートウェイ](./media/log-analytics-oms-gateway/gateway-service.png)  

## <a name="configure-network-load-balancing"></a>ネットワーク負荷分散を構成する 
ネットワーク負荷分散 (NLB) を使って高可用性用にゲートウェイを構成することができます。 Microsoft のネットワーク負荷分散またはハードウェア ベースのロード バランサーのいずれかを使います。  ロード バランサーは、OMS エージェントまたは Operations Manager 管理サーバーからの接続要求を自らのノードにリダイレクトする形で、トラフィックを管理します。 1 台のゲートウェイ サーバーで障害が発生した場合、トラフィックは他のノードにリダイレクトされます。

Windows Server 2016 のネットワーク負荷分散クラスターの設計およびデプロイの方法については、「[ネットワーク負荷分散](https://technet.microsoft.com/windows-server-docs/networking/technologies/network-load-balancing)」を参照してください。  以下の手順では、Microsoft ネットワーク負荷分散クラスターの構成方法を説明します。  

1.  管理者アカウントを使って、NLB クラスターのメンバーとなっている Windows サーバーにサインインします。  

2.  サーバー マネージャーでネットワーク負荷分散マネージャーを開きます。 **[ツール]** を選択し、**[ネットワーク負荷分散マネージャー]** を選択します。

3. Microsoft Monitoring Agent がインストールされている OMS ゲートウェイ サーバーを接続するには、**[ホストをクラスターに追加]** を選びます。

 ![ネットワーク負荷分散マネージャー -- ホストをクラスターに追加](./media/log-analytics-oms-gateway/nlb02.png)

4. 接続するゲートウェイ サーバーの IP アドレスを入力します。

    ![ネットワーク負荷分散マネージャー -- ホストをクラスターに追加: 接続](./media/log-analytics-oms-gateway/nlb03.png) 
    
## <a name="configure-the-oms-agent-and-the-operations-manager-management-group"></a>OMS エージェントと Operations Manager 管理グループを構成する
この記事の以下のセクションでは、直接接続されている OMS エージェント、Operations Manager 管理グループ、または Azure Automation Hybrid Runbook Worker に対して Azure Automation または Log Analytics との通信のために OMS ゲートウェイを構成する手順を説明します。  

Log Analytics に直接接続している Windows コンピューターに OMS エージェントをインストールするための要件と手順については、「[Azure Log Analytics を使用して環境内のコンピューターからデータを収集する](log-analytics-concept-hybrid.md#prerequisites)」をご覧ください。

 Linux コンピューターの場合は、「[Linux コンピューターを Log Analytics に接続する](log-analytics-quick-collect-linux-computer.md)」をご覧ください。 Automation Hybrid Runbook Worker に関連する情報は、[Hybrid Runbook Worker のデプロイ](../automation/automation-hybrid-runbook-worker.md)に関するページをご覧ください。

### <a name="configure-the-standalone-oms-agent"></a>スタンドアロンの OMS エージェントを構成する
プロキシ サーバー (この場合はゲートウェイ) を使うようにエージェントを構成する方法について詳しくは、「[Azure Log Analytics を使用して環境内のコンピューターからデータを収集する](log-analytics-concept-hybrid.md#prerequisites)」をご覧ください。 ネットワーク ロード バランサーの背後に複数のゲートウェイ サーバーをデプロイしている場合には、OMS エージェントのプロキシ構成が、NLB の仮想 IP アドレスとなります。

![Microsoft Monitoring Agent のプロパティ – プロキシの設定](./media/log-analytics-oms-gateway/nlb04.png)

### <a name="configure-operations-manager-all-agents-use-the-same-proxy-server"></a>Operations Manager を構成する: エージェントがすべて同じプロキシ サーバーを使用する
ゲートウェイ サーバーを追加するように Operations Manager を構成します。  Operations Manager に報告するすべてのエージェントに Operations Manager のプロキシ構成が自動的に適用されます (設定が空欄であっても同じです)。  

OMS ゲートウェイを使って Operations Manager をサポートするには、次のコンポーネントが配置されている必要があります。

* Microsoft Monitoring Agent (エージェント バージョン **8.0.10900.0** 以降) がゲートウェイ サーバーにインストールされていて、通信する Log Analytics ワークスペース用に構成されていること。

* ゲートウェイがインターネットに接続されていること、またはインターネットに接続できるプロキシ サーバーに接続されていること。

> [!NOTE]
> ゲートウェイの値を指定しなかった場合には、全部のエージェントに対して空の値がプッシュされます。
> 

今回初めて Operations Manager 管理グループを Log Analytics ワークスペースに登録する場合、管理グループのプロキシ構成を指定するオプションはオペレーション コンソールで使用できません。 

このオプションを使用可能にするには、管理グループがサービスに正常に登録される必要があります。 オペレーション コンソールおよび管理グループ内のすべての管理サーバーを実行しているのと同じシステムで、Netsh を使ってシステム プロキシ構成を更新します。

1. 管理者特権のコマンド プロンプトを開きます。

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 **[スタート]** に移動します。 「**cmd**」と入力します。
    
    b. **[コマンド プロンプト]** を右クリックします。 **[管理者として実行]** を選びます。
    
2. 次のコマンドを入力して、**Enter** キーを押します。

    `netsh winhttp set proxy <proxy>:<port>`

Log Analytics との統合を完了した後は、`netsh winhttp reset proxy` を実行して変更を削除できます。 その後、オペレーション コンソールの **[プロキシ サーバーの構成]** オプションを使って、OMS ゲートウェイ サーバーを指定します。 

1. Operations Manager コンソールを開きます。 **[Operations Management Suite]** で **[接続]** を選びます。 次に、**[プロキシ サーバーの構成]** を選びます。

    ![Operations Manager -- プロキシ サーバーの構成](./media/log-analytics-oms-gateway/scom01.png)

2. **[Operations Management Suite へのアクセスにプロキシ サーバーを使用する]** を選択します。 OMS ゲートウェイ サーバーの IP アドレス、または NLB の仮想 IP アドレスを入力します。 `http://` プレフィックスを省略しないで入力します。

    ![Operations Manager -- プロキシ サーバーのアドレス](./media/log-analytics-oms-gateway/scom02.png)

3. **[完了]** を選択します。 Operations Manager 管理グループは、Log Analytics サービスにゲートウェイ サーバー経由で通信するように構成されています。

### <a name="configure-operations-manager-specific-agents-use-proxy-server"></a>Operations Manager を構成する: 特定のエージェントがプロキシ サーバーを使用する
環境が大規模であるか、複雑な場合には、特定のサーバー (またはグループ) のみ OMS ゲートウェイ サーバーを使用する構成が必要になることも考えられます。 そのようなサーバーでは、Operations Manager エージェントを直接更新する方法は使えません。値が、管理グループ全体に適用されるグローバルな値で上書きされてしまうからです。 そこで、これらの値をプッシュするルールを上書きします。  

> [!NOTE] 
> 環境で複数の OMS ゲートウェイ サーバーを使用する場合にも、これと同じ構成のテクニックを流用できます。 たとえば、リージョンごとに固有の OMS ゲートウェイ サーバーの指定を必要とする場合が考えられます。
>  

1. Operations Manager コンソールを開き、**[作成]** ワークスペースを選びます。

2. [作成] ワークスペースで、**[ルール]** を選びます。 Operations Manager のツール バーで、**[スコープ]** ボタンを選びます。 このボタンが利用できない場合には、**[監視]** ウィンドウでフォルダーではなくオブジェクトを選択していることを確認してください。 **[管理パック オブジェクトのスコープ設定]** ダイアログ ボックスには、ターゲットになることが多いクラス、グループ、またはオブジェクトが一覧表示されます。 

3. **[検索]** フィールドに「**ヘルス サービス**」と入力します。 一覧からサービスを選びます。 **[OK]**を選択します。  

4. "**Advisor Proxy Setting Rule (Advisor プロキシ設定ルール)**" を検索します。 オペレーション コンソールのツール バーで、**[上書き]** を選びます。 **[Override the Rule\For a specific object of class: Health Service]\(ルールの上書き\クラス Health Service の特定のオブジェクト\)** を選びます。 

5. 次に、一覧から特定のオブジェクトを選びます。 

6. (省略可能) この上書きを適用するサーバーのヘルス サービス オブジェクトを含むカスタム グループを作成します。 その後は、そのグループに上書きを適用できます。

7. **[上書きのプロパティ]** ダイアログ ボックスで **WebProxyAddress** パラメーターの隣の **[上書き]** 列にチェック マークを付けます。  **[上書き値]** フィールドに、OMS ゲートウェイ サーバーの URL を入力します (URL にはプレフィックスとして `http://` を忘れずに付けてください)。  

    >[!NOTE]
    > このルールを手動で有効にする必要はありません。 このルールは、Microsoft System Center Advisor 監視サーバー グループを対象とした Microsoft System Center Advisor Secure Reference Override 管理パックに含まれる上書きで既に自動で管理されています。
    >   

8. **[目的の管理パックの選択]** の一覧から管理パックを選ぶか、**[新規]** を選んで封印されていない管理パックを新規作成します。 

9. 変更が済んだら **[OK]** を選びます。 

### <a name="configure-the-oms-gateway-for-automation-hybrid-runbook-workers"></a>Automation Hybrid Runbook Worker 用に OMS ゲートウェイを構成する
環境に Automation Hybrid Runbook Worker がある場合は、次の手順で、それらをサポートするように OMS ゲートウェイを構成するための手動で一時的な回避策を設定します。

次の手順では、Automation アカウントが存在する Azure リージョンがわかっている必要があります。 場所を見つけるには、次の手順を実行します。

1. [Azure Portal](https://portal.azure.com/) にサインインします。
2. Azure Automation サービスを選びます。
3. 適切な Azure Automation アカウントを選びます。
4. **[場所]** で、アカウントのリージョンを表示します。

    ![Azure Portal -- Automation アカウントの場所](./media/log-analytics-oms-gateway/location.png)  

次の表を使って、各場所の URL を確認します。

**ジョブ実行時データ サービスの URL**

| **場所** | **URL** |
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

| **場所** | **URL** |
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

更新の管理ソリューションを使う修正プログラムのためにコンピューターが Hybrid Runbook Worker として自動的に登録される場合は、次の手順に従います。

1. OMS ゲートウェイの許可ホスト一覧に、ジョブ ランタイム データ サービスの URL を追加します。 たとえば、次を入力します。`Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`

2. 次の PowerShell コマンドレットを使って、OMS ゲートウェイ サービスを再起動します。`Restart-Service OMSGatewayService`

Hybrid Runbook Worker 登録コマンドレットを使って Azure Automation にコンピューターをオンボードする場合は、次の手順を使います。

1. OMS ゲートウェイの許可ホスト一覧に、エージェント サービス登録 URL を追加します。 たとえば、次を入力します。`Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`

2. OMS ゲートウェイの許可ホスト一覧に、ジョブ ランタイム データ サービスの URL を追加します。 たとえば、次を入力します。`Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`

3. OMS ゲートウェイ サービスを再起動します。`Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>便利な PowerShell コマンドレット
OMS ゲートウェイの構成設定の更新に必要なタスクを実行するには、コマンドレットが役に立ちます。 それらを使う前に、次の手順を実行します。

1. OMS ゲートウェイ (MSI) をインストールします。
2. PowerShell コンソール ウィンドウを開きます。
3. 次のコマンドを入力してモジュールをインポートします。`Import-Module OMSGateway`
4. 前の手順でエラーが発生しなかった場合は、モジュールが正常にインポートされたので、コマンドレットを使うことができます。 「 `Get-Module OMSGateway`」と入力します。
5. コマンドレットを使って変更を行った後は、OMS ゲートウェイ サービスを必ず開始し直します。

手順 3 でエラーが発生した場合は、モジュールはインポートされませんでした。 PowerShell がモジュールを発見できない場合、エラーが発生することがあります。 OMS ゲートウェイのインストール パス *C:\Program Files\Microsoft OMS Gateway\PowerShell* にあります。

| **コマンドレット** | **パラメーター** | **説明** | **例** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |キー |サービスの構成を取得します |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |キー (必須) <br> 値 |サービスの構成を変更します |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |リレー (アップストリーム) プロキシのアドレスを取得します |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |Address<br> ユーザー名<br> パスワード |リレー (アップストリーム) プロキシのアドレス (および資格情報) を設定します |1.リレー プロキシと資格情報を設定します。<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2.認証を必要としないリレー プロキシを設定します。`Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 手順 3.リレー プロキシ設定をクリアします。<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |現在許可されているホストを取得します (ローカルに構成されている許可ホストだけであり、自動的にダウンロードされた許可ホストは含みません) |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |ホスト (必須) |許可リストにホストを追加します |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |ホスト (必須) |許可リストからホストを削除します |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |サブジェクト (必須) |クライアント証明書のサブジェクトを許可リストに追加します |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |サブジェクト (必須) |クライアント証明書のサブジェクトを許可リストから削除します |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |現在許可されているクライアント証明書のサブジェクトを取得します (ローカルに構成されている許可サブジェクトだけであり、自動的にダウンロードされた許可サブジェクトは含みません) |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>トラブルシューティング
ゲートウェイによってログに記録されたイベントを収集するには、ほかにも OMS エージェントをインストールしておく必要があります。

![イベント ビューアー -- OMS ゲートウェイ ログ](./media/log-analytics-oms-gateway/event-viewer.png)

**OMS ゲートウェイのイベント ID と説明**

次の表では、OMS ゲートウェイ ログ イベントのイベント ID と説明を示します。

| **ID** | **説明** |
| --- | --- |
| 400 |特定の ID がないすべてのアプリケーション エラー。 |
| 401 |正しくない構成。 例: listenPort が整数ではなくテキスト。 |
| 402 |TLS ハンドシェイク メッセージ解析中の例外。 |
| 403 |ネットワーク エラー。 例: ターゲット サーバーに接続できません。 |
| 100 |一般情報。 |
| 101 |サービスが開始しました。 |
| 102 |サービスが停止しました。 |
| 103 |クライアントから HTTP CONNECT コマンドを受信しました。 |
| 104 |HTTP CONNECT コマンドではありません。 |
| 105 |接続先サーバーが許可リストにないか、または接続先ポートがセキュリティで保護されたポート (443) ではありません。 <br> <br> ゲートウェイ サーバー上の MMA エージェントと、ゲートウェイと接続しているエージェントが、同じ Log Analytics ワークスペースに接続されていることを確認します。 |
| 105 |エラー TcpConnection – 無効なクライアント証明書: CN=Gateway <br><br> 次のことを確認します。 <br>    <br> - バージョン番号 1.0.395.0 以降のゲートウェイを使っていること。 <br> - ゲートウェイ サーバー上の MMA エージェントと、ゲートウェイと接続しているエージェントが、同じ Log Analytics ワークスペースに接続されていること。 |
| 106 |OMS ゲートウェイは、TLS 1.0、1.1、1.2 のみをサポートします。  SSL はサポートされません。 サポートされていない TLS または SSL プロトコルのバージョンについては、OMS ゲートウェイによってイベント ID 106 が生成されます。|
| 107 |TLS セッションが確認されました。 |

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

サポートを要求するには、ポータルの右上隅にある疑問符記号を選びます。 次に、**[新しいサポート要求]** を選びます。 新しいサポート要求フォームを作成します。

![新しいサポート要求](./media/log-analytics-oms-gateway/support.png)

## <a name="next-steps"></a>次の手順
[データ ソースを追加](log-analytics-data-sources.md)して、接続されたソースからデータを収集して Log Analytics ワークスペースに格納します。
