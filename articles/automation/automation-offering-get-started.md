---
title: Azure オートメーションの使用
description: この記事では、Azure Automation サービスの概要について説明します。 ここでは、Azure Marketplace のサービスを利用開始するための準備における設計と実装の詳細をレビューします。
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 2fcbbc2532e5cb9963922b4987ba0c7080fdb170
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/05/2018
---
# <a name="get-started-with-azure-automation"></a>Azure オートメーションの使用

この記事では、Azure Automation のデプロイに関連する主要な概念を紹介します。 Automation in Azure を初めて使用する場合や、System Center Orchestrator などのオートメーション ワークフロー ソフトウェアを使用した経験がある場合は、Automation を準備して利用開始するための方法を学習できます。 この記事を読み終わると、プロセス オートメーションのニーズをサポートする Runbook の開発を開始する準備ができています。 


## <a name="automation-architecture-overview"></a>Automation アーキテクチャの概要

![Azure Automation の概要](media/automation-offering-get-started/automation-infradiagram-networkcomms.png)

Azure Automation は、Runbook を使用してプロセスを自動化できるスケーラブルでかつ信頼性の高いマルチテナント環境を提供する、サービスとしてのソフトウェア (SaaS) アプリケーションです。 Azure やその他のクラウド サービスまたはオンプレミスの環境で望ましい状態への構成 (DSC) を使用して、Windows および Linux システムへの構成変更を管理できます。 Automation アカウント内のエンティティ (Runbook、資産、および実行アカウントを含む) は、サブスクリプション内の他の Automation アカウントや他のサブスクリプションから分離されます。  

Azure で実行する Runbook は、Automation サンドボックス上で実行されます。 サンドボックスは、Azure サービスとしてのプラットフォーム (PaaS) 仮想マシンでホストされます。 

Automation サンドボックスは、モジュール、ストレージ、メモリ、ネットワーク通信、ジョブ ストリームなどの Runbook 実行のすべての側面に対するテナント分離を提供します。 このロールは、サービスによって管理されます。 このロールは、Azure または Automation アカウントからアクセスしたり管理したりすることはできません。         

ローカル データセンターまたは他のクラウド サービスでリソースのデプロイと管理を自動化するには、Automation アカウントを作成した後、[Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) ロールを実行する 1 つ以上の VM を指定できます。 各 Hybrid Runbook Worker には、インストールされる Microsoft Management Agent と Automation アカウントが必要です。 このエージェントには、Azure Log Analytics ワークスペースへの接続が必要です。 Log Analytics を使用すると、インストールをブートストラップしたり、Microsoft Management Agent を保守したり、Hybrid Runbook Worker の機能を監視したりできます。 Azure Automation は、Runbook の配信や、それらを実行するための指示を行います。

複数の Hybrid Runbook Worker をデプロイできます。 Runbook や負荷分散 Runbook ジョブに高可用性を提供するには、Hybrid Runbook Worker を使用します。 場合によっては、Runbook ジョブを特定のワークロードまたは環境専用にできます。 Hybrid Runbook Worker 上の Microsoft Monitoring Agent は、TCP ポート 443 経由で Automation サービスとの通信を開始します。 Hybrid Runbook Worker には、受信ファイアウォールの要件はありません。  

環境内の他のマシンまたはサービスに対する管理タスクを実行するために Hybrid Runbook Worker 上で実行されている Runbook が必要になることがあります。 そのシナリオでは、その Runbook も他のポートへのアクセスが必要になることがあります。 IT セキュリティ ポリシーでネットワーク上のコンピューターによるインターネットへの接続が許可されない場合は、[OMS ゲートウェイ](../log-analytics/log-analytics-oms-gateway.md)を確認してください。 OMS ゲートウェイは、Hybrid Runbook Worker のプロキシとして機能します。 これはジョブの状態を収集し、Automation アカウントから構成情報を受信します。

