---
title: "Azure 実行アカウントの構成 | Microsoft Docs"
description: "Azure Automation のセキュリティ プリンシパル認証の作成、テスト、使用例をわかりやすく説明しています。"
services: automation
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: 
keywords: "サービス プリンシパル名, setspn, Azure の認証"
ms.assetid: 2f783441-15c7-4ea0-ba27-d7daa39b1dd3
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/10/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: 15cbf897f3f67b9d1bee0845b4d287fdabe63ba8
ms.lasthandoff: 03/11/2017


---
# <a name="authenticate-runbooks-with-azure-run-as-account"></a>Azure 実行アカウントを使用した Runbook の認証
このトピックでは、Azure Portal から実行アカウント機能を使用して Automation アカウントを構成し、Azure Resource Manager または Azure サービス管理のリソースを管理するための Runbook を認証する方法について説明します。

Azure Portal で Automation アカウントを作成すると、次のものが自動的に作成されます。

* Azure Active Directory のサービス プリンシパルと証明書を作成し、ロールベースのアクセス制御 (RBAC) の共同作成者ロールを割り当てる実行アカウント。この共同作成者ロールは、Runbook を使用した Resource Manager のリソースの管理に使用されます。   
* クラシック実行アカウント。Azure サービス管理リソースまたはクラシック リソースを Runbook で管理する際に使用する管理証明書をアップロードすることで作成されます。  

これによって必要な作業が単純化され、オートメーションのニーズを満たす Runbook をすぐに作成し、デプロイすることができます。      

実行アカウントとクラシック実行アカウントを使用すると、次のことができます。

* Azure ポータルでの Runbook を使用した Azure Resource Manager リソースまたは Azure サービス管理リソースの管理に、Azure を使用した標準的な認証方法を取り入れる。  
* Azure アラート内で構成されるグローバル Runbook の使用を自動化する。

> [!NOTE]
> Azure [アラート統合機能](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) と Automation Global Runbook の連携には、実行アカウントとクラシック実行アカウントを使って構成された Automation アカウントが必要です。 既に実行アカウントとクラシック実行アカウントが定義されている Automation アカウントを選んで利用できるほか、Automation アカウントを新たに作成することもできます。
>  

Azure Portal からの Automation アカウントの作成方法を紹介すると共に、PowerShell を使用して Automation アカウントを更新する方法、アカウント構成を管理する方法、Runbook 内で認証を行う方法について説明します。

次に進む前に、いくつかの注意事項と考慮事項があります。

