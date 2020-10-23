---
title: デプロイ資格情報の構成
description: Azure App Service のデプロイ資格情報の種類と、それらを構成および使用する方法について説明します。
ms.topic: article
ms.date: 08/14/2019
ms.reviewer: byvinyal
ms.custom: seodec18
ms.openlocfilehash: 653110b953b6947254d5063a9e389505d45ea4cb
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2020
ms.locfileid: "92149021"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Azure App Service のデプロイ資格情報の構成
[Azure App Service](./overview.md) では、[ローカル Git デプロイ](deploy-local-git.md)と [FTP/S デプロイ](deploy-ftp.md)デプロイ用の 2 種類の資格情報をサポートしています。 これらの資格情報は Azure サブスクリプションの資格情報とは異なります。

[!INCLUDE [app-service-deploy-credentials](../../includes/app-service-deploy-credentials.md)]

## <a name="configure-user-level-credentials"></a><a name="userscope"></a>ユーザーレベルの資格情報を構成する

ユーザー レベルの資格情報は、任意のアプリの[リソース ページ](../azure-resource-manager/management/manage-resources-portal.md#manage-resources)で構成できます。 どのアプリで構成した場合でも、これらの資格情報は、Azure アカウント内のすべてのアプリのすべてのサブスクリプションに適用されます。 

### <a name="in-the-cloud-shell"></a>Cloud Shell で

[Cloud Shell](https://shell.azure.com) でデプロイ ユーザーを構成するには、[az webapp deployment user set](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) コマンドを実行します。 \<username> と \<password> を、デプロイ ユーザーのユーザー名とパスワードで置き換えます。 

- ユーザー名は、Azure 内で一意である必要があり、ローカル Git プッシュの場合は "\@" シンボルを含めることはできません。 
- パスワードは長さが 8 文字以上で、文字、数字、記号のうち 2 つを含む必要があります。 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

JSON 出力には、パスワードが `null` として表示されます。 `'Conflict'. Details: 409` エラーが発生した場合は、ユーザー名を変更します。 `'Bad Request'. Details: 400` エラーが発生した場合は、より強力なパスワードを使用します。 

### <a name="in-the-portal"></a>ポータルで

Azure portal で、[デプロイ資格情報] ページにアクセスするには、少なくとも 1 つのアプリが必要です。 ユーザー レベルの資格情報を構成するには:

1. [Azure portal](https://portal.azure.com) の左側のメニューで、 **[App Services]**  >  **\<any_app>**  >  **[デプロイ センター]**  >  **[FTP]**  >  **[ダッシュボード]** を選択します。

    ![Azure App Services のデプロイ センターから FTP ダッシュボードを選択する方法を示します。](./media/app-service-deployment-credentials/access-no-git.png)

    あるいは、Git デプロイを既に構成している場合、 **[App Services]** 、 **[&lt;任意のアプリ>]** 、 **[デプロイ センター]** 、 **[FTP/資格情報]** を選択します。

    ![構成済み Git デプロイのために Azure App Services のデプロイ センターから FTP ダッシュボードを選択する方法を示します。](./media/app-service-deployment-credentials/access-with-git.png)

2. **[ユーザーの資格情報]** を選択し、ユーザー名とパスワードを構成してから、 **[資格情報の保存]** を選択します。

デプロイ資格情報を設定すると、*Git* デプロイのユーザー名がアプリの **[概要]** ページに表示されます。

![アプリの [概要] ページで Git デプロイ ユーザー名を見つける方法を示します。](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

Git デプロイが構成されている場合、ページに **Git/デプロイ ユーザー名**が表示されます。構成されていない場合、**FTP/デプロイ ユーザー名**が表示されます。

> [!NOTE]
> Azure では、ユーザー レベルのデプロイ パスワードを表示しません。 パスワードを忘れた場合は、このセクションの手順に従って、資格情報をリセットできます。
>
> 

## <a name="use-user-level-credentials-with-ftpftps"></a>ユーザーレベルの資格情報と FTP/FTPS を使用する

ユーザーレベルの資格情報を使用した FTP または FTPS エンドポイントの認証には、`<app-name>\<user-name>` という形式のユーザー名が必要です。

ユーザーレベルの資格情報は、特定のリソースではなく、ユーザーにリンクされているため、適切なアプリのエンドポイントに対してサインイン アクションを実行するには、ユーザー名はこの形式でなければなりません。

## <a name="get-and-reset-app-level-credentials"></a><a name="appscope"></a>アプリ レベルの資格情報の設定とリセット
アプリ レベルの資格情報を取得するには:

1. [Azure portal](https://portal.azure.com) の左側のメニューで、 **[App Services]** 、 **[&lt;任意のアプリ>]** 、 **[デプロイ センター]** 、 **[FTP/資格情報]** を選択します。

2. **[アプリの資格情報]** を選択し、 **[コピー]** リンクを選択し、ユーザー名またはパスワードをコピーします。

アプリレベルの資格情報をリセットするには、同じダイアログで **[資格情報のリセット]** を選択します。

## <a name="disable-basic-authentication"></a>基本認証を無効にする

組織によっては、セキュリティ要件を満たす必要があるため、FTP または WebDeploy によるアクセスを無効にした方がよい場合があります。 こうすることで、組織のメンバーは Azure Active Directory (Azure AD) で制御されている API を介してのみ App Services にアクセスできます。

### <a name="ftp"></a>FTP

サイトへの FTP アクセスを無効にするには、次の CLI コマンドを実行します。 プレースホルダーを目的のリソース グループとサイト名に置き換えます。 

```bash
az resource update --resource-group <resource-group> --name ftp --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<site-name> --set properties.allow=false
```

FTP アクセスがブロックされていることを確認するには、FileZilla などの FTP クライアントを使用して認証を試みます。 発行資格情報を取得するには、サイトの概要ブレードに移動して [発行プロファイルのダウンロード] をクリックします。 ファイルの FTP ホスト名、ユーザー名、パスワードを使用して認証すると、許可されないことを示す 401 エラー応答が表示されます。

### <a name="webdeploy-and-scm"></a>WebDeploy と SCM

WebDeploy ポートと SCM サイトへの基本認証アクセスを無効にするには、次の CLI コマンドを実行します。 プレースホルダーを目的のリソース グループとサイト名に置き換えます。 

```bash
az resource update --resource-group <resource-group> --name scm --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<site-name> --set properties.allow=false
```

WebDeploy で発行プロファイルの資格情報がブロックされていることを確認するには、[Visual Studio 2019 を使用して Web アプリを発行](/visualstudio/deployment/quickstart-deploy-to-azure?view=vs-2019)してみます。

### <a name="disable-access-to-the-api"></a>API へのアクセスを無効にする

前のセクションの API は、Azure ロールベースのアクセス制御 (Azure RBAC) によってサポートされます。つまり、[カスタム ロールを作成](../role-based-access-control/custom-roles.md#steps-to-create-a-custom-role)し、低い特権を持つユーザーをこのロールに割り当てると、これらのユーザーはどのサイトでも基本認証を有効化できなくなります。 カスタム ロールを構成するには、[こちらの手順を実行します](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#create-a-custom-rbac-role)。

また、[Azure Monitor](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#audit-with-azure-monitor) を使用して成功した認証要求を監査し、[Azure Policy](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#enforce-compliance-with-azure-policy) を使用してサブスクリプション内のすべてのサイトにこの構成を適用することもできます。

## <a name="next-steps"></a>次のステップ

これらの資格情報を使用して、[ローカル Git](deploy-local-git.md) から、または [FTP/S](deploy-ftp.md) を使用してアプリをデプロイする方法を確認します。