---
title: Logic Apps との統合
titleSuffix: Azure Digital Twins
description: カスタム コネクタを使用して Logic Apps を Azure Digital Twins に接続する方法について説明します
author: baanders
ms.author: baanders
ms.date: 9/1/2021
ms.topic: how-to
ms.service: digital-twins
ms.reviewer: baanders
ms.openlocfilehash: 21658ad276b16292a15fe7dc778c152104e6916e
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132718635"
---
# <a name="integrate-with-logic-apps-using-a-custom-connector"></a>カスタム コネクタを使用して Logic Apps と統合する

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) は、アプリとサービス全体のワークフローを自動化するのに役立つクラウド サービスです。 Logic Apps を Azure Digital Twins API に接続すると、Azure Digital Twins とそのデータに関するそのような自動化されたフローを作成できます。

<<<<<<< HEAD
現在、Azure Digital Twins に、Logic Apps 用の認定された (構築済みの) コネクタはありません。 代わりに、Azure Digital Twins で Logic Apps を使用するための現在のプロセスでは、Logic Apps で動作するように変更された [Azure Digital Twins custom Swaggers](/samples/azure-samples/digital-twins-custom-swaggers/azure-digital-twins-custom-swaggers/) を使用して、 [**Logic Apps カスタム コネクタ**](../logic-apps/custom-connector-overview.md)を作成します。
=======
現在、Azure Digital Twins に、Logic Apps 用に認定された (構築済みの) コネクタはありません。 代わりに、Azure Digital Twins で Logic Apps を使用するための現在のプロセスでは、Logic Apps で動作するように変更された[カスタム Azure Digital Twins Swagger](/samples/azure-samples/digital-twins-custom-swaggers/azure-digital-twins-custom-swaggers/) 定義ファイルを使用して、[カスタム Logic Apps コネクタ](../logic-apps/custom-connector-overview.md)を作成します。
>>>>>>> repo_sync_working_branch

> [!NOTE]
> 上記のリンクされたカスタム Swagger サンプルには、複数のバージョンの Swagger 定義ファイルが含まれています。 最新バージョンは、最新の日付のサブフォルダーにありますが、サンプルに含まれていた以前のバージョンも引き続きサポートされます。

