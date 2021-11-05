---
title: アプリとGitHubの間に信頼関係を作成します。
titleSuffix: Microsoft identity platform
description: AzureADのアプリとGitHubリポジトリの間に信頼関係を設定します。  これにより、GitHub Actions ワークフローは、シークレットや証明書を使用せずにAzureADで保護されたリソースにアクセスできます。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 10/18/2021
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: keyam, udayh, vakarand
ms.openlocfilehash: 3e1e3d4857555b648b841a544e02346e6144bb2b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091750"
---
# <a name="configure-an-app-to-trust-a-github-repo-preview"></a>GitHub リポジトリを信頼するようにアプリを構成します。 (プレビュー)

この記事では、Azure Active Directory (Azure AD) のアプリケーションと GitHub リポジトリの間に信頼関係を作成する方法について説明します。  その後、GitHubアクションワークフローを構成して、GitHubからのトークンをMicrosoft IDプラットフォームからのアクセストークンと交換し、シークレットを管理することなくAzureADで保護されたリソースにアクセスできます。  トークン交換ワークフローの詳細については、[ワークロードIDフェデレーション](workload-identity-federation.md)について参照してください。  信頼関係を確立するには、Azure portal のアプリ登録でフェデレーションID資格情報を構成するか、Microsoft Graph を使用します。

アプリ登録を作成し、シークレットまたは証明書を追加するアクセス許可を持つユーザーは、フェデレーション ID 資格情報を追加できます。  **ユーザーがアプリケーションを登録できる場合** [ユーザー設定](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings)ブレードのスイッチが **いいえ** に設定されている場合、アプリ登録を作成したり、フェデレーションID資格情報を構成したりすることはできません。  管理者を探して、代理でフェデレーション ID 資格情報を構成します。  アプリケーション管理者ロールまたはアプリケーション所有者ロールのすべてのユーザーがこれを行うことができます。

GitHub リポジトリを信頼するようにアプリを構成したら、 [GitHub アクションワークフローを構成](/azure/developer/github/connect-from-azure)して、Microsoft  ID プロバイダーからアクセストークンを取得し、保護されたリソース Azure AD アクセスできるようにします。

## <a name="prerequisites"></a>前提条件
Azure AD で[アプリの登録を作成します。](quickstart-register-app.md)  GitHub ワークフローの対象となる Azure リソースへのアクセスをアプリに付与します。  

次の手順で必要になる、(アプリケーション (クライアント) ID ではなく) アプリのオブジェクト ID を検索します。  アプリのオブジェクト ID は、Azure portal で確認できます。  Azure portal で [登録済みアプリケーション](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) の一覧にアクセスし、アプリの登録を選択します。  [**の概要**-> **要点**] で、**オブジェクト ID** を見つけます。

GitHubリポジトリの組織、リポジトリ、および環境の情報を取得します。これは、次の手順で必要になります。

## <a name="configure-a-federated-identity-credential"></a>フェデレーションID資格情報を構成します。

# <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

