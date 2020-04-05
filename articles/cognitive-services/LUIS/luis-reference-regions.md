---
title: 公開リージョンとエンドポイント - LUIS
description: Azure portal で指定するリージョンは、LUIS アプリを発行する場所と同じであり、この同じリージョンに対してエンドポイント URL が生成されます。
ms.topic: reference
ms.date: 11/19/2019
ms.openlocfilehash: a7709d15a51637772d7a4775cd71c307dc21c52b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292081"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>作成と公開のリージョンと関連付けられているキー

3 つのオーサリング リージョンは、対応する LUIS ポータルによってサポートされます。 LUIS アプリを複数のリージョンに公開するには、リージョンごとに少なくとも 1 つのキーが必要です。

<a name="luis-website"></a>

## <a name="luis-authoring-regions"></a>LUIS オーサリング リージョン
リージョンに基づいて、3 つの LUIS オーサリング ポータルがあります。 作成と公開は同じリージョンで行う必要があります。

|LUIS|オーサリング リージョン|Azure リージョン名|
|--|--|--|
|[www.luis.ai][www.luis.ai] <br>[preview.luis.ai](https://preview.luis.ai)|米国<br>ヨーロッパ以外<br>オーストラリア以外| `westus`|
|[au.luis.ai][au.luis.ai] <br>[preview.au.luis.ai](https://preview.au.luis.ai)|オーストラリア| `australiaeast`|
|[eu.luis.ai][eu.luis.ai] <br>[preview.eu.luis.ai](https://preview.eu.luis.ai)|ヨーロッパ|`westeurope`|

オーサリング リージョンには、[ペア フェールオーバー リージョン](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)があります。

<a name="regions-and-azure-resources"></a>

## <a name="publishing-regions-and-azure-resources"></a>公開リージョンと Azure リソース
アプリは、LUIS ポータルで追加された LUIS リソースと関連付けられているすべてのリージョンに発行されます。 たとえば、[www.luis.ai][www.luis.ai] で作成されたアプリの場合は、**westus** で LUIS または Cognitive Service リソースを作成し、[それをリソースとしてアプリに追加する](luis-how-to-azure-subscription.md)と、アプリはそのリージョンで公開されます。

## <a name="public-apps"></a>パブリック アプリ
パブリック アプリはすべてのリージョンで公開されるので、リージョン ベースの LUIS リソース キーを持つユーザーは、どのリージョンがそれらのリソース キーと関連付けられているかにかかわらず、アプリにアクセスできます。

<a name="publishing-regions"></a>

## <a name="publishing-regions-are-tied-to-authoring-regions"></a>公開リージョンはオーサリング リージョンに関連付けられています。

オーサリング リージョン アプリは、対応する公開リージョンにのみ公開できます。 お使いのアプリが間違ったオーサリング リージョンにある場合は、アプリをエクスポートし、ご自身の公開リージョンの正しいオーサリング リージョンにインポートしてください。

作成した LUIS アプリ https://www.luis.ai 以外のすべてのエンドポイントに発行できる、[欧州](#publishing-to-europe)と[オーストラリア](#publishing-to-australia)リージョン。

## <a name="publishing-to-europe"></a>ヨーロッパへの公開

ヨーロッパ リージョンに公開するには、 https://eu.luis.ai でのみ LUIS を作成します。 それ以外の場所で、ヨーロッパ リージョンのキーを使って公開しようとすると、LUIS によって警告メッセージが表示されます。 代わりに https://eu.luis.ai を使用してください。 [https://eu.luis.ai][eu.luis.ai] で作成された LUIS アプリは、他のリージョンに自動的には移行されません。 移行するには、その LUIS アプリをエクスポートしてから、インポートします。

## <a name="europe-publishing-regions"></a>ヨーロッパの公開リージョン

 グローバル リージョン | オーサリング API リージョンとオーサリング Web サイト| 公開およびクエリ実行リージョン<br>`API region name`   |  エンドポイント URL 形式   |
|-----|------|------|------|
| [ヨーロッパ](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| フランス中部<br>`francecentral`     | `https://francecentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| [ヨーロッパ](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| 北ヨーロッパ<br>`northeurope`     | `https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| [ヨーロッパ](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| 西ヨーロッパ<br>`westeurope`    |  `https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| [ヨーロッパ](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| 英国南部<br>`uksouth`    |  `https://uksouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="publishing-to-australia"></a>オーストラリアへの公開

オーストラリア リージョンに公開するには、 https://au.luis.ai でのみ LUIS を作成します。 それ以外の場所で、オーストラリア リージョンのキーを使って公開しようとすると、LUIS によって警告メッセージが表示されます。 代わりに https://au.luis.ai を使用してください。 [https://au.luis.ai][au.luis.ai] で作成された LUIS アプリは、他のリージョンに自動的には移行されません。 移行するには、その LUIS アプリをエクスポートしてから、インポートします。

## <a name="australia-publishing-regions"></a>オーストラリアの公開リージョン

 グローバル リージョン | オーサリング API リージョンとオーサリング Web サイト| 公開およびクエリ実行リージョン<br>`API region name`   |  エンドポイント URL 形式   |
|-----|------|------|------|
| [オーストラリア](#publishing-to-australia) | `australiaeast`<br>[au.luis.ai][au.luis.ai]| オーストラリア東部<br>`australiaeast`     |  `https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="publishing-to-other-regions"></a>他のリージョンへの公開

他のリージョンに公開するには、[https://www.luis.ai](https://www.luis.ai) でのみ LUIS アプリを作成します。

## <a name="other-publishing-regions"></a>その他の公開リージョン

 グローバル リージョン | オーサリング API リージョンとオーサリング Web サイト| 公開およびクエリ実行リージョン<br>`API region name`   |  エンドポイント URL 形式   |
|-----|------|------|------|
| アフリカ | `westus`<br>[www.luis.ai][www.luis.ai]| 南アフリカ北部<br>`southafricanorth` |  `https://southafricanorth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| アジア | `westus`<br>[www.luis.ai][www.luis.ai]| インド中部<br>`centralindia` |  `https://centralindia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| アジア | `westus`<br>[www.luis.ai][www.luis.ai]| 東アジア<br>`eastasia`     |  `https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| アジア | `westus`<br>[www.luis.ai][www.luis.ai]| 東日本<br>`japaneast`     |   `https://japaneast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| アジア | `westus`<br>[www.luis.ai][www.luis.ai]| 西日本<br>`japanwest`     |   `https://japanwest.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| アジア | `westus`<br>[www.luis.ai][www.luis.ai]| 韓国中部<br>`koreacentral`     |   `https://koreacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| アジア | `westus`<br>[www.luis.ai][www.luis.ai]| 東南アジア<br>`southeastasia`     |   `https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| 北米 |`westus`<br>[www.luis.ai][www.luis.ai] | カナダ中部<br>`canadacentral`     |   `https://canadacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| 北米 |`westus`<br>[www.luis.ai][www.luis.ai] | 米国中部<br>`centralus`     |   `https://centralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| 北米 |`westus`<br>[www.luis.ai][www.luis.ai] | East US<br>`eastus`      |  `https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| 北米 | `westus`<br>[www.luis.ai][www.luis.ai] | 米国東部 2<br>`eastus2`     |  `https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| 北米 | `westus`<br>[www.luis.ai][www.luis.ai] | 米国中北部<br>`northcentralus`  |  `https://northcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| 北米 | `westus`<br>[www.luis.ai][www.luis.ai] | 米国中南部<br>`southcentralus`  |  `https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| 北米 |`westus`<br>[www.luis.ai][www.luis.ai] | 米国中西部<br>`westcentralus`    |  `https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| 北米 | `westus`<br>[www.luis.ai][www.luis.ai] | 米国西部<br>`westus`  |   `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`  |
| 北米 |`westus`<br>[www.luis.ai][www.luis.ai] | 米国西部 2<br>`westus2`    |  `https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`  |
| 南アメリカ | `westus`<br>[www.luis.ai][www.luis.ai] | ブラジル南部<br>`brazilsouth`    |  `https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="endpoints"></a>エンドポイント

[オーサリングおよび予測エンドポイント](developer-reference-resource.md)の詳細について説明します。

## <a name="failover-regions"></a>フェールオーバー リージョン

各リージョンには、フェールオーバー先のセカンダリ リージョンがあります。 ヨーロッパではヨーロッパ内でフェールオーバーし、オーストラリアではオーストラリア内でフェールオーバーします。

オーサリング リージョンには、[ペア フェールオーバー リージョン](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)があります。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [事前構築済みのエンティティのリファレンス](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai
