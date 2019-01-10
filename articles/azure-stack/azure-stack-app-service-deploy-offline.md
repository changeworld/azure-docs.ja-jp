---
title: Azure Stack のオフライン環境に App Service を展開する | Microsoft Docs
description: AD FS によって保護されているオフラインの Azure Stack 環境に App Service を展開する方法についての詳細なガイダンスです。
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: anwestg
ms.openlocfilehash: a3703133e5dc5f0525638562fab98f9416bcf26e
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/08/2019
ms.locfileid: "54104660"
---
# <a name="add-an-app-service-resource-provider-to-a-disconnected-azure-stack-environment-secured-by-ad-fs"></a>App Service リソースプロバイダーを AD FS によって保護されているオフラインの Azure Stack 環境に追加する

*適用対象: Azure Stack 統合システムと Azure Stack Development Kit*

> [!IMPORTANT]
> Azure App Service 1.4 をデプロイする前に、Azure Stack 統合システムに 1809 更新プログラムを適用するか、または最新の Azure Stack 開発キットをデプロイします。
>
>

この記事の手順に従うと、次のような Azure Stack 環境に [App Service リソースプロバイダー](azure-stack-app-service-overview.md)をインストールできます。

- インターネットに接続されていない
- Active Directory フェデレーション サービス (AD FS) によって保護されている

App Service リソースプロバイダーをオフライン Azure Stack の展開に追加するには、次の最上位のタスクをすべて実行する必要があります。

1. [前提となる手順](azure-stack-app-service-before-you-get-started.md)をすべて実行します (証明書の購入など。受け取るまでに数日かかる場合があります)。
2. インターネットに接続しているマシンに[インストールとヘルパー ファイルをダウンロードして展開](azure-stack-app-service-before-you-get-started.md)します。
3. オフライン インストール パッケージを作成します。
4. appservice.exe インストーラー ファイルを実行します。

## <a name="create-an-offline-installation-package"></a>オフライン インストール パッケージを作成する

オフライン環境で App Service を展開するには、まず、インターネットに接続されているマシンでオフライン インストール パッケージを作成する必要があります。

1. インターネットに接続されているマシンで AppService.exe インストーラーを実行します。

2. **[詳細]** > **[オフライン インストール パッケージを作成する]** の順にクリックします。

    ![App Service インストーラー][1]

3. App Service インストーラーにより、オフライン インストール パッケージが作成され、そのパッケージへのパスが表示されます。 **[フォルダーを開く]** をクリックすると、エクスプローラーでフォルダーが開きます。

    ![App Service インストーラー](media/azure-stack-app-service-deploy-offline/image02.png)

4. インストーラー (AppService.exe) とオフライン インストール パッケージを Azure Stack ホスト マシンにコピーします。

## <a name="complete-the-offline-installation-of-app-service-on-azure-stack"></a>App Service on Azure Stack のオフライン インストールを完了させる

1. Azure Stack 管理の Azure Resource Management エンドポイントにアクセスできるコンピューターから、appservice.exe を管理者として実行します。

2. **[詳細設定]** > **[オフライン インストールが完了しました]** の順にクリックします。

    ![App Service インストーラー][2]

3. 先ほど作成したオフライン インストール パッケージの場所を参照し、**[次へ]** をクリックします。

    ![App Service インストーラー](media/azure-stack-app-service-deploy-offline/image04.png)

4. マイクロソフト ソフトウェア ライセンス条項を確認して同意し、**[次へ]** をクリックします。

5. サード パーティのライセンス条項を確認して同意し、**[次へ]** をクリックします。

6. App Service クラウド構成情報が正しいことを確認します。 Azure Stack Development Kit の展開中に既定の設定を使用した場合は、ここで既定値を受け入れることができます。 ただし、Azure Stack のデプロイ時にオプションをカスタマイズした場合、または統合システムにデプロイしている場合は、それを反映するように、このウィンドウで値を編集する必要があります。 たとえば、ドメイン サフィックス mycloud.com を使用する場合は、Azure Stack テナントの Azure Resource Manager エンドポイントを management.<region>.mycloud.com に変更する必要があります。 自分の情報を確認したら、**[次へ]** をクリックします。

    ![App Service インストーラー][3]

