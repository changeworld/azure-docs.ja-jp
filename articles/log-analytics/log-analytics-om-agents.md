---
title: Operations Manager を Log Analytics に接続する | Microsoft Docs
description: Operations Manager とワークスペースを統合することで、System Center Operations Manager への投資を維持しながら、Log Analytics で拡張機能を利用することができます。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 245ef71e-15a2-4be8-81a1-60101ee2f6e6
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/02/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: 4d0c8a4395ee70881ffee56f9ed030943c6fa557
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2018
ms.locfileid: "39495375"
---
# <a name="connect-operations-manager-to-log-analytics"></a>Operations Manager を Log Analytics に接続する
Operations Manager とLog Analyticsワークスペースを統合することで、System Center Operations Manager への投資を維持しながら、Log Analytics で拡張機能を利用することができます。  この統合により、Operations Manager を以下の目的に引き続き利用しながら､Log Analytics を活用することができます。

* Operations Manager で IT サービスの正常性を監視する
* インシデントおよび問題の管理をサポートする ITSM ソリューションとの統合を維持する
* Operations Manager で監視するオンプレミスおよびパブリック クラウド IaaS 仮想マシンにデプロイされたエージェントのライフ サイクルを管理する

System Center Operations Manager と統合すると、Operations Manager からのデータの収集、保管、および分析において Log Analytics の スピードと効率性を活かし､サービスの運用戦略の価値を高めることができます｡  Log Analytics は､既存の問題管理プロセスに対するサポートとして相互関連付けを行い､問題の原因の特定と再発の表面化に取り組むのに役立ちます｡  検索エンジンのパフォーマンスやイベント､アラート データをきめ細かく調査する際の柔軟性とそのデータを意味のある方法で公開する機能豊富なダッシュボードやレポート機能は､Log Analytics によって Operations Manager の機能は充実強化されたことを実証します｡

Operations Manager の管理グループに報告をするエージェントは､ワークスペースで有効にされている Log Analytics データ ソースとソリューションに基づいてサーバーからデータを収集します｡  有効にされているソリューションによって､データは Operations Manager 管理サーバーからサービスに直接に送信されるか､あるいはエージェント管理システムで収集されたデータ量を考慮して､エージェントから Log Analytics に直接に送信されます｡ 管理サーバーはサービスにデータを直接に転送するため､運用データベースやデータ ウェアハウス データベースにデータが書き込まれることはありません｡  管理サーバーと Log Analytics との接続が切断されると､Log Analytics との通信が再確立されるまで､データはローカルにキャッシュされます｡  保守計画または突然の停電が原因で管理サーバーがオフラインになった場合は､管理グループの別の管理サーバーが Log Analytics との接続を再開します｡  

次図は､System Center Operations Manager 管理グループの管理サーバーとエージェント､Log Analytics 間の接続 (方向とポートを含む) を表しています｡   

![oms-operations-manager-integration-diagram](./media/log-analytics-om-agents/oms-operations-manager-connection.png)

IT セキュリティ ポリシーによってネットワーク上のコンピューターがインターネットに接続できない場合は､管理サーバーが OMS ゲートウェイに接続するように設定することで､構成情報を受信したり､収集データを送信したりできます (有効にされているソリューションによる)｡  Operations Manager 管理グループが OMS ゲートウェイ経由で Log Analytics サービスと通信するように設定する方法についての詳細と手順は､[Connect computers to OMS using the OMS Gateway](log-analytics-oms-gateway.md)を参照してください｡  

## <a name="prerequisites"></a>前提条件 
始める前に、以下の要件を確認してください。

