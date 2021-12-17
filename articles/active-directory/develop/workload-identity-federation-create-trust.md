---
title: アプリと外部 ID プロバイダーの間に信頼関係を作成する
titleSuffix: Microsoft identity platform
description: Azure AD 内のアプリと外部 ID プロバイダーの間に信頼関係を築きます。  これにより、Azure の外部にあるソフトウェア ワークロードは、シークレットまたは証明書を使用せずに、 Azure AD で保護されたリソースにアクセスできます。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 10/25/2021
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: keyam, udayh, vakarand
ms.openlocfilehash: 1d2c4631b663a57a8b1b2465c8cba6d6f81d803b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091747"
---
# <a name="configure-an-app-to-trust-an-external-identity-provider-preview"></a>外部 ID プロバイダーを信頼するようにアプリを構成する (プレビュー)

この記事では、Azure Active Directory (Azure AD) のアプリケーションと外部 ID プロバイダーの間に信頼関係を作成する方法について説明します。  その後、Microsoft ID プラットフォーム経由でアクセス トーク用外部 IdP からトークンを交換するために、外部ソフトウェア ワークロードを構成できます。 外部ワークロードは、(サポートされている Azure AD で) シークレットを管理することなく、Azure AD で保護されたリソースにアクセスできます。  トークン交換ワークフローの詳細については、[ワークロードIDフェデレーション](workload-identity-federation.md)について参照してください。  信頼関係を確立するには、Microsoft Graph を使用して、アプリ登録でフェデレーションID資格情報を構成します。

アプリ登録を作成し、シークレットまたは証明書を追加するアクセス許可を持つユーザーは、フェデレーション ID 資格情報を追加できます。  [ユーザー設定](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings)ブレード内にある **ユーザーは、アプリケーションを登録できる** スイッチを **いいえ** に設定できても、アプリの登録の作成や、フェデレーション ID 資格情報を構成したりすることはできません。  管理者を探して、代理でフェデレーション ID 資格情報を構成します。  アプリケーション管理者ロールまたはアプリケーション所有者ロールのだれでもこれを行います。

外部 IdP を信頼するアプリを構成した後、Microsoft ID プロバイダーからアクセス トークンを取得し、Azure AD の保護されたリソースにアクセスするためにソフトウェアワークロードを構成します。

## <a name="prerequisites"></a>前提条件
Azure で[アプリの登録を作成します。](quickstart-register-app.md)  外部ソフトウェア ワークロードの対象となる Azure リソースへのアクセス権をアプリに付与します。  

次の手順で必要になる、(アプリケーション (クライアント) ID ではなく) アプリのオブジェクト ID を検索します。  アプリのオブジェクト ID は、Azure portal で確認できます。  Azure portal で [登録済みアプリケーション](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) の一覧にアクセスし、アプリの登録を選択します。  [**の概要**-> **要点**] で、**オブジェクト ID** を見つけます。

次の手順で必要な外部 IdP とソフトウェア ワークロードの情報を取得します。

## <a name="configure-a-federated-identity-credential-using-microsoft-graph"></a>Microsoft Graph を使用してフェデレーション ID 資格情報を構成する

[Azure Cloud Shell](https://portal.azure.com/#cloudshell/)を起動し、テナントにサインインします。

### <a name="create-a-federated-identity-credential"></a>フェデレーションID資格情報を作成する

次のコマンドを実行して、アプリケーション (アプリのオブジェクト ID により指定されています) の[新しいフェデレーション ID 資格情報を作成](/graph/api/application-post-federatedidentitycredentials?view=graph-rest-beta&preserve-view=true)します。  

*issuer* と *subject* は、信頼関係を設定するために必要な重要な情報です。 *issuer* は、外部 ID プロバイダーの URL であり、交換される外部トークンの要求と `issuer` 一致する必要があります。  *subject* は、外部 ソフトウェア　ワークロードの識別子であり、交換される外部トークンの要求と `sub` (`subject`)  一致する必要があります。 *subject* には固定形式はありません。各 IdP は独自の GUID (場合によってはコロンで区切られた識別子、もしくは任意の文字列) が使用されます。  `issuer` と `subject` の組合せ、アプリで一意である必要があります。  外部ソフトウェア ワークロードが Microsoft ID プラットフォーム に対してアクセス トークンの外部トークンの交換を要求すると、フェデレーション ID 資格情報の *subject* と *subject* の値が、外部トークンで指定された`issuer`および`subject`要求に対してチェックされます。 その検証チェックに合格するとMicrosoft ID プラットフォームは外部ソフトウェア ワークロードにアクセス トークンを発行します。

*audiences* には、外部トークンに表示できる対象ユーザーが一覧表示されます。  このフィールドは必須であり、既定値は "api://AzureADTokenExchange" です。 これは、Microsoft ID プラットフォームが受信トークンで受け入れる必要 がある`aud`要求を示します。  この値は、外部プロバイダー内の Azure AD を表し、ID プロバイダー全体に固定値はありません。このトークンの対象ユーザーとして機能するために、IdP に新しいアプリケーション登録を作成する必要がある場合があります。

*name* はフェデレーション ID 資格情報の一意識別子であり、文字数は 120 文字に制限され、URL に対応している必要があります。 作成後は変更できません。

*description* は、フェデレーション ID 資格情報の検証されていないユーザー指定の説明です。 

次のコマンドを実行して、アプリケーション (アプリのオブジェクト ID により指定されています) の[新しいフェデレーション ID 資格情報を作成](/graph/api/application-post-federatedidentitycredentials?view=graph-rest-beta&preserve-view=true)します。  *issuer* は、GitHub を外部トークンの発行者として識別します。  *subject* は、GitHub 組織、リポジトリ、GitHub Actions ワークフローの環境を識別します。  GitHub Actions ワークフローがアクセス トークンの GitHub トークンを交換するために Microsoft ID プラットフォーム を要求すると、フェデレーション ID 資格情報の値が、指定された GitHub トークンに対してチェックされます。

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

> [!IMPORTANT]
> 誤って *サブジェクト設* 定に正しくない外部ワークロード情報を追加すると、フェデレーション ID 資格情報がエラーなしで正常に作成されます。  トークン交換が失敗するまで、エラーは明らかではありません。

### <a name="list-federated-identity-credentials-on-an-app"></a>アプリでフェデレーション ID 資格情報を一覧表示する

次のコマンドを実行して、アプリケーション (アプリのオブジェクト ID により指定されています) の [フェデレーション ID 資格情報を一覧表示](/graph/api/application-list-federatedidentitycredentials?view=graph-rest-beta&preserve-view=true) します。

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

### <a name="delete-a-federated-identity-credential"></a>フェデレーション ID 資格情報を削除する

次のコマンドを実行して、アプリケーション (アプリのオブジェクト ID により指定されています) から[フェデレーション ID 資格情報を削除](/graph/api/application-list-federatedidentitycredentials?view=graph-rest-beta&preserve-view=true)します。

```azurecli
az rest -m DELETE  -u 'https://graph.microsoft.com/beta/applications/f6475511-fd81-4965-a00e-41e7792b7b9c/federatedIdentityCredentials/51ecf9c3-35fc-4519-a28a-8c27c6178bca' 

```

## <a name="next-steps"></a>次の手順
詳細については、「この Azure AD [OAuth 2.0 クライアント資格情報付与](v2-oauth2-client-creds-grant-flow.md#third-case-access-token-request-with-a-federated-credential)と、別の IdP によって発行されたクライアント アサーションを使用してトークンを取得する方法」をお読みください。