---
title: Azure Functions の Premium プラン
description: Azure Functions の Premium プランの詳細と構成オプション (VNet、コールド スタートなし、無制限の実行期間)。
author: jeffhollan
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: jehollan
ms.custom:
- references_regions
- fasttrack-edit
- devx-track-azurecli
ms.openlocfilehash: 7efcff5709995898a6ec950dfea6450f7e0dd48d
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2020
ms.locfileid: "92736795"
---
# <a name="azure-functions-premium-plan"></a>Azure Functions の Premium プラン

Azure Functions の Premium プラン (Elastic Premium プランとも呼ばれます) は、関数アプリのホスティング オプションです。 Premium プランでは、VNet 接続、コールド スタートなし、プレミアム ハードウェアなどの機能が提供されます。  複数の関数アプリを同じ Premium プランにデプロイできます。プランでは、コンピューティング インスタンス サイズ、基本プラン サイズ、および最大プラン サイズを構成できます。  Premium プランと、他のプランおよびホスティング タイプの比較については、[関数のスケールとホスティング オプション](functions-scale.md)に関するページを参照してください。

## <a name="create-a-premium-plan"></a>Premium プランを作成する

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]

Premium プランは、Azure CLI から [az functionapp plan create](/cli/azure/functionapp/plan#az-functionapp-plan-create) を使用して作成することもできます。 次の例では、 _Elastic Premium 1_ レベルのプランを作成しています。

```azurecli-interactive
az functionapp plan create --resource-group <RESOURCE_GROUP> --name <PLAN_NAME> \
--location <REGION> --sku EP1
```

この例の `<RESOURCE_GROUP>` と `<PLAN_NAME>` は、それぞれ実際のリソース グループおよびリソース グループ内で一意となる実際のプランの名前に置き換えてください。 [サポートされる `<REGION>`](https://azure.microsoft.com/global-infrastructure/services/?products=functions) を指定します。 Linux をサポートする Premium プランを作成するには、`--is-linux` オプションを指定します。

プランが作成されたら、[az functionapp create](/cli/azure/functionapp#az-functionapp-create) を使用して関数アプリを作成できます。 ポータルで、プランとアプリの両方が同時に作成されます。 完全な Azure CLI スクリプトの例については、「[Premium プランの関数アプリを作成する](scripts/functions-cli-create-premium-plan.md)」を参照してください。

## <a name="features"></a>特徴

Premium プランにデプロイされた関数アプリでは、次の機能を利用できます。

### <a name="always-ready-instances"></a>常時使用可能なインスタンス

従量課金プランで今日はイベントも実行も発生しない場合、ゼロ インスタンスまでアプリをスケールインできます。 新しいイベントが発生したら、新しいインスタンスを専用化して、そこでアプリを実行する必要があります。  アプリによっては、新しいインスタンスの専用化に時間がかかる場合があります。  最初の呼び出しでこのように待機時間が長くなることを、アプリのコールド スタートと呼ぶことがよくあります。

Premium プランでは、指定された数のインスタンスでアプリを常時使用可能にしておくことができます。  常時使用可能なインスタンスの最大数は 20 です。  イベントがアプリのトリガーを開始すると、そのイベントは最初に常時使用可能なインスタンスにルーティングされます。  関数がアクティブになると、追加のインスタンスがバッファーとしてウォーミングされます。  このバッファーは、スケール中に要求された新しいインスタンスのコールド スタートを防止します。  これらのバッファーに格納されたインスタンスは、[事前ウォーミングされたインスタンス](#pre-warmed-instances)と呼ばれます。  常時使用可能なインスタンスと事前ウォーミングされたバッファーを組み合わせることで、アプリは効果的にコールド スタートを排除できます。

> [!NOTE]
> すべての Premium プランには、常に 1 つ以上のアクティブな (課金された) インスタンスがあります。

Azure portal で常時使用可能なインスタンスの数を構成するには、選択した **関数アプリ** から、 **[プラットフォーム機能]** タブへ移動して、 **[スケールアウト]** オプションを選択します。 関数アプリの編集ウィンドウでは、常時使用可能なインスタンスはそのアプリに固有のものです。

![エラスティック スケールの設定](./media/functions-premium-plan/scale-out.png)

アプリの常時使用可能なインスタンスは、Azure CLI を使用して構成することもできます。

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.minimumElasticInstanceCount=<desired_always_ready_count> --resource-type Microsoft.Web/sites
```

#### <a name="pre-warmed-instances"></a>事前ウォーミングされたインスタンス

事前ウォーミングされたインスタンスは、スケールおよびアクティブ化イベント中にバッファーとしてウォーミングされるインスタンスの数です。  事前ウォーミングされたインスタンスは、スケールアウトの上限に達するまでバッファーに格納され続けます。  事前ウォーミングされたインスタンスの既定の数は 1 であり、ほとんどのシナリオでは 1 のままにしておく必要があります。  アプリが長時間のウォームアップ (カスタム コンテナー イメージなど) を行う場合は、このバッファーを増やすことができます。  事前ウォーミングされたインスタンスは、すべてのアクティブなインスタンスが十分に利用された後にのみ、アクティブになります。

常時使用可能なインスタンスと事前ウォーミングされたインスタンスが連携して動作する例について考えてみましょう。  Premium 関数アプリに、5 つの常時使用可能なインスタンスが構成されており、事前ウォーミングされたインスタンスが既定で 1 つあります。  アプリがアイドル状態であり、トリガーしているイベントがない場合、このアプリは 5 つのインスタンスでプロビジョニングされ、実行されます。  現時点で、常時使用可能なインスタンスが使用されておらず、事前ウォーミングされたインスタンスも割り当てられていないため、事前ウォーミングされたインスタンスに対して課金されることはありません。

最初のトリガーが開始されるとすぐに、5 つの常時使用可能なインスタンスがアクティブになり、事前ウォーミングされたインスタンスが割り当てられます。  アプリは 6 つのプロビジョニングされたインスタンスを使用して実行されています。つまり、5 つの現在アクティブな常時使用可能なインスタンスと、6 番目の事前ウォーミングされた非アクティブなバッファーです。  実行率が増加し続けた場合は、最終的に 5 つのアクティブなインスタンスが使用されます。  5 つのインスタンスを超えて拡張することがプラットフォームによって決定された場合は、事前ウォーミングされたインスタンスに拡張されます。  その場合、6 つのアクティブなインスタンスが存在することになり、7 番目のインスタンスがすぐにプロビジョニングされ、事前ウォーミングされたバッファーに格納されます。  この一連のスケーリングと事前ウォーミングは、アプリの最大インスタンス数に達するまで続行されます。  最大値を超えてインスタンスが事前ウォーミングまたはアクティブ化されることはありません。

アプリの事前ウォーミングされたインスタンスの数は、Azure CLI を使用して変更できます。

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.preWarmedInstanceCount=<desired_prewarmed_count> --resource-type Microsoft.Web/sites
```

#### <a name="maximum-instances-for-an-app"></a>アプリの最大インスタンス数

[プランの最大インスタンス数](#plan-and-sku-settings)に加えて、アプリごとの最大数を構成できます。  アプリの最大数は、[アプリのスケール制限](./functions-scale.md#limit-scale-out)を使用して構成できます。

### <a name="private-network-connectivity"></a>プライベート ネットワーク接続

Premium プランにデプロイされた Azure Functions では、[Web アプリ向けの新しい VNet 統合](../app-service/web-sites-integrate-with-vnet.md)を利用します。  構成すると、アプリを VNet 内のリソースと通信させる、またはサービス エンドポイントを介してセキュリティで保護することができます。  受信トラフィックを制限するための IP 制限もアプリで利用できます。

Premium プランで関数アプリにサブネットを割り当てるときは、個々の潜在的インスタンスのための十分な IP アドレスがあるサブネットが必要です。 使用可能なアドレスが 100 以上の IP ブロックが必要です。

詳細については、[お使いの関数アプリと VNet の統合](functions-create-vnet.md)に関するページを参照してください。

### <a name="rapid-elastic-scale"></a>高速エラスティック スケール

従量課金プランと同じ高速スケーリング ロジックを使用して、追加のコンピューティング インスタンスが自動的にアプリのために追加されます。 同じ Azure App Service のアプリは、個々のアプリのニーズに基づき、互いに依存せずにスケーリングします。 ただし、同じ Azure App Service の関数アプリでは、可能であれば、コストを下げる目的で VM リソースが共有されます。 VM に関連付けられているアプリの数は、各アプリの占有領域と VM のサイズによって変わります。

スケーリングのしくみの詳細については、[関数のスケールとホスティング](./functions-scale.md#how-the-consumption-and-premium-plans-work)に関するページを参照してください。

### <a name="longer-run-duration"></a>実行継続時間の延長

Azure Functions の従量課金プランでは、1 回の実行が 10 分までに制限されています。  Premium プランでは、実行中の暴走を防ぐために、実行継続時間の既定値が 30 分になっています。 ただし、[host.json 構成を変更](./functions-host-json.md#functiontimeout)して、Premium プランのアプリの継続時間を無制限 (60 分を保証) にすることができます。

## <a name="plan-and-sku-settings"></a>プランと SKU の設定

プランを作成するときは、2 つのプラン サイズ設定があります。インスタンスの最小数 (またはプラン サイズ) と最大バースト制限です。

常時使用可能なインスタンス数を超えるインスタンスがアプリに必要な場合、インスタンスの数が最大バースト制限に達するまでアプリのスケール アウトを続けることができます。  プラン サイズを超えたインスタンスに対する課金は、それらが実行されていて、秒あたりの単位で割り当てられている間のみ発生します。  定義された最大制限までのアプリのスケールアウトに関してはベスト エフォートでの提供となります。

プラン サイズまたは最大値は Azure portal で構成できます。プランまたはそのプランにデプロイされている関数アプリの **[スケール アウト]** オプションを選択します ( **[プラットフォーム機能]** の下にあります)。

Azure CLI から最大バースト制限を増やすこともできます。

```azurecli-interactive
az functionapp plan update -g <resource_group> -n <premium_plan_name> --max-burst <desired_max_burst>
```

各プランの最小値は、1 つ以上のインスタンスになります。  実際のインスタンスの最小数は、プラン内のアプリによって要求された常時使用可能なインスタンス数に基づいて自動構成されます。  たとえば、アプリ A が 5 つの常時使用可能なインスタンスを要求し、同じプラン内でアプリ B が 2 つの常時使用可能なインスタンスを要求した場合、最小プラン サイズは 5 として計算されます。  アプリ A は 5 つのすべてで実行され、アプリ B は 2 つでのみ実行されます。

> [!IMPORTANT]
> 関数が実行されているかどうかにかかわらず、最小インスタンスで割り当てられているインスタンスごとに課金されます。

ほとんどの場合、この自動計算される最小値で十分のはずです。  ただし、最小値を超えるスケーリングはベスト エフォートで実行されます。  追加のインスタンスを使用できない場合は、まれですが、特定の時間にスケールアウトが遅延する可能性があります。  自動計算された最小値よりも大きな最小値を設定することにより、スケールアウトの前にインスタンスを予約します。

プランの計算された最小値を増やすには、Azure CLI を使用します。

```azurecli-interactive
az functionapp plan update -g <resource_group> -n <premium_plan_name> --min-instances <desired_min_instances>
```

### <a name="available-instance-skus"></a>利用可能インスタンス SKU

プランを作成またはスケーリングするときは、3 つのインスタンス サイズから選択できます。  プロビジョニングされたコアとメモリの合計数に対して、各インスタンスが割り当てられている秒単位で課金されます。  アプリは必要に応じて自動的に複数のインスタンスにスケール アウトできます。

|SKU|コア|メモリ|ストレージ|
|--|--|--|--|
|EP1|1|3.5 GB|250 GB|
|EP2|2|7 GB|250 GB|
|EP3|4|14 GB|250 GB|

### <a name="memory-utilization-considerations"></a>メモリ使用率に関する注意点
メモリの多いコンピューターでお使いの関数アプリを実行しても、利用可能なすべてのメモリを使用できるとは限りません。

たとえば、JavaScript 関数アプリには、Node.js の既定のメモリ上限の制限があります。 この固定のメモリ制限を増やすには、値に `--max-old-space-size=<max memory in MB>` を使用して `languageWorkers:node:arguments` のアプリ設定を追加します。

## <a name="region-max-scale-out"></a>リージョン最大スケール アウト

以下は、1 つのプランでサポートされている最大スケールアウト値をリージョンと OS の構成ごとにまとめたものです。 増加を依頼するには、サポート チケットをご利用ください。

Functions のリージョン別アベイラビリティはすべて、[Azure.com](https://azure.microsoft.com/global-infrastructure/services/?products=functions) で確認できます。

|リージョン| Windows | Linux |
|--| -- | -- |
|オーストラリア中部| 100 | 利用不可 |
|オーストラリア中部 2| 100 | 利用不可 |
|オーストラリア東部| 100 | 20 |
|オーストラリア南東部 | 100 | 20 |
|ブラジル南部| 100 | 20 |
|カナダ中部| 100 | 20 |
|米国中部| 100 | 20 |
|中国東部 2| 100 | 20 |
|中国北部 2| 100 | 20 |
|東アジア| 100 | 20 |
|米国東部 | 100 | 20 |
|米国東部 2| 100 | 20 |
|フランス中部| 100 | 20 |
|ドイツ中西部| 100 | 利用不可 |
|東日本| 100 | 20 |
|西日本| 100 | 20 |
|韓国中部| 100 | 20 |
|韓国南部| 利用不可 | 20 |
|米国中北部| 100 | 20 |
|北ヨーロッパ| 100 | 20 |
|ノルウェー東部| 100 | 20 |
|米国中南部| 100 | 20 |
|インド南部 | 100 | 利用不可 |
|東南アジア| 100 | 20 |
|スイス北部| 100 | 利用不可 |
|スイス西部| 100 | 利用不可 |
|英国南部| 100 | 20 |
|英国西部| 100 | 20 |
|USGov アリゾナ| 100 | 20 |
|USGov バージニア州| 100 | 20 |
|USNat 東部| 100 | 利用不可 |
|USNat 西部| 100 | 利用不可 |
|西ヨーロッパ| 100 | 20 |
|インド西部| 100 | 20 |
|米国中西部| 100 | 20 |
|米国西部| 100 | 20 |
|米国西部 2| 100 | 20 |

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Functions のスケールとホスティングのオプションを理解する](functions-scale.md)