Hybrid Runbook Worker 上で実行されている Runbook は、コンピューター上のローカル システム アカウントのコンテキストで実行されます。 ローカルの Windows マシンに対する管理アクションを実行する場合は、セキュリティ コンテキストをお勧めします。 Runbook でローカル コンピューターの外部にあるリソースに対するタスクを実行する場合は、Automation アカウントでのセキュリティ保護された資格情報資産の定義が必要になることがあります。 Runbook からそれらのセキュリティ保護された資格情報資産にアクセスし、それを使用して外部リソースに対して認証できます。 Runbook では、[資格情報](automation-credentials.md)、[証明書](automation-certificates.md)、および[接続](automation-connections.md)資産を使用できます。 これらの資産は、それを認証する資格情報を指定するために使用できるコマンドレットで使用します。

仮想マシンには、Azure Automation に格納されている DSC 構成を適用できます。 その他の物理および仮想マシンは、Automation DSC プル サーバーに構成を要求できます。 オンプレミスの物理または仮想 Windows および Linux システムの構成を管理するために、Automation DSC プル サーバーをサポートするインフラストラクチャをデプロイする必要はありません。 Automation DSC を使用して管理する各システムからの送信インターネット アクセスだけが必要です。 Log Analytics サービスへの通信が TCP ポート 443 経由で発生します。   

## <a name="prerequisites"></a>前提条件

### <a name="automation-dsc"></a>Automation DSC
Automation DSC を使用して次のマシンを管理できます。

* Windows または Linux を実行する Azure 仮想マシン (クラシック)。
* Windows または Linux を実行する Azure 仮想マシン。
* Windows または Linux を実行する Amazon Web Services (AWS) 仮想マシン。
* オンプレミスの、または Azure や AWS 以外のクラウド内の物理および仮想 Windows コンピューター。
* オンプレミスの、または Azure や AWS 以外のクラウド内の物理および仮想 Linux コンピューター。

