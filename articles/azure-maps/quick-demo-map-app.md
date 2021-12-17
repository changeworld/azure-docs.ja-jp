---
title: クイック スタート:Azure Maps を使った対話型のマップ検索
description: 'クイックスタート: 対話型の検索可能なマップを作成する方法について説明します。 Azure Maps アカウントを作成して主キーを取得し、Web SDK を使用してマップ アプリケーションを設定する方法をご覧ください'
author: stevemunk
ms.author: v-munksteve
ms.date: 09/30/2021
ms.topic: quickstart
ms.service: azure-maps
services: azure-maps
ms.custom: mvc
ms.openlocfilehash: e4c4cdeeb032b0d6a6691987cec43c8ac1b2d437
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130043379"
---
# <a name="quickstart-create-an-interactive-search-map-with-azure-maps"></a>クイック スタート:Azure Maps を使用して対話形式の検索マップを作成する

このクイックスタートでは、Azure Maps を使用して、ユーザーに対話形式の検索エクスペリエンスを提供するマップを作成する方法を学習します。 次の基本的な手順について説明します。

* 自分用の Azure Maps アカウントを作成する。
* デモ版の Web アプリケーションで使用する主キーを取得する。
* デモ マップ アプリケーションをダウンロードして開く。

このクイックスタートでは Azure Maps Web SDK を使用しますが、Azure Maps サービスは、任意のマップ コントロールで使用できます。これには、Azure Maps チームがプラグインを作成したこれらの一般的な[オープン ソース マップ コントロール](open-source-projects.md#third-part-map-control-plugins)などが含まれます。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

* [Azure portal](https://portal.azure.com) にサインインします。

<a id="createaccount"></a>

## <a name="create-an-azure-maps-account"></a>Azure Maps アカウントを作成する

次の手順で、新しい Azure Maps アカウントを作成します。

1. [Azure Portal](https://portal.azure.com) の左上隅にある **[リソースの作成]** をクリックします。
2. *[Marketplace を検索]* ボックスに「**Azure Maps**」と入力します。
3. *[結果]* から **[Azure Maps]** を選択します。 マップの下に表示される **[作成]** ボタンをクリックします。
4. **[Azure Maps アカウントの作成]** ページで、次の値を入力します。
    * このアカウントで使う "*サブスクリプション*"。
    * このアカウントの "*リソース グループ*" の名前。 *[新規作成]* を選んで新しく作成することも、 *[既存のものを使用]* を選んで既存のリソース グループを使うこともできます。
    * 新しいアカウントの "*名前*"。
    * このアカウントの "*価格レベル*"。
    * *[ライセンス]* と *[プライバシーに関する声明]* の内容を読み、チェック ボックスをオンにして条件に同意します。
    * **[作成]** ボタンをクリックします。

    :::image type="content" source="./media/quick-demo-map-app/create-account.png" alt-text="ポータルで Maps アカウントを作成する" lightbox="./media/quick-demo-map-app/create-account.png":::

<a id="getkey"></a>

## <a name="get-the-primary-key-for-your-account"></a>アカウントの主キーを取得する

Maps アカウントが正常に作成されたら、Maps API のクエリを実行できる主キーを取得します。

1. ポータルで、Maps アカウントを開きます。
2. [設定] セクションで **[認証]** を選択します。
3. **[主キー]** をクリップボードにコピーします。 このチュートリアルで後ほど使用するためにローカルに保存します。

:::image type="content" source="./media/quick-demo-map-app/get-key.png" alt-text="Azure portal で主キー Azure Maps キーを取得する" lightbox="./media/quick-demo-map-app/get-key.png":::

>[!NOTE]
> このクイックスタートでは、デモンストレーションの目的で[共有キー](azure-maps-authentication.md#shared-key-authentication)認証アプローチを使用しますが、どの運用環境でも推奨されるアプローチは [Azure Active Directory](azure-maps-authentication.md#azure-ad-authentication) 認証を使用することです。

## <a name="download-and-update-the-azure-maps-demo"></a>Azure Maps デモをダウンロードして更新する

1. [interactiveSearch .html](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/interactiveSearch.html) にアクセスします。 ファイルの内容をコピーします。
2. このファイルの内容を **AzureMapDemo.html** としてローカルに保存します。 それをテキスト エディターで開きます。
3. 前のセクションで取得した **主キー** の値を追加します
    1. `authOptions` 関数内のすべてのコードをコメント アウトします。このコードは、Azure Active Directory の認証に使用されます。
    1. `authOptions` 関数内の最後の 2 行をコメント解除します。このコードは、このコードは、このクイックスタートで使用されている方法である共有キー認証に使用されます。
    1. `<Your Azure Maps Key>` を前のセクションで取得した **主キー** の値に置き換えます。

## <a name="open-the-demo-application"></a>デモ アプリケーションを開く

1. **AzureMapDemo.html** ファイルを任意のブラウザーで開きます。
2. 表示されたロサンゼルス市の地図を見てください。 拡大/縮小すると、ズームのレベルに応じて、マップに表示される情報が自動的に増減します。
3. マップの既定の中心を変更します。 **AzureMapDemo.html** ファイルで **center** という名前の変数を検索します。 この変数の緯度と経度のペア値を、新しい値 **[-74.0060, 40.7128]** に置き換えます。 ファイルを保存し、ブラウザーを更新します。
4. これで、対話形式の検索を実行できます。 デモ版の Web アプリケーションの左上隅にある検索ボックスに「**レストラン**」と入力します。
5. 検索ボックスの下に表示された住所/場所のリストにマウスを合わせます。 対応するピンがマップに現れ、その場所に関する情報がポップアップに表示されることがわかります。 民間事業のプライバシー保護のために、架空の名前と住所が表示されています。

    :::image type="content" source="./media/quick-demo-map-app/interactive-search.png" alt-text="対話型のマップ検索 Web アプリケーション" lightbox="./media/quick-demo-map-app/interactive-search.png":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

>[!WARNING]
>「[次のステップ](#next-steps)」セクションに示されているチュートリアルでは、お使いのアカウントで Azure Maps を使用および構成する方法を詳しく説明しています。 チュートリアルに進む場合は、このクイック スタートで作成したリソースをクリーンアップしないでください。

チュートリアルに進まない場合は、次の手順に従ってリソースをクリーンアップしてください。

1. **AzureMapDemo.html** Web アプリケーションを実行しているブラウザーを閉じます。
2. Azure portal ページに移動します。 ポータルのメイン ページで、 **[すべてのリソース]** を選択します。 または、左上隅にあるメニュー アイコンをクリックします。 **[すべてのリソース]** を選択します。
3. お使いの Azure Maps アカウントをクリックします。 ページの上部にある **[削除]** をクリックします。

他のコード例や対話型のコーディング エクスペリエンスについては、以下のガイドを参照してください。

* [Azure Maps Search サービスを使用して住所を検索する](how-to-search-for-address.md)
* [Azure Maps のマップ コントロールを使用する](how-to-use-map-control.md)

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Azure Maps アカウントを作成し、デモ アプリケーションを作成しました。 Azure Maps の詳細については、以下のチュートリアルを参照してください。

> [!div class="nextstepaction"]
> [Azure Maps を使用して近くの目的地を検索する](tutorial-search-location.md)
