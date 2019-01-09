---
title: デプロイ資格情報を構成する - Azure App Service | Microsoft Docs
description: Azure App Service のデプロイ資格情報を使用する方法について説明します。
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnoc
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/22/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: a92440e97f47f0778eb73b81b239b45476d4e733
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/17/2018
ms.locfileid: "53551117"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Azure App Service のデプロイ資格情報の構成
[Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) では、[ローカル Git デプロイ](deploy-local-git.md)と [FTP/S デプロイ](deploy-ftp.md)デプロイ用の 2 種類の資格情報をサポートしています。 これらは Azure Active Directory の資格情報とは異なります。

* **ユーザー レベルの資格情報**: Azure アカウント全体の資格情報セットです。 これを使用して、Azure アカウントがアクセス許可を持っているすべてのアプリをサブスクリプションに関係なく App Service にデプロイできます。 これは、ポータルの GUI (アプリの[リソース ページ](../azure-resource-manager/resource-group-portal.md#manage-resources)の **[概要]** や **[プロパティ]** など) に表示される既定のセットです。 ユーザーがロールベースのアクセス制御 (RBAC) または共同管理者のアクセス許可を使用してアプリのアクセス権を付与されると、そのユーザーは、アクセス権が取り消されるまで自分のユーザーレベル資格情報を使用できます。 これらの資格情報は他の Azure ユーザーと共有しないでください。

* **アプリ レベルの資格情報**: アプリごとの資格情報セットです。 そのアプリのみにデプロイするために使用できます。 各アプリの資格情報は、アプリの作成時に自動的に生成されます。 これらは手動で構成できませんが、いつでもリセットできます。 (RBAC) を使用してアプリレベルの資格情報へのアクセス権がユーザーに付与される場合、そのユーザーはアプリに対して共同作成者以上の権限を持つ必要があります。 閲覧者は発行を許可されていないため、この資格情報にアクセスできません。

## <a name="userscope"></a>ユーザー レベルの資格情報の設定とリセット

ユーザー レベルの資格情報は、任意のアプリの[リソース ページ](../azure-resource-manager/resource-group-portal.md#manage-resources)で構成できます。 どのアプリで構成した場合でも、これらの資格情報は、Azure アカウント内のすべてのアプリのすべてのサブスクリプションに適用されます。 

ユーザー レベルの資格情報を構成するには:

1. [Azure portal](https://portal.azure.com) の左側のメニューで、**[App Services]** > **[&lt;任意のアプリ>]** > **[デプロイ センター]** > **[デプロイ資格情報]** をクリックします。

    ポータルで、[デプロイ資格情報] ページにアクセスするには、少なくとも 1 つのアプリが必要です。 ただし、[Azure CLI](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) を使用する場合、ユーザー レベルの資格情報は既存のアプリなしで構成できます。

2. **[ユーザーの資格情報]** をクリックし、ユーザー名とパスワードを構成してから、**[資格情報の保存]** をクリックします。

    ![](./media/app-service-deployment-credentials/deployment_credentials_configure.png)

デプロイ資格情報を設定すると、*Git* デプロイのユーザー名がアプリの **[概要]** に表示されます。

![](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

さらに、*FTP* デプロイ ユーザー名が、アプリの **[プロパティ]** に表示されます。

![](./media/app-service-deployment-credentials/deployment_credentials_properties.png)

> [!NOTE]
> Azure では、ユーザー レベルのデプロイ パスワードを表示しません。 パスワードを忘れた場合は、このセクションの手順に従って、資格情報をリセットできます。
>
>  

## <a name="appscope"></a>アプリ レベルの資格情報の設定とリセット
アプリ レベルの資格情報を取得するには:

1. [Azure portal](https://portal.azure.com) の左側のメニューで、**[App Services]** > **[&lt;任意のアプリ>]** > **[デプロイ センター]** > **[デプロイ資格情報]** をクリックします。

2. **[アプリの資格情報]** をクリックし、**[コピー]** リンクをクリックして、ユーザー名またはパスワードをコピーします。

    ![](./media/app-service-deployment-credentials/deployment_credentials_app_level.png)

アプリレベルの資格情報をリセットするには、同じダイアログで **[資格情報のリセット]** をクリックします。

## <a name="next-steps"></a>次の手順

これらの資格情報を使用して、[ローカル Git](deploy-local-git.md) から、または [FTP/S](deploy-ftp.md) を使用してアプリをデプロイする方法を確認します。
