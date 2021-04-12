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
ms.openlocfilehash: d4a6e25578cd26b10b34f74a9f859d4957cc553b
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104783814"
---
# <a name="how-to-use-postman-to-send-requests-to-the-azure-digital-twins-apis"></a>Postman を使用して Azure Digital Twins API に要求を送信する方法

[Postman](https://www.getpostman.com/) は、デスクトップとプラグイン ベースの GUI に重要な HTTP 要求機能を提供する REST テスト ツールです。 これを使用して HTTP 要求を作成し、[Azure Digital Twins REST API](how-to-use-apis-sdks.md) に送信できます。

この記事では、Azure Digital Twins API を操作するために、次の手順に従って [Postman REST クライアント](https://www.getpostman.com/)を構成する方法について説明します。

1. Azure CLI を使用して、Postman で API 要求を行うために使用する [**ベアラー トークンを取得**](#get-bearer-token)します。
1. [**Postman コレクション**](#about-postman-collections)を設定し、ベアラー トークンを使用して認証するように Postman REST クライアントを構成します。 コレクションを設定するときに、次のいずれかのオプションを選択できます。
    1. Azure Digital Twins API 要求の事前構築済みコレクションを [ **[インポート]** ](#import-collection-of-azure-digital-twins-apis) します。
    1. 独自のコレクションを最初から [ **[作成]** ](#create-your-own-collection) します。
1. 構成されたコレクションに [ **[要求の追加]** ](#add-an-individual-request) を行い、Azure Digital Twins API に送信します。

Azure Digital Twins には、**データ プレーン** と **コントロール プレーン** で使用できる 2 つの API セットがあります。 これらの API セットの相違点の詳細については、「[*方法: Azure Digital Twins の API および SDK を使用する*](how-to-use-apis-sdks.md)」を参照してください。 この記事には、両方の API セットに関する情報が含まれています。

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

2. 次に、[az account get-access-token](/cli/azure/account#az_account_get_access_token) コマンドを使用して、Azure Digital Twins サービスにアクセスできるベアラー トークンを取得します。 このコマンドでは、Azure Digital Twins リソースにアクセスできるアクセス トークンを取得するために、Azure Digital Twins サービス エンドポイントのリソース ID を渡します。 

    トークンに必要なコンテキストは、使用している API のセットによって異なります。そのため、次のタブを使用して、[データ プレーン](how-to-use-apis-sdks.md#overview-data-plane-apis)と[コントロール プレーン](how-to-use-apis-sdks.md#overview-control-plane-apis)の API を選択します。

    # <a name="data-plane"></a>[データ プレーン](#tab/data-plane)
    
    **データ プレーン** API で使用するトークンを取得するには、トークン コンテキストに対して静的な値 `0b07f429-9f4b-4714-9392-cc5e8e80c8b0` を使用します。 これは、Azure Digital Twins サービス エンドポイントのリソース ID です。
    
    ```azurecli-interactive
    az account get-access-token --resource 0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```
    
    # <a name="control-plane"></a>[コントロール プレーン](#tab/control-plane)
    
    **コントロール プレーン** API で使用するトークンを取得するには、トークン コンテキストに対して `https://management.azure.com/` の値を使用します。
    
    ```azurecli-interactive
    az account get-access-token --resource https://management.azure.com/
    ```
    ---


3. 結果の `accessToken` の値をコピーし、次のセクションで使用するために保存します。 これが、要求を許可するために Postman に提供する **トークン値** です。

    :::image type="content" source="media/how-to-use-postman/console-access-token.png" alt-text="az account get-access-token コマンドの結果を示すコンソールのスクリーンショット。accessToken フィールドとのそのサンプル値は強調表示されています。":::

>[!TIP]
>このトークンは少なくとも 5 分間かつ最大 60 分間有効です。 現在のトークンに割り当てられた時間が足りなくなった場合は、このセクションの手順を繰り返して新しいものを取得できます。

次に、このトークンを使用して Azure Digital Twins に API 要求を行うように Postman を設定します。

## <a name="about-postman-collections"></a>Postman コレクションについて

Postman の要求は **コレクション** (要求のグループ) に保存されます。 コレクションを作成して要求をグループ化すると、多くの要求に共通の設定を一度に適用できます。 これにより、Azure Digital Twins API に対して複数の要求を作成することを予定している場合、承認が大幅に簡素化されます。これは、コレクション全体に対してこれらの詳細を 1 回だけ構成すれば済むためです。

Azure Digital Twins を使用する場合は、[すべての Azure Digital Twins 要求の事前構築済みコレクション](#import-collection-of-azure-digital-twins-apis)をインポートすることで開始できます。 この方法は、この API を調査しており、要求の例を使用してプロジェクトをすばやく設定するために使用できます。

または、最初から開始することにし、[独自の空のコレクションを作成](#create-your-own-collection)して必要な API のみを呼び出す個々の要求を設定することもできます。 

以降のセクションでは、これらの両方の手順について説明します。 この記事の以降のセクションは、ローカルの Postman アプリケーション内で実行するため、お使いのコンピューターで Postman アプリケーションを開いてください。

## <a name="import-collection-of-azure-digital-twins-apis"></a>Azure Digital Twins API のコレクションをインポートする

Postman で Azure Digital Twins の使用を開始する簡単な方法は、Azure Digital Twins API 要求の事前構築済みコレクションをインポートすることです。

### <a name="download-the-collection-file"></a>コレクション ファイルをダウンロードする

API セットをインポートするための最初の手順は、コレクションをダウンロードすることです。 選択したデータ プレーンまたはコントロール プレーンの下にあるタブを選択すると、事前構築済みコレクションのオプションが表示されます。

# <a name="data-plane"></a>[データ プレーン](#tab/data-plane)

現在、次の中から選択できる Azure Digital Twins データ プレーン コレクションが現在 2 つあります。
* [**Azure Digital Twins Postman コレクション**](https://github.com/microsoft/azure-digital-twins-postman-samples): このコレクションは、Postman で Azure Digital Twins の使用を開始するための簡単なベースになります。 要求にはサンプル データが含まれているので、必要最小限の編集で実行できます。 サンプル情報を含むキー API 要求の使いやすいセットが必要な場合は、このコレクションを選択します。
    - コレクションを検索するには、リポジトリ リンクに移動し、*postman_collection.json* という名前のファイルを開きます。
* **[Azure Digital Twins データ プレーン Swagger](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins)** : このリポジトリには、Azure Digital Twins API セットの完全な Swagger ファイルが含まれています。このファイルをダウンロードして、コレクションとして Postman にインポートできます。 これにより、すべての API 要求の包括的なセットが提供されますが、サンプル データではなく空のデータ本文が用意されています。 すべての API 呼び出しにアクセスし、自分ですべてのデータを入力する場合は、このコレクションを選択します。
    - コレクションを検索するには、リポジトリ リンクに移動し、最新の仕様バージョンのフォルダーを選択します。 ここで、*digitaltwins.json* という名前のファイルを開きます。

# <a name="control-plane"></a>[コントロール プレーン](#tab/control-plane)

現在コントロール プレーンに使用できるコレクションは、[**Azure Digital Twins コントロール プレーン Swagger**](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins) です。 このリポジトリには、Azure Digital Twins API セットの完全な Swagger ファイルが含まれています。このファイルをダウンロードして、コレクションとして Postman にインポートできます。 これにより、すべての API 要求の包括的なセットが提供されます。

コレクションを検索するには、リポジトリ リンクに移動し、最新の仕様バージョンのフォルダーを選択します。 ここで、*digitaltwins.json* という名前のファイルを開きます。

---

選択したコレクションをコンピューターにダウンロードして、Postman にインポートできるようにするには、次の手順を実行します。
1. 上のリンクを使用して、ブラウザーで GitHub のコレクション ファイルを開きます。
1. **[Raw]\(未加工\)** ボタンを選択して、ファイルの未加工のテキストを開きます。
    :::image type="content" source="media/how-to-use-postman/swagger-raw.png" alt-text="GitHub 内のデータ プレーン digitaltwins.json ファイルのスクリーンショット。[Raw]\(未加工\) ボタンの周辺が強調表示されています。" lightbox="media/how-to-use-postman/swagger-raw.png":::
1. ウィンドウからテキストをコピーして、コンピューター上の新しいファイルに貼り付けます。
1. ファイルに *.json* という拡張子を付けて保存します (ファイル名は、後でファイルを見つけられる限り、どのような名前にしてもかまいません)。

### <a name="import-the-collection"></a>コレクションをインポートする

次に、コレクションを Postman にインポートします。

1. メインの Postman ウィンドウで、 **[インポート]** ボタンを選択します。
    :::image type="content" source="media/how-to-use-postman/postman-import-collection.png" alt-text="新しく開かれた Postman ウィンドウのスクリーンショット。[インポート] ボタンが強調表示されています。" lightbox="media/how-to-use-postman/postman-import-collection.png":::

1. 次に表示されるインポート ウィンドウで、 **[ファイルをアップロードする]** を選択し、前の手順で作成したコンピューター上のコレクション ファイルに移動します。 [開く] を選択します。
1. **[インポート]** ボタンを選択して確認します。

    :::image type="content" source="media/how-to-use-postman/postman-import-collection-2.png" alt-text="Postman の [インポート] ウィンドウのスクリーンショット。コレクションとしてインポートするファイルと [インポート] ボタンが表示されています。":::

新しくインポートされたコレクションが、メインの Postman ビューの [コレクション] タブで確認できるようになりました。

:::image type="content" source="media/how-to-use-postman/postman-post-collection-imported.png" alt-text="Postman のメイン ウィンドウのスクリーンショット。新しくインポートされたコレクションが [コレクション] タブで強調表示されています。" lightbox="media/how-to-use-postman/postman-post-collection-imported.png":::

次のセクションに進んで、承認のためにベアラー トークンをコレクションに追加し、それを Azure Digital Twins インスタンスに接続します。

### <a name="configure-authorization"></a>承認を構成する

次に、アクセスの詳細を構成するために、作成したコレクションを編集します。 作成したコレクションを強調表示し、 **[View more actions]\(その他のアクションを表示\)** アイコンを選択してメニューをプルします。 **[編集]** を選択します。

:::image type="content" source="media/how-to-use-postman/postman-edit-collection.png" alt-text="Postman のスクリーンショット。インポートされたコレクションの [View more actions]\(その他のアクションを表示\) アイコンが強調表示され、[編集] がオプションで強調表示されます。" lightbox="media/how-to-use-postman/postman-edit-collection.png":::

承認のためにベアラー トークンをコレクションに追加するには、次の手順に従います。 ここでは、「[ベアラー トークンを取得する](#get-bearer-token)」セクションで収集した **トークンの値** を、コレクション内のすべての API 要求に使用するために使用します。

1. コレクションの編集ダイアログで、 **[承認]** タブが表示されていることを確認します。 

    :::image type="content" source="media/how-to-use-postman/postman-authorization-imported.png" alt-text="Postman のインポートされたコレクションの編集ダイアログのスクリーンショット。[承認] タブが表示されています。" lightbox="media/how-to-use-postman/postman-authorization-imported.png":::

1. [種類] を **[OAuth 2.0]** に設定し、[アクセス トークン] ボックスにアクセス トークンを貼り付け、 **[保存]** を選択します。

    :::image type="content" source="media/how-to-use-postman/postman-paste-token-imported.png" alt-text="Postman のインポートされたコレクションの編集ダイアログの [承認] タブのスクリーンショット。種類は &quot;OAuth 2.0&quot; であり、[アクセス トークン] ボックスは強調表示されています。" lightbox="media/how-to-use-postman/postman-paste-token-imported.png":::

### <a name="additional-configuration"></a>追加構成

# <a name="data-plane"></a>[データ プレーン](#tab/data-plane)

[データ プレーン](how-to-use-apis-sdks.md#overview-data-plane-apis) コレクションを作成している場合は、コレクションに用意されているいくつかの **変数** を設定すると、コレクションを Azure Digital Twins リソースに簡単に接続させることができます。 コレクション内の多くの要求に同じ値 (Azure Digital Twins インスタンスのホスト名など) が必要な場合は、コレクション内のすべての要求に適用される変数に値を格納できます。 Azure Digital Twins の両方のダウンロード可能なコレクションには、コレクション レベルで設定できる事前に作成された変数が付属しています。

1. コレクションの編集ダイアログで、 **[変数]** タブに移動します。

1. [ *[前提条件]*](#prerequisites) セクションから、インスタンスの **ホスト名** を使用して、関連する変数の [CURRENT VALUE] フィールドを設定します。 **[保存]** を選択します。

    :::image type="content" source="media/how-to-use-postman/postman-variables-imported.png" alt-text="[変数] タブが表示されている、Postman のインポートされたコレクションの編集ダイアログのスクリーンショット。[CURRENT VALUE] フィールドが強調表示されています。" lightbox="media/how-to-use-postman/postman-variables-imported.png":::

1. コレクションに追加の変数が含まれている場合は、それらの値も入力して保存します。

上記の手順を完了すると、コレクションの構成が完了します。 必要に応じて、コレクションの編集タブを閉じることができます。

# <a name="control-plane"></a>[コントロール プレーン](#tab/control-plane)

[コントロール プレーン](how-to-use-apis-sdks.md#overview-control-plane-apis) コレクションを作成している場合は、これでコレクションを構成するために必要なすべての処理が完了しました。 必要に応じてコレクションの編集タブを閉じ、次のセクションに進みます。

--- 

### <a name="explore-requests"></a>要求を調べる

次に、Azure Digital Twins API コレクション内の要求を調べます。 コレクションを展開して、事前に作成された要求 (操作のカテゴリ別に並べ替えられたもの) を表示することができます。 

異なる要求には、インスタンスとそのデータに関する異なる情報が必要になります。 特定の要求を作成するために必要なすべての情報を表示するには、「[Azure Digital Twins の REST API リファレンス ドキュメント](/rest/api/azure-digitaltwins/)」で要求の詳細を参照してください。

Postman コレクションの要求の詳細を編集するには、次の手順を実行します。

1. 編集可能な詳細情報を取得するには、一覧から選択します。 

1. **[パス変数]** の **[パラメーター]** タブに一覧表示されている変数の値を入力します。

    :::image type="content" source="media/how-to-use-postman/postman-request-details-imported.png" alt-text="Postman のスクリーンショット。コレクションは、要求を表示するために展開されます。[パス変数] セクションが、要求の詳細で強調表示されます。" lightbox="media/how-to-use-postman/postman-request-details-imported.png":::

1. 各タブで、必要な **[ヘッダー]** または **[Body]** の詳細を指定します。

必要なすべての詳細情報が提供されたら、 **[送信]** ボタンを使用して要求を実行できます。

また、次の「[*個々の要求を追加する*](#add-an-individual-request)」セクションで説明されているプロセスを使用して、独自の要求をコレクションに追加することもできます。

## <a name="create-your-own-collection"></a>独自のコレクションを作成する

すべての Azure Digital Twins API の既存のコレクションをインポートする代わりに、独自のコレクションを最初から作成することもできます。 その後、「[Azure Digital Twins の REST API リファレンス ドキュメント](/rest/api/azure-digitaltwins/)」を使用して、個々の要求を設定できます。

### <a name="create-a-postman-collection"></a>Postman Collection の作成

1. コレクションを作成するには、メインの postman ウィンドウで **[新規]** ボタンを選択します。

    :::image type="content" source="media/how-to-use-postman/postman-new.png" alt-text="Postman のメイン ウィンドウのスクリーンショット。[新規] ボタンが強調表示されています。" lightbox="media/how-to-use-postman/postman-new.png":::

    **[コレクション]** の種類を選択します。

    :::image type="content" source="media/how-to-use-postman/postman-new-collection-2.png" alt-text="Postman の [新規作成] ダイアログのスクリーンショット。[コレクション] オプションが強調表示されています。":::

1. 新しいコレクションの詳細を入力するためのタブが開きます。 コレクションの既定の名前 ("**新しいコレクション**") の横にある [編集] アイコンを選択して、独自に選択した名前に置き換えます。 

    :::image type="content" source="media/how-to-use-postman/postman-new-collection-3.png" alt-text="Postman の新しいコレクションの編集ダイアログのスクリーンショット。&quot;新しいコレクション&quot; という名前の横にある [編集] アイコンが強調表示されています。" lightbox="media/how-to-use-postman/postman-new-collection-3.png":::

続けて次のセクションに進んで、認証のためのベアラー トークンをコレクションに追加します。

### <a name="configure-authorization"></a>承認を構成する

承認のためにベアラー トークンをコレクションに追加するには、次の手順に従います。 ここでは、「[ベアラー トークンを取得する](#get-bearer-token)」セクションで収集した **トークンの値** を、コレクション内のすべての API 要求に使用するために使用します。

1. 新しいコレクションの編集ダイアログで、 **[承認]** タブに移動します。

    :::image type="content" source="media/how-to-use-postman/postman-authorization-custom.png" alt-text="[承認] タブが表示された、Postman の新しいコレクションの編集ダイアログのスクリーンショット。" lightbox="media/how-to-use-postman/postman-authorization-custom.png":::

1. [種類] を **[OAuth 2.0]** に設定し、[アクセス トークン] ボックスにアクセス トークンを貼り付け、 **[保存]** を選択します。

    :::image type="content" source="media/how-to-use-postman/postman-paste-token-custom.png" alt-text="Postman の [承認] タブの新しいコレクションの編集ダイアログのスクリーンショット。種類は &quot;OAuth 2.0&quot; であり、[アクセス トークン] ボックスは強調表示されています。" lightbox="media/how-to-use-postman/postman-paste-token-custom.png":::

上記の手順を完了すると、コレクションの構成が完了します。 必要に応じて、新しいコレクションの編集タブを閉じることができます。

新しくインポートされたコレクションが、Postman のメイン ビューの [コレクション] タブで確認できるようになりました。

:::image type="content" source="media/how-to-use-postman/postman-post-collection-custom.png" alt-text="Postman のメイン ウィンドウのスクリーンショット。新しく作成したカスタム コレクションが [コレクション] タブで強調表示されています。" lightbox="media/how-to-use-postman/postman-post-collection-custom.png":::

## <a name="add-an-individual-request"></a>個々の要求を追加する

コレクションが設定されたので、独自の要求を Azure Digital Twisn API に追加できます。

1. 要求を作成するには、 **[新規]** ボタンを再度使用します。

    :::image type="content" source="media/how-to-use-postman/postman-new.png" alt-text="Postman のメイン ウィンドウのスクリーンショット。[新規] ボタンが強調表示されています。" lightbox="media/how-to-use-postman/postman-new.png":::

    **[要求]** の種類を選択します。

    :::image type="content" source="media/how-to-use-postman/postman-new-request-2.png" alt-text="Postman の [新規作成] ダイアログのスクリーンショット。[要求] オプションが強調表示されています。":::

1. この操作により、[要求の保存] ウィンドウが開き、そこで要求の名前を入力し、必要に応じて説明を入力して、保存先のコレクションを選択できます。 詳細を入力し、前の手順で作成したコレクションに要求を保存します。

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-use-postman/postman-save-request.png" alt-text="説明されているフィールドを示す Postman の [要求の保存] ウィンドウのスクリーンショット。[Save to Azure Digital Twins collection]\(Azure Digital Twins コレクションに保存\) ボタンが強調表示されています。":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

これで、コレクションに要求が表示され、それを選択して編集可能な詳細を取得できるようになりました。

:::image type="content" source="media/how-to-use-postman/postman-request-details-custom.png" alt-text="Postman のスクリーンショット。Azure Digital Twins コレクションが展開され、要求の詳細が表示されます。" lightbox="media/how-to-use-postman/postman-request-details-custom.png":::

### <a name="set-request-details"></a>要求の詳細を設定する

Azure Digital Twins API のいずれかに対する Postman 要求を行うには、API の URL と、どのような詳細が必要かについての情報が必要です。 この情報については、[Azure Digital Twins REST API のリファレンス ドキュメント](/rest/api/azure-digitaltwins/)を参照してください。

クエリの例に進み、この記事ではクエリ API (およびその[リファレンス ドキュメント](/rest/api/digital-twins/dataplane/query/querytwins)) を使用して、インスタンス内のすべてのデジタル ツインを照会します。

1. リファレンス ドキュメントから要求 URL と種類を取得します。 クエリ API の場合、現在これは *POST `https://digitaltwins-hostname/query?api-version=2020-10-31`* です。
1. Postman で要求の種類を設定し、要求 URL を入力します。必要に応じて URL のプレースホルダーに入力します。 ここでは、「[*前提条件*](#prerequisites)」セクションにあるインスタンスの **ホスト名** を使用します。
    
   :::image type="content" source="media/how-to-use-postman/postman-request-url.png" alt-text="Postman の新しい要求の詳細のスクリーンショット。リファレンス ドキュメントからのクエリ URL が要求 URL のボックスに入力されています。" lightbox="media/how-to-use-postman/postman-request-url.png":::
    
1. **[Params]\(パラメーター\)** タブで、要求に対して表示されているパラメーターが、リファレンス ドキュメントに記載されているものと一致していることを確認します。 Postman でのこの要求には、前の手順で要求 URL を入力したときに、`api-version` パラメーターが自動的に入力されました。 クエリ API については、これが唯一の必須パラメーターであるため、この手順は完了です。
1. **[Authorization]\(認証\)** タブで、 [Type]\(種類\) を **[Inherit auth from parent]\(親から認証を継承\)** に設定します。 これは、この要求で、コレクション全体に対して以前に設定した承認が使用されることを示します。
1. **[Header]\(ヘッダー\)** タブに表示されている要求のヘッダーが、リファレンス ドキュメントに記載されているものと一致していることを確認します。 この要求には複数のヘッダーが自動的に入力されています。 クエリ API の場合、ヘッダー オプションはどれも必要ないため、この手順は完了です。
1. **[Body]\(本文\)** タブに表示されている要求の本文が、リファレンス ドキュメントに記載されているニーズに一致していることを確認します。 クエリ API には、クエリ テキストを指定する JSON 本文が必要です。 この要求の本文の例を次に示します。インスタンス内のすべてのデジタル ツインを照会しています。

   :::image type="content" source="media/how-to-use-postman/postman-request-body.png" alt-text="Postman の新しい要求の詳細に [Body] タブがあるスクリーンショット。それには、&quot;SELECT * FROM DIGITALTWINS&quot; というクエリを含む生の JSON 本文が含まれています。" lightbox="media/how-to-use-postman/postman-request-body.png":::

   Azure Digital Twins クエリの作成の詳細については、[*ツイン グラフにクエリを実行する*](how-to-query-graph.md)方法に関する記事を参照してください。

1. 要求の種類に応じて必要になる場合があるその他のフィールドについては、リファレンス ドキュメントを参照してください。 クエリ API については、Postman 要求ですべての要件が満たされたので、この手順は完了です。
1. **[Send]\(送信\)** ボタンを使用して、完成した要求を送信します。
   :::image type="content" source="media/how-to-use-postman/postman-request-send.png" alt-text="新しい要求の詳細を示す Postman のスクリーンショット。[送信] ボタンが強調表示されています。" lightbox="media/how-to-use-postman/postman-request-send.png":::

要求の送信後、Postman ウィンドウで要求の下に応答の詳細が表示されます。 応答の状態コードと本文のテキストを確認できます。

:::image type="content" source="media/how-to-use-postman/postman-request-response.png" alt-text="Postman で送信された要求のスクリーンショット。要求の詳細の下に、応答が表示されます。状態は 200 OK で、本文はクエリの結果を示します。" lightbox="media/how-to-use-postman/postman-request-response.png":::

また、応答と、リファレンス ドキュメントに示されている予想される応答データを比較して、結果を確認したり、発生したエラーの詳細を確認したりすることもできます。

## <a name="next-steps"></a>次のステップ

Digital Twins API の詳細については、「[*方法: Azure Digital Twins API と SDK を使用する*](how-to-use-apis-sdks.md)」を参照するか、[REST API のリファレンス ドキュメント](/rest/api/azure-digitaltwins/)を参照してください。