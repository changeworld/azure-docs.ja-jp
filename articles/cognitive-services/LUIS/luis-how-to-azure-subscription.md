---
title: LUIS リソースを作成して管理する方法
titleSuffix: Azure Cognitive Services
description: LUIS アプリの Azure リソースを使用して管理する方法について説明します。
services: cognitive-services
author: aahill
ms.author: aahi
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 07/12/2021
ms.custom: contperf-fy21q4
ms.openlocfilehash: 4fe1e8cd4c78d4411a7cb9058648fc0e782cb0ee
ms.sourcegitcommit: aaaa6ee55f5843ed69944f5c3869368e54793b48
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2021
ms.locfileid: "113666305"
---
# <a name="how-to-create-and-manage-luis-resources"></a>LUIS リソースを作成して管理する方法

この記事では、LUIS で使用できる Azure リソースの種類と、それらを管理する方法について説明します。

## <a name="authoring-resource"></a>作成リソース

作成リソースを使用すると、アプリケーションの作成、管理、トレーニング、テスト、発行を行いうことができます。 LUIS 作成リソースで利用できる[価格レベル](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/)は 1 つです。無料 (F0) レベルで、次の機能が提供されます。

* 100 万件の作成トランザクション 
* 1 か月あたり 1,000 件のテスト予測エンドポイント要求。

[v3.0-preview LUIS Programmatic API](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2f) を使用して、作成リソースを管理できます。 

## <a name="prediction-resource"></a>予測リソース

予測リソースを使用すると、作成リソースによって提供される要求が 1,000 件を超える場合は、予測エンドポイントに対してクエリを実行できます。 予測リソースで利用できる[価格レベル](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/)は次の 2 つです。

* Free F0 予測リソース。これによって、毎月 10,000 件の予測エンドポイント要求が提供されます。
* Standard S0 予測リソース。これは有料のサービス レベルです。 