Windows マシンの場合は、最新バージョンの Windows Management Framework (WMF) 5 がインストールされている必要があります。 Linux マシンの場合は、最新バージョンの [PowerShell DSC エージェント for Linux](https://www.microsoft.com/en-us/download/details.aspx?id=49150) がインストールされている必要があります。 PowerShell DSC エージェントは、WMF 5 を使用して Automation と通信します。 

### <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker  
ハイブリッド Runbook ジョブを実行するコンピューターを指定する場合、そのコンピューターは次の前提条件を満たしている必要があります。

* Windows Server 2012 以降。
* Windows PowerShell 4.0 またはそれ以降。 信頼性を向上させるために Windows PowerShell 5.0 をお勧めします。 Microsoft ダウンロード センターから[新しいバージョンをダウンロード](https://www.microsoft.com/download/details.aspx?id=50395)できます。
* .NET Framework 4.6.2 以降。
* 少なくとも 2 つのコア。
* 少なくとも 4 GB の RAM。

### <a name="permissions-required-to-create-an-automation-account"></a>Automation アカウントを作成するために必要なアクセス許可
Automation アカウントを作成または更新したり、この記事で説明されているタスクを完了したりするには、次の特権とアクセス許可が必要です。   
 
* Automation アカウントを作成するには、Azure Active Directory (Azure AD) ユーザー アカウントが、**Microsoft.Automation** リソースの所有者ロールに相当するアクセス許可を持つロールに追加されている必要があります。 詳細については、「[Azure Automation におけるロールベースのアクセス制御](automation-role-based-access-control.md)」を参照してください。  
* Azure Portal の **[Azure Active Directory]** > **[管理]** > **[アプリの登録]** で、(**[アプリの登録]** が **[はい]** に設定されている場合)、Azure AD テナント内の管理者以外のユーザーは [Active Directory アプリケーションを登録](../azure-resource-manager/resource-group-create-service-principal-portal.md#check-azure-subscription-permissions)できます。 **[アプリの登録]** が **[いいえ]** に設定されている場合、このアクションを実行するユーザーは Azure AD 内のグローバル管理者である必要があります。 

サブスクリプションの Active Directory インスタンスのメンバーになっていない状態で、サブスクリプションのグローバル管理者/共同管理者ロールに追加された場合、Active Directory にはゲストとして追加されます。 このシナリオでは、**[Automation アカウントの追加]** ページに "作成するためのアクセス許可がありません" というメッセージが表示されます。 

ユーザーが先にグローバル管理者/共同管理者ロールに追加された場合は、そのユーザーをサブスクリプションの Active Directory インスタンスから削除した後、Active Directory の完全なユーザー ロールに再度追加できます。

ユーザー ロールを確認するには
1. Azure Portal で、**[Azure Active Directory]** ウィンドウに移動します。
2. **[ユーザーとグループ]** を選択します。
3. **[すべてのユーザー]** を選択します。 
4. 特定のユーザーを選択した後、**[プロファイル]** を選択します。 ユーザーのプロファイルの下にある **[ユーザー タイプ]** 属性の値が **[ゲスト]** であってはいけません。

## <a name="authentication-planning"></a>認証を計画する
Azure Automation では、Azure、オンプレミス、およびその他のクラウド サービス内のリソースに対するタスクを自動化できます。 Runbook が必要なアクションを実行するには、リソースに安全にアクセスするためのアクセス許可を持っている必要があります。 そのサブスクリプション内で必要な最小限の権利が必要です。  

### <a name="what-is-an-automation-account"></a>Automation アカウントとは 
Azure Automation のコマンドレットを使用してリソースに対して実行するオートメーション タスクはすべて、Azure AD 組織 ID 資格情報ベースの認証を使用して Azure に対して認証されます。  Automation アカウントは、Azure リソースを構成および使用するためにポータルにサインインするときに使用するアカウントとは異なります。 

Automation アカウントには、次のリソースが含まれています。

* **証明書**。 Runbook または DSC 構成からの認証に使用される証明書が含まれています。 証明書を追加することもできます。
* **接続**。 Runbook または DSC 構成から外部サービスまたはアプリケーションに接続するために必要な認証および構成情報が含まれています。
* **資格情報**。 ユーザー名やパスワードなどのセキュリティ資格情報を持つ **PSCredential** オブジェクトが含まれています。 これらの資格情報は、Runbook または DSC 構成から認証するために必要です。
* **統合モジュール**。 Automation アカウントに含まれている PowerShell モジュール。 Runbook および DSC 構成でコマンドレットを実行するには、PowerShell モジュールを使用します。
* **スケジュール**。 指定された時間に Runbook を開始または停止するスケジュール (繰り返し頻度を含む) が含まれています。
* **変数**。 Runbook または DSC 構成から使用可能な値が含まれています。
* **DSC 構成**。 オペレーティング システムの機能や設定を構成する方法、あるいは Windows または Linux コンピューターにアプリケーションをインストールする方法を記述した PowerShell スクリプト。  
* **Runbook**。 Windows PowerShell に基づいて Automation で自動プロセスを実行する一連のタスク。    

Automation アカウントごとの Automation リソースは、1 つの Azure リージョンに関連付けられています。 ただし、Automation アカウントを使用してサブスクリプション内のすべてのリソースを管理できます。 ポリシーにより、データやリソースを特定のリージョンに分離する必要がある場合は、異なるリージョンで Automation アカウントを作成します。

Azure Portal で Automation アカウントを作成すると、次の 2 つの認証エンティティが自動的に作成されます。

* **実行アカウント**。 このアカウントは、次のタスクを実行します。
  - Azure AD 内にサービス プリンシパルを作成します。
  - 証明書を作成します。
  - Runbook を使用して Azure Resource Manager リソースを管理する、共同作成者のロールベースのアクセス制御 (RBAC) を割り当てます。
* **クラシック実行アカウント**。 このアカウントは、管理証明書をアップロードします。 この証明書は、Runbook を使用してクラシック リソースを管理するために使用されます。

Azure AD ユーザー アカウントや実行アカウントに許可されたアクションを付与するために、Resource Manager では RBAC が使用できます。 また、RBAC を使用してそのサービス プリンシパルを認証することもできます。 詳細については、また Automation アクセス許可を管理するためのモデルの開発に役立つ情報については、[「Azure Automation におけるロールベースのアクセス制御」の記事](automation-role-based-access-control.md)を参照してください。  

#### <a name="authentication-methods"></a>認証方法
次の表は、Azure Automation でサポートされる環境ごとに使用できる認証方法をまとめたものです。

| 方法 | 環境 
| --- | --- | 
| Azure 実行アカウントとクラシック実行アカウント |Azure Resource Manager と Azure クラシック デプロイメント |  
| Azure AD ユーザー アカウント |Azure Resource Manager と Azure クラシック デプロイメント |  
| Windows 認証 |Hybrid Runbook Worker ロールを使用したローカル データセンターまたはその他のクラウド サービス プロバイダー |  
| Amazon Web Services 資格情報 |Amazon Web Services |  

次の記事では、これらの環境の認証を構成するための概要および実装手順について説明します。 これらの記事では、既存のアカウントの使用、およびその環境専用にする新しいアカウントの使用について説明します。 
* [スタンドアロン Azure Automation アカウントを作成する](automation-create-standalone-account.md)
* [Azure クラシック デプロイと Resource Manager で Runbook を認証する](automation-create-aduser-account.md)
* [Amazon Web Services で Runbook を認証する](automation-config-aws-account.md)
* [Automation 実行アカウントを更新する](automation-create-runas-account.md)

Azure 実行アカウントおよびクラシック実行アカウントの場合は、「[Automation 実行アカウントを更新する](automation-create-runas-account.md)」に、ポータルからそれらの実行アカウントを使用して既存の Automation アカウントを更新する方法が示されています。 そこにはまた、最初に実行アカウントまたはクラシック実行アカウントと共に Automation アカウントが構成されなかった場合に PowerShell を使用する方法も説明されています。 実行アカウントおよびクラシック実行アカウントは、エンタープライズ証明機関 (CA) によって発行された証明書を使用して作成できます。 この構成を使用してアカウントを作成する方法を学習するには、「[Automation 実行アカウントを更新する](automation-create-runas-account.md)」をレビューしてください。     
 
## <a name="network-planning"></a>ネットワークを計画する
Hybrid Runbook Worker が Log Analytics に接続して登録するには、このセクションで説明されているポート番号と URL へのアクセスが必要です。 これは、Log Analytics に接続するために [Microsoft Monitoring Agent に必要なポートと URL](../log-analytics/log-analytics-windows-agent.md) に加えて必要です。 

エージェントと Log Analytics サービスの間の通信にプロキシ サーバーを使用する場合は、適切なリソースにアクセスできることを確認してください。 ファイアウォールを使用してインターネットへのアクセスを制限する場合は、アクセスを許可するようにファイアウォールを構成する必要があります。

Hybrid Runbook Worker ロールが Automation と通信するには、次のポートと URL が必要です。

* ポート: 送信インターネット アクセスには TCP 443 のみが必要です。
* グローバル URL: *.azure-automation.net。

特定のリージョンに対して定義された Automation アカウントがある場合は、そのリージョン データセンターへの通信を制限できます。 次の表は、リージョンごとの DNS レコードを示しています。

| **[リージョン]** | **DNS レコード** |
| --- | --- |
| 米国中南部 |scus-jobruntimedata-prod-su1.azure-automation.net |
| 米国東部 2 |eus2-jobruntimedata-prod-su1.azure-automation.net |
| 米国中西部 | wcus-jobruntimedata-prod-su1.azure-automation.net |
| 西ヨーロッパ |we-jobruntimedata-prod-su1.azure-automation.net |
| 北ヨーロッパ |ne-jobruntimedata-prod-su1.azure-automation.net |
| カナダ中部 |cc-jobruntimedata-prod-su1.azure-automation.net |
| 東南アジア |sea-jobruntimedata-prod-su1.azure-automation.net |
| インド中部 |cid-jobruntimedata-prod-su1.azure-automation.net |
| 東日本 |jpe-jobruntimedata-prod-su1.azure-automation.net |
| オーストラリア東南部 |ase-jobruntimedata-prod-su1.azure-automation.net |
| 英国南部 | uks-jobruntimedata-prod-su1.azure-automation.net |
| 米国政府バージニア州 | usge-jobruntimedata-prod-su1.azure-automation.us |

リージョン名の代わりにリージョン IP アドレスの一覧を入手するには、Microsoft ダウンロード センターから [Azure データセンター IP アドレス](https://www.microsoft.com/download/details.aspx?id=41653) XML ファイルをダウンロードします。 

> [!NOTE]
> Azure データセンター IP アドレス XML ファイルには、Microsoft Azure データセンターで使用されている IP アドレス範囲が一覧表示されています。 このファイルには、計算、SQL、およびストレージの範囲が含まれています。 
>
>更新されたファイルが毎週投稿されます。 このファイルには、現在デプロイされている範囲と、次に予定されている IP 範囲の変更が反映されています。 このファイルに現れる新しい範囲は、少なくとも 1 週間はデータセンターで使用されません。 
>
> 新しい XML ファイルを毎週ダウンロードすることをお勧めします。 その後、Azure で実行されているサービスを正しく識別するようにサイトを更新します。 Azure ExpressRoute ユーザーは、このファイルが、毎月第 1 週に Azure 領域のボーダー ゲートウェイ プロトコル (BGP) アドバタイズを更新するために使用されることに注意してください。 
> 

## <a name="creating-an-automation-account"></a>Automation アカウントを作成する

次の表では、Azure Portal で Automation アカウントを作成するための方法を紹介します。 次の表では、各種類のデプロイ エクスペリエンスとそれらの違いについて説明します。  

|方法 | [説明] |
|-------|-------------|
| Azure Marketplace で **[オートメーションと制御]** を選択する | Azure Marketplace サービスは、互いにリンクされ、同じリソース グループおよびリージョン内にある Automation アカウントと Log Analytics ワークスペースを作成します。 Log Analytics との統合には、Log Analytics を使用して Runbook ジョブの状態やジョブ ストリームを一定期間にわたって監視したり、分析したりするメリットも含まれます。 また、Log Analytics の高度な機能を使用して、問題をエスカレートまたは調査することもできます。 このサービスは、既定で有効になっている **[変更の追跡]** および **[更新管理]** ソリューションをデプロイします。 |
| Marketplace で **[Automation]** を選択する | この方法では、Log Analytics ワークスペースにリンクされていない新規または既存のリソース グループ内に Automation アカウントを作成します。 これには、**[オートメーションと制御]** サービスの使用可能なソリューションは含まれません。 この方法は、ユーザーを Automation に導入するための基本構成です。 これは、Runbook および DSC 構成を書き込む方法を学習したり、このサービスの機能の使用方法を学習したりするのに役立ちます。 |
| **管理**ソリューションを選択する | **[管理]**ソリューション ([[更新管理]](../operations-management-suite/oms-solution-update-management.md)、[[勤務時間外に VM を起動/停止する]](automation-solution-vm-management.md)、または [[変更の追跡]](../log-analytics/log-analytics-change-tracking.md) を含む) を選択すると、そのソリューションから既存の Automation アカウントと Log Analytics ワークスペースを選択するよう求められます。 このソリューションでは、サブスクリプション内にデプロイされるソリューション用の Automation アカウントと Log Analytics ワークスペースを必要に応じて作成するオプションが提供されます。 |

### <a name="create-an-automation-account-thats-integrated-with-log-analytics"></a>Log Analytics と統合された Automation アカウントを作成する
Automation を利用開始するには、Marketplace で **[オートメーションと制御]** サービスを選択することをお勧めします。 この方法を使用すると、Automation アカウントが作成され、Log Analytics ワークスペースとの統合が確立されます。 この方法を使用する場合は、そのサービスで使用可能な管理ソリューションをインストールするオプションも提供されます。  

「[スタンドアロン Azure Automation アカウントを作成する](automation-create-standalone-account.md)」では、**Automation & Control** サービスを利用開始することによって Automation アカウントと Log Analytics ワークスペースを作成するプロセスについて説明しています。 このサービスのテストまたはプレビューのためにスタンドアロン Automation アカウントを作成する方法を学習できます。  

**Automation & Control** Marketplace サービスを使用して Automation アカウントと Log Analytics ワークスペースを作成するには:

1. サブスクリプション管理者ロールのメンバーであり、かつサブスクリプションの共同管理者であるアカウントを使用して Azure Portal にサインインします。
2. **[新規]**を選択します。<br><br> ![Azure Portal で [新規] を選択する](media/automation-offering-get-started/automation-portal-martketplacestart.png)<br>  
3. **Automation** を検索します。 検索結果で、**[オートメーションと制御]** を選択します。<br><br> ![Azure Marketplace で [オートメーションと制御] を検索して選択します](media/automation-offering-get-started/automation-portal-martketplace-select-automationandcontrol.png)。<br>   
4. サービスの説明を確認してから、**[作成]** を選択します。  
5. **[オートメーションと制御]** で、**[OMS ワークスペース]** を選択します。 **[OMS ワークスペース]** で、Automation アカウントが存在する Azure サブスクリプションにリンクされた Log Analytics ワークスペースを選択します。 Log Analytics ワークスペースがない場合は、**[新しいワークスペースの作成]** を選択します。 **[OMS ワークスペース]** で、次の操作を行います。 
  1. **[OMS ワークスペース]** では、新しいワークスペースの名前を入力します。
  2. **[サブスクリプション]** では、リンク先のサブスクリプションを選択します。 既定の選択が使用するサブスクリプションでない場合は、ドロップダウン リストからサブスクリプションを選択します。
  3. **[リソース グループ]** では、リソース グループを作成するか、または既存のリソース グループを選択できます。  
  4. **[場所]** では、リージョンを選択します。 詳細については、「[which regions Azure Automation is available in (Azure Automation を使用できるリージョン)](https://azure.microsoft.com/regions/services/)」を参照してください。 ソリューションは、Free とノードごと (OMS) の 2 つのレベルで提供されています。 Free レベルには、1 日に収集されるデータ量、保有期間、および Runbook ジョブの実行時間 (分) に関する制限があります。 ノードごと (OMS) レベルには、1 日に収集されるデータ量に関する制限はありません。  
  5. **[Automation アカウント]** を選択します。  新しい Log Analytics ワークスペースを作成する場合は、新しい Log Analytics ワークスペースに関連付けられた Automation アカウントも作成する必要があります。 Azure サブスクリプション、リソース グループ、およびリージョンを含めます。 
    1. **[Create an Automation account] (Automation アカウントの作成)** を選択します。
    2. **[Automation アカウント]** の **[名前]** フィールドに、Automation アカウントの名前を入力します。
    それ以外のオプションはすべて、選択した Log Analytics ワークスペースに基づいて自動的に入力されます。 これらのオプションは変更できません。 
    3. このサービスでは、Azure 実行アカウントが既定の認証方法になります。 **[OK]** を選択すると、構成オプションが検証され、Automation アカウントが作成されます。 その進行状況を追跡するには、メニューの **[通知]** を選択します。 
    4. または、既存の Automation 実行アカウントを選択します。 選択するアカウントが既に別の Log Analytics ワークスペースにリンクされていてはいけません。 リンクされていると、通知メッセージが表示されます。 アカウントが既に Log Analytics ワークスペースにリンクされている場合は、別の Automation 実行アカウントを選択または作成します。
    5. 必要な情報を入力または選択したら、**[作成]** を選択します。 情報が検証され、Automation アカウントと実行アカウントが作成されます。 自動的に **[OMS ワークスペース]** ペインに戻ります。  
6. **[OMS ワークスペース]** ペインで必要な情報を入力または選択したら、**[作成]** を選択します。  情報が検証され、ワークスペースが作成されます。 その進行状況を追跡するには、メニューの **[通知]** を選択します。 **[ソリューションの追加]** ペインに戻ります。  
7. **[オートメーションと制御]** 設定で、推奨される事前に選択されたソリューションをインストールすることを確認します。 既定のオプションのいずれかを変更した場合は、後でソリューションを個別にインストールできます。  
8. Automation と Log Analytics ワークスペースの利用開始を続行するには、**[作成]** を選択します。 すべての設定が検証された後、Azure はサブスクリプションへのサービスのデプロイを試みます。 このプロセスには数秒かかることがあります。 その進行状況を追跡するには、メニューの **[通知]** を選択します。 

このサービスが利用開始された後は、次のタスクを実行できます。
* Runbook の作成を開始します。
* 有効にした管理ソリューションを操作します。
* [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) ロールをデプロイします。
* クラウドまたはオンプレミスの環境内のリソースによって生成されたデータを収集するために、[Log Analytics](https://docs.microsoft.com/azure/log-analytics) の操作を開始します。   

## <a name="next-steps"></a>次の手順
* 新しい Automation アカウントが Azure リソースに対して認証できることを確認します。 詳細については、「[Azure Automation 実行アカウントの認証をテストする](automation-verify-runas-authentication.md)」を参照してください。
* オーサリングを開始する前に、Runbook の作成を開始する方法および関連する考慮事項を学習します。 詳細については、「[Automation runbook types (Automation Runbook の種類)](automation-runbook-types.md)」を参照してください。