この記事では、[Azure portal](https://portal.azure.com) を使用して、Azure Digital Twins インスタンスに Logic Apps を接続するために使用できる **カスタム コネクタを作成** します。 その後、シナリオの例に対してこの接続を使用する **ロジック アプリを作成** します。ここでは、タイマーによってトリガーされるイベントにより、Azure Digital Twins インスタンスのツインが自動的に更新されます。 

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。
このアカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。 

事前に必要な設定の一部として、さらに次の項目を完了する必要があります。 この手順については、残りのセクションで順を追って説明します。
- Azure Digital Twins インスタンスを設定する
- デジタル ツインを追加する
- Azure Active Directory (Azure AD) アプリの登録を設定する

### <a name="set-up-azure-digital-twins-instance"></a>Azure Digital Twins インスタンスを設定する

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="add-a-digital-twin"></a>デジタル ツインを追加する

この記事では、Logic Apps を使用して、Azure Digital Twins インスタンス内のツインを更新します。 続行するには、インスタンスにツインを少なくとも 1 つ追加する必要があります。 

[DigitalTwins API](/rest/api/digital-twins/dataplane/twins)、[NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)、または [Azure Digital Twins CLI](/cli/azure/dt?view=azure-cli-latest&preserve-view=true) を使用してツインを追加できます。 これらの方法を使用してツインを作成する方法に関する詳細な手順については、「[デジタル ツインを管理する](how-to-manage-twin.md)」を参照してください。

作成したインスタンス内のツインの **ツイン ID** が必要になります。

### <a name="set-up-app-registration"></a>アプリ登録を設定する

[!INCLUDE [digital-twins-prereq-registration.md](../../includes/digital-twins-prereq-registration.md)]

## <a name="create-custom-logic-apps-connector"></a>Logic Apps カスタム コネクタを作成する

<<<<<<< HEAD
このステップでは、Azure Digital Twins API 用の[Logic Apps カスタム コネクタ](../logic-apps/custom-connector-overview.md)を作成します。 これを行った後は、次のセクションでロジック アプリを作成するときに、Azure Digital Twins を接続できるようになります。
=======
これで Azure Digital Twins API 用の[カスタム Logic Apps コネクタ](../logic-apps/custom-connector-overview.md)を作成する準備ができました。 それを実行すると、次のセクションでロジック アプリを作成するときに Azure Digital Twins を接続できるようになります。
>>>>>>> repo_sync_working_branch

Azure portal の [[Logic Apps カスタム コネクタ]](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Web%2FcustomApis) ページに移動します (このリンクを使用しても、ポータルの検索バーで検索してもかまいません)。 *[+ 作成]* を選択します。

:::image type="content" source="media/how-to-integrate-logic-apps/logic-apps-custom-connector.png" alt-text="Azure portal の [Logic Apps カスタム コネクタ] ページのスクリーンショット。[追加] ボタンが強調表示されています。":::

次の **[ロジック アプリ カスタム コネクタの作成]** ページで、お使いのサブスクリプション、リソース グループ、新しいコネクタの名前とデプロイの場所を選択します。 **[Review + create]\(レビュー + 作成\)** を選択します。 

:::image type="content" source="media/how-to-integrate-logic-apps/create-logic-apps-custom-connector.png" alt-text="Azure portal の [ロジック アプリ カスタム コネクタの作成] ページのスクリーンショット。":::

それを実行すると **[確認および作成]** タブが表示されて、下部にある **[作成]** を選択してリソースを作成できます。

:::image type="content" source="media/how-to-integrate-logic-apps/review-logic-apps-custom-connector.png" alt-text="Azure portal の [Logic Apps カスタム コネクタの確認] ページの [確認と作成] タブのスクリーンショット。":::

コネクタのデプロイ ページが表示されます。 デプロイが終了したら、 **[リソースに移動]** ボタンを選択して、ポータルでコネクタの詳細を表示します。

### <a name="configure-connector-for-azure-digital-twins"></a>Azure Digital Twins 用にコネクタを構成する

次に、作成したコネクタを Azure Digital Twins に接続するように構成します。

<<<<<<< HEAD
まず、Logic Apps で動作するように変更された Azure Digital Twins custom Swaggers をダウンロードします。 *[Download ZIP]* ボタンを押し、 [**このリンク**](/samples/azure-samples/digital-twins-custom-swaggers/azure-digital-twins-custom-swaggers/)から **Azure Digital Twins Custom Swaggers (Logic Apps コネクタ)** サンプルをダウンロードします。 ダウンロードした *Azure_Digital_Twins_custom_Swaggers__Logic_Apps_connector_.zip* フォルダーに移動し、解凍します。 
=======
まず、Logic Apps で動作するように変更されたカスタム Azure Digital Twins Swagger をダウンロードします。 [Azure Digital Twins のカスタム Swaggers (Logic Apps コネクター) のサンプル](/samples/azure-samples/digital-twins-custom-swaggers/azure-digital-twins-custom-swaggers/)に移動し、タイトルの下にある **[Browse code]\(コードの参照\)** ボタンを選択して、サンプルの GitHub リポジトリに移動します。 **[コード]** ボタンをクリックした後、 **[Download ZIP]\(ZIP のダウンロード\)** をクリックすると、ご使用のマシンでサンプルが取得されます。
>>>>>>> repo_sync_working_branch

:::image type="content" source="media/how-to-integrate-logic-apps/download-repo-zip.png" alt-text="zip としてダウンロードする手順を強調表示した GitHub 上のデジタル ツインのカスタム swagger リポジトリを示すスクリーンショット。" lightbox="media/how-to-integrate-logic-apps/download-repo-zip.png"::: 

ダウンロードしたフォルダーに移動してそれを解凍します。 

このチュートリアル用のカスタム Swagger は、*digital-twins-custom-swaggers-main\LogicApps* フォルダーに配置されます。 このフォルダーには、*stable* と *preview* という名前のサブフォルダーが含まれています。どちらも、日付別に編成された異なるバージョンの Swagger を保持しています。 最新の日付のフォルダーには、Swagger 定義ファイルの最新のコピーが含まれます。 どちらのバージョンを選択した場合でも、Swagger ファイルには _digitaltwins.json_ という名前が付けられています。

> [!NOTE]
> プレビュー機能を使用している場合を除き、一般的には、最新の *stable* バージョンの Swagger ファイルを使用することをお勧めします。 ただし、以前のバージョンとプレビュー バージョンの Swagger ファイルも引き続きサポートされます。 

次に、[Azure portal](https://portal.azure.com) のコネクタの [概要] ページに進み、 **[編集]** を選択します。

:::image type="content" source="media/how-to-integrate-logic-apps/edit-connector.png" alt-text="前のステップで作成したコネクタの [概要] ページのスクリーンショット。[編集] ボタンが強調表示されています。":::

次の **[Logic Apps カスタム コネクタの編集]** ページで、次の情報を構成します。
* **カスタム コネクタ**
    - API エンドポイント: REST (既定値のまま)
    - インポート モード: OpenAPI ファイル (既定値のまま)
    - ファイル: この構成は、前にダウンロードしたカスタム Swagger ファイルとなります。 **[インポート]** を選択し、マシン上のファイル (*Azure_Digital_Twins_custom_Swaggers__Logic_Apps_connector_\LogicApps\...\digitaltwins.json*) を見つけて、 **[開く]** を選択します。
* **一般情報**
    - アイコン: 好みのアイコンをアップロードします
    - アイコンの背景色:'#xxxxxx' という形式で、色の 16 進コードを入力します。
    - 説明: 必要な値を入力します。
    - オンプレミス データ ゲートウェイ経由で接続する: トグルがオフ (既定値のまま)
    - スキーム: HTTPS (既定値のまま)
    - [Host]\(ホスト\):Azure Digital Twins インスタンスの "**ホスト名**"。
    - ベース URL: / (既定値のまま)

次に、ウィンドウの下部にある **[セキュリティ]** ボタンを選択して、次の構成ステップに進みます。

:::image type="content" source="media/how-to-integrate-logic-apps/configure-next.png" alt-text="[Logic Apps カスタム コネクタの編集] ページの下部のスクリーンショット。セキュリティに進むためのボタンが強調表示されています。":::

[セキュリティ] 手順で **[編集]** を選択し、次の情報を構成します。
* **[認証の種類]** : OAuth 2.0
* **OAuth 2.0**:
    - ID プロバイダー: Azure Active Directory
    - クライアント ID:「[前提条件](#prerequisites)」で作成した Azure AD アプリ登録の **アプリケーション (クライアント) ID**
    - クライアント シークレット: アプリ登録の **クライアント シークレット** 
    - ログイン URL: https://login.windows.net (既定のまま)
    - テナント ID: Azure AD アプリ登録の "**ディレクトリ (テナント) ID**"
    - リソース URL: 0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    - スコープ:Directory.AccessAsUser.All
    - リダイレクト URL: (現時点では既定のまま)

[リダイレクト URL] フィールドに *[カスタム コネクタを保存してリダイレクト URL を生成します]* と表示されます。 ここで、ペインの上部にある **[コネクタの更新]** を選択してそれを生成し、コネクタの設定を確認します。

:::image type="content" source="media/how-to-integrate-logic-apps/update-connector.png" alt-text="[Logic Apps カスタム コネクタの編集] ページの上部のスクリーンショット。[コネクタの更新] ボタンが強調表示されています。":::

[リダイレクト URL] フィールドに戻り、生成された値をコピーします。 これは、次の手順で使用します。

:::image type="content" source="media/how-to-integrate-logic-apps/copy-redirect-url.png" alt-text="[Logic Apps カスタム コネクタの編集] ページの [リダイレクト URL] フィールドのスクリーンショット。値をコピーするボタンが強調表示されています。":::

これで、コネクタを作成するために必要な情報をすべて入力しました ([セキュリティ] から [定義] の手順を続行する必要はありません)。 **[Logic Apps カスタム コネクタの編集]** ペインを閉じてかまいません。

>[!NOTE]
>最初に **[編集]** を選択したコネクタの [概要] ページに戻ります。 [編集] を再びクリックすると、構成選択入力プロセス全体が再び始まるのでご注意ください。 前回の設定値は残っていないので、変更した値で更新した構成を保存したい場合は、既定値で上書きされないように、他の値もすべて再入力する必要があります。

### <a name="grant-connector-permissions-in-the-azure-ad-app"></a>Azure AD アプリでコネクタのアクセス許可を付与する

次に、最後のステップでコピーしたカスタム コネクタの "**リダイレクト URL**" の値を使用して、Azure AD アプリの登録でコネクタのアクセス許可を付与します。

Azure portal の [[アプリの登録]](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) ページに移動し、一覧から登録を選択します。 

登録のメニューの **[認証]** で、URI を追加します。

:::image type="content" source="media/how-to-integrate-logic-apps/add-uri.png" alt-text="Azure portal のアプリ登録の [認証] ページのスクリーンショット。[URI の追加] ボタンと [認証] メニューが強調表示されています。"::: 

カスタム コネクタの "**リダイレクト URL**" を新しいフィールドに入力し、 **[保存]** アイコンを選択します。

:::image type="content" source="media/how-to-integrate-logic-apps/save-uri.png" alt-text="Azure portal のアプリ登録の [認証] ページのスクリーンショット。新しいリダイレクト URL と [保存] ボタンが強調表示されています。":::

これで、Azure Digital Twins API にアクセスできるカスタム コネクタの設定が完了しました。 

## <a name="create-logic-app"></a>ロジック アプリを作成する

次に、新しいコネクタを使用して Azure Digital Twins の更新を自動化するロジック アプリを作成します。

[Azure portal](https://portal.azure.com) の検索バーで、「**Logic Apps**」を検索します。 それを選択すると、 **[Logic Apps]** ページが表示されます。 **[+ 追加]** を選択して新しいロジック アプリを作成します。

:::image type="content" source="media/how-to-integrate-logic-apps/create-logic-app.png" alt-text="Azure portal の [Logic Apps] ページのスクリーンショット。[ロジック アプリの作成] ボタンが強調表示されています。":::

続いて表示される **[Logic Apps]** ページで、サブスクリプションとリソース グループを入力します。 **[インスタンスの詳細]** で、インスタンスの種類として **[消費]** を選択し、ロジック アプリの名前を選択したら、デプロイの場所を選択します。 ログ分析を有効にするか無効にするかを選択します。

_[確認および作成]_ ボタンを選択します。

それを行うと **[確認と作成]** タブが表示されるので、そこで詳細を確認し、下部にある **[作成]** を選択してリソースを作成できます。

ロジック アプリのデプロイ ページが表示されます。 デプロイが終了したら、 **[リソースに移動]** ボタンを選択して **[Logic Apps デザイナー]** に進み、そこでワークフローのロジックを入力します。

### <a name="design-workflow"></a>ワークフローを設計する

Logic Apps デザイナーの **[一般的なトリガーで開始する]** で、**[繰り返し]** を選択します。

:::image type="content" source="media/how-to-integrate-logic-apps/logic-apps-designer-recurrence.png" alt-text="Azure portal の [Logic Apps デザイナー] ページのスクリーンショット。[繰り返し] 一般トリガーが強調表示されています。":::

次の [Logic Apps デザイナー] ページで、 **[繰り返し]** 頻度を **[秒]** に変更して、イベントが 3 秒ごとにトリガーされるようにします。 この頻度を選択すると、後で長時間待つことなく結果を簡単に確認できるようになります。

**[+ New step (+ 新しいステップ)]** を選択します。

それを行うと [アクションの選択] ボックスが開かれます。 **[カスタム]** タブに切り替えます。上部のボックスに、前に作成したカスタム コネクタが表示されます。

:::image type="content" source="media/how-to-integrate-logic-apps/custom-action.png" alt-text="Azure portal の Logic Apps デザイナーでフローを作成するスクリーンショット。カスタム コネクタが強調表示されています。":::

それを選択して、そのコネクタに含まれる API の一覧を表示します。 検索バーを使用するか、一覧をスクロールして、**DigitalTwins_Add** を選択します。 (この記事で使用されている API 呼び出しは **DigitalTwins_Add** アクションですが、Logic Apps の接続用に有効な選択肢として、他の任意の API を選択してもかまいません)。

コネクタに接続するために、Azure の資格情報でサインインするように求められる場合があります。 **[要求されているアクセス許可]** ダイアログが表示される場合は、画面の指示に従ってアプリに同意して受け入れます。

新しい **DigitalTwinsAdd** ボックスのフィールドを次のように設定します。
* id: ロジック アプリで更新するインスタンス内のデジタル ツインの "**ツイン ID**" を入力します。
* ツイン:このフィールドには、選択した API 要求で必要な本文を入力します。 **DigitalTwinsUpdate** の場合、この本文は JSON Patch コードの形式です。 ツインを更新するための JSON Patch の構成の詳細については、「[デジタル ツインを更新する](how-to-manage-twin.md#update-a-digital-twin)」セクションを参照してください ("*方法: 「デジタル ツインを管理する」方法* を参照してください。
* API バージョン:最新の API バージョン。 現在、この値は *2020-10-31* です。

Logic Apps デザイナーで **[保存]** を選択します。

同じウィンドウで、 _[+ 新しいステップ]_ を選択すると、他の操作を選択できます。

:::image type="content" source="media/how-to-integrate-logic-apps/save-logic-app.png" alt-text="ロジック アプリ コネクタで完成したアプリの表示のスクリーンショット。DigitalTwinsAdd ボックスに、上で説明した値が入力されています。":::

## <a name="query-twin-to-see-the-update"></a>ツインのクエリを実行して更新を確認する

ロジック アプリを作成したので、Logic Apps デザイナーで定義したツイン更新イベントが、3 秒ごとに繰り返し発生するようになります。 このように構成された頻度は、3 秒後にはツインに対してクエリを実行し、新しくパッチが適用された値が反映されていることを確認できるはずであることを意味します。

任意の方法 ([カスタム クライアント アプリ](tutorial-command-line-app.md)、[Azure Digital Twins Explorer](concepts-azure-digital-twins-explorer.md)、[SDK と API](concepts-apis-sdks.md)、[CLI](concepts-cli.md) など) を選択して、ツインのクエリを実行できます。 

Azure Digital Twins インスタンスに対するクエリ実行の詳細については、[ツイン グラフに対するクエリの実行](how-to-query-graph.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

この記事では、指定したパッチを使用して、Azure Digital Twins インスタンス内のツインを定期的に更新するロジック アプリを作成しました。 カスタム コネクタで他の API を選択して、インスタンスに対するさまざまなアクションのためにロジック アプリを作成してみることができます。

<<<<<<< HEAD
使用可能な API 操作とその詳細については、" [*Azure Digital Twins の API および SDK を使用する方法*](how-to-use-apis-sdks.md)" に関するページで参照してください。
=======
使用可能な API 操作の詳細と、それに必要な詳細事項については、「[Azure Digital Twins API および SDK](concepts-apis-sdks.md)」を参照してください。
>>>>>>> repo_sync_working_branch
