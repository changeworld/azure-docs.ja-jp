---
title: Azure App Service をオフラインで更新する | Microsoft Docs
description: Azure App Service on Azure Stack をオフラインで更新するための詳細なガイダンス
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
ms.date: 08/15/2018
ms.author: anwestg
ms.openlocfilehash: f48872d1853dfd4c40022f42c8e237973ac70fe6
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2018
ms.locfileid: "41947945"
---
# <a name="offline-update-of-azure-app-service-on-azure-stack"></a>Azure App Service on Azure Stack のオフライン更新

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

> [!IMPORTANT]
> Azure App Service 1.3 をデプロイする前に、Azure Stack 統合システムに 1807 更新プログラムを適用するか、または最新の Azure Stack 開発キットをデプロイします。
>
>

この記事の手順に従うことによって、次のような Azure Stack 環境にデプロイされた [App Service リソース プロバイダー](azure-stack-app-service-overview.md)をアップグレードできます。

* インターネットに接続されていない
* Active Directory フェデレーション サービス (AD FS) によって保護されている

> [!IMPORTANT]
> アップグレードを実行する前に、[Azure App Service on Azure Stack リソース プロバイダーのデプロイ](azure-stack-app-service-deploy-offline.md)を既に完了していることを確認してください。
>
>

## <a name="run-the-app-service-resource-provider-installer"></a>App Service リソースプロバイダーのインストーラーを実行する

Azure Stack 環境内の App Service リソース プロバイダーをアップグレードするには、次のタスクを完了する必要があります。

1. [App Service インストーラー](https://aka.ms/appsvcupdate3installer)をダウンロードする
2. オフライン アップグレード パッケージを作成する。
3. App Service インストーラー (appservice.exe) を実行し、アップグレードを完了します。

このプロセス中に、アップグレードでは次のことを行います。

* App Service の以前のデプロイを検出する
* ストレージにアップロードする
* すべての App Service ロール (コントローラー、管理、フロントエンド、パブリッシャー、および worker ロール) をアップグレードする
* App Service スケール セット定義を更新する
* App Service リソース プロバイダー マニフェストを更新する

## <a name="create-an-offline-upgrade-package"></a>オフライン アップグレード パッケージを作成する

切断された環境で App Service をアップグレードするには、まずインターネットに接続されたマシン上でオフライン アップグレード パッケージを作成する必要があります。

1. 管理者として appservice.exe を実行する

    ![App Service インストーラー][1]

2. **[詳細]** > **[Create offline package] (オフライン パッケージを作成する)** をクリックします。

    ![App Service インストーラーの [詳細]][2]

3. App Service インストーラーはオフライン アップグレード パッケージを作成し、そのパスを表示します。  **[フォルダーを開く]** をクリックすると、エクスプローラーでフォルダーが開きます。

4. インストーラー (AppService.exe) とオフライン アップグレード パッケージを Azure Stack ホスト マシンにコピーします。

## <a name="complete-the-upgrade-of-app-service-on-azure-stack"></a>App Service on Azure Stack のアップグレードを完了する

> [!IMPORTANT]
> App Service インストーラーは、Azure Stack Administrator Azure Resource Manager エンドポイントに到達できるマシン上で実行する必要があります。
>
>

1. 管理者として appservice.exe を実行します。

    ![App Service インストーラー][1]

2. **[詳細]** > **[Complete offline installation or upgrade] (オフライン インストールまたはアップグレードを完了する)** をクリックします。

    ![App Service インストーラーの [詳細]][2]

3. 前に作成したオフライン アップグレード パッケージの場所を参照し、**[次へ]** をクリックします。

4. マイクロソフト ソフトウェア ライセンス条項を確認して同意し、**[次へ]** をクリックします。

5. サード パーティのライセンス条項を確認して同意し、**[次へ]** をクリックします。

6. Azure Stack Azure Resource Manager エンドポイントと Active Directory テナントの情報が正しいことを確認します。 Azure Stack Development Kit の展開中に既定の設定を使用した場合は、ここで既定値を受け入れることができます。 ただし、Azure Stack のデプロイ時にオプションをカスタマイズした場合は、そのカスタマイズ内容を反映するように、このウィンドウで値を編集する必要があります。 たとえば、ドメイン サフィックス *mycloud.com* を使用する場合は、Azure Stack Azure Resource Manager エンドポイントを *management.region.mycloud.com* に変更する必要があります。 自分の情報を確認したら、**[次へ]** をクリックします。

    ![Azure Stack クラウドの情報][3]

7. 次のページで、次の操作を行います。

   1. **[Azure Stack Subscriptions]\(Azure Stack サブスクリプション\)** ボックスの横にある **[接続]** をクリックします。
        * Azure Active Directory (Azure AD) を使っている場合は、Azure Stack のデプロイ時に指定した、Azure AD の管理者アカウントとパスワードを入力します。 **[サインイン]** をクリックします。
        * Active Directory フェデレーション サービス (AD FS) を使用している場合は、ご自分の管理者アカウントを指定します。 たとえば、*cloudadmin@azurestack.local* です。 パスワードを入力し、**[サインイン]** をクリックします。
   2. **[Azure Stack Subscriptions]\(Azure Stack サブスクリプション\)** ボックスで、**[Default Provider Subscription]\(既定のプロバイダー サブスクリプション\)** を選びます。
   3. **[Azure Stack Locations]\(Azure Stack の場所\)** ボックスで、デプロイしているリージョンに対応する場所を選びます。 たとえば、Azure Stack Development Kit にデプロイしている場合は、**[ローカル]** を選びます。
   4. 既存の App Service デプロイが検出された場合は、リソース グループとストレージ アカウントにデータが入力され、灰色表示されます。
   5. **[次へ]** をクリックして、アップグレードの概要を確認します。

    ![検出された App Service インストール][4]

8. 概要ページで、次のことを行います。
   1. 選択した内容を確認します。 変更を加えるには、**[前へ]** を使って前のページに戻ります。
   2. 構成が正しい場合は、チェック ボックスをオンにします。
   3. アップグレードを開始するには、**[次へ]** をクリックします。

       ![App Service アップグレードの概要][5]

9. アップグレードの進行状況ページ:
    1. アップグレードの進行状況を追跡します。 App Service on Azure Stack のアップグレードの期間は、デプロイされるロール インスタンスの数によって異なります。
    2. アップグレードが正常に完了したら、**[終了]** をクリックします。

        ![App Service アップグレードの進行状況][6]

<!--Image references-->
[1]: ./media/azure-stack-app-service-update-offline/app-service-exe.png
[2]: ./media/azure-stack-app-service-update-offline/app-service-exe-advanced.png
[3]: ./media/azure-stack-app-service-update-offline/app-service-azure-resource-manager-endpoints.png
[4]: ./media/azure-stack-app-service-update-offline/app-service-installation-detected.png
[5]: ./media/azure-stack-app-service-update-offline/app-service-upgrade-summary.png
[6]: ./media/azure-stack-app-service-update-offline/app-service-upgrade-complete.png

## <a name="next-steps"></a>次の手順

その他の [Platform as a Service (PaaS) サービス](azure-stack-tools-paas-services.md)を試してみることもできます。

* [SQL Server リソースプロバイダー](azure-stack-sql-resource-provider-deploy.md)
* [MySQL リソースプロバイダー](azure-stack-mysql-resource-provider-deploy.md)
