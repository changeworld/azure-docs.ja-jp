---
title: Azure App Service on Azure Stack を更新する | Microsoft Docs
description: Azure App Service on Azure Stack を更新するための詳細なガイダンス
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
ms.openlocfilehash: f12eb7d74d9c47567c72cddda8d2813f394fb1de
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2018
ms.locfileid: "41947917"
---
# <a name="update-azure-app-service-on-azure-stack"></a>Azure App Service on Azure Stack を更新する

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

> [!IMPORTANT]
> Azure App Service 1.3 をデプロイする前に、Azure Stack 統合システムに 1807 更新プログラムを適用するか、または最新の Azure Stack 開発キットをデプロイします。
>
>

この記事の手順に従うことによって、インターネットに接続されている Azure Stack 環境にデプロイされた [App Service リソース プロバイダー](azure-stack-app-service-overview.md)をアップグレードできます。

> [!IMPORTANT]
> アップグレードを実行する前に、[Azure App Service on Azure Stack リソース プロバイダーのデプロイ](azure-stack-app-service-deploy.md)を既に完了していることを確認してください。
>
>

## <a name="run-the-app-service-resource-provider-installer"></a>App Service リソースプロバイダーのインストーラーを実行する

このプロセス中に、アップグレードでは次のことを行います。

* App Service の以前のデプロイを検出する
* すべての更新プログラム パッケージとデプロイされるすべての OSS ライブラリの新しいバージョンを準備する
* ストレージにアップロードする
* すべての App Service ロール (コントローラー、管理、フロントエンド、パブリッシャー、および worker ロール) をアップグレードする
* App Service スケール セット定義を更新する
* App Service リソース プロバイダー マニフェストを更新する

> [!IMPORTANT]
> App Service インストーラーは、Azure Stack Administrator Azure Resource Manager エンドポイントに到達できるマシン上で実行する必要があります。
>
>

App Service on Azure Stack のデプロイをアップグレードするには、次の手順に従います。

1. [App Service インストーラー](https://aka.ms/appsvcupdate3installer)をダウンロードする

2. 管理者として appservice.exe を実行する

    ![App Service インストーラー][1]

3. **[Deploy App Service or upgrade to the latest version] (App Service をデプロイするか、または最新バージョンにアップグレードする)** をクリックします。

4. マイクロソフト ソフトウェア ライセンス条項を確認して同意し、**[次へ]** をクリックします。

5. サード パーティのライセンス条項を確認して同意し、**[次へ]** をクリックします。

6. Azure Stack Azure Resource Manager エンドポイントと Active Directory テナントの情報が正しいことを確認します。 Azure Stack Development Kit の展開中に既定の設定を使用した場合は、ここで既定値を受け入れることができます。 ただし、Azure Stack のデプロイ時にオプションをカスタマイズした場合は、そのカスタマイズ内容を反映するように、このウィンドウで値を編集する必要があります。 たとえば、ドメイン サフィックス *mycloud.com* を使用する場合は、Azure Stack Azure Resource Manager エンドポイントを *management.region.mycloud.com* に変更する必要があります。 自分の情報を確認したら、**[次へ]** をクリックします。

    ![Azure Stack クラウドの情報][2]

7. 次のページで、次の操作を行います。

   1. **[Azure Stack Subscriptions]\(Azure Stack サブスクリプション\)** ボックスの横にある **[接続]** をクリックします。
        * Azure Active Directory (Azure AD) を使っている場合は、Azure Stack のデプロイ時に指定した、Azure AD の管理者アカウントとパスワードを入力します。 **[サインイン]** をクリックします。
        * Active Directory フェデレーション サービス (AD FS) を使用している場合は、ご自分の管理者アカウントを指定します。 たとえば、*cloudadmin@azurestack.local* です。 パスワードを入力し、**[サインイン]** をクリックします。
   2. **[Azure Stack Subscriptions]\(Azure Stack サブスクリプション\)** ボックスで、**[Default Provider Subscription]\(既定のプロバイダー サブスクリプション\)** を選びます。
   3. **[Azure Stack Locations]\(Azure Stack の場所\)** ボックスで、デプロイしているリージョンに対応する場所を選びます。 たとえば、Azure Stack Development Kit にデプロイしている場合は、**[ローカル]** を選びます。
   4. 既存の App Service デプロイが検出された場合は、リソース グループとストレージ アカウントにデータが入力され、灰色表示されます。
   5. **[次へ]** をクリックして、アップグレードの概要を確認します。

    ![検出された App Service インストール][3]

8. 概要ページで、次のことを行います。
   1. 選択した内容を確認します。 変更を加えるには、**[前へ]** を使って前のページに戻ります。
   2. 構成が正しい場合は、チェック ボックスをオンにします。
   3. アップグレードを開始するには、**[次へ]** をクリックします。

       ![App Service アップグレードの概要][4]

9. アップグレードの進行状況ページ:
    1. アップグレードの進行状況を追跡します。 App Service on Azure Stack のアップグレードの期間は、デプロイされるロール インスタンスの数によって異なります。
    2. アップグレードが正常に完了したら、**[終了]** をクリックします。

        ![App Service アップグレードの進行状況][5]

<!--Image references-->
[1]: ./media/azure-stack-app-service-update/app-service-exe.png
[2]: ./media/azure-stack-app-service-update/app-service-azure-resource-manager-endpoints.png
[3]: ./media/azure-stack-app-service-update/app-service-installation-detected.png
[4]: ./media/azure-stack-app-service-update/app-service-upgrade-summary.png
[5]: ./media/azure-stack-app-service-update/app-service-upgrade-complete.png

## <a name="next-steps"></a>次の手順

その他の [Platform as a Service (PaaS) サービス](azure-stack-tools-paas-services.md)を試してみることもできます。

* [SQL Server リソースプロバイダー](azure-stack-sql-resource-provider-deploy.md)
* [MySQL リソースプロバイダー](azure-stack-mysql-resource-provider-deploy.md)
