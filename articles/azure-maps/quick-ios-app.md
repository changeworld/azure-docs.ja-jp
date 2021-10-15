---
title: iOS アプリの作成
description: Azure Maps アカウントと最初の iOS アプリを作成する手順
author: deniseatmicrosoft
ms.author: v-dbogomolov
ms.date: 09/30/2021
ms.topic: conceptual
ms.service: azure-maps
ms.openlocfilehash: 46e5ec41c0cea9a417b920e8441ff1129040c209
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2021
ms.locfileid: "129389610"
---
#  <a name="create-an-ios-app-public-preview"></a>iOS アプリを作成する (パブリック プレビュー)

この記事では、iOS アプリに Azure Maps を追加する方法について説明します。 次の基本的な手順について説明します。

* 使用する開発環境を設定する。
* 自分用の Azure Maps アカウントを作成する。
* アプリで使用する Azure Maps のプライマリ キーを取得する。
* プロジェクトから Azure Maps ライブラリを参照する。
* Azure Maps コントロールをアプリに追加する。

## <a name="prerequisites"></a>[前提条件]

* [Azure portal](https://portal.azure.com/) にサインインして Azure Maps アカウントを作成します。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。
* [Azure Maps アカウントを作成します](quick-demo-map-app.md#create-an-azure-maps-account)
* [プライマリ サブスクリプション キーを取得します](quick-demo-map-app.md#get-the-primary-key-for-your-account) (主キーまたはサブスクリプション キーとも呼ばれます)。 Azure Maps での認証の詳細については、「[Azure Maps での認証の管理](how-to-manage-authentication.md)」を参照してください。
* [Mac App Store から Xcode](https://apps.apple.com/cz/app/xcode/id497799835?mt=12) を無料でダウンロードします。

## <a name="create-an-azure-maps-account"></a>Azure Maps アカウントを作成する

次の手順で、新しい Azure Maps アカウントを作成します。

1. [Azure portal](https://portal.azure.com/) の左上隅にある **[リソースの作成]** をクリックします。

2. _[Marketplace を検索]_ ボックスに「**Azure Maps**」と入力します。

3. _[結果]_ から **[Azure Maps]** を選択します。 マップの下に表示される **[作成]** ボタンをクリックします。

4. **[Azure Maps アカウントの作成]** ページで、次の値を入力します。
   - このアカウントで使う "_サブスクリプション_"。
   - このアカウントの "_リソース グループ_" の名前。 _[新規作成]_ を選んで新しく作成することも、 _[既存のものを使用]_ を選んで既存のリソース グループを使うこともできます。
   - 新しいアカウントの "_名前_"。
   - このアカウントの "_価格レベル_"。
   - _[ライセンス]_ と _[プライバシーに関する声明]_ の内容を読み、チェック ボックスをオンにして条件に同意します。
   - **[作成]** ボタンをクリックします。

   :::image source="./media/quick-ios-app/create-account.png" alt-text="Azure Maps アカウントを作成しているスクリーンショット。":::

## <a name="get-the-primary-key-for-your-account"></a>アカウントの主キーを取得する

Maps アカウントが正常に作成されたら、Maps API のクエリを実行できる主キーを取得します。

1. ポータルで、Maps アカウントを開きます。

2. [設定] セクションで **[認証]** を選択します。

3. **[主キー]** をクリップボードにコピーします。 このチュートリアルで後ほど使用するためにローカルに保存します。

   > [!Note]
   > Azure Maps のプライマリ キーではなく Azure サブスクリプション キーを使用した場合、マップは正しく表示されません。 また、セキュリティ上の理由から、主キーとセカンダリ キーをローテーションすることをお勧めします。 キーをローテーションするには、セカンダリ キーを使用するようにアプリを更新してデプロイします。次に、主キーの横にある [cycle/refresh]\(サイクル/更新\) ボタンを押して、新しい主キーを生成します。 古い主キーは無効になります。 キー ローテーションの詳細については、「[キー ローテーションと監査で Azure Key Vault を設定する](../key-vault/secrets/tutorial-rotation-dual.md)」を参照してください。

   :::image source="./media/quick-ios-app/get-key.png" alt-text="サブスクリプション キーを取得しているスクリーンショット。":::

## <a name="create-a-project-in-xcode"></a>Xcode でプロジェクトを作成する

最初に、新しい iOS アプリ プロジェクトを作成します。 Xcode プロジェクトを作成するには、次の手順を完了します。

1. **[File]\(ファイル\)** から、 **[New]\(新規作成\)**  ->  **[Project]\(プロジェクト\)** の順に選択します。

2. **[iOS]** タブで、 **[App]\(アプリ\)** と選択してから、 **[Next]\(次へ\)** を選択します。

3. アプリ名とバンドル ID を入力したら、 **[Next]\(次へ\)** を選択します。

   新しいプロジェクトの作成に関する詳細については、[アプリ用の Xcode プロジェクトを作成する](https://developer.apple.com/documentation/xcode/creating-an-xcode-project-for-an-app)方法に関する記事を参照してください。

:::image source="./media/quick-ios-app/create-app.png" alt-text="最初の iOS アプリケーションを作成します。":::

## <a name="install-the-azure-maps-ios-sdk"></a>Azure Maps iOS SDK をインストールする

アプリケーションのビルドの次の手順は、Azure Maps iOS SDK をインストールすることです。 SDK をインストールするには、以下の手順を完了します。

1. **[Project navigator]\(プロジェクト ナビゲーター\)** で自分のプロジェクトを選択します

2. 開いた **[Project Editor]\(プロジェクト エディター\)** でプロジェクトを選択します

3. **[Swift Package]\(Swift パッケージ\)** タブに切り替えます

4. Azure Maps iOS SDK を追加します: `{link goes here}`

   :::image source="./media/quick-ios-app/add-project.png" alt-text="iOS プロジェクトを追加しているスクリーンショット":::
  
## <a name="add-mapcontrol-view"></a>MapControl ビューを追加します

1. ビュー コントローラーにカスタム `UIView` を追加します

2. `AzureMapsControl` モジュールから `MapControl` クラスを選択します

   :::image source="./media/quick-ios-app/add-map-control.png" alt-text="Azure Maps コントロールを追加するスクリーンショット。":::

3. **AppDelegate.swift** ファイルで、以下の操作を行う必要があります。

   - Azure Maps SDK のインポートを追加する。
   - Azure Maps の認証情報を設定する。
   
   `AzureMaps.configure(subscriptionKey:)` または `AzureMaps.configure(aadClient:aadAppId:aadTenant:)` メソッドを使用して AzureMaps クラスにグローバルに認証情報を設定すると、すべてのビューで認証情報を追加する必要がなくなります。

4. 以下の図に示すように、実行ボタンを選択して (または `CMD` + `R` キーを押す)、アプリケーションをビルドします。

   :::image source="./media/quick-ios-app/run.png" alt-text="iOS アプリケーションを実行しているスクリーンショット。":::

   Xcode でアプリケーションをビルドするには、数秒かかります。 ビルドが完了した後、シミュレートされた iOS デバイスでアプリケーションをテストできます。 次のようなマップが表示されます。

   :::image source="./media/quick-ios-app/example.png" alt-text="iOS アプリケーションで動作する最初のマップのスクリーンショット。":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

> [!WARNING]
> 「[次のステップ](#next-steps)」セクションに示されているチュートリアルでは、お使いのアカウントで Azure Maps を使用および構成する方法を詳しく説明しています。 チュートリアルに進む場合は、このクイック スタートで作成したリソースをクリーンアップしないでください。

チュートリアルに進まない場合は、次の手順に従ってリソースをクリーンアップしてください。

1. Xcode を閉じ、作成したプロジェクトを削除します。
2. 外部のデバイスでアプリケーションをテストした場合は、そのデバイスからアプリケーションをアンインストールします。

今後 Azure Maps iOS SDK を使用した開発を行う予定がない場合は次の手順を実行します。

1. Azure portal ページに移動します。 ポータルのメイン ページで、 **[すべてのリソース]** を選択します。 または、左上隅にあるメニュー アイコンをクリックします。 **[すべてのリソース]** を選択します。
2. お使いの Azure Maps アカウントをクリックします。 ページの上部にある **[削除]** をクリックします。
3. iOS アプリの開発を今後行う予定がない場合は、必要に応じて Xcode をアンインストールします。

<!--
For more code examples, see these guides:

*  [Manage authentication in Azure Maps](how-to-manage-authentication.md)
*  [Change map styles in iOS maps](Set%20map%20style%20%28iOS%20SDK%29.md)
*  [Add a symbol layer](Add%20a%20symbol%20layer%20%28iOS%20SDK%29.md)
*  [Add a line layer](Add%20a%20line%20layer%20to%20the%20map%20%28iOS%20SDK%29.md)
*  [Add a polygon layer](Add%20a%20polygon%20layer%20to%20the%20map%20%28iOS%20SDK%29.md)
-->

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Azure Maps アカウントを作成し、デモ アプリケーションを作成しました。 Azure Maps の詳細については、以下のチュートリアルを参照してください。

> [!div class="nextstepaction"]
> [GeoJSON データを Azure Maps に読み込む](tutorial-load-geojson-file-android.md)