1. これは、クラシック デプロイメント モデルまたは Resource Manager デプロイメント モデルで既に作成した既存の Automation アカウントには影響しません。  
2. これは、Azure ポータルで作成した Automation アカウントに対してのみ有効です。  クラシック ポータルからアカウントを作成しようとしても、実行アカウントの構成はレプリケートされません。
3. 現時点で、クラシック リソースを管理するために以前作成された Runbook と資産 (スケジュールや変数など) を所有しており、かつその Runbook を新しいクラシック実行アカウントで認証する場合は、実行アカウント管理を使用してクラシック実行アカウントを作成するか、次の PowerShell スクリプトを使用して既存のアカウントを更新する必要があります。  
4. 新しい実行アカウントまたはクラシック実行 Automation アカウントを使って認証を行うには、「[認証コードの例](#authentication-code-examples)」のセクションにあるサンプル コードを使用して既存の Runbook を編集する必要があります。  
   
    >[!NOTE] 
    >実行アカウントは、Resource Manager リソースに対する認証を証明書ベースのサービス プリンシパルを使って行う場合に使用します。これに対し、クラシック実行アカウントは、サービス管理リソースに対する認証を管理証明書を使って行う場合に使用します。     

## <a name="create-a-new-automation-account-from-the-azure-portal"></a>Azure Portal から新しい Automation アカウントを作成する
このセクションでは、以下の手順に従って、Azure Portal から新しい Azure Automation アカウントを作成します。  実行アカウントとクラシック実行アカウントの両方を作成します。  

> [!NOTE]
> この手順を実行するユーザーは、サービス管理者ロールのメンバーであるか、ユーザーにサブスクリプションへのアクセスを付与できるサブスクリプションの共同管理者である必要があります。 さらにこのユーザーは、そのサブスクリプションの既定の Active Directory にユーザーとして追加される必要があります。アカウントを特権ロールに割り当てる必要はありません。 サブスクリプションの共同管理者ロールに追加されるまでサブスクリプションの Active Directory のメンバーではなかったユーザーは、Active Directory にゲストとして追加され、作成するためのアクセス許可がないことを示す 警告が **[Add Automation Account (Automation アカウントの追加)]** ブレードに表示されます。 先に共同管理者ロールに追加されていたユーザーは、サブスクリプションの Active Directory から削除した後、Active Directory の完全なユーザーとして再度追加できます。 このような状況を検証するには、Azure Portal の **[Azure Active Directory]** ウィンドウで、**[ユーザーとグループ]**、**[すべてのユーザー]**、特定のユーザー、**[プロファイル]** の順に選択します。  ユーザーのプロファイルの下部にある **[ユーザー タイプ]** 属性の値は、**[ゲスト]** と一致しないようにする必要があります。  
> 

1. サブスクリプション管理ロールのメンバーかつサブスクリプションの共同管理者であるアカウントを使用して、Azure Portal にサインインします。
2. **[Automation アカウント]**を選択します。
3. [Automation アカウント] ブレードで **[追加]** をクリックします。<br>![[Automation アカウントの追加]](media/automation-sec-configure-azure-runas-account/create-automation-account-properties-b.png)
   
   > [!NOTE]
   > お使いのアカウントが、サブスクリプション管理ロールのメンバーではなく、サブスクリプションの共同管理者でもない場合、**[Add Automation Account (Automation アカウントの追加)]** ブレードに次の警告が表示されます。<br>![Add Automation Account Warning](media/automation-sec-configure-azure-runas-account/create-account-without-perms.png)
   > 
   > 
4. **[Automation アカウントの追加]** ブレードの **[名前]** ボックスに、新しい Automation アカウントの名前を入力します。
5. 複数のサブスクリプションがある場合は、新しいアカウントに対して&1; つのサブスクリプションを指定し、新規または既存の**リソース グループ**と、Azure データ センターの**場所**を指定します。
6. **[Azure 実行アカウントの作成]** オプションで **[はい]** が選択されていることを確認し、**[作成]** ボタンをクリックします。  
   
   > [!NOTE]
   > 実行アカウントを作成しなかった場合 (先ほどのオプションで **[いいえ]** を選択した場合)、**[Automation アカウントの追加]** ブレードに警告メッセージが表示されます。  Azure ポータルでアカウントを作成している間は、クラシック サブスクリプションまたは Resource Manager サブスクリプションのディレクトリ サービスには対応する認証 ID が割り当てられず、サブスクリプション内のリソースにアクセスすることはできません。  そのため、このアカウントを参照する Runbook は認証を通過できず、これらのデプロイメント モデルのリソースに対するタスクを実行することができません。
   > 
   > ![Add Automation Account Warning](media/automation-sec-configure-azure-runas-account/create-account-decline-create-runas-msg.png)<br>
   > サービス プリンシパルが作成されていない場合、Contributor ロールは割り当てられません。
   > 

7. Azure によって Automation アカウントが作成されている間、メニューの **[通知]** で進行状況を追跡できます。

### <a name="resources-included"></a>含まれるリソース
Automation アカウントが正常に作成されると、いくつかのリソースが自動的に作成されます。  実行アカウントのリソースを次の表に示します。<br>

| リソース | Description |
| --- | --- |
| AzureAutomationTutorial Runbook |実行アカウントを使用した認証の方法と、すべての Resource Manager リソースの取得方法を示す、サンプルのグラフィカルな Runbook。 |
| AzureAutomationTutorialScript Runbook |実行アカウントを使用した認証の方法と、すべての Resource Manager リソースの取得方法を示す、サンプルの PowerShell Runbook。 |
| AzureRunAsCertificate |Automation アカウントの作成時に自動的に作成される証明書資産、または既存のアカウント用に PowerShell スクリプト (後述) を使用して作成される証明書資産。  これにより、Azure を使用して認証を行い、Runbook から Azure Resource Manager リソースを管理できるようになります。  この証明書には、1 年の有効期間があります。 |
| AzureRunAsConnection |Automation アカウントの作成時または既存のアカウント用に下の PowerShell スクリプトを使用した場合に自動的に作成される、接続資産。 |

クラシック実行アカウントのリソースを次の表に示します。<br>

| リソース | Description |
| --- | --- |
| AzureClassicAutomationTutorial Runbook |クラシック実行アカウント (証明書) を使用してサブスクリプション内のすべてのクラシック VM を取得し、VM の名前と状態を出力するサンプルのグラフィカルな Runbook。 |
| AzureClassicAutomationTutorial Script Runbook |クラシック実行アカウント (証明書) を使用してサブスクリプション内のすべてのクラシック VM を取得し、VM の名前と状態を出力するサンプルの PowerShell Runbook。 |
| AzureClassicRunAsCertificate |Runbook から Azure のクラシック リソースを管理できるように、Azure を使用した認証に使用される、自動的に作成される証明書資産。  この証明書には、1 年の有効期間があります。 |
| AzureClassicRunAsConnection |Runbook から Azure のクラシック リソースを管理できるように、Azure を使用した認証に使用される、自動的に作成される接続資産。 |

## <a name="verify-run-as-authentication"></a>実行アカウントの認証を検証する
次に、新しい実行アカウントを使用して正しく認証できることを確認するために小さなテストを実施します。     

1. 先ほど作成した Automation アカウントを Azure Portal で開きます。  
2. **[Runbook]** タイルをクリックして、Runbook の一覧を開きます。
3. **AzureAutomationTutorialScript** Runbook を選択し、**[開始]** をクリックして、Runbook を開始します。  Runbook を開始することを確認するプロンプトが表示されます。
4. [Runbook ジョブ](automation-runbook-execution.md) が作成されると、[ジョブ] ブレードが表示され、ジョブの状態が **[ジョブの概要]** タイルに表示されます。  
5. 最初のジョブの状態は " *キュー登録済み* " であり、クラウドの Runbook ワーカーが使用できるようになるのを待っていることを示します。 その後、ワーカーがジョブを要求すると*開始中*になり、Runbook が実際に実行を開始すると*実行中*になります。  
6. Runbook ジョブが完了すると、状態は **[完了]** と表示されます。<br> ![Security Principal Runbook Test](media/automation-sec-configure-azure-runas-account/job-summary-automationtutorialscript.png)<br>
7. Runbook の詳細な結果を表示するには、 **[出力]** タイルをクリックします。
8. **[出力]** ブレードに、正常に認証され、リソース グループ内で使用可能なすべてのリソースの一覧が返されたことが示されます。
9. **[出力]** ブレードを閉じて、**[ジョブの概要]** ブレードに戻ります。
10. **[ジョブの概要]** と、対応する **[AzureAutomationTutorialScript]** Runbook ブレードを閉じます。

## <a name="verify-classic-run-as-authentication"></a>クラシック実行認証を検証する
次に、新しいクラシック実行アカウントを使用して正しく認証できることを確認するために小さなテストを実施します。     

1. 先ほど作成した Automation アカウントを Azure Portal で開きます。  
2. **[Runbook]** タイルをクリックして、Runbook の一覧を開きます。
3. **AzureClassicAutomationTutorialScript** Runbook を選択し、**[開始]** をクリックして、Runbook を開始します。  Runbook を開始することを確認するプロンプトが表示されます。
4. [Runbook ジョブ](automation-runbook-execution.md) が作成されると、[ジョブ] ブレードが表示され、ジョブの状態が **[ジョブの概要]** タイルに表示されます。  
5. 最初のジョブの状態は " *キュー登録済み* " であり、クラウドの Runbook ワーカーが使用できるようになるのを待っていることを示します。 その後、ワーカーがジョブを要求すると*開始中*になり、Runbook が実際に実行を開始すると*実行中*になります。  
6. Runbook ジョブが完了すると、状態は **[完了]** と表示されます。<br> ![Security Principal Runbook Test](media/automation-sec-configure-azure-runas-account/job-summary-automationclassictutorialscript.png)<br>
7. Runbook の詳細な結果を表示するには、 **[出力]** タイルをクリックします。
8. **[出力]** ブレードに、正常に認証され、サブスクリプション内のすべてのクラシック VM の一覧が返されたことが示されます。
9. **[出力]** ブレードを閉じて、**[ジョブの概要]** ブレードに戻ります。
10. **[ジョブの概要]** と、対応する **[AzureClassicAutomationTutorialScript]** Runbook ブレードを閉じます。

## <a name="managing-azure-run-as-account"></a>Azure 実行アカウントの管理
Automation アカウントの有効期間中、期限切れになる前に証明書を書き換える必要があります。また、アカウントが侵害されたと思われる場合は、実行アカウントを削除して再作成することができます。  このセクションでは、これを行う方法について説明します。  

### <a name="self-signed-certificate-renewal"></a>自己署名証明書の書き換え
Azure 実行アカウント用に作成された自己署名証明書は、作成日から&1; 年後の期限日までいつでも書き換えることができます。  証明書を書き換えるとき、古い有効な証明書は、キューに登録された、または実行中の Runbook が影響を受けないように保持されます。この Runbook は実行アカウントで認証が行われるためです。  証明書は、期限が切れるまで存在し続けます。    

> [!NOTE]
> エンタープライズ証明機関によって発行された証明書を使用するように Automation 実行アカウントを構成している場合、このオプションを使用すると、その証明書は自己署名証明書に置き換えられます。  

1. Azure Portal で Automation アカウントを開きます。  
2. [Automation アカウント] ブレードのアカウントのプロパティ ウィンドウで、**[アカウント設定]** セクションの **[実行アカウント]** をクリックします。<br><br> ![[Automation アカウント] プロパティ ウィンドウ](media/automation-sec-configure-azure-runas-account/automation-account-properties-pane.png)<br><br>
3. **[実行アカウント]** プロパティ ブレードで、証明書を更新する実行アカウントまたはクラシック実行アカウントを選択し、選択したアカウントのプロパティ ブレードで **[証明書の書き換え]** をクリックします。<br><br> ![実行アカウントの証明書を書き換える](media/automation-sec-configure-azure-runas-account/automation-account-renew-runas-certificate.png)<br><br> 続行するかどうかを確認するプロンプトが表示されます。  
4. 証明書が書き換えられている間、メニューの **[通知]** で進行状況を追跡できます。

### <a name="delete-run-as-account"></a>Azure 実行アカウントを削除する
次の手順では、Azure 実行アカウントまたはクラシック実行アカウントを削除して、再作成する方法について説明します。  この操作を実行するとき、Automation アカウントが保持されます。  削除した実行アカウントまたはクラシック実行アカウントは、ポータルで再作成できます。  

1. Azure Portal で Automation アカウントを開きます。  
2. [Automation アカウント] ブレードのアカウントのプロパティ ウィンドウで、**[アカウント設定]** セクションの **[実行アカウント]** をクリックします。
3. **[実行アカウント]** プロパティ ブレードで、削除する実行アカウントまたはクラシック実行アカウントを選択し、選択したアカウントのプロパティ ブレードで **[削除]** をクリックします。<br><br> ![実行アカウントを削除する](media/automation-sec-configure-azure-runas-account/automation-account-delete-runas.png)<br><br>  続行するかどうかを確認するプロンプトが表示されます。
4. アカウントが削除されている間、メニューの **[通知]** で進行状況を追跡できます。  削除が完了したら、**[実行アカウント]** プロパティ ブレードでそのアカウントを再作成できます。作成オプションとして **[Azure 実行アカウント]** を選択します。<br><br> ![Automation 実行アカウントを再作成する](media/automation-sec-configure-azure-runas-account/automation-account-create-runas.png)<br> 

### <a name="misconfiguration"></a>誤った構成
実行アカウントまたはクラシック実行アカウントが正しく機能するうえで必要な構成項目が削除されたり、初期セットアップ中に適切に作成されなかったりすることがあります。たとえば、次のような項目です。

* 証明書資産 
* 接続資産 
* 共同作業者ロールからの実行アカウントの削除
* Azure AD のサービス プリンシパルまたはアプリケーション

Automation ではこうした変更が検出され、アカウントの **[実行アカウント]** プロパティ ブレードで **[不完全]** 状態として通知されます。<br><br> ![実行構成が不完全であることを示すステータス メッセージ](media/automation-sec-configure-azure-runas-account/automation-account-runas-incomplete-config.png)<br><br>実行アカウントを選択すると、アカウントのプロパティ ウィンドウに次のエラーが表示されます。<br><br> ![実行構成が不完全であることを示す警告メッセージ](media/automation-sec-configure-azure-runas-account/automation-account-runas-incomplete-config-msg.png)。<br>  
実行アカウントが正しく構成されていない場合、これを迅速に解決するには、実行アカウントを削除して再作成します。   

## <a name="update-an-automation-account-using-powershell"></a>PowerShell を使用して Automation アカウントを更新する
次に示す場合に、PowerShell を使用して既存の Automation アカウントを更新できます。

1. Automation アカウントは作成済みであるものの、実行アカウントの作成を拒否した場合 
2. Resource Manager リソースを管理するための Automation アカウントを既に所有しており、それを更新して Runbook 認証用の実行アカウントを含める場合
4. クラシック リソースを管理するための Automation アカウントを既に所有しており、それを、新しいアカウントを作成する代わりにクラシック実行アカウントを使用するように更新し、Runbook と資産をそのアカウントに移行する場合   
5. エンタープライズ CA によって発行された証明書を使用して Azure 実行アカウントとクラシック実行アカウントを作成する場合

このスクリプトには、以下の前提条件があります。

1. このスクリプトは、Azure Resource Manager モジュール 2.01 以降がインストールされた Windows 10 および Windows Server 2016 でのみ実行できます。  前のバージョンの Windows ではサポートされません。  
2. Azure PowerShell 1.0 以降。 このリリースとそのインストール方法については、「 [Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs)」を参照してください。
3. Automation アカウントが作成済みであること。  このアカウントは、以下のスクリプトの –AutomationAccountName パラメーターと -ApplicationDisplayName パラメーターの値として参照されます。

スクリプトの必須パラメーターである *SubscriptionID*、*ResourceGroup*、*AutomationAccountName* の値を取得するには、Azure Portal の **[Automation アカウント]** ブレードからお使いの Automation アカウントを選択し、**[すべての設定]** を選択します。  **[すべての設定]** ブレードで、**[アカウント設定]** の **[プロパティ]** を選択します。  **[プロパティ]** ブレードで、値をメモすることができます。<br><br> ![Automation Account properties](media/automation-sec-configure-azure-runas-account/automation-account-properties.png)  

### <a name="create-run-as-account-powershell-script"></a>実行アカウント用の PowerShell スクリプトを作成する
この PowerShell スクリプトでは、以下の構成がサポートされます。 

* 自己署名証明書を使用して Azure 実行アカウントを作成する
* 自己署名証明書を使用して Azure 実行アカウントと Azure クラシック実行アカウントを作成する
* エンタープライズ証明書を使用して Azure 実行アカウントと Azure クラシック実行アカウントを作成する
* Azure Government クラウドで自己署名証明書を使用して Azure 実行アカウントと Azure クラシック実行アカウントを作成する

選択する構成オプションに応じて、以下のものが作成されます。

* Azure AD アプリケーション。自己署名証明書またはエンタープライズ証明書の公開キーでエクスポートされ、このアプリケーションの Azure AD におけるサービス プリンシパル アカウントを作成します。また、現在のサブスクリプションで、このアカウントの共同作成者ロールが割り当てられます (これは所有者など他の任意のロールに変更できます)。  詳細については、「[Azure Automation におけるロールベースのアクセス制御](automation-role-based-access-control.md)」をご覧ください。
* Automation 証明書資産。指定された Automation アカウントに **AzureRunAsCertificate** という名前で存在し、Azure AD アプリケーションで使用される証明書の秘密キーが格納されます。
* Automation 接続資産。指定された Automation アカウントに **AzureRunAsConnection** という名前で存在し、アプリケーション ID、テナント ID、サブスクリプション ID、証明書の拇印が格納されます。    

クラシック実行アカウントの場合:

* Automation 証明書資産。指定された Automation アカウントに **AzureClassicRunAsCertificate** という名前で存在し、管理証明書で使用される証明書の秘密キーが格納されます。  
* Automation 接続資産。指定された Automation アカウントに **AzureClassicRunAsConnection** という名前で存在し、サブスクリプション名、サブスクリプション ID、証明書資産名が格納されます。

クラシック実行アカウントを作成するオプションを選択した場合、スクリプトの実行後に、Automation アカウントが作成されたサブスクリプションの管理ストアに公開証明書 (.cer 形式) をアップロードする必要があります。 次の手順では、スクリプトを実行して証明書をアップロードする手順を説明します。    

1. ご使用のコンピューターに次のスクリプトを保存します。  この例では、**New-RunAsAccount.ps1** というファイル名で保存します。  
   
         #Requires -RunAsAdministrator
         Param (
        [Parameter(Mandatory=$true)]
        [String] $ResourceGroup,

        [Parameter(Mandatory=$true)]
        [String] $AutomationAccountName,

        [Parameter(Mandatory=$true)]
        [String] $ApplicationDisplayName,

        [Parameter(Mandatory=$true)]
        [String] $SubscriptionId,

        [Parameter(Mandatory=$true)]
        [Boolean] $CreateClassicRunAsAccount,

        [Parameter(Mandatory=$true)]
        [String] $SelfSignedCertPlainPassword,
 
        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPathForRunAsAccount,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPlainPasswordForRunAsAccount,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPathForClassicRunAsAccount,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPlainPasswordForClassicRunAsAccount,

        [Parameter(Mandatory=$false)]
        [ValidateSet("AzureCloud","AzureUSGovernment")]
        [string]$EnvironmentName="AzureCloud",

        [Parameter(Mandatory=$false)]
        [int] $SelfSignedCertNoOfMonthsUntilExpired = 12
        )

        function CreateSelfSignedCertificate([string] $keyVaultName, [string] $certificateName, [string] $selfSignedCertPlainPassword, 
                                      [string] $certPath, [string] $certPathCer, [string] $selfSignedCertNoOfMonthsUntilExpired ) {
        $Cert = New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation cert:\LocalMachine\My `
           -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
           -NotAfter (Get-Date).AddMonths($selfSignedCertNoOfMonthsUntilExpired)

        $CertPassword = ConvertTo-SecureString $selfSignedCertPlainPassword -AsPlainText -Force
        Export-PfxCertificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPath -Password $CertPassword -Force | Write-Verbose
        Export-Certificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPathCer -Type CERT | Write-Verbose 
        }

        function CreateServicePrincipal([System.Security.Cryptography.X509Certificates.X509Certificate2] $PfxCert, [string] $applicationDisplayName) {  
        $CurrentDate = Get-Date
        $keyValue = [System.Convert]::ToBase64String($PfxCert.GetRawCertData())
        $KeyId = (New-Guid).Guid 

        $KeyCredential = New-Object  Microsoft.Azure.Commands.Resources.Models.ActiveDirectory.PSADKeyCredential
        $KeyCredential.StartDate = $CurrentDate
        $KeyCredential.EndDate= [DateTime]$PfxCert.GetExpirationDateString()
        $KeyCredential.KeyId = $KeyId
        $KeyCredential.CertValue  = $keyValue

        # Use Key credentials and create AAD Application
        $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $applicationDisplayName) -IdentifierUris ("http://" + $KeyId) -KeyCredentials $KeyCredential
        $ServicePrincipal = New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId 
        $GetServicePrincipal = Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id
   
        # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
        Sleep -s 15
        $NewRole = New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
        $Retries = 0;
        While ($NewRole -eq $null -and $Retries -le 6)
        {
           Sleep -s 10
           New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
           $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
           $Retries++;
        }
           return $Application.ApplicationId.ToString();
        }

        function CreateAutomationCertificateAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $certifcateAssetName,[string] $certPath, [string] $certPlainPassword, [Boolean] $Exportable) {
        $CertPassword = ConvertTo-SecureString $certPlainPassword -AsPlainText -Force   
        Remove-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $certifcateAssetName -ErrorAction SilentlyContinue
        New-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Path $certPath -Name $certifcateAssetName -Password $CertPassword -Exportable:$Exportable  | write-verbose
        }

        function CreateAutomationConnectionAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $connectionAssetName, [string] $connectionTypeName, [System.Collections.Hashtable] $connectionFieldValues ) {
        Remove-AzureRmAutomationConnection -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -Force -ErrorAction SilentlyContinue
        New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -ConnectionTypeName $connectionTypeName -ConnectionFieldValues $connectionFieldValues 
        }

        Import-Module AzureRM.Profile
        Import-Module AzureRM.Resources

        $AzureRMProfileVersion= (Get-Module AzureRM.Profile).Version
        if (!(($AzureRMProfileVersion.Major -ge 2 -and $AzureRMProfileVersion.Minor -ge 1) -or ($AzureRMProfileVersion.Major -gt 2)))
        {
           Write-Error -Message "Please install the latest Azure PowerShell and retry. Relevant doc url : https://docs.microsoft.com/powershell/azureps-cmdlets-docs/ "
           return
        }
 
        Login-AzureRmAccount -EnvironmentName $EnvironmentName
        $Subscription = Select-AzureRmSubscription -SubscriptionId $SubscriptionId

        # Create Run As Account using Service Principal
        $CertifcateAssetName = "AzureRunAsCertificate"
        $ConnectionAssetName = "AzureRunAsConnection"
        $ConnectionTypeName = "AzureServicePrincipal"
 
        if ($EnterpriseCertPathForRunAsAccount -and $EnterpriseCertPlainPasswordForRunAsAccount) {
        $PfxCertPathForRunAsAccount = $EnterpriseCertPathForRunAsAccount
        $PfxCertPlainPasswordForRunAsAccount = $EnterpriseCertPlainPasswordForRunAsAccount
        } else {
          $CertificateName = $AutomationAccountName+$CertifcateAssetName
          $PfxCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".pfx")
          $PfxCertPlainPasswordForRunAsAccount = $SelfSignedCertPlainPassword
          $CerCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".cer")
          CreateSelfSignedCertificate $KeyVaultName $CertificateName $PfxCertPlainPasswordForRunAsAccount $PfxCertPathForRunAsAccount $CerCertPathForRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired 
        }

        # Create Service Principal
        $PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPathForRunAsAccount, $PfxCertPlainPasswordForRunAsAccount)
        $ApplicationId=CreateServicePrincipal $PfxCert $ApplicationDisplayName

        # Create the automation certificate asset
        CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $CertifcateAssetName $PfxCertPathForRunAsAccount $PfxCertPlainPasswordForRunAsAccount $true

        # Populate the ConnectionFieldValues
        $SubscriptionInfo = Get-AzureRmSubscription -SubscriptionId $SubscriptionId
        $TenantID = $SubscriptionInfo | Select TenantId -First 1
        $Thumbprint = $PfxCert.Thumbprint
        $ConnectionFieldValues = @{"ApplicationId" = $ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Thumbprint; "SubscriptionId" = $SubscriptionId} 

        # Create a Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ConnectionAssetName $ConnectionTypeName $ConnectionFieldValues

        if ($CreateClassicRunAsAccount) {
            # Create Run As Account using Service Principal
            $ClassicRunAsAccountCertifcateAssetName = "AzureClassicRunAsCertificate"
            $ClassicRunAsAccountConnectionAssetName = "AzureClassicRunAsConnection"
            $ClassicRunAsAccountConnectionTypeName = "AzureClassicCertificate "
            $UploadMessage = "Please upload the .cer format of #CERT# to the Management store by following the steps below." + [Environment]::NewLine +
                    "Log in to the Microsoft Azure Management portal (https://manage.windowsazure.com) and select Settings -> Management Certificates." + [Environment]::NewLine +
                    "Then click Upload and upload the .cer format of #CERT#" 
 
             if ($EnterpriseCertPathForClassicRunAsAccount -and $EnterpriseCertPlainPasswordForClassicRunAsAccount ) {
             $PfxCertPathForClassicRunAsAccount = $EnterpriseCertPathForClassicRunAsAccount
             $PfxCertPlainPasswordForClassicRunAsAccount = $EnterpriseCertPlainPasswordForClassicRunAsAccount
             $UploadMessage = $UploadMessage.Replace("#CERT#", $PfxCertPathForClassicRunAsAccount)
        } else {
             $ClassicRunAsAccountCertificateName = $AutomationAccountName+$ClassicRunAsAccountCertifcateAssetName
             $PfxCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".pfx")
             $PfxCertPlainPasswordForClassicRunAsAccount = $SelfSignedCertPlainPassword
             $CerCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".cer")
             $UploadMessage = $UploadMessage.Replace("#CERT#", $CerCertPathForClassicRunAsAccount)
             CreateSelfSignedCertificate $KeyVaultName $ClassicRunAsAccountCertificateName $PfxCertPlainPasswordForClassicRunAsAccount $PfxCertPathForClassicRunAsAccount $CerCertPathForClassicRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired 
        }

        # Create the automation certificate asset
        CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountCertifcateAssetName $PfxCertPathForClassicRunAsAccount $PfxCertPlainPasswordForClassicRunAsAccount $false

        # Populate the ConnectionFieldValues
        $SubscriptionName = $subscription.Subscription.SubscriptionName
        $ClassicRunAsAccountConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicRunAsAccountCertifcateAssetName}

        # Create a Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountConnectionAssetName $ClassicRunAsAccountConnectionTypeName $ClassicRunAsAccountConnectionFieldValues

        Write-Host -ForegroundColor red $UploadMessage
        }

2. コンピューターの**スタート**画面から、昇格されたユーザー権限で **Windows PowerShell** を起動します。
3. 管理者特権での PowerShell コマンドライン シェルから、手順 1. で作成したスクリプトが含まれているフォルダーに移動し、必要な構成に基づく必要なパラメーター値を設定してスクリプトを実行します。  

    **自己署名証明書を使用して Azure 実行アカウントを作成する**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false` 

    **自己署名証明書を使用して Azure 実行アカウントと Azure クラシック実行アカウントを作成する**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true`

    **エンタープライズ証明書を使用して Azure 実行アカウントと Azure クラシック実行アカウントを作成する**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>`

    **Azure Government クラウドで自己署名証明書を使用して Azure 実行アカウントと Azure クラシック実行アカウントを作成する**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment`
 
    > [!NOTE]
    > スクリプトの実行後、Azure での認証が求められます。 サブスクリプション管理ロールのメンバーかつサブスクリプションの共同管理者であるアカウントを使用してログインする必要があります。
    > 
    > 

クラシック実行アカウントを作成した場合、スクリプトが正常に完了した後、手順に従って Azure クラシック ポータルに [Management API 証明書をアップロード](../azure-api-management-certs.md)します。  自己署名公開証明書 (.cer 形式) を使ってクラシック実行アカウントを作成した場合、コンピューターの一時ファイル フォルダーに証明書のコピーが作成されています。PowerShell セッションの実行に使用されたユーザー プロファイルの下のフォルダー (*%USERPROFILE%\AppData\Local\Temp*) を探してください。  それ以外の場合、エンタープライズ CA (.cer 形式) によって生成された証明書を使うようにクラシック実行アカウントを構成した場合は、その証明書を使用する必要があります。  証明書をアップロードしたら、サービス管理リソースで資格情報の構成を検証する[サンプル コード](#sample-code-to-authenticate-with-service-management-resources)を参照してください。  

クラシック実行アカウントを作成しなかった場合は、次の[サンプル コード](#sample-code-to-authenticate-with-resource-manager-resources)を参照し、Resource Manager リソースでの認証と、資格情報の構成の検証を行います。   

##  <a name="authentication-code-examples"></a>認証コードの例

次の例は、Resource Manager リソースまたはクラシック リソースに対する Runbook の認証を実行アカウントを使って行う方法を示しています。

### <a name="authenticate-with-resource-manager-resources"></a>Resource Manager リソースで認証を行う
**AzureAutomationTutorialScript** のサンプル Runbook から次の更新済みサンプル コードを取得して使用することで、実行アカウントを使用して認証を行い、Runbook で Resource Manager リソースを管理することができます。   

    $connectionName = "AzureRunAsConnection"
    $SubId = Get-AutomationVariable -Name 'SubscriptionId'
    try
    {
       # Get the connection "AzureRunAsConnection "
       $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

       "Signing in to Azure..."
       Add-AzureRmAccount `
         -ServicePrincipal `
         -TenantId $servicePrincipalConnection.TenantId `
         -ApplicationId $servicePrincipalConnection.ApplicationId `
         -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
       "Setting context to a specific subscription"     
       Set-AzureRmContext -SubscriptionId $SubId              
    }
    catch {
        if (!$servicePrincipalConnection)
        {
           $ErrorMessage = "Connection $connectionName not found."
           throw $ErrorMessage
         } else{
            Write-Error -Message $_.Exception
            throw $_.Exception
         }
    }


スクリプトには、サブスクリプション コンテキストの参照をサポートする&2; つのコード行が追加されているため、複数のサブスクリプション間での作業がしやすくなっています。 SubscriptionId という名前の変数資産にはサブスクリプションの ID が格納されており、Add-AzureRmAccount コマンドレットのステートメントの後に、 [-SubscriptionId](https://msdn.microsoft.com/library/mt619263.aspx) パラメーター セットを使用して *Set-AzureRmContext コマンドレット*が宣言されます。 変数名が一般的すぎる場合は、目的に沿った特定しやすい名前になるよう、プレフィックスやその他の名前付け規則を含むように変数名を変更できます。 また、-SubscriptionId の代わりに、-SubscriptionName パラメーター セットを対応する変数資産と共に使用することもできます。    

Runbook での認証に使用されるコマンドレット ( **Add-AzureRmAccount**) は、 *ServicePrincipalCertificate* パラメーター セットを使用することに注意してください。  認証に使用するのはサービス プリンシパル証明書であり、資格情報ではありません。  

### <a name="authenticate-with-service-management-resources"></a>サービス管理リソースで認証を行う
サンプルの **AzureClassicAutomationTutorialScript** Runbook から次の更新済みサンプル コードを取得して使用することで、クラシック実行アカウントを使用して認証を行い、Runbook でクラシック リソースを管理することができます。

    $ConnectionAssetName = "AzureClassicRunAsConnection"
    # Get the connection
    $connection = Get-AutomationConnection -Name $connectionAssetName        

    # Authenticate to Azure with certificate
    Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
    $Conn = Get-AutomationConnection -Name $ConnectionAssetName
    if ($Conn -eq $null)
    {
       throw "Could not retrieve connection asset: $ConnectionAssetName. Assure that this asset exists in the Automation account."
    }

    $CertificateAssetName = $Conn.CertificateAssetName
    Write-Verbose "Getting the certificate: $CertificateAssetName" -Verbose
    $AzureCert = Get-AutomationCertificate -Name $CertificateAssetName
    if ($AzureCert -eq $null)
    {
       throw "Could not retrieve certificate asset: $CertificateAssetName. Assure that this asset exists in the Automation account."
    }

    Write-Verbose "Authenticating to Azure with certificate." -Verbose
    Set-AzureSubscription -SubscriptionName $Conn.SubscriptionName -SubscriptionId $Conn.SubscriptionID -Certificate $AzureCert
    Select-AzureSubscription -SubscriptionId $Conn.SubscriptionID

## <a name="next-steps"></a>次のステップ
* サービス プリンシパルの詳細については、 [アプリケーション オブジェクトおよびサービス プリンシパル オブジェクト](../active-directory/active-directory-application-objects.md)に関するページを参照してください。
* Azure Automation におけるロールベースのアクセス制御の詳細については、「 [Azure Automation におけるロールベースのアクセス制御](automation-role-based-access-control.md)」を参照してください。
* 証明書と Azure サービスの詳細については、「 [Azure Cloud Services の証明書の概要](../cloud-services/cloud-services-certs-create.md)


