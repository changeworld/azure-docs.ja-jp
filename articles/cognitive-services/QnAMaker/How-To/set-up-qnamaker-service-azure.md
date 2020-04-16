---
title: QnA Maker サービスを設定する - QnA Maker
description: QnA Maker のナレッジ ベースを作成する前に、まず Azure で QnA Maker サービスをセットアップしておく必要があります。 QnA Maker サービスは、サブスクリプション内で新しいリソースを作成することが認可されているユーザーであれば、だれでもセットアップできます。
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 563a56fdb288568e7fe667fa54658400064a560f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402984"
---
# <a name="manage-qna-maker-resources"></a>QnA Maker のリソースを管理する

QnA Maker のナレッジ ベースを作成する前に、まず Azure で QnA Maker サービスをセットアップしておく必要があります。 QnA Maker サービスは、サブスクリプション内で新しいリソースを作成することが認可されているユーザーであれば、だれでもセットアップできます。

リソースを作成する前に、次の概念について十分に理解しておくことをお勧めします。

* "[QnA Maker のリソース](../Concepts/azure-resources.md)"
* [キーの作成と発行](../Concepts/azure-resources.md#keys-in-qna-maker)

## <a name="create-a-new-qna-maker-service"></a>新しい QnA Maker サービスの作成

この手順では、ナレッジ ベースのコンテンツを管理するために必要な Azure リソースを作成します。 これらの手順を完了すると、Azure portal のリソース用の **[キー]** ページに、_サブスクリプション_ キーが表示されます。

1. Azure portal にサインインし、[QnA Maker リソースを作成](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)します。

1. 使用条件を読んだ後、 **[作成]** を選択します。

    ![新しい QnA Maker サービスの作成](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. **QnA Maker** で、適切なレベルとリージョンを選択します。

    ![新しい QnA Maker サービスを作成する - 価格レベルとリージョン](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * **[名前]** フィールドに、この QnA Maker サービスを識別する一意の名前を入力します。 ナレッジ ベースが関連付けられる QnA Maker のエンドポイントも、この名前によって識別されます。
    * QnA Maker リソースのデプロイ先となる**サブスクリプション**を選択します。
    * QnA Maker 管理サービス (ポータルおよび Management API) の**価格レベル**を選択します。 [詳細については、SKU の価格](https://aka.ms/qnamaker-pricing)に関するページをご覧ください。
    * この QnA Maker リソースのデプロイ先として、新しい**リソース グループ**を作成する (推奨) か、既存のリソース グループを使用します。 QnA Maker はいくつかの Azure リソースを作成します。 これらのリソースを保持するリソース グループを作成すると、リソースの検索、管理、および削除を、リソース グループ名によって簡単に実行できます。
    * **リソース グループの場所**を選択します。
    * Azure Cognitive Search サービスの**検索価格レベル**を選択します。 Free レベル オプションを利用できない (灰色表示されている) 場合、それは、ご利用のサブスクリプションで Free サービスが既にデプロイされていることを意味します。 その場合は、Basic レベルから開始する必要があります。 [Azure Cognitive Search の価格の詳細](https://azure.microsoft.com/pricing/details/search/)に関するページを参照してください。
    * Azure Cognitive Search インデックスのデプロイ先となる**検索場所**を選択します。 顧客データをどの場所に格納する必要があるかの制限に基づいて、Azure Cognitive Search で使用する場所を選択できます。
    * **[アプリ名]** フィールドに、Azure App Service インスタンスの名前を入力します。
    * App Service の既定のレベルは Standard (S1) になります。 このプランは、作成後に変更することができます。 詳細については、「[App Service の価格](https://azure.microsoft.com/pricing/details/app-service/)」をご覧ください。
    * App Service のデプロイ先となる **Web サイトの場所**を選択します。

        > [!NOTE]
        > **検索場所**は **Web サイトの場所**と異なっていてもかまいません。

    * **Application Insights** を有効にするかどうかを選択します。 **Application Insights** を有効にした場合、トラフィック、チャットのログ、エラーに関するテレメトリが QnA Maker によって収集されます。
    * Application Insights リソースのデプロイ先となる **App Insights の場所**を選択します。
    * コスト削減対策として、QnA Maker 用に作成する Azure リソースのすべてではなく、一部を[共有](#configure-qna-maker-to-use-different-cognitive-search-resource)することができます。

1. すべてのフィールドを確認した後、 **[作成]** を選択します。 プロセスが完了するまでに数分かかることがあります。

1. デプロイが完了すると、サブスクリプション内に次のリソースが作成されていることがわかります。

   ![リソースによって新しい QnA Maker サービスが作成された](../media/qnamaker-how-to-setup-service/resources-created.png)

    種類が _Cognitive Services_ であるリソースには、_サブスクリプション_ キーがあります。

## <a name="find-subscription-keys-in-the-azure-portal"></a>Azure portal でサブスクリプション キーを確認する

QnA Maker リソースを作成した Azure portal で、サブスクリプション キーの表示とリセットを実行できます。

1. Azure portal で QnA Maker リソースに移動し、種類が _Cognitive Services_ であるリソースを選択します。

    ![QnA Maker のリソース リスト](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. **[キー]** に移動します。

    ![Subscription key](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="find-endpoint-keys-in-the-qna-maker-portal"></a>QnA Maker ポータルでエンドポイント キーを確認する

ナレッジ ベースの呼び出しにはエンドポイント キーが使用されるため、エンドポイントはリソースと同じリージョンにあります。

エンドポイント キーは、[QnA Maker ポータル](https://qnamaker.ai)から管理することができます。

1. [QnA Maker ポータル](https://qnamaker.ai)にサインインし、お使いのプロファイルに移動してから、 **[サービスの設定]** を選択します。

    ![エンドポイント キー](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. キーを表示またはリセットします。

    > [!div class="mx-imgBorder"]
    > ![エンドポイント キー マネージャー](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >キーが漏えいしていると思われる場合は、キーを更新してください。 それに合わせて、クライアント アプリまたはボットのコードの変更が必要になることがあります。

## <a name="upgrade-qna-maker-sku"></a>QnA Maker の SKU をアップグレードする

現在のレベルよりも多くの質問と回答をナレッジ ベースに含めたい場合は、QnA Maker サービスの価格レベルをアップグレードします。

QnA Maker 管理 SKU をアップグレードするには、次の手順に従います。

1. Azure portal の QnA Maker リソースに移動し、 **[価格レベル]** を選択します。

    ![QnA Maker リソース](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

1. 適切な SKU を選択し、 **[選択]** を押します。

    ![QnA Maker の価格](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

## <a name="upgrade-app-service"></a>App Service をアップグレードする

 ナレッジ ベースでクライアント アプリからのより多くの要求に対応する必要がある場合は、App Service の価格レベルをアップグレードします。

App Service は、[スケールアップ](https://docs.microsoft.com/azure/app-service/manage-scale-up)またはスケールアウトすることができます。

Azure portal で App Service リソースに移動し、必要に応じて **[スケールアップ]** と **[スケールアウト]** のどちらかのオプションを選択します。

![QnA Maker App Service のスケーリング](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

## <a name="upgrade-the-azure-cognitive-search-service"></a>Azure Cognitive Search サービスのアップグレード

多数のナレッジ ベースを使用する予定の場合は、Azure Cognitive Search サービスの価格レベルをアップグレードします。

現在、Azure Search SKU のインプレース アップグレードを実行することはできません。 ただし、必要な SKU で新しい Azure Search リソースを作成し、その新しいリソースにデータを復元して、QnA Maker スタックにリンクすることはできます。 そのためには、次の手順に従います。

1. Azure portal で新しい Azure Search リソースを作成し、必要な SKU を選択します。

    ![QnA Maker Azure Search リソース](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

1. 元の Azure Search リソースのインデックスを新しいリソースに復元します。 [バックアップと復元のサンプル コード](https://github.com/pchoudhari/QnAMakerBackupRestore)を参照してください。

1. データが復元されたら、新しい Azure Search リソースに移動し、 **[キー]** を選択して、 **[名前]** と **[管理者キー]** を書き留めます。

    ![QnA Maker Azure Search キー](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

1. 新しい Azure Search リソースを QnA Maker スタックにリンクするために、QnA Maker App Service インスタンスに移動します。

    ![QnA Maker App Service インスタンス](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

1. **[アプリケーションの設定]** を選択し、 **[AzureSearchName]** フィールドと **[AzureSearchAdminKey]** フィールドを手順 3 で書き留めた値に変更します。

    ![QnA Maker App Service の設定](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

1. App Service インスタンスを再起動します。

    ![QnA Maker App Service インスタンスの再起動](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="get-the-latest-runtime-updates"></a>最新のランタイム更新プログラムを取得する

QnA Maker ランタイムは、Azure portal で [QnA Maker サービスを作成する](./set-up-qnamaker-service-azure.md)ときにデプロイされる Azure App Service インスタンスの一部です。 ランタイムの更新は定期的に行われます。 QnA Maker App Service インスタンスは、2019 年 4 月のサイト拡張リリース (バージョン 5+) 以降は自動更新モードになります。 この更新は、アップグレード中のダウンタイムがゼロになるように設計されています。

[https://www.qnamaker.ai/UserSettings](https://www.qnamaker.ai/UserSettings ) で現在のバージョンを確認できます。 ご使用のバージョンがバージョン 5.x よりも古い場合は、App Service を再起動して最新の更新プログラムを適用する必要があります。

1. [Azure portal](https://portal.azure.com) で QnA Maker サービス (リソース グループ) に移動します。

    > [!div class="mx-imgBorder"]
    > ![QnA Maker Azure リソース グループ](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. App Service インスタンスをクリックして、 **[概要]** セクションを開きます。

    > [!div class="mx-imgBorder"]
    > ![QnA Maker App Service インスタンス](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)


1. App Service を再起動します。 更新プロセスは数秒で完了します。 この再起動中、エンド ユーザーは、この QnA Maker サービスを使用する依存アプリケーションやボットを使用できなくなります。

    ![QnA Maker App Service インスタンスの再起動](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="configure-qna-maker-to-use-different-cognitive-search-resource"></a>別の Cognitive Search リソースを使用するように QnA Maker を構成する

ポータルを使用して QnA サービスとその依存関係 (Search など) を作成すると、Search サービスが自動的に作成され、QnA Maker サービスにリンクされます。 これらのリソースが作成された後、既存の Search サービスを使用するように App Service 設定を更新して、先ほど作成されたサービスを削除できます。

QnA Maker の **App Service** リソースには Cognitive Search リソースが使用されます。 QnA Maker に使用される Cognitive Search リソースを変更するには、Azure portal の設定を変更する必要があります。

1. QnA Maker で使用する Cognitive Search リソースの**管理者キー**と**名前**を取得します。

1. [Azure portal](https://portal.azure.com) にサインインし、QnA Maker リソースに関連付けられている**アプリ サービス**を見つけます。 両方とも同じ名前です。

1. **[設定]** 、 **[構成]** の順に選択します。 これで、QnA Maker の App Service の既存の設定がすべて表示されます。

    > [!div class="mx-imgBorder"]
    > ![App Service の構成設定を表示する Azure portal のスクリーンショット](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-app-service-configuration.png)

1. 次のキーの値を変更します。

    * **AzureSearchAdminKey**
    * **AzureSearchName**

1. 新しい設定を使用するには、アプリ サービス を再起動する必要があります。 **[概要]** を選択し、 **[再起動]** を選択します。

    > [!div class="mx-imgBorder"]
    > ![構成設定の変更後に App Service を再起動する Azure portal のスクリーンショット](../media/qnamaker-how-to-upgrade-qnamaker/screenshot-azure-portal-restart-app-service.png)

Azure Resource Manager テンプレートを使用して QnA サービスを作成する場合は、すべてのリソースを作成し、既存の Search サービスを使用するように App Service の作成を制御できます。

詳しくは、App Service の[アプリケーションの設定](../../../app-service/configure-common.md#configure-app-settings)を構成する方法をご覧ください。

## <a name="configure-app-service-idle-setting-to-avoid-timeout"></a>タイムアウトを回避するように App Service のアイドル設定を構成する

公開されたナレッジ ベースの QnA Maker 予測ランタイムを提供する App Service にはアイドル タイムアウトの構成があり、サービスがアイドル状態の場合、既定では自動的にタイムアウトします。 QnA Maker の場合、これは、予測ランタイム GenerateAnswer API は、一定期間トラフィックがないとタイムアウトする場合があることを意味します。

トラフィックがない場合でも、予測エンドポイント アプリが読み込まれたままにするには、アイドル状態を常時オンに設定します。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. QnA Maker リソースの App Service を探して選択します。 名前は QnA Maker リソースと同じですが、App Service の**種類**は異なります。
1. **[設定]** を探し、 **[構成]** を選択します。
1. [構成] ペインで **[一般設定]** を選択して、 **[常にオン]** を探し、値として **[オン]** を選択します。

    > [!div class="mx-imgBorder"]
    > ![[構成] ペインの **[一般設定]** の **[常にオン]** を **[オン]** に設定する。](../media/qnamaker-how-to-upgrade-qnamaker/configure-app-service-idle-timeout.png)

1. **[保存]** を選んで構成を保存します。
1. アプリを再起動して新しい設定を使用するかどうかを確認するメッセージが表示されます。 **[続行]** をクリックします。

詳しくは、App Service の[一般設定](../../../app-service/configure-common.md#configure-general-settings)を構成する方法をご覧ください。

## <a name="delete-azure-resources"></a>Azure リソースを削除する

QnA Maker ナレッジ ベースに使用されているいずれかの Azure リソースを削除すると、ナレッジ ベースは機能しなくなります。 いずれかのリソースを削除する前に、 **[設定]** ページから確実にナレッジ ベースをエクスポートしてください。

## <a name="next-steps"></a>次のステップ

[App Service](../../../app-service/index.yml) と [Search Service](../../../search/index.yml) の詳細をご覧ください。

> [!div class="nextstepaction"]
> [ナレッジ ベースの作成と発行](../Quickstarts/create-publish-knowledge-base.md)