7. 次のページで、次の操作を行います。
    1. **[Azure Stack Subscriptions]\(Azure Stack サブスクリプション\)** ボックスの横にある **[接続]** をクリックします。
        - 管理者アカウントを指定します。 たとえば、「 cloudadmin@azurestack.local 」のように入力します。 パスワードを入力し、**[サインイン]** をクリックします。
    2. **[Azure Stack Subscriptions]\(Azure Stack サブスクリプション\)** ボックスで、**[Default Provider Subscription]\(既定のプロバイダー サブスクリプション\)** を選びます。
    
    > [!NOTE]
    > App Service は、**既定のプロバイダー サブスクリプション**にのみデプロイできます。
    >
    
    3. **[Azure Stack Locations]\(Azure Stack の場所\)** ボックスで、デプロイしているリージョンに対応する場所を選びます。 たとえば、Azure Stack Development Kit にデプロイしている場合は、**[ローカル]** を選びます。
    4. **[次へ]** をクリックします。

    ![App Service インストーラー][4]

8. [ここ](azure-stack-app-service-before-you-get-started.md#virtual-network)で示されている手順で構成された既存の仮想ネットワークにデプロイするオプションと、App Service インストーラーで仮想ネットワークと関連サブネットを作成するオプションがあります。
    1. **[Create VNet with default settings]\(既定の設定で VNet を作成する\)** をオンにし、既定値を受け入れて **[次へ]** をクリックします。または
    2. **[Use existing VNet and Subnets]\(既存の VNet とサブネットを使用する\)** をオンにします。
        1. 仮想ネットワークが含まれている**リソース グループ**を選択します。
        2. デプロイする適切な**仮想ネットワーク**名を選択します。
        3. 必要なロール サブネットそれぞれに適切な**サブネット**値を選択します。
        4. **[次へ]** をクリックします

    ![App Service インストーラー][5]

9. ファイル共有の情報を入力してから、**[次へ]** をクリックします。 ファイル共有のアドレスには、ファイル サーバーの完全修飾ドメイン名、または IP アドレスを使用する必要があります。 たとえば、\\\appservicefileserver.local.cloudapp.azurestack.external\websites、または \\\10.0.0.1\websites を使用します。

    > [!NOTE]
    > インストーラーは、続行する前にファイル共有への接続性をテストしようとします。  ただし、既存の仮想ネットワークにデプロイすることを選んだ場合、インストーラーはファイル共有に接続できない可能性があり、続行するかどうかを確認する警告が表示されます。  ファイル共有情報を確認し、正しい場合は続行します。
    >
    >

   ![App Service インストーラー][8]

10. 次のページで、次の操作を行います。
    1. **[Identity Application ID]\(ID アプリケーションの ID\)** ボックスで、(Azure AD から) 本人確認のために使っているアプリケーションの GUID を入力します。
    2. **[Identity Application certificate file]\(ID アプリケーションの証明書ファイル\)** ボックスで、証明書ファイルの場所を入力 (または参照) します。
    3. **[Identity Application certificate password]\(ID アプリケーションの証明書パスワード\)** ボックスで、証明書のパスワードを入力します。 このパスワードは、証明書作成のスクリプトを使ったときに書き留めたものです。
    4. **[Azure Resource Manager root certificate file]\(Azure Resource Manager のルート証明書ファイル\)** ボックスで、証明書ファイルの場所を入力 (または参照) します。
    5. **[次へ]** をクリックします。

    ![App Service インストーラー][10]

11. 3 つの証明書ファイルの各ボックスで、**[参照]** をクリックし、適切な証明書ファイルに移動します。 各証明書のパスワードを入力する必要があります。 これらの証明書は、[必要な証明書の作成の手順](azure-stack-app-service-before-you-get-started.md#get-certificates)で作成したものです。 すべての情報を入力したら、**[次へ]** をクリックします。

    | Box | 証明書ファイル名の例 |
    | --- | --- |
    | **App Service の既定の SSL 証明書ファイル** | \_.appservice.local.AzureStack.external.pfx |
    | **App Service API の SSL 証明書ファイル** | api.appservice.local.AzureStack.external.pfx |
    | **App Service 公開元の SSL 証明書ファイル** | ftp.appservice.local.AzureStack.external.pfx |

    証明書の作成時に別のドメイン サフィックスを使った場合は、証明書ファイル名で *local.AzureStack.external* を使わないでください。 代わりに、ご自分のカスタム ドメイン情報を使用します。

    ![App Service インストーラー][11]

12. App Service リソースプロバイダー データベースをホストするために使うサーバー インスタンスについて、SQL Server の詳細を入力してから、**[次へ]** をクリックします。 インストーラーにより、SQL 接続のプロパティが検証されます。 内部 IP または SQL Server 名の完全修飾ドメイン名を入力する**必要**があります。

    > [!NOTE]
    > インストーラーは、続行する前に SQL Server への接続性をテストしようとします。  ただし、既存の仮想ネットワークにデプロイすることを選んだ場合、インストーラーは SQL Server に接続できない可能性があり、続行するかどうかを確認する警告が表示されます。  SQL Server 情報を確認し、正しい場合は続行します。
    >
    > Azure App Service on Azure Stack 1.3 以降では、インストーラーは、SQL Server がデータベース包含を SQL Server レベルで有効にしているかどうかを確認します。  そうなっていない場合は、次の例外が表示されます。
    > ```sql
    >    Enable contained database authentication for SQL server by running below command on SQL server (Ctrl+C to copy)
    >    ***********************************************************
    >    sp_configure 'contained database authentication', 1;  
    >    GO  
    >    RECONFIGURE;  
    >    GO
    >    ***********************************************************
    > ```
    > 詳細については、[Azure App Service on Azure Stack 1.3 のリリース ノート](azure-stack-app-service-release-notes-update-three.md)を参照してください。
   
   ![App Service インストーラー][12]

13. ロール インスタンスと SKU のオプションを確認します。 既定値には、ASDK デプロイの各ロールに対するインスタンスの最小数および SKU の最小値が入力されています。 デプロイの計画に役立つ vCPU 要件とメモリ要件の概要が表示されています。 必要な項目を選んだら、**[次へ]** をクリックします。

     > [!NOTE]
     > 運用環境デプロイの場合は、「[Azure Stack での Azure App Service サーバー ロールの容量計画](azure-stack-app-service-capacity-planning.md)」のガイダンスに従ってください。
     >
     >

    | Role | インスタンスの最小値 | SKU の最小値 | メモ |
    | --- | --- | --- | --- |
    | コントローラー | 1 | Standard_A2 - (2 vCPU、3584 MB) | App Service クラウドの正常性を管理および維持します。 |
    | 管理 | 1 | Standard_A2 - (2 vCPU、3584 MB) | App Service Azure Resource Manager および API のエンドポイント、ポータル拡張機能 (管理、テナント、Functions ポータル)、データ サービスを管理します。 フェールオーバーをサポートする場合は、お勧めのインスタンス数は 2 つに増えます。 |
    | 発行元 | 1 | Standard_A1 - (1 vCPU、1792 MB) | FTP および Web デプロイによってコンテンツを公開します。 |
    | FrontEnd | 1 | Standard_A1 - (1 vCPU、1792 MB) | App Service アプリケーションに要求をルーティングします。 |
    | 共有 Worker | 1 | Standard_A1 - (1 vCPU、1792 MB) | Web または API アプリケーション、および Azure Functions アプリをホストします。 より多くのインスタンスの追加が必要になる場合があります。 オペレーターは、サービスを定義することや任意の SKU レベルを選ぶことができます。 レベルには、少なくとも 1 つの vCPU が必要です。 |

    ![App Service インストーラー][14]

    > [!NOTE]
    > **Windows Server 2016 Core は、Azure Stack 上で Azure App Service と共に使用するためにサポートされているプラットフォーム イメージではありません。運用デプロイには評価版イメージを使用しないでください。Azure App Service on Azure Stack では、デプロイに使用されるイメージで Microsoft .Net 3.5.1 SP1 をアクティブにすることが必要です。 Marketplace にシンジケート化された Windows Server 2016 イメージでは、この機能は有効ではありません。そのため、この機能をあらかじめ有効にして Windows Server 2016 イメージを作成し、使用する必要があります。**

14. **[プラットフォーム イメージの選択]** ボックスで、App Service クラウド用のコンピューティング リソースプロバイダーで選択可能な項目の中から、ご自分のデプロイの Windows Server 2016 仮想マシン イメージを選びます。 **[次へ]** をクリックします。

15. 次のページで、次の操作を行います。
     1. Worker ロールの仮想マシン管理者のユーザー名とパスワードを入力します。
     2. その他のロールの仮想マシン管理者のユーザー名とパスワードを入力します。
     3. **[次へ]** をクリックします。

    ![App Service インストーラー][16]

16. 概要ページで、次のことを行います。
    1. 選択した内容を確認します。 変更を加えるには、**[前へ]** を使って前のページに戻ります。
    2. 構成が正しい場合は、チェック ボックスをオンにします。
    3. 展開を始めるには、**[次へ]** をクリックします。

    ![App Service インストーラー][17]

17. 次のページで、次の操作を行います。
    1. インストールの進行状況を追跡します。 App Service on Azure Stack を既定の選択内容に基づいてデプロイするには、60 分ほどかかります。
    2. インストーラーが正常に完了したら、**[終了]** をクリックします。

    ![App Service インストーラー][18]

## <a name="validate-the-app-service-on-azure-stack-installation"></a>App Service on Azure Stack インストールを検証する

1. Azure Stack 管理ポータルで、**[Administration - App Service]\(管理 - App Service\)** に移動します。

2. 概要の状態で、**[状態]** に **[ロールはいずれも準備ができています]** と表示されていることを確認します。

    ![App Service の管理](media/azure-stack-app-service-deploy/image12.png)

> [!NOTE]
> ファイル サーバーに接続するために既存の仮想ネットワークと内部 IP アドレスへデプロイする場合は、送信セキュリティ規則を追加して、worker サブネットとファイル サーバー間の SMB トラフィックを有効にする必要があります。  これを行うには、管理者ポータルで WorkersNsg に移動し、次のプロパティを持つ送信セキュリティ規則を追加します。
> * ソース:任意
> * 送信元ポート範囲: *
> * 変換先:IP アドレス
> * 宛先 IP アドレス範囲:ファイルサーバーの IP の範囲
> * 送信先ポート範囲:445
> * プロトコル:TCP
> * アクション:ALLOW
> * 優先順位:700
> * 名前:Outbound_Allow_SMB445
>

## <a name="test-drive-app-service-on-azure-stack"></a>App Service on Azure Stack を試してみる

App Service リソースプロバイダーをデプロイして登録したら、テストしてユーザーが Web アプリと API アプリをデプロイできることを確認します。

> [!NOTE]
> プラン内に Microsoft.Web 名前空間があるオファーを作る必要があります。 その後、このオファーをサブスクライブするテナント サブスクリプションが必要となります。 詳しくは、「[Azure Stack でのオファーの作成](azure-stack-create-offer.md)」および「[Azure Stack でのプランの作成](azure-stack-create-plan.md)」をご覧ください。
>
> App Service on Azure Stack を使うアプリケーションを作るには、テナント サブスクリプションが*必要です*。 管理ポータル内でサービス管理者が実行できる機能は、App Service のリソースプロバイダー管理に関連するものだけです。 これらの機能には、容量の追加、展開ソースの構成、Worker 層と SKU の追加などがあります。
>
> 3 番目のテクニカル プレビューの時点では、Web アプリ、API アプリ、Azure Functions アプリを作るには、テナント ポータルを使う必要があり、テナント サブスクリプションがある必要があります。

1. Azure Stack テナント ポータルで、**[+ リソースの作成]** > **[Web + モバイル]** > **[Web アプリ]** の順にクリックします。

2. **[Web アプリ]** ブレードで、**[Web アプリ]** ボックスに名前を入力します。

3. **[リソース グループ]** の下にある **[新規]** をクリックします。 **[リソース グループ]** ボックスに名前を入力します。

4. **[App Service プラン/場所]** > **[新規作成]** の順にクリックします。

5. **[App Service プラン]** ブレードで、**[App Service プラン]** ボックスに名前を入力します。

6. **[価格レベル]**  >  **[Free-Shared]\(無料 - 共有\)** または **[Shared-Shared]\(共有 - 共有\)**  >  **[選択]**  >  **[OK]**  >  **[作成]** の順にクリックします。

7. 1 分以内に、ダッシュボードに新規 Web アプリのタイルが表示されます。 タイルをクリックします。

8. **[Web アプリ]** ブレードで、**[参照]** をクリックして、このアプリの既定の Web サイトを表示します。

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>WordPress、DNN、Django の Web サイトをデプロイする (省略可能)

1. Azure Stack テナント ポータルで、**[+]** をクリックして Azure Marketplace にアクセスし、Django Web サイトをデプロイして、正常に完了するまで待ちます。 Django Web プラットフォームでは、ファイル システム ベースのデータベースが使われます。 SQL や MySQL など、追加のリソース プロバイダーは必要ありません。

2. MySQL リソース プロバイダーもデプロイした場合は、Marketplace から WordPress Web サイトをデプロイできます。 データベース パラメーターを求められたら、お好きなユーザー名とサーバー名で、ユーザー名を *User1@Server1* の形式で入力します。

3. SQL Server リソース プロバイダーもデプロイした場合は、Marketplace から DNN Web サイトをデプロイできます。 データベース パラメーターを求められたら、使用しているリソース プロバイダーに接続された SQL Server を実行しているコンピューター内のデータベースを選びます。

## <a name="next-steps"></a>次の手順

その他の [Platform as a Service (PaaS) サービス](azure-stack-tools-paas-services.md)を試してみることもできます。

- [SQL Server リソースプロバイダー](azure-stack-sql-resource-provider-deploy.md)
- [MySQL リソースプロバイダー](azure-stack-mysql-resource-provider-deploy.md)

<!--Links-->
[Azure_Stack_App_Service_preview_installer]: https://go.microsoft.com/fwlink/?LinkID=717531
[App_Service_Deployment]: https://go.microsoft.com/fwlink/?LinkId=723982
[AppServiceHelperScripts]: https://go.microsoft.com/fwlink/?LinkId=733525

<!--Image references-->
[1]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-advanced-create-package.png
[2]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-advanced-complete-offline.png
[3]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-arm-endpoints.png
[4]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-subscription-information.png
[5]: ./media/azure-stack-app-service-deploy-offline/app-service-default-VNET-config.png
[6]: ./media/azure-stack-app-service-deploy-offline/app-service-custom-VNET-config.png
[7]: ./media/azure-stack-app-service-deploy-offline/app-service-custom-VNET-config-with-values.png
[8]: ./media/azure-stack-app-service-deploy-offline/app-service-fileshare-configuration.png
[9]: ./media/azure-stack-app-service-deploy-offline/app-service-fileshare-configuration-error.png
[10]: ./media/azure-stack-app-service-deploy-offline/app-service-identity-app.png
[11]: ./media/azure-stack-app-service-deploy-offline/app-service-certificates.png
[12]: ./media/azure-stack-app-service-deploy-offline/app-service-sql-configuration.png
[13]: ./media/azure-stack-app-service-deploy-offline/app-service-sql-configuration-error.png
[14]: ./media/azure-stack-app-service-deploy-offline/app-service-cloud-quantities.png
[15]: ./media/azure-stack-app-service-deploy-offline/app-service-windows-image-selection.png
[16]: ./media/azure-stack-app-service-deploy-offline/app-service-role-credentials.png
[17]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-deployment-summary.png
[18]: ./media/azure-stack-app-service-deploy-offline/app-service-deployment-progress.png
