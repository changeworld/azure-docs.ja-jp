---
title: Windows コンピューターを Azure Log Analytics に接続する | Microsoft Docs
description: この記事では、Microsoft Monitoring Agent (MMA) を使用して、他のクラウドやオンプレミス内にホストされている Windows コンピューターを Log Analytics に接続する方法について説明します。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: 975b3d30f242b877512312f8a5a030bde9349287
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2018
ms.locfileid: "40003649"
---
# <a name="connect-windows-computers-to-the-log-analytics-service-in-azure"></a>Windows コンピューターを Azure の Log Analytics サービスに接続する

Log Analytics を使用して、ローカル データ センターやその他のクラウド環境内にある仮想マシンや物理コンピューターを監視、および管理するには、Microsoft Monitoring Agent (MMA) をデプロイし、1 つ以上の Log Analytics ワークスペースにレポートを送信するように構成する必要があります。  エージェントでは、Azure Automation 用の Hybrid Runbook Worker ロールもサポートされます。  

監視対象の Windows コンピューターでは、エージェントは Microsoft Monitoring Agent サービスとしてリストされます。 Microsoft Monitoring Agent サービスは、ログ ファイル、Windows イベント ログ、パフォーマンス データ、およびその他のテレメトリからイベントを収集します。 エージェントは、レポート送信先の Log Analytics サービスと通信できないときにも常時実行され、収集したデータを監視対象コンピューターのディスク キューに配置します。 接続が復元されると、Microsoft Monitoring Agent サービスは収集したデータをサービスに送信します。

エージェントは、次のいずれかの方法を使用してインストールされます。 多くの場合は、これらのメソッドを必要に応じて組み合わせて使用し、さまざまなコンピューターをインストールします。  各メソッドの使い方の詳細については、記事の後半で説明します。

* 手動インストール。 セットアップ ウィザード、コマンドライン、または既存のソフトウェア配布ツールを使用して、コンピューター上でセットアップを手動で実行します。
* Azure Automation Desired State Configuration (DSC)。 Azure Automation 内の DSC と、環境内に既にデプロイされている Windows コンピューター用のスクリプトを使用します。  
* PowerShell スクリプト。
* Azure Stack 内のオンプレミスで Windows を実行している仮想マシン用の、Resource Manager テンプレート。  

