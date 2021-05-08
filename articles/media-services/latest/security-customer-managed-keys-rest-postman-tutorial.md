---
title: カスタマー マネージド キーまたは BYOK REST API を使用する
description: このチュートリアルでは、カスタマー マネージド キーまたは Bring Your Own Key (BYOK) を Azure Media Services ストレージ アカウントで使用します。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: tutorial
ms.date: 10/18/2020
ms.openlocfilehash: c8a5b682e2ac4879d2181bdb069cf554bad512d9
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2021
ms.locfileid: "106281111"
---
# <a name="tutorial-use-customer-managed-keys-or-byok-with-media-services-rest-api"></a>チュートリアル:Media Services REST API でカスタマー マネージド キーまたは BYOK を使用する

2020-05-01 API を使用すると、システム マネージド ID が割り当てられた Azure Media Services アカウントでカスタマー マネージド RSA キーを使用できます。このチュートリアルには、REST 要求を Azure サービスに送信するための Postman コレクションと環境が含まれています。 次のサービスが使用されます。

- Postman 用の Azure Active Directory (Azure AD) アプリケーションの登録
- Microsoft Graph API
- Azure Storage
- Azure Key Vault
- Azure Media Services

このチュートリアルでは、Postman を使用して以下を行う方法を学習します。

> [!div class="checklist"]
> - Azure サービスで使用するためのトークンを取得する。
> - リソース グループとストレージ アカウントを作成する。
> - システム マネージド ID が割り当てられた Media Services アカウントを作成する。
> - カスタマー マネージド RSA キーを格納するためのキー コンテナーを作成する。
> - ストレージ アカウントで RSA キーを使用するように Media Services アカウントを更新する。
> - Postman で変数を使用する。

