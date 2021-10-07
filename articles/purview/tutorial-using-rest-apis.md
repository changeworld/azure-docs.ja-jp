---
title: Purview データ プレーンに REST API を使用する方法
description: このチュートリアルでは、Azure Purview REST API を使用して Purview の内容にアクセスする方法について説明します。
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 09/17/2021
ms.openlocfilehash: 5074e0017a5811b9418771aeef3b0883e10891c1
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129212819"
---
# <a name="tutorial-use-the-rest-apis"></a>チュートリアル:REST API を使用する

このチュートリアルでは、Azure Purview REST API の使用方法を説明します。 Azure Purview にデータを送信したり、Purview を自動プロセスに追加したり、Purview をベースにして独自のユーザー エクスペリエンスを構築したりする際には、REST API を使用することができます。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)してください。

## <a name="prerequisites"></a>前提条件

* 最初に、既存の Azure Purview アカウントが必要です。 カタログがない場合は、[Azure Purview アカウントの作成に関するクイックスタート](create-catalog-portal.md)を参照してください。

## <a name="create-a-service-principal-application"></a>サービス プリンシパル (アプリケーション) を作成する

カタログにアクセスする REST API クライアントは、サービス プリンシパル (アプリケーション) と、カタログが認識し信頼するように構成された ID とを備えている必要があります。 このサービス プリンシパルの ID が、カタログへの REST API 呼び出しを行う際に使用されます。

既存のサービス プリンシパル (アプリケーション ID) を使用した場合に、高い確率でエラーが発生しています。 したがって API の呼び出しには、新しいサービス プリンシパルを作成することをお勧めします。

新しいサービス プリンシパルを作成するには:

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ポータルで **[Azure Active Directory]** を検索して選択します。
1. **[Azure Active Directory]** ページの左ペインで **[アプリの登録]** を選択します。
1. **[新規登録]** を選択します。
1. **[アプリケーションの登録]** ページで、次のようにします。
    1. **アプリケーション** の名前 (サービス プリンシパル名) を入力します。
    1. **[この組織ディレクトリのみに含まれるアカウント ( _&lt;ご利用のテナント名&gt;_ のみ - シングル テナント)]** を選択します。
    1. **[リダイレクト URI (省略可能)]** で **[Web]** を選択し、値を入力します。 この値は有効な URI である必要はありません。
    1. **[登録]** を選択します。
1. 新しいサービス プリンシパル ページで、 **[表示名]** と **[アプリケーション (クライアント) ID]** の値をコピーして、後で使用するために保存します。

   サンプル コード内の `client_id` の値がアプリケーション ID です。

サービス プリンシパル (アプリケーション) を使用するには、そのパスワードを取得する必要があります。 その方法は次のとおりです。

1. Azure portal で **[Azure Active Directory]** を検索して選択し、左ペインから **[アプリの登録]** を選択します。
1. 一覧から自分のサービス プリンシパル (アプリケーション) を選択します。
1. 左ペインから **[証明書とシークレット]** を選択します。
1. **[新しいクライアント シークレット]** を選択します。
1. **[クライアント シークレットの追加]** ページで **説明** を入力し、 **[有効期限]** で有効期限を選択して、 **[追加]** を選択します。

   **[クライアント シークレット]** ページに表示される新しいシークレットの **[値]** 列の文字列が自分のパスワードです。 この値を保存します。

   :::image type="content" source="./media/tutorial-using-rest-apis/client-secret.png" alt-text="クライアント シークレットを示すスクリーンショット。":::

## <a name="set-up-authentication-using-service-principal"></a>サービス プリンシパルを使用して認証を設定する

サービス プリンシパルを作成したら、Purview アカウントのデータ プレーン ロールを、上記で作成したサービス プリンシパルに割り当てる必要があります。 ロールを割り当ててサービス プリンシパルと Purview アカウントの間で信頼を確立するには、下の手順に従う必要があります。

1. [Purview Studio](https://web.purview.azure.com/resource/) に移動します。
1. 左側のメニューで [Data Map]\(データ マップ\) を選択します。
1. [Collections]\(コレクション\) を選択します。
1. コレクション メニューでルート コレクションを選択します。 これはリストの一番上のコレクションで、Purview アカウントと同じ名前になります。
1. [ロールの割り当て] タブを選択します。
1. Purview でさまざまなデータ プレーンにアクセスするには、上記で作成したサービス プリンシパルに次のロールを割り当てます。
    1. カタログ データ プレーンにアクセスするための "データ キュレーター" ロール。
    1. スキャン データ プレーンにアクセスするための "データ ソース管理者" ロール。 
    1. アカウント データ プレーンにアクセスするための "コレクション管理者" ロール。
    1. メタデータ ポリシー データ プレーンにアクセスするための "コレクション管理者" ロール。

> [!Note]
> [Azure Purview のアクセス制御](./catalog-permissions.md)でデータ プレーン ロールを割り当てることができるのは、"コレクション管理者" のみです。

## <a name="get-token"></a>トークンを取得する
次の URL に POST 要求を送信して、アクセス トークンを取得できます。

https://login.microsoftonline.com/{your-tenant-id}/oauth2/token

上記の URL に次のパラメーターを渡す必要があります。

- **client_id**: Azure Active Directory に登録されたアプリケーションのクライアント ID。Purview アカウントのデータ プレーン ロールに割り当てられます。
- **client_secret**: 上記のアプリケーションに対して作成されたクライアント シークレット。
- **grant_type**: これは "client_credentials" にする必要があります。
- **resource**: これは "https://purview.azure.net" にする必要があります
 
サンプル応答トークン:

```json
    {
        "token_type": "Bearer",
        "expires_in": "86399",
        "ext_expires_in": "86399",
        "expires_on": "1621038348",
        "not_before": "1620951648",
        "resource": "https://purview.azure.net",
        "access_token": "<<access token>>"
    }
```

上記のアクセス トークンを使用して、データ プレーン API を呼び出します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [データ ソースを管理する](manage-data-sources.md)
> [Purview データ プレーン REST API](/rest/api/purview/)
