---
title: 'App Services のデプロイ: Azure Stack | Microsoft Docs'
description: Azure Stack への App Service のデプロイに関する詳しいガイダンスです
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
ms.date: 06/29/2018
ms.author: anwestg
ms.openlocfilehash: 22593fc470325fbfb74cfb432207abeea7d96ac2
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2018
ms.locfileid: "37343108"
---
# <a name="add-an-app-service-resource-provider-to-azure-stack"></a>App Service リソース プロバイダーを Azure Stack に追加する

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

この記事のガイダンスを使用して、Azure Stack に App Service をデプロイします。

>[!IMPORTANT]  
>Azure App Service 1.2 をデプロイする前に、Azure Stack 統合システムに 1804 更新プログラムを適用するか、最新の Azure Stack Development Kit (ASDK) をデプロイしてください。

Web および API アプリケーションを作成する機能をユーザーに提供できます。 これらのアプリケーションをユーザーが作成できるようにするには、次のことを行う必要があります。

 - この記事で説明する手順を使用して、Azure Stack デプロイに [App Service リソース プロバイダー](azure-stack-app-service-overview.md)を追加します。
 - App Service リソース プロバイダーをインストールした後で、オファーやプランに含めることができます。 ユーザーはサブスクライブしてサービスを取得し、アプリケーションの作成を開始できます。

> [!IMPORTANT]  
> リソース プロバイダーのインストーラーを実行する前に、[開始前の準備](azure-stack-app-service-before-you-get-started.md)に関するページのガイダンスに従っていることを確認してください。

## <a name="run-the-app-service-resource-provider-installer"></a>App Service リソースプロバイダーのインストーラーを実行する

App Service リソース プロバイダーのインストールには少なくとも 1 時間かかります。 必要な時間の長さは、デプロイするロール インスタンスの数によって異なります。 デプロイ中に、インストーラーは次のタスクを実行します。

 - 指定された Azure Stack ストレージ アカウントに BLOB コンテナーを作成します。
 - App Service の DNS ゾーンとエントリを作成します。
 - App Service リソース プロバイダーを登録します。
 - App Service のギャラリー アイテムを登録します。

App Service リソースプロバイダーをデプロイするには、次の手順を実行します。

1. Azure Stack 管理の Azure Resource Management エンドポイントにアクセスできるコンピューターから、appservice.exe を管理者として実行します。

2. **[Deploy App Service or upgrade to the latest version]\(App Service をデプロイするか、または最新バージョンにアップグレードする\)** を選択します。

    ![App Service インストーラー][1]

3. マイクロソフト ソフトウェア ライセンス条項を確認して同意し、**[次へ]** を選択します。

4. サード パーティのライセンス条項を確認して同意し、**[次へ]** を選択します。

5. App Service クラウド構成情報が正しいことを確認します。 Azure Stack Development Kit (ASDK) のデプロイ中に既定の設定を使用した場合は、既定値をそのまま使用することもできます。 しかし、ASDK のデプロイ時にオプションをカスタマイズした場合、または Azure Stack 統合システムにデプロイしている場合は、その差異を反映するように、このウィンドウで値を編集する必要があります。

   たとえば、ドメイン サフィックス mycloud.com を使用する場合は、Azure Stack テナントの Azure Resource Manager エンドポイントを management.&lt;region&gt;.mycloud.com に変更する必要があります。 これらの設定を確認し、**[次へ]** を選択して設定を保存します。

   ![App Service インストーラー][2]

6. 次の App Service インストーラー ページで、次の手順に従います。

    a. **[Azure Stack Subscriptions]\(Azure Stack サブスクリプション\)** の横にある **[接続]** を選択します。

     - Azure Active Directory (Azure AD) を使っている場合は、Azure Stack のデプロイ時に指定した、Azure AD の管理者アカウントとパスワードを入力します。 **[サインイン]** を選択します。
     - Active Directory フェデレーション サービス (AD FS) を使用している場合は、ご自分の管理者アカウントを指定します。 たとえば、「cloudadmin@azurestack.local」のように入力します。 パスワードを入力し、**[サインイン]** を選択します。

   b. **[Azure Stack Subscriptions]\(Azure Stack サブスクリプション\)** で、**[Default Provider Subscription]\(既定のプロバイダー サブスクリプション\)** を選択します。

     >[!NOTE]
     >現在、App Service は、**[Default Provider Subscription]\(既定のプロバイダー サブスクリプション\)** にのみデプロイできます。

   c. **[Azure Stack Locations]\(Azure Stack の場所\)** で、デプロイしているリージョンに対応する場所を選択します。 たとえば、Azure Stack Development Kit にデプロイしている場合は、**[ローカル]** を選びます。

    ![App Service インストーラー][3]

