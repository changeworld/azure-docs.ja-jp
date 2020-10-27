---
title: Media Services でのカスタマー マネージド キーまたは Bring Your Own Key (BYOK)
description: これは、Media Services ストレージ アカウントでカスタマー マネージド キーを使用するためのチュートリアルです
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: tutorial
ms.date: 10/18/2020
ms.openlocfilehash: c26da9d888bbcdf72c052fa4bd7fcdf443a2d8f7
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/21/2020
ms.locfileid: "92325862"
---
# <a name="tutorial-use-customer-managed-keys-or-bring-your-own-key-byok-with-media-services"></a>チュートリアル:Media Services でのカスタマー マネージド キーまたは Bring Your Own Key (BYOK) の使用

2020-05-01 API では、システム マネージド ID が割り当てられた Media Services アカウントでカスタマー マネージド RSA キーを使用できます。  このチュートリアルには、REST 要求を Azure サービスに送信するための Postman コレクションと環境が含まれています。  使用するサービス:

- Postman 用の Azure Active Directory アプリケーションの登録
- Microsoft Graph API
- Azure Storage
- Azure Key Vault
- Media Services

このチュートリアルでは、Postman を使用して以下を行う方法を学習します。

> [!div class="checklist"]
> * Azure サービスで使用するためのトークンを取得する。
> * リソース グループとストレージ アカウントを作成する。
> * システム マネージド ID が割り当てられた Media Services アカウントを作成する
> * ストレージ アカウントで使用する RSA (カスタマー マネージド) キーを格納するためのキー コンテナーを作成する。
> * ストレージ アカウントで RSA キーを使用するように Media Services アカウントを更新する。
> * Postman で変数を使用する。

