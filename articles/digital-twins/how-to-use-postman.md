---
title: Postman で要求を行う
titleSuffix: Azure Digital Twins
description: Azure Digital Twins API をテストするために Postman を構成して使用する方法について説明します。
ms.author: baanders
author: baanders
ms.service: digital-twins
services: digital-twins
ms.topic: how-to
ms.date: 11/10/2020
ms.openlocfilehash: c6b9cfab4256c72118a65f7fb0c8e672e2082ffe
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102199664"
---
# <a name="how-to-use-postman-to-send-requests-to-the-azure-digital-twins-apis"></a>Postman を使用して Azure Digital Twins API に要求を送信する方法

[Postman](https://www.getpostman.com/) は、デスクトップとプラグイン ベースの GUI に重要な HTTP 要求機能を提供する REST テスト ツールです。 これを使用して HTTP 要求を作成し、[Azure Digital Twins REST API](how-to-use-apis-sdks.md) に送信できます。

この記事では、Azure Digital Twins API を操作するために、次の手順に従って [Postman REST クライアント](https://www.getpostman.com/)を構成する方法について説明します。

1. [Azure CLI](/cli/azure/install-azure-cli) を使用して、Postman で API 要求を行うために使用するベアラー トークンを取得します。
1. Postman コレクションを設定し、ベアラー トークンを使用して認証するように Postman REST クライアントを構成します。
1. 構成した Postman を使用して要求を作成し、Azure Digital Twins API に送信します。

## <a name="prerequisites"></a>前提条件

Postman を使用して Azure Digital Twins API にアクセスするには、Azure Digital Twins インスタンスを設定し、Postman をダウンロードする必要があります。 このセクションの残りの部分では、これらの手順について説明します。

### <a name="set-up-azure-digital-twins-instance"></a>Azure Digital Twins インスタンスを設定する

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="download-postman"></a>Postman をダウンロードします

次に、デスクトップ バージョンの Postman クライアントをダウンロードします。 [*www.getpostman.com/apps*](https://www.getpostman.com/apps) に移動し、画面の指示に従ってアプリをダウンロードします。

## <a name="get-bearer-token"></a>ベアラー トークンを取得する

Postman と Azure Digital Twins インスタンスを設定したので、次は、Postman 要求で Azure Digital Twins API に対する認証に使用できるベアラー トークンを取得する必要があります。

このトークンを取得するには、いくつかの方法があります。 この記事では、[Azure CLI](/cli/azure/install-azure-cli) を使用して Azure アカウントにサインインし、その方法でトークンを取得します。

Azure CLI が[ローカルにインストールされている](/cli/azure/install-azure-cli)場合は、お使いのコンピューターでコマンド プロンプトを起動して次のコマンドを実行できます。
そうでない場合は、ブラウザーで [Azure Cloud Shell](https://shell.azure.com) ウィンドウを開き、そこでコマンドを実行できます。

1. まず、次のコマンドを実行して、適切な資格情報を使用して Azure にログインしていることを確認します。

    ```azurecli-interactive
    az login
    ```

1. 次に、[az account get-access-token](/cli/azure/account#az_account_get_access_token) コマンドを使用して、Azure Digital Twins サービスにアクセスできるベアラー トークンを取得します。

    ```azurecli-interactive
    az account get-access-token --resource 0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```

1. 結果の `accessToken` の値をコピーし、次のセクションで使用するために保存します。 これが、要求を認証するために Postman に提供する **トークン値** です。

    :::image type="content" source="media/how-to-use-postman/console-access-token.png" alt-text="az account get-access-token コマンドの結果を示すローカル コンソール ウィンドウの表示。結果のフィールドの 1 つは accessToken という名前で、そのサンプル値 (ey で始まる) が強調表示されています。":::

>[!TIP]
>このトークンは少なくとも 5 分間かつ最大 60 分間有効です。 現在のトークンに割り当てられた時間が足りなくなった場合は、このセクションの手順を繰り返して新しいものを取得できます。

## <a name="set-up-postman-collection-and-authorization"></a>Postman コレクションと認証を設定する

次に、API 要求を行うように Postman を設定します。
これらの手順はローカルの Postman アプリケーションで行うので、お使いのコンピューターで Postman アプリケーションを開いてください。

### <a name="create-a-postman-collection"></a>Postman Collection の作成

Postman の要求は **コレクション** (要求のグループ) に保存されます。 コレクションを作成して要求をグループ化すると、多くの要求に共通の設定を一度に適用できます。 これにより、Azure Digital Twins API に対して複数の要求を作成することを予定している場合、認証が大幅に簡素化されます。これは、コレクション全体に対して認証を 1 回だけ構成すれば済むためです。

1. コレクションを作成するには、 *[+ New Collection]\(新しいコレクション\)* ボタンをクリックします。

    :::image type="content" source="media/how-to-use-postman/postman-new-collection.png" alt-text="新しく開かれた Postman ウィンドウの表示。[New Collection]\(新しいコレクション\) ボタンが強調表示されています":::

1. その後の *[CREATE A NEW COLLECTION]\(新しいコレクションの作成\)* ウィンドウで、コレクションの **[Name]\(名前\)** と、必要に応じて **[Description]\(説明\)** を入力します。

続けて次のセクションに進んで、認証のためのベアラー トークンをコレクションに追加します。

### <a name="add-authorization-token-and-finish-collection"></a>認証トークンを追加してコレクションを完了させる

1. *[CREATE A NEW COLLECTION]\(新しいコレクションの作成\)* ダイアログで、 *[Authorization]\(認証\)* タブに移動します。ここでは、「[ベアラー トークンを取得する](#get-bearer-token)」セクションで収集した **トークンの値** を、コレクション内のすべての API 要求に使用するために配置します。

    :::image type="content" source="media/how-to-use-postman/postman-authorization.png" alt-text="[Authorization]\(認証\) タブが表示されている、Postman の [CREATE A NEW COLLECTION]\(新しいコレクションの作成\) ウィンドウ。":::

1. *[Type]\(種類\)* を _**[OAuth 2.0]**_ に設定し、 *[Access Token]\(アクセス トークン\)* ボックスにアクセス トークンを貼り付けます。

    :::image type="content" source="media/how-to-use-postman/postman-paste-token.png" alt-text="[Authorization]\(認証\) タブが表示されている、Postman の [CREATE A NEW COLLECTION]\(新しいコレクションの作成\) ウィンドウ。[OAuth 2.0] の種類が選択されており、アクセス トークンの値を貼り付けることができる [Access Token]\(アクセス トークン\) ボックスが強調表示されています。":::

1. ベアラー トークンを貼り付けた後、 *[Create]\(作成\)* をクリックしてコレクションの作成を完了します。

これで、新しいコレクションが Postman のメイン ビューの *[Collection]\(コレクション\)* に表示されるようになりました。

:::image type="content" source="media/how-to-use-postman/postman-post-collection.png" alt-text="Postman のメイン ウィンドウの表示。新しく作成したコレクションが [Collections]\(コレクション\) タブで強調表示されています。":::

## <a name="create-a-request"></a>要求を作成する

ここまでの手順を完了したら、Azure Digital Twins API への要求を作成できます。

1. 要求を作成するには、 *[+ New]\(新規\)* ボタンをクリックします。

    :::image type="content" source="media/how-to-use-postman/postman-new-request.png" alt-text="Postman のメイン ウィンドウの表示。[New]\(新規\) ボタンが強調表示されています":::

1. *[Request]\(要求\)* を選択します。

    :::image type="content" source="media/how-to-use-postman/postman-new-request-2.png" alt-text="新規作成のために選択できるオプションの表示。[Request]\(要求\) オプションが強調表示されています":::

1. この操作により、 *[Save request]\(要求の保存\)* ウィンドウが開き、そこで要求の名前を入力し、必要に応じて説明を入力して、保存先のコレクションを選択できます。 詳細を入力し、前の手順で作成したコレクションに要求を保存します。

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-use-postman/postman-save-request.png" alt-text="[Save request]\(要求の保存\) ウィンドウの表示。説明されているフィールドに入力できます。[Save to Azure Digital Twins collection]\(Azure Digital Twins コレクションに保存\) ボタンが強調表示されています":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

これで、コレクションに要求が表示され、それを選択して編集可能な詳細を取得できるようになりました。

:::image type="content" source="media/how-to-use-postman/postman-request-details.png" alt-text="Postman のメイン ウィンドウの表示。Azure Digital Twins コレクションが展開され、[Query twins]\(ツインのクエリ\) 要求が強調表示されています。要求の詳細がページの中央に表示されています。" lightbox="media/how-to-use-postman/postman-request-details.png":::

### <a name="set-request-details"></a>要求の詳細を設定する

Azure Digital Twins API のいずれかに対する Postman 要求を行うには、API の URL と、どのような詳細が必要かについての情報が必要です。 この情報については、[Azure Digital Twins REST API のリファレンス ドキュメント](/rest/api/azure-digitaltwins/)を参照してください。

クエリの例に進み、この記事ではクエリ API (およびその[リファレンス ドキュメント](/rest/api/digital-twins/dataplane/query/querytwins)) を使用して、インスタンス内のすべてのデジタル ツインを照会します。

1. リファレンス ドキュメントから要求 URL と種類を取得します。 クエリ API の場合、現在これは *POST `https://digitaltwins-hostname/query?api-version=2020-10-31`* です。
1. Postman で要求の種類を設定し、要求 URL を入力します。必要に応じて URL のプレースホルダーに入力します。 ここでは、「[*前提条件*](#prerequisites)」セクションにあるインスタンスの **ホスト名** を使用します。
    
   :::image type="content" source="media/how-to-use-postman/postman-request-url.png" alt-text="新しい要求の詳細で、リファレンス ドキュメントにあるクエリ URL が要求 URL のボックスに入力されています。" lightbox="media/how-to-use-postman/postman-request-url.png":::
    
1. *[Params]\(パラメーター\)* タブで、要求に対して表示されているパラメーターが、リファレンス ドキュメントに記載されているものと一致していることを確認します。 Postman でのこの要求には、前の手順で要求 URL を入力したときに、`api-version` パラメーターが自動的に入力されました。 クエリ API については、これが唯一の必須パラメーターであるため、この手順は完了です。
1. *[Authorization]\(認証\)* タブで、 *[Type]\(種類\)* を *[Inherit auth from parent]\(親から認証を継承\)* に設定します。 これは、この要求で、コレクション全体に対して以前に設定した認証が使用されることを示します。
1. *[Header]\(ヘッダー\)* タブに表示されている要求のヘッダーが、リファレンス ドキュメントに記載されているものと一致していることを確認します。 この要求には複数のヘッダーが自動的に入力されています。 クエリ API の場合、ヘッダー オプションはどれも必要ないため、この手順は完了です。
1. *[Body]\(本文\)* タブに表示されている要求の本文が、リファレンス ドキュメントに記載されているニーズに一致していることを確認します。 クエリ API には、クエリ テキストを指定する JSON 本文が必要です。 この要求の本文の例を次に示します。インスタンス内のすべてのデジタル ツインを照会しています。

   :::image type="content" source="media/how-to-use-postman/postman-request-body.png" alt-text="新しい要求の詳細に [Body]\(本文\) タブが表示されています。それには、&quot;SELECT * FROM DIGITALTWINS&quot; というクエリを含む生の JSON 本文が含まれています。" lightbox="media/how-to-use-postman/postman-request-body.png":::

   Azure Digital Twins クエリの作成の詳細については、[*ツイン グラフにクエリを実行する*](how-to-query-graph.md)方法に関する記事を参照してください。

1. 要求の種類に応じて必要になる場合があるその他のフィールドについては、リファレンス ドキュメントを参照してください。 クエリ API については、Postman 要求ですべての要件が満たされたので、この手順は完了です。
1. *[Send]\(送信\)* ボタンを使用して、完成した要求を送信します。
   :::image type="content" source="media/how-to-use-postman/postman-request-send.png" alt-text="新しい要求の詳細の近くにある [Send]\(送信\) ボタンが強調表示されています。" lightbox="media/how-to-use-postman/postman-request-send.png":::

要求の送信後、Postman ウィンドウで要求の下に応答の詳細が表示されます。 応答の状態コードと本文のテキストを確認できます。

:::image type="content" source="media/how-to-use-postman/postman-request-response.png" alt-text="送信した要求の詳細の下に、応答の詳細が強調表示されています。クエリによって返されたデジタル ツインを説明する &quot;200 OK&quot; という状態と本文のテキストがあります。" lightbox="media/how-to-use-postman/postman-request-response.png":::

また、応答と、リファレンス ドキュメントに示されている予想される応答データを比較して、結果を確認したり、発生したエラーの詳細を確認したりすることもできます。

## <a name="next-steps"></a>次のステップ

Digital Twins API の詳細については、「[*方法: Azure Digital Twins API と SDK を使用する*](how-to-use-apis-sdks.md)」を参照するか、[REST API のリファレンス ドキュメント](/rest/api/azure-digitaltwins/)を参照してください。