Azure サブスクリプションを持っていない場合は、[無料試用版アカウントを作成できます](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>前提条件

1. 適切なアクセス許可を付与したサービス プリンシパルを登録します。
1. [Postman](https://www.postman.com) をインストールします。
1. 「[Azure Samples: media-services-customer-managed-keys-byok](https://github.com/Azure-Samples/media-services-customer-managed-keys-byok)」から、このチュートリアル用の Postman コレクションをダウンロードします。

### <a name="register-a-service-principal-with-the-needed-permissions"></a>必要なアクセス許可を付与したサービス プリンシパルを登録する

1. [サービス プリンシパルを作成します](../../active-directory/develop/howto-create-service-principal-portal.md)。
1. 「[オプション 2: 新しいアプリケーション シークレットを作成する](../../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options)」に移動して、サービス プリンシパル シークレットを取得します。

   > [!IMPORTANT]
   >後で使用するためにシークレットをコピーして保存します。 ポータルでシークレット ページから離れた後に、シークレットにアクセスすることはできません。

1. 次のスクリーンショットに示すように、サービス プリンシパルにアクセス許可を割り当てます。

   :::image type="complex" source="./media/tutorial-byok/service-principal-permissions-1.png" alt-text="サービス プリンシパルに必要なアクセス許可を示すスクリーンショット。":::
   アクセス許可は、サービス、アクセス許可の名前、種類、説明ごとに一覧表示されます。 Azure Key Vault: user impersonation、委任済み、Azure Key Vault へのフル アクセス。 Azure サービス管理: user impersonation、委任済み、組織ユーザーとしての Azure サービス管理へのアクセス。 Azure Storage: user impersonation、委任済み、Azure Storage へのアクセス。 Media Services: user impersonation、委任済み、Media Services へのアクセス。 Microsoft Graph: User.Read、委任済み、サインインとユーザー プロファイルの読み取り。
   :::image-end:::

### <a name="install-postman"></a>Postman のインストール

Azure で使用するために Postman をまだインストールしていない場合は、[postman.com](https://www.postman.com/) から入手できます。

### <a name="download-and-import-the-collection"></a>コレクションをダウンロードしてインポートする

「[Azure Samples: media-services-customer-managed-keys-byok](https://github.com/Azure-Samples/media-services-customer-managed-keys-byok)」から、このチュートリアル用の Postman コレクションをダウンロードします。

## <a name="install-the-postman-collection-and-environment"></a>Postman コレクションと環境をインストールする

1. Postman を実行します。
1. **[インポート]** を選択します。
1. **[Upload files]\(ファイルのアップロード\)** を選択します。
1. コレクションと環境ファイルを保存した場所に移動します。
1. コレクションと環境ファイルを選択します。
1. **[Open (開く)]** を選択します。 ファイルが、API としてではなく、コレクションとしてインポートされることを示す警告が表示されます。 この警告は問題ありません。 それが目的だからです。

このコレクションがコレクションに BYOK として表示されるようになります。 環境変数もご利用の環境に表示されます。

### <a name="understand-the-rest-api-requests-in-the-collection"></a>コレクション内の REST API 要求を理解する

コレクションは、次の REST API 要求を提供します。

> [!NOTE]
>
>- 要求は、指定されたシーケンスで送信される必要があります。
>- ほとんどの要求には、シーケンス内の次の要求に対してグローバル変数を動的に作成するテスト スクリプトがあります。
>- グローバル変数を手動で作成する必要はありません。

Postman の場合、これらの変数はブラケットで囲まれています。 たとえば、「 `{{bearerToken}}` 」のように入力します。

1. Azure AD トークンを取得します。このテストによって、グローバル変数 **bearerToken** が設定されます。
2. Microsoft Graph トークンを取得します。このテストによって、グローバル変数 **graphToken** が設定されます。
3. サービス プリンシパルの詳細を取得します。このテストによって、グローバル変数 **servicePrincipalObjectId** が設定されます。
4. ストレージ アカウントを作成します。このテストによって、グローバル変数 **storageAccountId** が設定されます。
5. システム マネージド ID が割り当てられた Media Services アカウントを作成します。このテストによって、グローバル変数 **principalId** が設定されます。
6. サービス プリンシパルへのアクセス権を付与するキー コンテナーを作成します。このテストによって、グローバル変数 **keyVaultId** が設定されます。
7. キー コンテナーのトークンを取得します。このテストによって、グローバル変数 **keyVaultToken** が設定されます。
8. キー コンテナーに RSA キーを作成します。このテストによって、グローバル変数 **keyId** が設定されます。
9. ストレージ アカウントでそのキーを使用するように Media Services アカウントを更新します。この要求のテスト スクリプトはありません。

## <a name="define-environment-variables"></a>環境変数を定義する

1. 環境のドロップダウン リストを選択して、ダウンロードした環境に切り替えます。
1. Postman で環境変数を確立します。 これらも変数としてブラケットで囲まれています。 たとえば、「 `{{tenantId}}` 」のように入力します。

    - **tenantId**: テナント ID。
    - **servicePrincipalId**: ポータル、CLI など、任意の方法で確立したサービス プリンシパルの ID。
    - **servicePrincipalSecret**: サービス プリンシパル用に作成したシークレット。
    - **[サブスクリプション]** : サブスクリプション ID。
    - **storageName**: お使いのストレージに付与する名前。
    - **accountName**: 使用する Media Service アカウントの名前。
    - **keyVaultName**: 使用するキー コンテナーの名前。
    - **resourceLocation**: **CentralUs**、またはご利用のリソースを配置する場所。 このコレクションは、**CentralUs** でのみテストされています。
    - **resourceGroup**: リソース グループ名。

    次の変数は Azure リソースを操作するための標準です。 そのため、これらを変更する必要はありません。

    - **armResource**: `https://management.core.windows.net`
    - **graphResource**: `https://graph.windows.net/`
    - **keyVaultResource**: `https://vault.azure.net`
    - **armEndpoint**: `management.azure.com`
    - **graphEndpoint**: `graph.windows.net`
    - **aadEndpoint**: `login.microsoftonline.com`
    - **keyVaultDomainSuffix**: `vault.azure.net`

## <a name="send-the-requests"></a>要求を送信する

環境変数を定義した後、前のシーケンスで一度に 1 つずつ要求を実行できます。 または、Postman のランナーを使用してコレクションを実行できます。

## <a name="change-the-key"></a>キーを変更する

キーが変更されると、Media Services によって自動的に検出されます。 このプロセスをテストするには、同じキーの別のキー バージョンを作成します。 Media Services により、15 分以内にこのキーが検出されます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

作成したリソースを今後使用せず、*課金が継続されないようにする* 場合は、削除してください。

## <a name="next-steps"></a>次のステップ

次の記事に進み、以下の方法を学習してください。
> [!div class="nextstepaction"]
> [REST を使用して、リモート ファイルを URL に基づいてエンコードし、ビデオをストリーム配信する](stream-files-tutorial-with-rest.md)