7. [ここで示されている手順を使用して](azure-stack-app-service-before-you-get-started.md#virtual-network)構成した既存の仮想ネットワークにデプロイするか、または、App Service インストーラーで仮想ネットワークとサブネットを作成できます。 VNet を作成するには、次の手順に従います。

   a. **[Create VNet with default settings]\(既定の設定で VNet を作成する\)** をオンにし、既定値を受け入れて **[次へ]** を選択します。

   b. または、**[Use existing VNet and Subnets]\(既存の VNet とサブネットを使用する\)** をオンにします。 次の操作を実行します。

     - 仮想ネットワークが含まれている**リソース グループ**を選択します。
     - デプロイする**仮想ネットワーク**の名前を選択します。
     - 必要なロール サブネットそれぞれに適切な**サブネット**値を選択します。
     - **[次へ]** を選択します。

   ![App Service インストーラー][4]

8. ファイル共有の情報を入力してから、**[次へ]** を選択します。 ファイル共有のアドレスには、ファイル サーバーの完全修飾ドメイン名 (FQDN)、または IP アドレスを使用する必要があります。 たとえば、\\\appservicefileserver.local.cloudapp.azurestack.external\websites、または \\\10.0.0.1\websites を使用します。

   >[!NOTE]
   >インストーラーは、続行する前にファイル共有への接続性をテストしようとします。 しかし、既存の仮想ネットワークにデプロイする場合、この接続テストが失敗する可能性があります。 警告と、続行するためのプロンプトが表示されます。 ファイル共有情報が正しい場合は、デプロイを続行します。

   ![App Service インストーラー][7]

9. 次の App Service インストーラー ページで、次の手順に従います。

   a. **[Identity Application ID]\(ID アプリケーションの ID\)** ボックスで、(Azure AD から) 本人確認のために使っているアプリケーションの GUID を入力します。

   b. **[Identity Application certificate file]\(ID アプリケーションの証明書ファイル\)** ボックスで、証明書ファイルの場所を入力 (または参照) します。

   c. **[Identity Application certificate password]\(ID アプリケーションの証明書パスワード\)** ボックスで、証明書のパスワードを入力します。 このパスワードは、証明書作成のスクリプトを使ったときに書き留めたものです。

   d. **[Azure Resource Manager root certificate file]\(Azure Resource Manager のルート証明書ファイル\)** ボックスで、証明書ファイルの場所を入力 (または参照) します。

   e. **[次へ]** を選択します。

   ![App Service インストーラー][9]

10. 3 つの証明書ファイルの各ボックスで、**[参照]** を選択し、適切な証明書ファイルに移動します。 各証明書のパスワードを入力する必要があります。 これらの証明書は、[必要な証明書の作成の手順](azure-stack-app-service-before-you-get-started.md#get-certificates)で作成したものです。 すべての情報を入力したら、**[次へ]** を選択します。

    | Box | 証明書ファイル名の例 |
    | --- | --- |
    | **App Service の既定の SSL 証明書ファイル** | \_.appservice.local.AzureStack.external.pfx |
    | **App Service API の SSL 証明書ファイル** | api.appservice.local.AzureStack.external.pfx |
    | **App Service 公開元の SSL 証明書ファイル** | ftp.appservice.local.AzureStack.external.pfx |

    証明書の作成時に別のドメイン サフィックスを使った場合は、証明書ファイル名で *local.AzureStack.external* を使わないでください。 代わりに、ご自分のカスタム ドメイン情報を使用します。

    ![App Service インストーラー][10]

11. App Service リソース プロバイダー データベースをホストするために使用するサーバー インスタンスについて、SQL Server の詳細を入力し、**[次へ]** を選択します。 インストーラーにより、SQL 接続のプロパティが検証されます。

    > [!NOTE]
    > インストーラーは、続行する前に SQL Server への接続性をテストしようとします。 しかし、既存の仮想ネットワークにデプロイする場合、この接続テストが失敗する可能性があります。 警告と、続行するためのプロンプトが表示されます。 SQL Server の情報が正しい場合は、デプロイを続行します。

    ![App Service インストーラー][11]

12. ロール インスタンスと SKU のオプションを確認します。 ASDK デプロイの各ロールに対するインスタンスの最小数および SKU の最小値には、既定値が入力されています。 デプロイの計画に役立つ vCPU 要件とメモリ要件の概要が表示されています。 必要な項目を選択したら、**[次へ]** を選択します。

    >[!NOTE]
    >運用環境デプロイの場合は、「[Azure Stack での Azure App Service サーバー ロールの容量計画](azure-stack-app-service-capacity-planning.md)」のガイダンスに従ってください。

    | 役割 | インスタンスの最小値 | SKU の最小値 | メモ |
    | --- | --- | --- | --- |
    | コントローラー | 1 | Standard_A2 - (2 vCPU、3584 MB) | App Service クラウドの正常性を管理および維持します。 |
    | 管理 | 1 | Standard_A2 - (2 vCPU、3584 MB) | App Service Azure Resource Manager および API のエンドポイント、ポータル拡張機能 (管理、テナント、Functions ポータル)、データ サービスを管理します。 フェールオーバーをサポートする場合は、お勧めのインスタンス数は 2 つに増えます。 |
    | 発行元 | 1 | Standard_A1 - (1 vCPU、1792 MB) | FTP および Web デプロイによってコンテンツを公開します。 |
    | FrontEnd | 1 | Standard_A1 - (1 vCPU、1792 MB) | App Service アプリケーションに要求をルーティングします。 |
    | 共有 Worker | 1 | Standard_A1 - (1 vCPU、1792 MB) | Web または API アプリケーション、および Azure Functions アプリをホストします。 より多くのインスタンスの追加が必要になる場合があります。 オペレーターは、サービスを定義することや任意の SKU レベルを選ぶことができます。 レベルには、少なくとも 1 つの vCPU が必要です。 |

    ![App Service インストーラー][13]

    >[!NOTE]
    >**Windows Server 2016 Core は、Azure Stack 上で Azure App Service と共に使用するためにサポートされているプラットフォーム イメージではありません。運用環境デプロイには評価版イメージを使用しないでください。**

13. **[プラットフォーム イメージの選択]** ボックスで、App Service クラウド用のコンピューティング リソース プロバイダーで選択可能なイメージの中から、ご自分のデプロイの Windows Server 2016 仮想マシン イメージを選びます。 **[次へ]** を選択します。

14. 次の App Service インストーラー ページで、次の手順に従います。

     a. Worker ロールの仮想マシン管理者のユーザー名とパスワードを入力します。

     b. その他のロールの仮想マシン管理者のユーザー名とパスワードを入力します。

     c. **[次へ]** を選択します。

    ![App Service インストーラー][15]

15. App Service インストーラーの概要ページで、次の手順に従います。

    a. 選択した内容を確認します。 変更を加えるには、**[前へ]** を使って前のページに戻ります。

    b. 構成が正しい場合は、チェック ボックスをオンにします。

    c. デプロイを開始するには、**[次へ]** を選択します。

    ![App Service インストーラー][16]

16. 次の App Service インストーラー ページで、次の手順に従います。

    a. インストールの進行状況を追跡します。 App Service on Azure Stack を既定の選択内容に基づいてデプロイするには、60 分ほどかかります。

    b. インストーラーが正常に完了したら、**[終了]** を選択します。

    ![App Service インストーラー][17]

## <a name="validate-the-app-service-on-azure-stack-installation"></a>App Service on Azure Stack インストールを検証する

1. Azure Stack 管理ポータルで、**[Administration - App Service]\(管理 - App Service\)** に移動します。

2. 概要の状態で、**[状態]** に **[ロールはいずれも準備ができています]** と表示されていることを確認します。

    ![App Service の管理](media/azure-stack-app-service-deploy/image12.png)

   >[!IMPORTANT]
   >既存の仮想ネットワークにデプロイし、内部 IP アドレスを使用してファイル サーバーに接続する場合は、送信セキュリティ規則を追加する必要があります。 この規則により、worker サブネットとファイル サーバー間の SMB トラフィックが有効になります。  これを行うには、管理者ポータルで WorkersNsg に移動し、次のプロパティを持つ送信セキュリティ規則を追加します。<br>
    >  - 送信元: 任意
    >  - 送信元ポート範囲: *
    >  - 送信先: IP アドレス
    >  - 送信先 IP アドレス: ファイルサーバーの IP の範囲
    >  - 送信先ポート範囲: 445
    >  - プロトコル: TCP
    >  - アクション: 許可
    >  - 優先順位: 700
    >  - 名前: Outbound_Allow_SMB445

## <a name="test-drive-app-service-on-azure-stack"></a>App Service on Azure Stack を試してみる

App Service リソースプロバイダーをデプロイして登録したら、テストしてユーザーが Web アプリと API アプリをデプロイできることを確認します。

>[!NOTE]
>プラン内に Microsoft.Web 名前空間があるオファーを作成する必要があります。 また、このオファーにサブスクライブするテナント サブスクリプションも必要です。 詳しくは、「[Azure Stack でのオファーの作成](azure-stack-create-offer.md)」および「[Azure Stack でのプランの作成](azure-stack-create-plan.md)」をご覧ください。
>
>App Service on Azure Stack を使うアプリケーションを作るには、テナント サブスクリプションが*必要です*。 管理ポータル内でサービス管理者が実行できるタスクは、App Service のリソース プロバイダー管理に関連するものだけです。 これには、容量の追加、デプロイ ソースの構成、worker 層と SKU の追加などが含まれます。
>
>Web アプリ、API アプリ、Azure Functions アプリを作るには、テナント ポータルを使う必要があり、テナント サブスクリプションがある必要があります。
>

テスト Web アプリを作成するには、次の手順に従います。

1. Azure Stack テナント ポータルで、**[新規]** > **[Web + モバイル]** > **[Web アプリ]** の順に選択します。

2. **[Web アプリ]** で、**[Web アプリ]** に名前を入力します。

3. **[リソース グループ]** で、**[新規]** を選択します。 **[リソース グループ]** の名前を入力します。

4. **[App Service プラン/場所]** > **[新規作成]** の順に選択します。

5. **[App Service プラン]** で、**[App Service プラン]** の名前を入力します。

6. **[価格レベル]** > **[Free-Shared]\(無料 - 共有\)** または **[Shared-Shared]\(共有 - 共有\)** > **[選択]** > **[OK]** > **[作成]** の順に選択します。

7. ダッシュボードに新規 Web アプリのタイルが表示されます。 タイルを選択します。

8. **[Web アプリ]** で **[参照]** を選択して、このアプリの既定の Web サイトを表示します。

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>WordPress、DNN、Django の Web サイトを展開する (省略可能)

1. Azure Stack テナント ポータルで、**+** を選択して Azure Marketplace にアクセスし、Django Web サイトをデプロイして、デプロイが完了するまで待ちます。 Django Web プラットフォームでは、ファイル システム ベースのデータベースが使われます。 SQL や MySQL など、追加のリソース プロバイダーは必要ありません。

2. MySQL リソース プロバイダーもデプロイした場合は、Marketplace から WordPress Web サイトをデプロイできます。 データベース パラメーターを求められたら、お好きなユーザー名とサーバー名で、ユーザー名を *User1@Server1* の形式で入力します。

3. SQL Server リソース プロバイダーもデプロイした場合は、Marketplace から DNN Web サイトをデプロイできます。 データベース パラメーターを求められたら、使用しているリソース プロバイダーに接続された SQL Server を実行しているコンピューター内のデータベースを選びます。

## <a name="next-steps"></a>次の手順

その他の [Platform as a Service (PaaS) サービス](azure-stack-tools-paas-services.md)を試してみることもできます。

 - [SQL Server リソースプロバイダー](azure-stack-sql-resource-provider-deploy.md)
 - [MySQL リソースプロバイダー](azure-stack-mysql-resource-provider-deploy.md)

<!--Links-->
[Azure_Stack_App_Service_preview_installer]: http://go.microsoft.com/fwlink/?LinkID=717531
[App_Service_Deployment]: http://go.microsoft.com/fwlink/?LinkId=723982
[AppServiceHelperScripts]: http://go.microsoft.com/fwlink/?LinkId=733525

<!--Image references-->
[1]: ./media/azure-stack-app-service-deploy/app-service-installer.png
[2]: ./media/azure-stack-app-service-deploy/app-service-azure-stack-arm-endpoints.png
[3]: ./media/azure-stack-app-service-deploy/app-service-azure-stack-subscription-information.png
[4]: ./media/azure-stack-app-service-deploy/app-service-default-VNET-config.png
[5]: ./media/azure-stack-app-service-deploy/app-service-custom-VNET-config.png
[6]: ./media/azure-stack-app-service-deploy/app-service-custom-VNET-config-with-values.png
[7]: ./media/azure-stack-app-service-deploy/app-service-fileshare-configuration.png
[8]: ./media/azure-stack-app-service-deploy/app-service-fileshare-configuration-error.png
[9]: ./media/azure-stack-app-service-deploy/app-service-identity-app.png
[10]: ./media/azure-stack-app-service-deploy/app-service-certificates.png
[11]: ./media/azure-stack-app-service-deploy/app-service-sql-configuration.png
[12]: ./media/azure-stack-app-service-deploy/app-service-sql-configuration-error.png
[13]: ./media/azure-stack-app-service-deploy/app-service-cloud-quantities.png
[14]: ./media/azure-stack-app-service-deploy/app-service-windows-image-selection.png
[15]: ./media/azure-stack-app-service-deploy/app-service-role-credentials.png
[16]: ./media/azure-stack-app-service-deploy/app-service-azure-stack-deployment-summary.png
[17]: ./media/azure-stack-app-service-deploy/app-service-deployment-progress.png