[Azure portal](https://portal.azure.com/) にサインインします。  **アプリの登録** にアクセスして、構成するアプリを開きます。

**[証明書とシークレット]** にアクセスします。  **[フェデレーション資格情報]** タブで、 **[資格情報の追加]** を選択します。  **資格情報の追加** 」ブレードが開きます。

**フェデレーション資格情報シナリオ** ドロップダウンボックスで、 **AzureリソースをデプロイするGitHubアクション** を選択します。

GitHub アクションワークフローの **組織** と **リポジトリ** を指定します。  

**[エンティティ型]** で、 **[環境]** 、 **[ブランチ]** 、 **[PR]** 、または **[タグ]** を選択し、値を指定します。

フェデレーション資格情報の **名前** を追加します。

**発行者**、**対象ユーザー**、および **サブジェクトの識別子** フィールドは、入力した値に基づいて自動入力されます。

フェデレーション資格情報を構成するには、 **[追加]** をクリックします。

:::image type="content" source="media/workload-identity-federation-create-trust-github/add-credential.png" alt-text="[資格情報の追加] ウィンドウのスクリーンショット。サンプル値が表示されます。" :::

> [!NOTE]
> *サブジェクト* の設定で他の人の GitHub リポジトリを誤って構成した場合 (他のユーザーの他人リポジトリと一致する入力ミスを入力)、フェデレーションID資格情報を正常に作成できます。  ただし、GitHub構成では、他の人のリポジトリにアクセスできないので、エラーが発生します。

> [!IMPORTANT]
> **組織**、**リポジトリ**、および **エンティティ型** の値は、GitHub ワークフロー構成の構成と完全に一致する必要があります。 それ以外の場合、Microsoft IDプラットフォームは着信外部トークンを確認し、アクセストークンとの交換を拒否します。  エラーは発生しません。交換はエラーなしで失敗します。

# <a name="microsoft-graph"></a>[Microsoft Graph](#tab/microsoft-graph)
[Azure Cloud Shell](https://portal.azure.com/#cloudshell/)を起動し、テナントにサインインします。

### <a name="create-a-federated-identity-credential"></a>フェデレーションID資格情報を作成します。

次のコマンドを実行して、アプリ (アプリのオブジェクト ID で指定) の[新しいフェデレーション ID 資格情報を作成します。](/graph/api/application-post-federatedidentitycredentials?view=graph-rest-beta&preserve-view=true)  *発行者* は、GitHub を外部トークンの発行者として識別します。  *subject* は、GitHub ActionsワークフローのGitHub組織、リポジトリ、環境を識別します。  GitHubActionsワークフローがMicrosoftIDプラットフォームにGitHubトークンをアクセストークンと交換するように要求すると、フェデレーションID資格情報の値が提供されたGitHubトークンに対してチェックされます。

```azurecli
az rest --method POST --uri 'https://graph.microsoft.com/beta/applications/f6475511-fd81-4965-a00e-41e7792b7b9c/federatedIdentityCredentials' --body '{"name":"Testing","issuer":"https://token.actions.githubusercontent.com/","subject":"repo:octo-org/octo-repo:environment:Production","description":"Testing","audiences":["api://AzureADTokenExchange"]}' 
```

また、次の応答が返されます。
```azurecli
{
  "@odata.context": "https://graph.microsoft.com/beta/$metadata#applications('f6475511-fd81-4965-a00e-41e7792b7b9c')/federatedIdentityCredentials/$entity",
  "audiences": [
    "api://AzureADTokenExchange"
  ],
  "description": "Testing",
  "id": "1aa3e6a7-464c-4cd2-88d3-90db98132755",
  "issuer": "https://token.actions.githubusercontent.com/",
  "name": "Testing",
  "subject": "repo:octo-org/octo-repo:environment:Production"
}
```

*名前*: Azure アプリケーションの名前。

*発行者*: GitHub OIDC プロバイダーへのパス: `https://token.actions.githubusercontent.com/`。 この発行者は、Azure アプリケーションによって信頼されるようになります。

*subject*: Azure がアクセストークンを付与する前に、要求がここで定義された条件と一致する必要があります。
- 環境に関連付けられているジョブの場合: `repo:< Organization/Repository >:environment:< Name >`
- 環境に関連付けられていないジョブの場合は、ワークフローのトリガーに使用される ref パスに基づいて、ブランチ/タグの ref パスを含めます: `repo:< Organization/Repository >:ref:< ref path>`。  たとえば、`repo:n-username/ node_express:ref:refs/heads/my-branch` または `repo:n-username/ node_express:ref:refs/tags/my-tag` です。
- プル要求イベントによってトリガーされるワークフローの場合: `repo:< Organization/Repository >:pull-request`。

*対象ユーザー*: `api://AzureADTokenExchange` 必須の値です。

> [!NOTE]
> *サブジェクト* の設定で他の人の GitHub リポジトリを誤って構成した場合 (他の人のリポジトリと一致する入力ミスを入力)、フェデレーションID資格情報を正常に作成できます。  ただし、GitHub構成では、他の人のリポジトリにアクセスできないので、エラーが発生します。

> [!IMPORTANT]
> *サブジェクト* の設定値は、GitHub ワークフロー構成の構成と完全に一致する必要があります。  それ以外の場合、Microsoft IDプラットフォームは着信外部トークンを確認し、アクセストークンとの交換を拒否します。  エラーは発生しません。交換はエラーなしで失敗します。

### <a name="list-federated-identity-credentials-on-an-app"></a>アプリにフェデレーションID資格情報を一覧表示します。

次のコマンドを実行して、アプリ (アプリのオブジェクト ID で指定) の[フェデレーション ID 資格情報をリストします。](/graph/api/application-list-federatedidentitycredentials?view=graph-rest-beta&preserve-view=true)

```azurecli
az rest -m GET -u 'https://graph.microsoft.com/beta/applications/f6475511-fd81-4965-a00e-41e7792b7b9c/federatedIdentityCredentials' 
```

次のような応答画面が表示されます。

```azurecli
{
  "@odata.context": "https://graph.microsoft.com/beta/$metadata#applications('f6475511-fd81-4965-a00e-41e7792b7b9c')/federatedIdentityCredentials",
  "value": [
    {
      "audiences": [
        "api://AzureADTokenExchange"
      ],
      "description": "Testing",
      "id": "1aa3e6a7-464c-4cd2-88d3-90db98132755",
      "issuer": "https://token.actions.githubusercontent.com/",
      "name": "Testing",
      "subject": "repo:octo-org/octo-repo:environment:Production"
    }
  ]
}
```

### <a name="delete-a-federated-identity-credential"></a>フェデレーション ID 資格情報を削除します。

次のコマンドを実行して、アプリケーション (アプリのオブジェクト ID により指定されています) から[フェデレーション ID 資格情報を削除します。](/graph/api/application-list-federatedidentitycredentials?view=graph-rest-beta&preserve-view=true)

```azurecli
az rest -m DELETE  -u 'https://graph.microsoft.com/beta/applications/f6475511-fd81-4965-a00e-41e7792b7b9c/federatedIdentityCredentials/1aa3e6a7-464c-4cd2-88d3-90db98132755' 
```
---

## <a name="get-the-application-client-id-and-tenant-id-from-the-azure-portal"></a>Azure portal からアプリケーション (クライアント) ID とテナント ID を取得します。

GitHub Actions ワークフローを構成する前に、アプリ登録の *テナント id* と *クライアント id* の値を取得します。  これらの値は Azure portal で見つけることができます。 [登録済みアプリケーション](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps)の一覧 にアクセスし、 アプリの登録を選択します。  **概要**-> **要点** で、**アプリケーション (クライアント)** ID と **ディレクトリ (テナント) id** を検索します。 ワークフローの Azure ログイン アクションで使用するように、GitHub環境でこれらの値を設定します。  

## <a name="next-steps"></a>次の手順
Microsoft ID プロバイダーからアクセストークンを取得して Azure リソースにアクセスするように[GitHub Actionsワークフローを構成します。](/azure/developer/github/connect-from-azure)

Microsoft ID プロバイダーからアクセス トークンを取得し、Azure リソースにアクセスするように GitHub Actions ワークフローを構成する方法の詳細については、[GitHub Actions のドキュメント](https://docs.github.com/actions/deployment/security-hardening-your-deployments/configuring-openid-connect-in-azure)を参照してください。