* Log Analytics は、System Center Operations Manager 1807、Operations Manager 1801、Operations Manager 2016、Operations Manager 2012 SP1 UR6 以降、Operations Manager 2012 R2 UR2 以降のみをサポートしています。  プロキシ サポートは、Operations Manager 2012 SP1 UR7 と Operations Manager 2012 R2 UR3 に追加されています。
* すべての Operations Manager エージェントが最小サポート要件を満たす必要があります。 エージェントに最小限の更新プログラムが適用されていることを確認してください。そうしないと、Windows エージェントの通信が失敗し、Operations Manager イベント ログにエラーが生成される可能性があります。
* Log Analytics ワークスペース。  詳細については、[環境内のコンピューターから Log Analytics に接続する方法](log-analytics-concept-hybrid.md)に関するページを参照してください。
* [Log Analytics Contributor ロール](log-analytics-manage-access.md#manage-accounts-and-users)のメンバーであるアカウントを使用して Azure の認証を受けます。  

>[!NOTE]
>Azure API の最近の変更により、初めて管理グループと Log Analytics 間の統合を構成する場合、正常に構成できなくなります。 管理グループを既にサービスに統合しているお客様は、既存の接続を再構成する必要がない限り、影響を受けることはありません。  
>Operations Manager の各バージョン用に新しい管理パックがリリースされました。  
>* System Center Operations Manager 1801 の場合は、[こちら](https://www.microsoft.com/download/details.aspx?id=57173)から管理パックをダウンロードします  
>* System Center 2016 - Operations Manager の場合は、[こちら](https://www.microsoft.com/download/details.aspx?id=57172)から管理パックをダウンロードします  
>* System Center Operations Manager 2012 R2 の場合は、[こちら](https://www.microsoft.com/en-us/download/details.aspx?id=57171)から管理パックをダウンロードします  

### <a name="network"></a>ネットワーク
下記は､Operations Manager のエージェントと管理サーバー､Operations コンソールが Log Analytics と通信するために必要なプロキシーとファイアウォールの構成情報の一覧です｡  各コンポーネントのトラフィックは､ネットワークから Log Analytics サービスのアウトバウンド方向に発生します｡     

|リソース | ポート番号| バイパス HTTP 検査|  
|---------|------|-----------------------|  
|**エージェント**|||  
|\*.ods.opinsights.azure.com| 443 |はい|  
|\*.oms.opinsights.azure.com| 443|はい|  
|\*.blob.core.windows.net| 443|はい|  
|\*.azure-automation.net| 443|はい|  
|**管理サーバー**|||  
|\*.service.opinsights.azure.com| 443||  
|\*.blob.core.windows.net| 443| はい|  
|\*.ods.opinsights.azure.com| 443| はい|  
|*.azure-automation.net | 443| はい|  
|**Operations Manager コンソール から OMS**|||  
|service.systemcenteradvisor.com| 443||  
|\*.service.opinsights.azure.com| 443||  
|\*.live.com| 80 および 443||  
|\*.microsoft.com| 80 および 443||  
|\*.microsoftonline.com| 80 および 443||  
|\*.mms.microsoft.com| 80 および 443||  
|login.windows.net| 80 および 443||  
|portal.loganalytics.io| 80 および 443||
|api.loganalytics.io| 80 および 443||
|docs.loganalytics.io| 80 および 443||  

### <a name="tls-12-protocol"></a>TLS 1.2 プロトコル
Log Analytics へのデータの転送時のセキュリティを保証するため、少なくともトランスポート層セキュリティ (TLS) 1.2 を使用するようにエージェントと管理グループを構成することを強くお勧めします。 以前のバージョンの TLS/SSL (Secure Sockets Layer) は脆弱であることが確認されています。現在、これらは下位互換性を維持するために使用可能ですが、**推奨されていません**。  詳細については、「[TLS 1.2 を使用して安全にデータを送信する](log-analytics-data-security.md#sending-data-securely-using-tls-12)」を参照してください。 

## <a name="connecting-operations-manager-to-log-analytics"></a>Operations Manager を Log Analytics に接続する
Operations Manager 管理グループが  Log Analytics ワークスペースの 1 つに接続するように設定するには､以下の手順を実行します｡

初めて Operations Manager 管理グループを Log Analytics ワークスペースに登録する場合、管理グループのプロキシ構成を指定するオプションはオペレーション コンソールで使用できません。  このオプションを使用可能にするには、管理グループがサービスに正常に登録される必要があります。  この問題を回避するには、オペレーション コンソールを実行しているシステムで Netsh を使用してシステム プロキシ構成を更新して、統合と、管理グループのすべての管理サーバーを構成する必要があります。  

1. 管理者特権でのコマンド プロンプトを開きます。
   a. **[スタート]** に移動し、「**cmd**」と入力します。
   b. **[コマンド プロンプト]** を右クリックし、[管理者として実行]** を選択します。
1. 次のコマンドを入力し、**Enter** キーを押します。

    `netsh winhttp set proxy <proxy>:<port>`

以下の手順で Log Analytics と統合すると､ `netsh winhttp reset proxy` を実行することで構成を削除した後､Operations コンソールの **Configure proxy server** オプションを使用して､プロキシまたは OMS ゲートウェイサーバーを指定することができます｡ 

1. Operations Manager コンソールで、 **[管理]** ワークスペースを選択します。
1. [Operations Management Suite] ノードを展開し、 **[接続]** をクリックします。
1. **[Operations Management Suite への登録]** リンクをクリックします。
1. **[Operations Management Suite オンボード ウィザード: 認証]** ページで、OMS サブスクリプションに関連付けられている管理者アカウントの電子メール アドレスまたは電話番号とパスワードを入力して、**[サインイン]** をクリックします。
1. 正しく認証されると､**Operations Management Suite Onboarding Wizard: Select Workspace** ページが表示され､Azure テナント、サブスクリプション、Log Analytics ワークスペースを選択するよう求められます｡  複数のワークスペースがある場合は、ドロップダウン リストから Operations Manager 管理グループに登録するワークスペースを選択し、 **[次へ]** をクリックします。
   
   > [!NOTE]
   > Operations Manager では､1 度に 1 つの Log Analytics ワークスペースをサポートしています｡ 前回のワークスペースで Log Analytics に登録されている接続とコンピューターは､Log Analytics から削除されます｡ 
   > 
   > 
1. **[Operations Management Suite オンボード ウィザード: 概要]** ページで設定を確認し、問題がなければ **[作成]** をクリックします。
1. **[Operations Management Suite オンボード ウィザード: 完了]** ページで、**[閉じる]** をクリックします。

### <a name="add-agent-managed-computers"></a>エージェントで管理されたコンピューターを追加する
Log Analytics ワークスペースとの統合が構成された後には､サービスとの接続が確立されるだけです｡管理グループに報告をするエージェントからデータが収集されることはありません｡ このデータ収集は、Log Analytics 用のデータを収集する、特定のエージェントで管理されたコンピューターを構成してはじめて行われるようになります。 コンピューター オブジェクトを個別に選択することも、Windows コンピューター オブジェクトを含むグループを選択することもできます。 論理ディスクや SQL データベースなどの別のクラスのインスタンスを含むグループを選択することはできません。

1. Operations Manager コンソールを開き、 **[Administration (管理)]** ワークスペースを選択します。
1. [Operations Management Suite] ノードを展開し、 **[接続]** をクリックします。
1. ウィンドウの右側の [アクション] 見出しの下にある **[コンピューター/グループの追加]** リンクをクリックします。
1. **[コンピューターの検索]** ダイアログ ボックスでは、Operations Manager で監視するコンピューターまたはグループを検索できます。 Log Analytics にオンボードするコンピューターまたはグループを選択し､**追加** をクリックして､**OK** をクリックします｡

オペレーション コンソールの **[管理]** ワークスペースにある [Operations Management Suite] の下に、マネージド コンピューター ノードからデータを収集するように構成されたコンピューターとグループが表示されます。  ここから、必要に応じて、コンピューターおよびグループの追加または削除ができます。

### <a name="configure-proxy-settings-in-the-operations-console"></a>Operations コンソールでプロキシの設定をする
管理グループと Log Analytics サービスの間に内部プロキシサーバーがある場合は､以下の手順を実行します｡  これらの設定は管理グループから一元的に管理され､Log Analytics 用のデータ収集対象スコープに含まれるエージェント管理システムに配信されます｡  これは､いくつかのソリューションで管理サーバーがバイパスされていて､データがサービスに直接送信される場合に有用です｡

1. Operations Manager コンソールを開き、 **[Administration (管理)]** ワークスペースを選択します。
1. [Operations Management Suite] を展開し、 **[接続]** をクリックします。
1. [OMS の接続] ビューで、 **[プロキシ サーバーの構成]** をクリックします。
1. **[Operations Management Suite 設定ウィザード: プロキシ サーバー]** ページで **[Operations Management Suite へのアクセスにプロキシ サーバーを使用する]** を選択して、ポート番号と URL を入力し (例: http://corpproxy:80 )、**[完了]** をクリックします。

プロキシ サーバーで認証が必要な場合は、次の手順を実行して、管理グループ内の OMS への報告を行うマネージド コンピューターに伝達される必要がある設定と資格情報を構成します。

1. Operations Manager コンソールを開き、 **[Administration (管理)]** ワークスペースを選択します。
1. **[RunAs Configuration (RunAs の構成)]** で **[Profiles (プロファイル)]** を選択します。
1. **System Center Advisor Run As Profile Proxy** というプロファイルを開きます。
1. 実行プロファイル ウィザードで [追加] をクリックし、実行アカウントを使用します。 [実行アカウント](https://technet.microsoft.com/library/hh321655.aspx) を作成することも、既存のアカウントを使用することもできます。 このアカウントには、プロキシ サーバーを通過するための十分な権限を持たせる必要があります。
1. 管理するアカウントを設定するには、**[選択したクラス、グループ、またはオブジェクト]** を選択し、**[選択...]** をクリックします。 次に、**[グループ...]** をクリックし、**[グループの検索]** ボックス開きます。
1. **Microsoft System Center Advisor Monitoring Server Group**を検索して選択します。  グループを選択したら、**[OK]** をクリックして、**[グループ検索]** ボックスを閉じます。
1. **[OK]** をクリックして、**[実行アカウントの追加]** ボックスを閉じます。
1. **[保存]** をクリックして、ウィザードを完了し、変更を保存します。

接続が作成されてから､データを収集して Log Analytics にデータを報告するエージェントを設定すると､管理グループで以下の設定が適用されます (ただし､必ずしもこの記載順に設定されるわけではありません)｡

* 実行アカウント **Microsoft.SystemCenter.Advisor.RunAsAccount.Certificate** が作成されます。  これは、実行プロファイル "**Microsoft System Center Advisor Run As Profile Blob**" に関連付けられ、**Collection Server** および **Operations Manager Management Group** という 2 つのクラスをターゲットにします。
* 2 つのコネクタが作成されます。  最初のコネクタは  **Microsoft.SystemCenter.Advisor.DataConnector** という名前であり､管理グループの全クラスのインスタンスから生成されたすべてのアラートを Log Analytics に転送するサブスクリプションを使って自動的に設定されます｡ 2 番目のコネクタは **Advisor Connector**であり、OMS Web サービスとの通信およびデータの共有を担当します。
* 管理グループ内でデータを収集するために選択したエージェントとグループは、**Microsoft System Center Advisor Monitoring Server Group** に追加されます。

## <a name="management-pack-updates"></a>管理パックの更新
設定が完了すると､Operations Manager 管理グループによって Log Analytics サービスとの接続が確立されます｡  管理サーバーはまず Web サービスと同期し、次に有効にされたソリューションで Operations Manager と統合されるソリューションに対応する管理パックの形式で最新の構成情報を受信します。   Operations Manager はこれらの管理パックの更新を確認し、更新がある場合はそれらを自動的にダウンロードしてインポートします。  この動作は特に 2 つのルールで制御されます。

* **Microsoft.SystemCenter.Advisor.MPUpdate** - ベース Log Analytics 管理パックを更新します｡ 既定では 12 時間おきに実行されます。
* **Microsoft.SystemCenter.Advisor.Core.GetIntelligencePacksRule** - ワークスペースで有効にされたソリューション管理パックを更新します。 既定では 5 分おきに実行されます。

この 2 つのルールはオーバーライドすることができます。具体的には、2 つのルールを無効にして自動ダウンロードを防止することも、新しい管理パックの有無とダウンロードの必要性を判断するために行う管理サーバーと OMS の同期の頻度を変更することもできます。  秒単位の値で **Frequency** パラメーターを変更して同期スケジュールに変更を加える場合、または **Enabled** パラメーターを変更してルールを無効にする場合は、「[How to Override a Rule or Monitor (ルールまたはモニターをオーバーライドする方法)](https://technet.microsoft.com/library/hh212869.aspx)」の手順に従ってください。  Operations Manager Management Group クラスのすべてのオブジェクトに対するオーバーライドを対象としています。

引き続き既存の変更管理プロセスに従って運用管理グループにおける管理パックのリリースを制御する場合は、ルールを無効にし、更新が許可されている特定の期間中にルールを有効にすることができます。 環境内に開発または QA 管理グループがあり､その管理グループがインターネットと接続できる場合は､Log Analytics ワークスペースを使ってその管理グループがそのシナリオをサポートするように設定できます｡  そうすることによって､Log Analytics 管理パックを生産管理グループにリリースする前に､その都度､管理パックをレビューし､評価することができます｡

## <a name="switch-an-operations-manager-group-to-a-new-log-analytics-workspace"></a>Operations Manager グループを新しい Log Analytics ワークスペースに切り替える
1. Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にサインインします。
1. Azure ポータルで、左下隅にある **[その他のサービス]** をクリックします。 リソースの一覧で、「**Log Analytics**」と入力します。 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 **Log Analytics** を選択して､ワークスペースを作成します｡  
1. Operations Manager 管理者ロールのメンバーであるアカウントを使用して Operations Manager コンソールを開き、 **[管理]** ワークスペースを選択します。
1. [Operations Management Suite] を展開し、 **[接続]** を選択します。
1. ウィンドウの中央にある **[Operation Management Suite の再構成]** リンクを選択します。
1. **Operations Management Suite Onboarding Wizard** に従って､新しい Log Analytics ワークスペースに関連付けられている管理者アカウントの電子メール アドレスか電話番号とパスワードを入力します｡
   
   > [!NOTE]
   > **[Operations Management Suite オンボード ウィザード: ワークスペースの選択]** ページに、使用されている既存のワークスペースが表示されます。
   > 
   > 

## <a name="validate-operations-manager-integration-with-log-analytics"></a>Log Analytics との Operations Manager の統合を検証する
Operations Manager への Log Analytics の統合の成功を確認する方法はいくつかあります｡

### <a name="to-confirm-integration-from-the-azure-portal"></a>Azure ポータルから統合を確認する
1. Azure ポータルで、左下隅にある **[その他のサービス]** をクリックします。 リソースの一覧で、「**Log Analytics**」と入力します。 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。
1. Log Analytics ワークスペースの一覧で該当するワークスペースを選択します｡  
1. **Advanced settings** を選択し､**Connected Sources** を選択してから､**System Center** を選択します｡ 
1. [System Center Operations Manager] セクションの表に、管理グループの名前と共に、エージェントの数、データを最後に受信したときの状態が一覧表示されます。
   
   ![oms-settings-connectedsources](./media/log-analytics-om-agents/oms-settings-connectedsources.png)

### <a name="to-confirm-integration-from-the-operations-console"></a>オペレーション コンソールから統合を確認するには
1. Operations Manager コンソールを開き、 **[Administration (管理)]** ワークスペースを選択します。
1. **[管理パック]** を選択し、**[Look for (検索する文字列)]** ボックスに「**Advisor**」または「**Intelligence**」と入力します。
1. 有効にしたソリューションによって、対応する管理パックが検索結果に表示されます。  たとえば、アラート管理ソリューションを有効にした場合、管理パック Microsoft System Center Advisor Alert Management がリストに表示されます。
1. **[監視]** ビューで、**[Operations Management Suite\Health State (Operations Management Suite\ヘルス状態)]** ビューに移動します。  **Management Server State** の部分から管理サーバーを選択し､**Detail View** の部分で､**Authentication service URI** プロパティの値が Log Analytics ワークスペース ID に一致していることを確認します｡
   
   ![oms-opsmgr-mg-authsvcuri-property-ms](./media/log-analytics-om-agents/oms-opsmgr-mg-authsvcuri-property-ms.png)

## <a name="remove-integration-with-log-analytics"></a>Log Analytics との統合を削除する
Operations Manager 管理グループと Log Analytics ワークスペースとの統合が不要になった場合､管理グループから接続と設定を正しく削除するには､いくつか手順を踏む必要があります｡ 以下の手順により､管理グループの参照を削除することで Log Analytics ワークスペースを更新し､Log Analytics コネクタを削除して､サービスとの統合をサポートしている管理パックを削除することができます｡   

Operations Manager との統合のために有効にされたソリューションに対する管理パック､および Log Analytics サービスとの統合をサポートするために必要な管理パックを､管理グループから簡単に削除することはできません｡  これは､Log Analytics 管理パックに他の関連する管理パックと依存関係を持つものがあるためです｡  他の管理パックに依存している管理パックを削除するには、TechNet スクリプト センターから、スクリプト[「依存関係を持つ管理パックを削除する」](https://gallery.technet.microsoft.com/scriptcenter/Script-to-remove-a-84f6873e)をダウンロードします。  

1. Operations Manager 管理者ロールのメンバーであるアカウントを使用して Operations Manager コマンド シェルを開きます。
   
    > [!WARNING]
    > 続行する前に、Advisor または IntelligencePack という語句を名前に含むカスタム管理パックが存在しないことを確認します。これを確認しないまま次の手順を実行すると、これらは管理グループから削除されます。
    > 

1. コマンド シェル プロンプトで、「 `Get-SCOMManagementPack -name "*Advisor*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. 次に、「 `Get-SCOMManagementPack -name “*IntelligencePack*” | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. 他の System Center Advisor 管理パックに依存している残りの管理パックを削除するには、TechNet スクリプト センターからダウンロードしたスクリプト *RecursiveRemove.ps1* を使用します。  
 
    > [!NOTE]
    > PowerShell を使用して Advisor 管理パックを削除する手順では、Microsoft System Center Advisor または Microsoft System Center Advisor の内部管理パックは自動的に削除されません。  これらの管理パックは削除しないでください。  
    >  

1. Operations Manager 管理者ロールのメンバーであるアカウントを使用して Operations Manager オペレーション コンソールを開きます。
1. **[管理]** で **[管理パック]** ノードを選択し、**[Look for (検索する文字列)]** ボックスに「**Advisor**」と入力して、次の管理パックが管理グループにインポートされた状態になっていることを確認します。
   
   * Microsoft System Center Advisor
   * Microsoft System Center Advisor Internal

1. OMS ポータルで、**[設定]** タイルをクリックします。
1. **[Connected Sources]**(接続されているソース) を選択します。
1. [System Center Operations Manager] セクションの下の表に、ワークスペースから削除する管理グループの名前が表示されます。  **[Last Data (最後のデータ)]** 列で、**[削除]** をクリックします。  
   
    > [!NOTE]
    > **[削除]** リンクは、接続された管理グループからアクティビティが検出されない場合、14 日後までは使用できません。  
    > 

1. 削除操作を続行するかどうかを確認するためのウィンドウが表示されます。  **[はい]** をクリックして続行します。 

Microsoft.SystemCenter.Advisor.DataConnector と Advisor Connector の 2 つのコネクタを削除するには、次の PowerShell スクリプトを自分のコンピューターに保存した後、次の例に従って実行します。

```
    .\OM2012_DeleteConnectors.ps1 “Advisor Connector” <ManagementServerName>
    .\OM2012_DeleteConnectors.ps1 “Microsoft.SytemCenter.Advisor.DataConnector” <ManagementServerName>
```

> [!NOTE]
> このスクリプトを実行するコンピューターが管理サーバーでない場合、そのコンピューターには、管理グループのバージョンに応じた Operations Manager のコマンド シェルがインストールされている必要があります。
> 
> 

```
    param(
    [String] $connectorName,
    [String] $msName="localhost"
    )
    $mg = new-object Microsoft.EnterpriseManagement.ManagementGroup $msName
    $admin = $mg.GetConnectorFrameworkAdministration()
    ##########################################################################################
    # Configures a connector with the specified name.
    ##########################################################################################
    function New-Connector([String] $name)
    {
         $connectorForTest = $null;
         foreach($connector in $admin.GetMonitoringConnectors())
    {
    if($connectorName.Name -eq ${name})
    {
         $connectorForTest = Get-SCOMConnector -id $connector.id
    }
    }
    if ($connectorForTest -eq $null)
    {
         $testConnector = New-Object Microsoft.EnterpriseManagement.ConnectorFramework.ConnectorInfo
         $testConnector.Name = $name
         $testConnector.Description = "${name} Description"
         $testConnector.DiscoveryDataIsManaged = $false
         $connectorForTest = $admin.Setup($testConnector)
         $connectorForTest.Initialize();
    }
    return $connectorForTest
    }
    ##########################################################################################
    # Removes a connector with the specified name.
    ##########################################################################################
    function Remove-Connector([String] $name)
    {
        $testConnector = $null
        foreach($connector in $admin.GetMonitoringConnectors())
       {
        if($connector.Name -eq ${name})
       {
         $testConnector = Get-SCOMConnector -id $connector.id
       }
      }
     if ($testConnector -ne $null)
     {
        if($testConnector.Initialized)
     {
     foreach($alert in $testConnector.GetMonitoringAlerts())
     {
       $alert.ConnectorId = $null;
       $alert.Update("Delete Connector");
     }
     $testConnector.Uninitialize()
     }
     $connectorIdForTest = $admin.Cleanup($testConnector)
     }
    }
    ##########################################################################################
    # Delete a connector's Subscription
    ##########################################################################################
    function Delete-Subscription([String] $name)
    {
      foreach($testconnector in $admin.GetMonitoringConnectors())
      {
      if($testconnector.Name -eq $name)
      {
        $connector = Get-SCOMConnector -id $testconnector.id
      }
    }
    $subs = $admin.GetConnectorSubscriptions()
    foreach($sub in $subs)
    {
      if($sub.MonitoringConnectorId -eq $connector.id)
      {
        $admin.DeleteConnectorSubscription($admin.GetConnectorSubscription($sub.Id))
      }
     }
    }
    #New-Connector $connectorName
    write-host "Delete-Subscription"
    Delete-Subscription $connectorName
    write-host "Remove-Connector"
    Remove-Connector $connectorName
```

将来､管理グループを Log Analytics ワークスペースに再び接続する予定がある場合は､`Microsoft.SystemCenter.Advisor.Resources.\<Language>\.mpb` 管理パックファイルをインポートし直す必要があります｡  環境にデプロイされている System Center Operations Manager のバージョンによって異なりますが､このファイルは以下の場所にあります｡

* System Center 2016 - Operations Manager 以降の場合､ソース メディアの `\ManagementPacks` フォルダー｡
* 管理グループへの最新の更新プログラムのロールアップの適用以降｡  Operations Manager 2012 の場合､ソース フォルダーは ` %ProgramFiles%\Microsoft System Center 2012\Operations Manager\Server\Management Packs for Update Rollups` であり､2012 R2 の場合は `System Center 2012 R2\Operations Manager\Server\Management Packs for Update Rollups` にあります｡

## <a name="next-steps"></a>次の手順
機能を追加し、データを収集するには、[ソリューション ギャラリーから Log Analytics ソリューションを追加する](log-analytics-add-solutions.md)方法に関するページを参照してください。


