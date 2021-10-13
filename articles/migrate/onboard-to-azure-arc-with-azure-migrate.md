---
title: VMware 仮想環境内のオンプレミス サーバーを Azure Arc にオンボードする
description: VMware 仮想環境内のオンプレミス サーバーを Azure Arc にオンボードする
author: deseelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: how-to
ms.date: 04/27/2021
ms.openlocfilehash: 83bee24ca8da03f6a6896bb47cc298860f941f4c
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2021
ms.locfileid: "129711979"
---
# <a name="onboard-on-premises-servers-in-vmware-virtual-environment-to-azure-arc"></a>VMware 仮想環境内のオンプレミス サーバーを Azure Arc にオンボードする   

この記事では、Azure Migrate: 検出および評価ツールを使用して、オンプレミスの VMware VM を Azure 管理用の Azure Arc にオンボードする方法について説明します。 

Azure Arc を使用すると、移行の対象として最適ではないオンプレミス サーバーに Azure 管理エクスペリエンスを拡張することで、ハイブリッド IT 資産を 1 つのウィンドウで管理できます。 [Azure Arc の詳細情報](../azure-arc/servers/overview.md)を参照してください。 

## <a name="before-you-get-started"></a>開始する前に

- Azure Migrate: 検出および評価ツールを使用して VMware 環境で実行されているサーバーを検出するための[要件を確認します](./tutorial-discover-vmware.md#prerequisites)。  
- 使用する [VMware vCenter](./tutorial-discover-vmware.md#prepare-vmware) を準備し、ソフトウェア インベントリを実行するための [VMware の要件](migrate-support-matrix-vmware.md#vmware-requirements)を確認します。 検出されたサーバーの Azure Arc へのオンボードを開始するには、ソフトウェア インベントリが完了している必要があります。   
- サーバー上でソフトウェア インベントリを開始する前に、[アプリケーションの検出要件](migrate-support-matrix-vmware.md#software-inventory-requirements)を確認します。 Windows サーバーには、PowerShell バージョン 3.0 以降がインストールされている必要があります。 
- 検出されたサーバーのインベントリへのリモート接続を許可して、Azure Arc にオンボードするための前提条件を確認します。 
    1. 検出されたサーバーへの受信リモート接続を許可します 
        - _Windows の場合:_ WinRM ポート 5985 (HTTP) 上の受信接続。 すべてのターゲット Windows サーバー上で "winrm qc" コマンドを実行して、ローカル コンピューター上の WS-Management プロトコルを有効にします。 
        - _Linux の場合:_ すべてのターゲット Linux サーバー上で、ポート 22 (SSH) 上の受信接続を許可します。
        - リモート マシン (検出されたサーバー) の IP アドレスを、アプライアンス上の WinRM TrustedHosts リストに追加することもできます。 
    2. Azure Migrate アプライアンスは、ターゲット サーバーへのネットワーク通信経路が必要です。 
- [Azure Arc の前提条件](../azure-arc/servers/agent-overview.md#prerequisites)を確認し、次の考慮事項を確認してください。
    - Azure Arc へのオンボードは、vCenter Server の検出とソフトウェア インベントリの完了後にのみ開始できます。 ソフトウェア インベントリをオンにした後、完了するまでに最大 6 時間かかる場合があります。
    -  Arc オンボード プロセス中に、検出されたサーバーに [Azure Arc ハイブリッド接続マシン エージェント](../azure-arc/servers/learn/quick-enable-hybrid-vm.md)がインストールされます。 エージェントをインストールして構成するために、サーバーで管理者権限を持つ資格情報を提供してください。 Linux では root アカウントを提供し、Windows ではローカルの Administrators グループのメンバー アカウントを提供します。 
    - サーバーで、[サポートされているオペレーティング システム](../azure-arc/servers/agent-overview.md#supported-operating-systems)が実行中であることを確認します。
    - [必要な Azure ロール](../azure-arc/servers/agent-overview.md#required-permissions)への割り当てが、Azure アカウントに付与されていることを確認します。
    - 検出されたサーバーがファイアウォールまたはプロキシ サーバーを介してインターネット経由で通信する場合、[必要な URL](../azure-arc/servers/agent-overview.md#networking-configuration) がブロックされていないことを確認します。
    - Azure Arc が[サポートされているリージョン](../azure-arc/servers/overview.md#supported-regions)を確認します。 
    - Azure Arc 対応サーバーでは、1 つのリソース グループで最大 5,000 個のマシン インスタンスがサポートされます。


## <a name="set-up-the-azure-migrate-project"></a>Azure Migrate プロジェクトを設定する  

1. 開始する前に、[Azure ユーザー アカウント](./tutorial-discover-vmware.md#prepare-an-azure-user-account)を準備し、Azure Migrate に必要なリソースを作成するためにサブスクリプション内に[必要なロール](./create-manage-projects.md#verify-permissions)があることを確認します。 
2. [この記事を使用して](./create-manage-projects.md)、Azure Migrate: 検出と評価ツールが追加された新しい Azure Migrate プロジェクトを設定します。  

    > [!Note]
    > また、既存の Migrate プロジェクトを使用し、検出されたサーバーのインベントリを Azure Arc にオンボードすることもできます。これを行うには、アプライアンス サーバーからアプライアンス構成マネージャーを起動し、サービスが最新バージョンに更新されていることを確認します。 [詳細情報](./migrate-appliance.md#appliance-upgrades) <br/> <br/> 次に、[この手順に従って](#onboard-to-azure-arc)サーバーをオンボードします。  

## <a name="deploy-and-register-the-azure-migrate-appliance"></a>Azure Migrate アプライアンスをデプロイおよび登録する 

Azure Migrate の検出および評価では、軽量の Azure Migrate アプライアンスを使用します。 このアプライアンスによって、サーバー検出が実行され、サーバーの構成とパフォーマンス メタデータが Azure Migrate に送信されます。 

アプライアンスを設定する前に、次の手順を実行します。 

1. Azure Migrate アプライアンスを展開するための要件を[確認](migrate-appliance.md#appliance---vmware)します。
2. アプライアンスが[パブリック](migrate-appliance.md#public-cloud-urls)および [Government クラウド](migrate-appliance.md#government-cloud-urls)でアクセスする必要がある Azure の URL について確認します。 
3. アプライアンスの[ポート アクセス要件](migrate-support-matrix-vmware.md#port-access-requirements)に注意します。 


次に、

- [Azure Migrate アプライアンスを設定する](./tutorial-discover-vmware.md#set-up-the-appliance)ための記事に従い、vCenter Server の検出を開始します。 アプライアンスをデプロイするには、OVA テンプレートをダウンロードして VMware にインポートし、vCenter Server で実行されるサーバーを作成します。  
- アプライアンスをデプロイした後、検出を開始する前に、プロジェクトに登録する必要があります。 アプライアンスを登録するには、[こちらの手順](./tutorial-discover-vmware.md#register-the-appliance-with-azure-migrate)に従います。 

## <a name="configure-the-appliance-and-start-discovery"></a>アプライアンスを構成して検出を開始する  

[こちらの記事](./tutorial-discover-vmware.md#start-continuous-discovery)を使用して Azure Migrate アプライアンスを構成し、vCenter Server の検出を開始します。 

検出のためにアプライアンスを構成するときに、アプライアンス構成マネージャーで詳細を指定する必要があります。

- 接続先の vCenter Server の詳細。  
- VMware 環境内のサーバーを検出する場合に対象となる vCenter Server 資格情報。 
- 管理者権限のあるサーバー資格情報。 Azure Arc へのオンボードでは、Azure Arc ハイブリッド接続マシン エージェントをインストールおよび構成するために、サーバーに対する管理者権限のある資格情報が必要です。 資格情報の指定方法とそれらの処理方法の詳細については、[こちら](add-server-credentials.md)を参照してください。

検出が正常に完了した後、検出されたサーバーがポータルに表示されるまでに約 15 分かかります。


## <a name="onboard-to-azure-arc"></a>Azure Arc にオンボードする

>[!Important]
>検出されたサーバーを Azure Arc にオンボードする前に、ソフトウェア インベントリが完了している必要があります。 

vCenter Server の検出が完了すると、ソフトウェア インベントリ (インストールされているアプリケーションの検出) が自動的に開始されます。 ソフトウェア インベントリ中は、指定されたサーバー資格情報が、検出されたサーバーに対して繰り返し検証されます。 ソフトウェア インベントリが完了し、資格情報がマップされた後にオンボードを開始できます。 ソフトウェア インベントリをオンにした後、完了するまでに最大 6 時間かかる場合があります。  
1. **[Azure Arc へのオンボード]** パネルに移動します。 

    ![Arc のオンボード](./media/onboard-to-azure-arc-with-azure-migrate/azure-arc-onboarding-panel-after-being-enabled.png)

2. Azure 内でサーバーを管理する **サブスクリプション** と **リソース グループ** を指定します。

3. **[リージョン]** ドロップダウンの一覧で、サーバーのメタデータを格納する Azure リージョンを選択します。

4. 大規模なオンボードの場合は **Azure Active Directory サービス プリンシパル** の詳細を指定します。 [Azure portal または Azure PowerShell を使用してサービス プリンシパルを作成する](../azure-arc/servers/onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale)ためのこちらの記事を参照してください。 <br/>

    次の入力を行う必要があります。
    - **ディレクトリ (テナント) ID** - Azure AD の専用インスタンスを表す [一意識別子 (GUID)](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)。 
    - **アプリケーション (クライアント) ID** - サービス プリンシパルのアプリケーション ID を表す [一意識別子 (GUID)](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)。
    - **サービス プリンシパルのシークレット (アプリケーション シークレット)** - パスワードベースの認証用の [クライアント シークレット](../active-directory/develop/howto-create-service-principal-portal.md#option-2-create-a-new-application-secret)。 
    
5. _省略可能_: 検出されたサーバーがインターネットに接続するためにプロキシ サーバーを必要とする場合は、**プロキシ サーバーの IP アドレス** または名前と **ポート番号** を指定します。 `http://<proxyURL>:<proxyport>` の形式で値を入力します。 検出されたサーバーによって使用されるこのプロキシ サーバーは、(アプライアンス構成マネージャーの前提条件に関するセクションで指定された) インターネットに接続するためにアプライアンス サーバーが必要とするプロキシ サーバーとは異なってもかまいません。   

    > [!Note]
    > プロキシ認証の設定はサポートされていません。 

6. **[オンボーディングの開始]** を選択して、Azure Arc のオンボード プロセスを開始します。 オンボード プロセスには少し時間がかかります。 オンボードが完了すると、完了ステータスと詳細なオンボード状態レポートが表示されます。    

    ![Arc オンボード レポート](./media/onboard-to-azure-arc-with-azure-migrate/onboarding-completion-report.png)

    > [!Note]
    > ログインの有効期限が切れた場合は、 **[もう一度ログインしてください]** を選択します。 これにより、デバイス コードを含むモーダルが開きます。 **[Copy code & Login]\(コードのコピーとログイン\)** を選択してデバイス コードをコピーし、新しいブラウザー タブで Azure ログイン プロンプトを開きます。表示されない場合は、ブラウザーでポップアップ ブロックを無効にしてあることを確認します。

7. オンボードが完了したら、[Azure Arc ホーム ページ](https://portal.azure.com/#blade/Microsoft_Azure_HybridCompute/AzureArcCenterBlade/servers)に移動して、オンボード サーバーを表示および管理します。   

8. 詳細なオンボード レポートを表示して、サーバーのオンボードの状態を把握し、適切なアクションを実行します。 

> [!Note]
> Azure Migrate アプライアンスでは、Azure Arc のオンボード スクリプトを実行するために WinRM セッションが開始されます。 ターゲット サーバー上の WinRM サービスへのアクセスを制限する設定がないことを確認します。    


## <a name="next-steps"></a>次のステップ 

- 詳細情報とエラーの詳細については、詳細なオンボード レポートを確認してください。 エラーがあれば解決して、サーバーのオンボードを正常に行います。 
- **[オンボーディングの開始]** を選択して、Azure Arc のオンボード プロセスを再実行することができます。 新しく検出されたサーバーと、前回の実行で正常にオンボードできなかったサーバーに対して、オンボードが試行されます。   

## <a name="troubleshooting-azure-arc-onboarding-errors"></a>Azure Arc のオンボード エラーのトラブルシューティング

Azure Migrate アプライアンスを使用して Azure Arc へのオンボード中にエラーが発生した場合は、次のセクションを参考にして、考えられる原因と、問題を解決するための推奨される手順を確認してください。 

以下のエラー コードが表示されない場合、またはエラーコードが **_AZCM_** で始まる場合は、[Azure Arc のトラブルシューティングのためのこちらのガイド](../azure-arc/servers/troubleshoot-agent-onboard.md)を参照してください。

### <a name="error-60001---unabletoconnecttophysicalserver"></a>エラー 60001 - UnableToConnectToPhysicalServer  

**考えられる原因**  
サーバーに接続するための[前提条件](./migrate-support-matrix-physical.md)が満たされていないか、サーバーへの接続でプロキシ設定などのネットワークの問題が発生しています。

**推奨アクション**   
- サーバーが[前提条件](#before-you-get-started)と[ポート アクセス要件](./migrate-support-matrix-physical.md)を満たしていることを確認します。 
- リモート マシン (検出されたサーバー) の IP アドレスを、Azure Migrate アプライアンス上の WinRM TrustedHosts リストに追加し、操作を再試行します。 これは、サーバー上のリモート受信接続を許可するためのものです - _Windows:_ WinRM ポート 5985 (HTTP) と _Linux:_ SSH ポート 22 (TCP)
- アプライアンスでサーバーに接続するための正しい認証方法が選択されていることを確認します。 
   > [!Note] 
   > Azure Migrate では、Linux サーバーに対するパスワードベースと SSH キーベースの両方の認証がサポートされています。
- 問題が引き続き発生する場合は、Microsoft サポート ケースを提出してください。その際、アプライアンス マシン ID (アプライアンス構成マネージャーのフッターにあります) を指定してください。     
   

### <a name="error-60002---invalidservercredentials"></a>エラー 60002 - InvalidServerCredentials  

**考えられる原因**  
サーバーに接続できません。 アプライアンスで間違った資格情報が指定されているか、以前に指定した資格情報の有効期限が切れています。

**推奨アクション**  
- アプライアンスにサーバーの正しい資格情報が入力されていることを確認します。 これを確認するには、これらの資格情報を使用してサーバーへの接続を試行します。
- 追加された資格情報が正しくないか、有効期限が切れている場合は、アプライアンスの資格情報を編集し、追加されたサーバーを再検証します。 検証に成功した場合、問題は解決されています。
- 問題が引き続き発生する場合は、Microsoft サポート ケースを提出してください。その際、アプライアンス マシン ID (アプライアンス構成マネージャーのフッターにあります) を指定してください。

### <a name="error-60005---sshoperationtimeout"></a>エラー 60005 - SSHOperationTimeout  

**考えられる原因**  
- ネットワーク待機時間の問題か、サーバー上の最新の更新プログラムがないため、操作に予想以上の時間がかかっています。 

**推奨アクション**  
- 影響を受けるサーバーに最新のカーネルと OS の更新プログラムがインストールされていることを確認します。
- アプライアンスとサーバーの間にネットワーク待機時間がないことを確認します。 待機時間の問題を回避するために、アプライアンスとソース サーバーを同じドメインに配置することをお勧めします。
- 影響を受けるサーバーにアプライアンスから接続し、[こちら](./troubleshoot-appliance.md)に記載されているコマンドを実行して、null または空のデータが返されるかどうかを確認します。
- 問題が引き続き発生する場合は、Microsoft サポート ケースを提出してください。その際、アプライアンス マシン ID (アプライアンス構成マネージャーのフッターにあります) を指定してください。  

### <a name="error-60108---softwareinventorycredentialnotassociated"></a>エラー 60108 - SoftwareInventoryCredentialNotAssociated  

**考えられる原因**  
- サーバーに関連付けられている資格情報が見つかりませんでした。

**推奨アクション**  
- 検出されたサーバーの Azure Arc へのオンボードを開始するには、ソフトウェア インベントリが完了している必要があります。[詳細については、こちらを参照してください](./how-to-discover-applications.md#add-credentials-and-initiate-discovery)
- アプライアンス構成マネージャーで指定された資格情報が有効であること、および資格情報を使用してサーバーにアクセスできることを確認してください。
- アプライアンス構成マネージャーに戻り、別の資格情報のセットを指定するか、既存の資格情報を編集します。  

### <a name="error-60109---arcosnotsupported"></a>エラー 60109 - ArcOsNotSupported  

**考えられる原因**  
- サーバーは、Azure Arc のオンボードに対してサポートされていないオペレーティング システムをホストしています。

**推奨アクション**  
- Azure Arc に対して[サポートされているオペレーティング システムを確認してください](../azure-arc/servers/agent-overview.md#supported-operating-systems)。 
 
### <a name="error-10002---scriptexecutiontimedoutonvm"></a>エラー 10002 - ScriptExecutionTimedOutOnVm  

**考えられる原因**  
- オンボード タスクが時間内に完了しませんでした。 その後、実行に予想以上の時間がかかりました。 

**推奨アクション**  
- この問題は、しばらくすると自動的に解決されます。 引き続き問題が発生する場合は、Microsoft サポートにお問い合わせください。  
 
### <a name="error-50000---accessdenied"></a>エラー 50000 - AccessDenied 

**考えられる原因**  
- サーバーへのアクセスが許可されていないため、操作を完了できませんでした。 サーバーにアクセスするためにアプライアンスに提供されたユーザー アカウントに、必要なアクセス許可がないか、資格情報が正しくありません。 WinRM エラー コード: 0x80070005

**推奨アクション**  
- 考えられる原因を確認してから、操作をやり直してください。 問題が解決しない場合は、サポートにお問い合わせください。

### <a name="error-9609516000960078---nullresultunhandledexceptionserverunknownerrorunhandlederror"></a>エラー 960/951/60009/60078 - NullResult/UnhandledException/ServerUnknownError/UnhandledError

**考えられる原因**  
- 内部エラーのために操作が失敗しました。 

**推奨アクション**  
- しばらくしてから操作を再試行してください。 問題が引き続き発生する場合は、サポートに問い合わせてアプライアンス マシン ID (アプライアンス構成マネージャーの **フッター** にあります) を連絡してください。