Azure サブスクリプションを持っていない場合は、[無料試用版アカウントを作成できます](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>前提条件

- 適切なアクセス許可を付与したサービス プリンシパルを登録します。
- [Postman](https://www.postman.com) をインストールします。
- 「[Azure Samples: media-services-customer-managed-keys-byok](https://github.com/Azure-Samples/media-services-customer-managed-keys-byok)」から、このチュートリアル用の Postman コレクションをダウンロードします

### <a name="register-a-service-principal-with-the-needed-permissions"></a>必要なアクセス許可を付与したサービス プリンシパルを登録する

[サービス プリンシパルを作成します](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)。  サービス プリンシパル シークレットの取得については、[オプション 2](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#authentication-two-options) を参照してください。  シークレットはどこかにメモしてください。ポータル内のシークレット ページを離れると、アクセスできなくなります。

このチュートリアルのタスクを実行するには、サービス プリンシパルに次のアクセス許可が必要です。

![サービス プリンシパルに必要なアクセス許可](./media/tutorial-byok/service-principal-permissions-1.png)

### <a name="install-postman"></a>Postman のインストール

Azure で使用するために Postman をまだインストールしていない場合は、[postman.com](https://www.postman.com/) から入手できます。

### <a name="download-and-import-the-collection"></a>コレクションをダウンロードしてインポートする

「[Azure Samples: media-services-customer-managed-keys-byok](https://github.com/Azure-Samples/media-services-customer-managed-keys-byok)」から、このチュートリアル用の Postman コレクションをダウンロードします

## <a name="installation-of-collection-and-environment"></a>コレクションと環境のインストール

1. Postman を実行します。
1. **[インポート]** を選択します。
1. **[Upload files]\(ファイルのアップロード\)** を選択します。
1. コレクションと環境ファイルを保存した場所に移動します。
1. コレクションと環境ファイルを選択します。
1. **[Open (開く)]** を選択します。  (ファイルが、API としてではなく、コレクションとしてインポートされることを示す警告が表示されます。  これは問題はありません。  それが目的です)。
1. このコレクションがコレクションに BYOK として表示されるようになります。
1. 環境変数が環境に表示されるようになります。

### <a name="understand-the-rest-api-requests-in-the-collection"></a>コレクション内の REST API 要求を理解する

コレクションは、次の REST API 要求を提供します。 要求は、指定された順序で送信する必要があります。そのほとんどには、順序が次の (またはそれ以降の) 要求のためにグローバル変数を動的に作成するテスト スクリプトが含まれているためです。 グローバル変数を手動で作成する必要はありません。

Postman では、これらの変数は `{{ }}` ブラケットで囲まれています。  たとえば、「 `{{bearerToken}}` 」のように入力します。

1. AAD トークンを取得する - このテストでは、グローバル変数 *bearerToken* を設定します
2. Graph トークンを取得する - このテストでは、グローバル変数 *graphToken* を設定します
3. サービス プリンシパルの詳細を取得する - このテストでは、グローバル変数 *servicePrincipalObjectId* を設定します
4. ストレージ アカウントを作成する - このテストでは、グローバル変数 *storageAccountId* を設定します
5. システム マネージド ID が割り当てられた Media Services アカウントを作成する - このテストでは、グローバル変数 *principalId* を設定します
6. キー コンテナーを作成し、サービス プリンシパルへのアクセスを許可する - このテストでは、グローバル変数 *keyVaultId* を設定します
7. Key Vault トークンを取得する - このテストでは、グローバル変数 *keyVaultToken* を設定します
8. キー コンテナー内に RSA キーを作成する - このテストでは、グローバル変数 *keyId* を設定します
9. ストレージ アカウントでキーを使用するように Media Services アカウントを更新する - この要求のテスト スクリプトはありません。

## <a name="define-environment-variables"></a>環境変数を定義する

1. 環境ドロップダウン リストを選択して、ダウンロードした環境に切り替えます。
1. Postman で環境変数を確立します。 これらも変数として `{{ }}` ブラケットで囲まれています。  たとえば、「 `{{tenantId}}` 」のように入力します。

    * *tenantId* = ご自身のテナント ID
    * *servicePrincipalId* = ポータル、CLI など、任意の方法で確立したサービス プリンシパルの ID。
    * *servicePrincipalSecret* = サービス プリンシパル用に作成したシークレット
    * *subscription* = ご自身のサブスクリプション ID
    * *storageName* = ご自身のストレージに付与する名前
    * *accountName* = 使用する Media Service アカウントの名前
    * *keyVaultName* = 使用するキー コンテナーの名前
    * *resourceLocation* = centralus (またはご利用のリソースを配置する場所。  このコレクションは、centralus でのみテストされています)。
    * *resourceGroup* = リソース グループ名

    次の変数は Azure リソースを操作するための標準であるため、変更する必要はありません。

    * *armResource* = `https://management.core.windows.net`
    * *graphResource* = `https://graph.windows.net/`
    * *keyVaultResource* = `https://vault.azure.net`
    * *armEndpoint* = `management.azure.com`
    * *graphEndpoint* = `graph.windows.net`
    * *aadEndpoint* = `login.microsoftonline.com`
    * *keyVaultDomainSuffix* = `vault.azure.net`

## <a name="send-the-requests"></a>要求を送信する

ご自身の環境変数を定義したら、上記の順序で一度に 1 つずつ要求を実行するか、Postman のランナーを使用してコレクションを実行することができます。

## <a name="change-the-key"></a>キーを変更する

Media Services は、キーがいつ変更されたかを自動的に検出します。  これをテストするには、同じキーの別のキー バージョンを作成します。 Media Services は、15 分以内にこのキーを検出します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

作成したリソースを今後使用せず、 **課金が継続されないようにする** 場合は、削除してください。

## <a name="next-steps"></a>次のステップ

次の記事に進み、以下の方法を学習してください。
> [!div class="nextstepaction"]
> [REST を使用して、リモート ファイルを URL に基づいてエンコードし、ビデオをストリーム配信する](stream-files-tutorial-with-rest.md)