[v3.0-preview LUIS  Endpoint API](https://westus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0-preview/operations/5f68f4d40a511ce5a7440859) を使用して、予測リソースを管理できます。

> [!Note]
> * [マルチサービス リソース](../cognitive-services-apis-create-account-cli.md?tabs=multiservice)を使用して、複数の Cognitive Services に使用できる 1 つのエンドポイントを取得することもできます。
> * LUIS には、2 種類の F0 (Free レベル) リソースが用意されています。1 つはオーサリング トランザクション用で、もう 1 つは予測トランザクション用です。 予測トランザクション用の無料クォータが不足する場合は、毎月 1,000 件の予測トランザクションが提供されるオーサリング リソースではなく、毎月 10,000 件の無料トランザクションが提供される F0 予測リソースを使用していることを確認してください。
> * 発行およびクエリを行う[リージョン](luis-reference-regions.md#publishing-regions)内で LUIS アプリを作成する必要があります。

## <a name="create-luis-resources"></a>LUIS リソースの作成

LUIS リソースを作成するには、LUIS ポータル、[Azure portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)、または Azure CLI を使用できます。 リソースを作成したら、それらをアプリに割り当てて使用できるようにする必要があります。

# <a name="luis-portal"></a>[LUIS ポータル](#tab/portal)

### <a name="create-a-luis-authoring-resource-using-the-luis-portal"></a>LUIS ポータルを使用して LUIS 作成リソースを作成する

1. [LUIS ポータル](https://www.luis.ai)にサインインし、国/地域を選択して利用規約に同意します。 ポータルに **[マイ アプリ]** セクションが表示されている場合は、LUIS リソースが既に存在するため、次の手順は省略できます。

2. 表示された **[Choose an authoring]\(作成の選択\)** ウィンドウで、Azure サブスクリプションと LUIS 作成リソースを見つけます。 リソースがない場合は、新しいものを作成できます。

    :::image type="content" source="./media/luis-how-to-azure-subscription/choose-authoring-resource.png" alt-text="Language Understanding の作成リソースの種類を選択します。":::
    
    新しい作成リソースを作成するときは、次の情報を指定します。
    * **[テナント名]** : Azure サブスクリプションが関連付けられているテナント。
    * **[Azure サブスクリプション名]** : リソースに対して課金されるサブスクリプション。
    * **[Azure リソース グループ名]** : 選択または作成するカスタム リソース グループ名。 リソース グループを使用すると、アクセスと管理のために Azure リソースをグループ化できます。
    * **[Azure リソース名]** : 選択するカスタム名。作成および予測エンドポイント クエリの URL の一部として使用されます。
    * **[Pricing tier]\(価格レベル\)** : 価格レベルによって、1 秒および 1 か月あたりの最大トランザクション数が決まります。

### <a name="create-a-luis-prediction-resource-using-the-luis-portal"></a>LUIS ポータルを使用して LUIS 予測リソースを作成する

[!INCLUDE [Create LUIS Prediction resource in LUIS portal](./includes/add-prediction-resource-portal.md)]

# <a name="without-luis-portal"></a>[LUIS ポータルなし](#tab/without-portal)

### <a name="create-luis-resources-without-using-the-luis-portal"></a>LUIS ポータルを使わずに LUIS リソースを作成する

[Azure CLI](/cli/azure/install-azure-cli) を使用し、各リソースを個別に作成します。

> [!TIP]
> * 作成リソース `kind` は `LUIS.Authoring`  
> * 予測リソース `kind` は `LUIS` 

1. Azure CLI にサインインします。

    ```azurecli
    az login
    ```

    このコマンドによりブラウザー開き、正しいアカウントを選択して、認証を行うことができます。

2. 種類が `LUIS.Authoring` で名前が `my-luis-authoring-resource` の LUIS オーサリング リソースを作成します。 それを、`westus` リージョンの `my-resource-group` という名前の "_既存の_" リソース グループに作成します。

    ```azurecli
    az cognitiveservices account create -n my-luis-authoring-resource -g my-resource-group --kind LUIS.Authoring --sku F0 -l westus --yes
    ```

3. 種類が `LUIS` で名前が `my-luis-prediction-resource` の LUIS 予測エンドポイント リソースを作成します。 それを、`westus` リージョンの `my-resource-group` という名前の "_既存の_" リソース グループに作成します。 Free レベルで提供されるものより高いスループットが必要であれば、`F0` を `S0` に変更します。 [価格レベルとスループットに関する詳細を確認してください。](luis-limits.md#key-limits)

    ```azurecli
    az cognitiveservices account create -n my-luis-prediction-resource -g my-resource-group --kind LUIS --sku F0 -l westus --yes
    ```

---


## <a name="assign-luis-resources"></a>LUIS リソースを割り当てる

リソースを作成することは、必ずしも使用することを意味するわけではなく、アプリに割り当てる必要があります。 LUIS の 1 つのアプリまたはすべてのアプリに対して、オーサリング リソースを割り当てることができます。

# <a name="luis-portal"></a>[LUIS ポータル](#tab/portal)

### <a name="assign-resources-using-the-luis-portal"></a>LUIS ポータルを使用してリソースを割り当てる

**すべてのアプリに作成リソースを割り当てる** 

 次の手順では、すべてのアプリに 1 つの作成リソースを割り当てます。

1. [LUIS ポータル](https://www.luis.ai)にサインインします。
1. 右上隅でユーザー アカウントを選択してから、 **[設定]** を選択します。
1. **[ユーザー設定]** ページで、 **[Add authoring resource]\(オーサリング リソースの追加\)** を選択し、既存のオーサリング リソースを選択します。 **[保存]** を選択します。

**特定のアプリにリソースを割り当てる**

次の手順では、特定のアプリにリソースを割り当てます。

1. [LUIS ポータル](https://www.luis.ai)にサインインします。 **[マイ アプリ]** の一覧からアプリを選択します。
1. **[管理]**  >  **[Azure リソース]** に移動します。

    :::image type="content" source="./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png" alt-text="Language Understanding の予測リソースの種類を選択します。" lightbox="./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png":::

1. **[Prediction resource]\(予測リソース\)** タブまたは **[Authoring resource]\(オーサリング リソース\)** タブで、 **[Add prediction resource]\(予測リソースの追加\)** ボタンまたは **[Add authoring resource]\(オーサリング リソースの追加\)** ボタンを選択します。
1. フォーム内のフィールドを使用して適切なリソースを探して、 **[保存]** を選択します。

# <a name="without-luis-portal"></a>[LUIS ポータルなし](#tab/without-portal)

## <a name="assign-prediction-resource-without-using-the-luis-portal"></a>LUIS ポータルを使わずに予測リソースを割り当てる

CI/CD パイプラインのような自動化されたプロセスの場合、次の手順で LUIS アプリへの LUIS リソースの割り当てを自動化することできます。

1. 英数字の文字列である [Azure Resource Manager トークン](https://resources.azure.com/api/token?plaintext=true) を取得します。 このトークンには有効期限があるため、すぐに使用してください。 次の Azure CLI コマンドを使うこともできます。

    ```azurecli
    az account get-access-token --resource=https://management.core.windows.net/ --query accessToken --output tsv
    ```
    
1. そのトークンを使用して、サブスクリプション間の LUIS ランタイム リソースを要求します。 API を使用して、ユーザー アカウントでアクセスできる [LUIS Azure アカウントを取得](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c) します。

    この POST API には、次の値が必要です。

    |ヘッダー|値|
    |--|--|
    |`Authorization`|`Authorization` の値は `Bearer {token}` です。 トークンの値の前に、`Bearer` という単語とスペースを 1 つ追加する必要があります。|
    |`Ocp-Apim-Subscription-Key`|実際のオーサリング キー。|

    API により、LUIS サブスクリプションを表す JSON オブジェクトの配列が返されます。 返された値には、サブスクリプション ID、リソース グループ、および `AccountName` として返されるリソース名が含まれます。 LUIS アプリに割り当てる LUIS リソースである要素を、配列から見つけます。

1. [アプリケーションへの LUIS Azure アカウントの割り当て](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) API を使用して、LUIS リソースにトークンを割り当てます。

    この POST API には、次の値が必要です。

    |Type|設定|値|
    |--|--|--|
    |ヘッダー|`Authorization`|`Authorization` の値は `Bearer {token}` です。 トークンの値の前に、`Bearer` という単語とスペースを 1 つ追加する必要があります。|
    |ヘッダー|`Ocp-Apim-Subscription-Key`|実際のオーサリング キー。|
    |ヘッダー|`Content-type`|`application/json`|
    |Querystring|`appid`|LUIS アプリ ID。
    |Body||{`AzureSubscriptionId`: ご使用のサブスクリプション ID,<br>`ResourceGroup`: 予測リソースがあるリソース グループ名,<br>`AccountName`: 予測リソースの名前}|

    この API が正常に完了すると、`201 - created status` が返されます。

---

## <a name="unassign-a-resource"></a>リソースの割り当てを解除する

リソースの割り当てを解除しても、Azure からそれは削除されません。 LUIS からのリンクが解除されるだけです。

# <a name="luis-portal"></a>[LUIS ポータル](#tab/portal)

## <a name="unassign-resources-using-luis-portal"></a>LUIS ポータルを使用してリソースの割り当てを解除する

1. [LUIS ポータル](https://www.luis.ai)にサインインして、 **[マイ アプリ]** の一覧からアプリを選択します。
1. **[管理]**  >  **[Azure リソース]** に移動します。
1. リソースの **[Unassign resource]\(リソースの割り当て解除\)** ボタンを選択します。

# <a name="without-luis-portal"></a>[LUIS ポータルなし](#tab/without-portal)

## <a name="unassign-prediction-resource-without-using-the-luis-portal"></a>LUIS ポータルを使わずに、予測リソースの割り当てを解除する

1. 英数字の文字列である [Azure Resource Manager トークン](https://resources.azure.com/api/token?plaintext=true) を取得します。 このトークンには有効期限があるため、すぐに使用してください。 次の Azure CLI コマンドを使うこともできます。

    ```azurecli
    az account get-access-token --resource=https://management.core.windows.net/ --query accessToken --output tsv
    ```
 
1. そのトークンを使用して、サブスクリプション間の LUIS ランタイム リソースを要求します。 ユーザー アカウントでアクセスできる [LUIS ユーザー アカウント取得 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c) を使用します。

    この POST API には、次の値が必要です。

    |ヘッダー|値|
    |--|--|
    |`Authorization`|`Authorization` の値は `Bearer {token}` です。 トークンの値の前に、`Bearer` という単語とスペースを 1 つ追加する必要があります。|
    |`Ocp-Apim-Subscription-Key`|実際のオーサリング キー。|

    API により、LUIS サブスクリプションを表す JSON オブジェクトの配列が返されます。 返された値には、サブスクリプション ID、リソース グループ、および `AccountName` として返されるリソース名が含まれます。 LUIS アプリに割り当てる LUIS リソースである要素を、配列から見つけます。

1. [Unassign a LUIS Azure account from an application](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32554f8591db3a86232e1/console) API を使用して、LUIS リソースにトークンを割り当てます。

    この DELETE API には、次の値が必要です。

    |Type|設定|値|
    |--|--|--|
    |ヘッダー|`Authorization`|`Authorization` の値は `Bearer {token}` です。 トークンの値の前に、`Bearer` という単語とスペースを 1 つ追加する必要があります。|
    |ヘッダー|`Ocp-Apim-Subscription-Key`|実際のオーサリング キー。|
    |ヘッダー|`Content-type`|`application/json`|
    |Querystring|`appid`|LUIS アプリ ID。
    |Body||{`AzureSubscriptionId`: ご使用のサブスクリプション ID,<br>`ResourceGroup`: 予測リソースがあるリソース グループ名,<br>`AccountName`: 予測リソースの名前}|

    この API が正常に完了すると、`200 - OK status` が返されます。

---

## <a name="resource-ownership"></a>リソース所有権

LUIS リソースのような Azure リソースは、そのリソースが含まれるサブスクリプションによって所有されています。

リソースの所有権を変更するには、次のいずれかの操作を実行します。
* サブスクリプションの[所有権](../../cost-management-billing/manage/billing-subscription-transfer.md)を譲渡します。
* LUIS アプリをファイルとしてエクスポートしてから、別のサブスクリプションにアプリをインポートします。 エクスポートは、LUIS ポータルの **[マイ アプリ]** ページで使用できます。

## <a name="resource-limits"></a>リソース制限

### <a name="authoring-key-creation-limits"></a>オーサリング キーの作成の制限

サブスクリプションごとに、各リージョンで最大 10 個のオーサリング キーを作成できます。 公開リージョンは、オーサリング リージョンとは異なります。 クライアント アプリケーションを配置する公開リージョンに対応するオーサリング リージョンにアプリを作成してください。 オーサリング リージョンを公開リージョンにマップする方法の詳細については、[オーサリング リージョンと公開リージョン](luis-reference-regions.md)に関するページを参照してください。 

キーの制限の詳細については、[キーの制限](luis-limits.md#key-limits)に関するページを参照してください。

### <a name="errors-for-key-usage-limits"></a>キーの使用制限に関するエラー

使用制限は、価格レベルに基づいています。

1 秒あたりのトランザクション数 (TPS) のクォータを超えると、HTTP 429 エラーが発生します。 1 か月あたりのトランザクション数 (TPM) のクォータを超えると、HTTP 403 エラーが発生します。

## <a name="change-the-pricing-tier"></a>価格レベルを変更する

1.  [Azure portal](https://portal.azure.com) で **[すべてのリソース]** に移動して、リソースを検索します。

    :::image type="content" source="./media/luis-usage-tiers/find.png" alt-text="Azure portal での LUIS サブスクリプションを示すスクリーンショット。" lightbox="./media/luis-usage-tiers/find.png":::

1.  左側のメニューから **[価格レベル]** を選択して、使用可能な価格レベルを確認します。
1.  使用したい価格レベルを選択し、 **[選択]** をクリックして変更内容を保存します。 価格の変更が完了すると、価格レベル更新の通知が右上に表示されます。

## <a name="view-azure-resource-metrics"></a>Azure リソースのメトリックを表示する

## <a name="view-a-summary-of-azure-resource-usage"></a>Azure リソースの使用状況の概要を表示する
LUIS の使用状況に関する情報は Azure portal で見ることができます。 **[概要]** ページには、最近の呼び出しやエラーなどの概要が示されます。 LUIS エンドポイント要求を行う場合、変更が表示されるまで最大 5 分かかります。

:::image type="content" source="./media/luis-usage-tiers/overview.png" alt-text="[概要] ページを示すスクリーンショット" lightbox="./media/luis-usage-tiers/overview.png":::

## <a name="customizing-azure-resource-usage-charts"></a>Azure リソースの使用状況グラフのカスタマイズ
**[メトリック]** ページには、データのさらに詳細なビューが表示されます。 特定の **期間** と **メトリック** のメトリック グラフを構成できます。

:::image type="content" source="./media/luis-usage-tiers/metrics.png" alt-text="[メトリック] ページを示すスクリーンショット。" lightbox="./media/luis-usage-tiers/metrics.png":::

## <a name="total-transactions-threshold-alert"></a>トランザクション合計しきい値のアラート
トランザクションしきい値に達したこと (たとえば、トランザクション数が 10,000 になったこと) を知るには、アラートを作成します。

1. 左側のメニューから、 **[アラート]** を選択します。
2. 上部のメニューで、 **[新しいアラート ルール]** を選択します。

    :::image type="content" source="./media/luis-usage-tiers/alerts.png" alt-text="[アラート ルール] ページを示すスクリーンショット。" lightbox="./media/luis-usage-tiers/alerts.png":::

3. **[条件の追加]** をクリックします。

    :::image type="content" source="./media/luis-usage-tiers/alerts-2.png" alt-text="アラート ルールの [条件の追加] ページを示すスクリーンショット。" lightbox="./media/luis-usage-tiers/alerts-2.png":::

4. **[呼び出し合計]** を選択する

    :::image type="content" source="./media/luis-usage-tiers/alerts-3.png" alt-text="アラートの [呼び出し合計] ページを示すスクリーンショット。" lightbox="./media/luis-usage-tiers/alerts-3.png":::

5. **[アラート ロジック]** セクション まで下にスクロールし、必要に応じて属性を設定し、 **[完了]** をクリック します。

    :::image type="content" source="./media/luis-usage-tiers/alerts-4.png" alt-text="[アラート ロジック] ページを示すスクリーンショット。" lightbox="./media/luis-usage-tiers/alerts-4.png":::

6. アラート ルールのトリガー時に通知を送信したり、アクションを呼び出したりするには、 **[アクション]** セクションに移動し、アクション グループを追加します。

    :::image type="content" source="./media/luis-usage-tiers/alerts-5.png" alt-text="アラートの [アクション] ページを示すスクリーンショット。" lightbox="./media/luis-usage-tiers/alerts-5.png":::

### <a name="reset-an-authoring-key"></a>オーサリング キーをリセットする

[移行されたオーサリング リソース](luis-migration-authoring.md) アプリの場合: オーサリング キーが侵害された場合は、Azure portal のそのオーサリング リソースの **[キー]** ページでキーをリセットします。

移行されていないアプリの場合: LUIS ポータルのすべてのアプリでキーがリセットされます。 オーサリング API を使用してアプリを作成する場合は、`Ocp-Apim-Subscription-Key` の値を新しいキーに変更する必要があります。

### <a name="regenerate-an-azure-key"></a>Azure キーを再生成する

Azure portal の **[キー]** ページから、Azure キーを再生成できます。

<a name="securing-the-endpoint"></a>

## <a name="app-ownership-access-and-security"></a>アプリの所有権、アクセス、セキュリティ

アプリは Azure リソースによって定義され、それは所有者のサブスクリプションによって決まります。

LUIS アプリを移行できます。 Azure portal または Azure CLI を使用してそれを行う方法については、次のリソースを参照してください。

* [LUIS のオーサリング リソース間でアプリを移行する](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [リソースを新しいリソース グループまたはサブスクリプションに移動する](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [同じサブスクリプション内またはサブスクリプション間でリソースを移行する](../../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)


## <a name="next-steps"></a>次のステップ

* [バージョンを使用](luis-how-to-manage-versions.md)してアプリのライフ サイクルを制御する方法を確認します。