サポートされている構成を確認するには、「[サポートされている Windows オペレーティング システム](log-analytics-concept-hybrid.md#supported-windows-operating-systems)」と「[ネットワーク ファイアウォールの構成](log-analytics-concept-hybrid.md#network-firewall-requirements)」をご覧ください。

## <a name="obtain-workspace-id-and-key"></a>ワークスペース ID とキーを取得する
Microsoft Monitoring Agent for Windows をインストールする前に、Log Analytics ワークスペースのワークスペース ID とキーが必要です。  この情報は、各インストール方法を通じたセットアップ時に、エージェントを適切に構成し、そのエージェントが Azure の商用クラウドや米国政府機関向けクラウド内にある Log Analytics と正常に通信できるようにするために必要です。  

1. Azure Portal で、**[すべてのサービス]** をクリックします。 リソースの一覧で、「**Log Analytics**」と入力します。 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 **[Log Analytics]** を選択します。
2. Log Analytics ワークスペースの一覧で、エージェントのレポート送信先にするワークスペースを選択します。
3. **[詳細設定]** を選択します。<br><br> ![Log Analytics の詳細設定](media/log-analytics-quick-collect-azurevm/log-analytics-advanced-settings-01.png)<br><br>  
4. **[接続されたソース]**、**[Windows サーバー]** の順に選択します。   
5. **ワークスペース ID**と**主キー**をコピーして、好みのエディターに貼り付けます。    
   
## <a name="install-the-agent-using-setup-wizard"></a>セットアップ ウィザードを使用してエージェントをインストールする
次の手順では、コンピューター上の Microsoft Monitoring Agent 用のセットアップ ウィザードを使用して、Azure クラウドと Azure Government クラウド内にある Log Analytics 用のエージェントをインストールし、構成します。  

1. Log Analyics ワークスペースで、先の手順で移動した **[Windows サーバー]** ページの **[Windows エージェントのダウンロード]** から、Windows オペレーティング システムのプロセッサ アーキテクチャに応じた適切なバージョンを選択します。   
2. セットアップを実行して、コンピューターにエージェントをインストールします。
2. **[ようこそ]** ページで **[次へ]** をクリックします。
3. **[ライセンス条項]** ページの記述内容を確認し、**[同意する]** をクリックします。
4. **[インストール先フォルダー]** ページで、既定のインストール フォルダーを変更するか、変更せずに **[次へ]** をクリックします。
5. **[エージェントのセットアップ オプション]** ページで、エージェントを接続する Azure Log Analytics (OMS) をクリックし、**[次へ]** をクリックします。   
6. **[Azure Log Analytics]** ページで、次の手順を実行します。
   1. **[ワークスペース ID]** と **[ワークスペース キー (主キー)]** に、先ほどコピーした値を貼り付けます。  コンピューターが Azure Government クラウド内の Log Analytics ワークスペースに報告する必要がある場合は、**[Azure クラウド]** ドロップダウン リストから **[Azure US Government]** を選択します。  
   2. コンピューターがプロキシ サーバーを介して Log Analytics サービスと通信する必要がある場合は、**[詳細]** をクリックし、プロキシ サーバーの URL とポート番号を指定します。  プロキシ サーバーで認証が必要な場合には、プロキシ サーバーにアクセスするためのユーザー名とパスワードを入力し、**[次へ]** をクリックします。  
7. 必要な構成設定をしたら、**[次へ]** をクリックします。<br><br> ![ワークスペース ID と主キーの貼り付け](media/log-analytics-quick-collect-windows-computer/log-analytics-mma-setup-laworkspace.png)<br><br>
8. **[インストールの準備完了]** ページで、設定内容を確認し、**[インストール]** をクリックします。
9. **[構成は正常に終了しました]** ページで **[完了]** をクリックします。

完了すると、**コントロール パネル**に **Microsoft Monitoring Agent** が表示されます。 Log Analytics にレポートが送信されていることを確認する方法については、「[Log Analytics へのエージェント接続を確認する](#verify-agent-connectivity-to-log-analytics)」をご覧ください。 

## <a name="install-the-agent-using-the-command-line"></a>コマンド ラインを使用してエージェントをインストールする
ダウンロードしたエージェント ファイルは、自己完結型インストール パッケージです。  パッケージにはエージェントとサポート ファイルのセットアップ プログラムが含まれており、次の例に示すようにコマンドラインを使用して正しくインストールするためには、それらを抽出する必要があります。    

>[!NOTE]
>エージェントをアップグレードするには、Log Analytics スクリプト API を使用する必要があります。 詳しくは、「[Windows および Linux での Log Analytics エージェントの管理とメンテナンス](log-analytics-agent-manage.md)」をご覧ください。

次の表は、エージェントのセットアップ (Automation DSC を使用してデプロイする場合を含む) でサポートされる、Log Analytics パラメーターを示したものです。

|MMA 固有のオプション                   |メモ         |
|---------------------------------------|--------------|
| NOAPM=1                               | 省略可能なパラメーター。 .NET アプリケーション パフォーマンス監視なしでエージェントをインストールします。|   
|ADD_OPINSIGHTS_WORKSPACE               | 1 = ワークスペースに報告するようにエージェントを構成します                |
|OPINSIGHTS_WORKSPACE_ID                | 追加するワークスペースのワークスペース ID (guid)                    |
|OPINSIGHTS_WORKSPACE_KEY               | ワークスペースで最初に認証するために使用するワークスペース キー |
|OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE  | ワークスペースが配置されるクラウド環境を指定します <br> 0 = Azure 商用クラウド (既定値) <br> 1 = Azure Government |
|OPINSIGHTS_PROXY_URL               | プロキシで使用される URI |
|OPINSIGHTS_PROXY_USERNAME               | 認証済みのプロキシにアクセスするためのユーザー名 |
|OPINSIGHTS_PROXY_PASSWORD               | 認証済みのプロキシにアクセスするためのパスワード |

1. 管理者特権でのコマンド プロンプトからエージェント インストール ファイルを抽出するには、`MMASetup-<platform>.exe /c` を実行します。ファイルの抽出先のパスを指定するように求められます。  `MMASetup-<platform>.exe /c /t:<Path>` 引数を渡すことでパスを指定することもできます。  
2. エージェントをサイレント モードでインストールし、Azure の商用クラウド内のワークスペースにレポートを送信するように構成するには、セットアップ ファイルを抽出したフォルダーから、次のコマンドを入力します。 
   
     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=0 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1
    ```

   Azure の米国政府機関向けクラウドをエージェントのレポート送信先として構成するには、次のコマンドを入力します。 

     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1
    ```

## <a name="install-the-agent-using-dsc-in-azure-automation"></a>Azure Automation の DSC を使用してエージェントをインストールする

次のサンプル スクリプトを使用して、Azure Automation の DSC を使用するエージェントをインストールできます。   Automation アカウントがない場合は、「[Azure Automation の概要](../automation/automation-offering-get-started.md)」を参照して、Automation DSC を使用する前に必要な Automation アカウントを作成するための要件と手順を確認してください。  Automation DSC のことをよく知らない場合は、「[Azure Automation DSC の使用](../automation/automation-dsc-getting-started.md)」をご覧ください。

次のサンプルは、`URI` 値によって識別される 64 ビット エージェントをインストールします。 URI 値を置き換えることで、32 ビット バージョンを使用することもできます。 両方のバージョンの URI は次のとおりです。

- Windows 64 ビット エージェント - https://go.microsoft.com/fwlink/?LinkId=828603
- Windows 32 ビット エージェント - https://go.microsoft.com/fwlink/?LinkId=828604


>[!NOTE]
>この手順とサンプル スクリプトでは、Windows コンピューターに既にデプロイされているエージェントのアップグレードはサポートされていません。

32 ビット バージョンと 64 ビット バージョンのエージェント パッケージには、それぞれ異なる製品コードがあり、リリースされる新バージョンにも、それぞれ一意の値が指定されます。  製品コードは、アプリケーションや製品の主要な識別情報となる GUID であり、Windows インストーラーの **ProductCode** プロパティによって表されます。  **MMAgent.ps1** スクリプトの `ProductId value` は、32 ビットまたは 64 ビットのエージェント インストーラー パッケージの製品コードと一致する必要があります。

製品コードをエージェント インストール パッケージから直接取得するには、Windows ソフトウェア開発キットのコンポーネントである [Windows SDK Components for Windows Installer Developers (Windows インストーラー開発者向け Windows SDK コンポーネント)](https://msdn.microsoft.com/library/windows/desktop/aa370834%28v=vs.85%29.aspx) に含まれている、Orca.exe を使用するか、Microsoft Valuable Professional (MVP) によって記述された[サンプル スクリプト](http://www.scconfigmgr.com/2014/08/22/how-to-get-msi-file-information-with-powershell/)に従った、PowerShell を使用できます。  どちらの方法でも、まず、MMASetup インストール パッケージから **MOMagent.msi** ファイルを抽出する必要があります。  これについては、前のセクション「[コマンド ラインを使用してエージェントをインストールする](#install-the-agent-using-the-command-line)」の最初の手順で説明しています。  

1. [http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration](http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) から Azure Automation に xPSDesiredStateConfiguration DSC モジュールをインポートします。  
2.  *OPSINSIGHTS_WS_ID* と *OPSINSIGHTS_WS_KEY* に対して Azure Automation 変数アセットを作成します。 *OPSINSIGHTS_WS_ID* を Log Analytics ワークスペース ID に設定し、*OPSINSIGHTS_WS_KEY* をワークスペースの主キーに設定します。
3.  スクリプトをコピーし、MMAgent.ps1 として保存します

    ```PowerShell
    Configuration MMAgent
    {
        $OIPackageLocalPath = "C:\Deploy\MMASetup-AMD64.exe"
        $OPSINSIGHTS_WS_ID = Get-AutomationVariable -Name "OPSINSIGHTS_WS_ID"
        $OPSINSIGHTS_WS_KEY = Get-AutomationVariable -Name "OPSINSIGHTS_WS_KEY"

        Import-DscResource -ModuleName xPSDesiredStateConfiguration
        Import-DscResource -ModuleName PSDesiredStateConfiguration

        Node OMSnode {
            Service OIService
            {
                Name = "HealthService"
                State = "Running"
                DependsOn = "[Package]OI"
            }

            xRemoteFile OIPackage {
                Uri = "https://go.microsoft.com/fwlink/?LinkId=828603"
                DestinationPath = $OIPackageLocalPath
            }

            Package OI {
                Ensure = "Present"
                Path  = $OIPackageLocalPath
                Name = "Microsoft Monitoring Agent"
                ProductId = "8A7F2C51-4C7D-4BFD-9014-91D11F24AAE2"
                Arguments = '/C:"setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=' + $OPSINSIGHTS_WS_ID + ' OPINSIGHTS_WORKSPACE_KEY=' + $OPSINSIGHTS_WS_KEY + ' AcceptEndUserLicenseAgreement=1"'
                DependsOn = "[xRemoteFile]OIPackage"
            }
        }
    }

    ```

4. Automation アカウントに [MMAgent.ps1 構成スクリプトをインポート](../automation/automation-dsc-getting-started.md#importing-a-configuration-into-azure-automation)します。 
5. 構成に、[Windows コンピューターまたはノードに割り当て](../automation/automation-dsc-getting-started.md#onboarding-an-azure-vm-for-management-with-azure-automation-state-configuration)ます。 15 分以内に、ノードはその構成を確認し、エージェントがノードにプッシュされます。

## <a name="verify-agent-connectivity-to-log-analytics"></a>Log Analytics へのエージェント接続を確認する

エージェントのインストールが完了した後、エージェントが正常に接続され、レポートが送信されていることを確認するには、次の 2 つの方法があります。  

**[コントロール パネル]** 内のコンピューターから、**[Microsoft Monitoring Agent]** という項目を見つけます。  これを選択すると、**[Azure Log Analytics (OMS)]** タブに、**Microsoft Monitoring Agent が Microsoft Operations Management Suite サービスに正常に接続している**ことを示すメッセージが表示されます。<br><br> ![Log Analytics への MMA 接続の状態](media/log-analytics-quick-collect-windows-computer/log-analytics-mma-laworkspace-status.png)

また、Azure ポータルで簡単なログ検索を行うこともできます。  

1. Azure Portal で、**[すべてのサービス]** をクリックします。 リソースの一覧で、「**Log Analytics**」と入力します。 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 **[Log Analytics]** を選択します。  
2. Log Analytics ワークスペース ページで、ターゲット ワークスペースを選択して **[ログ検索]** タイルを選択します。 
2. [ログ検索] ウィンドウのクエリ フィールドで、次のように入力します。  

    ```
    search * 
    | where Type == "Heartbeat" 
    | where Category == "Direct Agent" 
    | where TimeGenerated > ago(30m)  
    ```

検索結果には、コンピューターのハートビート レコードが表示されます。これにより、エージェントがサービスに接続され、レポートが送信されていることが示されます。   

## <a name="next-steps"></a>次の手順

マシンへのデプロイ ライフ サイクル中にエージェントを管理する方法については、「[Windows および Linux での Log Analytics エージェントの管理とメンテナンス](log-analytics-agent-manage.md)」をご覧ください。  
