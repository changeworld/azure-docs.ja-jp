---
title: パブリック マルチテナント App Service での可用性ゾーンのサポート
description: アプリがゾーン冗長になるようにアプリ サービスをデプロイする方法について説明します。
author: seligj95
ms.topic: article
ms.date: 11/16/2021
ms.author: jordanselig
ms.custom: references_regions
ms.openlocfilehash: e0388695708e8ab3c47ea8049038fb95384dabd2
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132551295"
---
# <a name="availability-zone-support-for-public-multi-tenant-app-service"></a>パブリック マルチテナント App Service での可用性ゾーンのサポート

Microsoft Azure App Service を[可用性ゾーン (AZ)](../availability-zones/az-overview.md) にデプロイして、ビジネス クリティカルなワークロードの回復性と信頼性を実現するのに役立てることができます。 このアーキテクチャは、ゾーン冗長とも呼ばれます。

アプリは App Service プラン (ASP) で稼働し、App Service プランは単一のスケール ユニット内に存在します。 アプリ サービスがゾーン冗長として構成されていると、プラットフォームによって、選択されたリージョン内の 3 つのゾーンすべてに対して App Service プランの VM インスタンスが自動的に分散されます。 3 を超える容量が指定されていて、インスタンスの数が 3 で割り切れる場合は、インスタンスが均等に分散されます。 それ以外の場合は、3*N を超えるインスタンス数が残りの 1 つまたは 2 つのゾーンに対して分散されます。 ゾーン冗長に構成されていない App Services の場合、VM インスタンスは選択したリージョンの単一ゾーンに配置されます。

## <a name="requirements"></a>必要条件

ゾーン冗長は App Service プランのプロパティです。 ゾーン冗長を有効にするための現在の要件および制限は次のとおりです。

- Windows と Linux はどちらもサポートされている
- **Premium v2** または **Premium v3** App Service プランが必要
- 最小インスタンス数は 3
  - インスタンス数を 3 未満に指定すると、この最小数がプラットフォームによってバックグラウンドで強制されます
- 次のいずれかのリージョンで有効にすることが可能:
  - 米国西部 2
  - 米国西部 3
  - 米国中部
  - 米国東部
  - 米国東部 2
  - カナダ中部
  - ブラジル南部
  - 北ヨーロッパ
  - 西ヨーロッパ
  - ドイツ中西部
  - フランス中部
  - 英国南部
  - 東日本
  - 東南アジア
  - オーストラリア東部
- ゾーン冗長を指定できるのは、**新しい** App Service プランを作成するときのみ
  - 現在、既存の App Service プランを変換することはできません。 ゾーン冗長をサポートする新しい App Service プランを作成する方法の詳細については、次の箇条書き項目を参照してください。
- ゾーン冗長がサポートされるのは、App Service フットプリントの新しい部分でのみ
  - 現在 Pv3 で動作している場合、ゾーン冗長がサポートされるフットプリントを既に利用している可能性があります。 このシナリオでは、新しい App Service プランを作成するときに新しい App Service プランを作成してゾーン冗長を指定できます。
  - Pv3 またはゾーン冗長をサポートするスケール ユニットを使用していない場合、サポート対象外のリージョンにいる場合、よくわからない場合は、以下の手順に従ってください。
    - サポートされているリージョンに新しいリソース グループを作成します
        - これにより、ゾーン冗長をサポートしている選択済みリージョンのスケール ユニットを App Service コントロール プレーンが検出できます
    - **新しい** リソース グループを使用して任意のリージョンに新しい App Service プラン (とアプリ) を作成します
    - 新しい App Service プランの作成時に zoneRedundant プロパティ (下で説明します) が true に設定されていることを確認します
- [Azure Resource Manager (ARM) テンプレート](../azure-resource-manager/templates/overview.md)を使用して作成する必要がある

ゾーンがダウンした場合は、App Service プラットフォームによって、失われたインスタンスが検出され、新しい置換インスタンスの検索が自動的に試行されます。 自動スケーリングも構成している場合は、より多くのインスタンスが必要と判断されると、自動スケーリングによって App Service への要求が発行され、インスタンスが追加されます (自動スケーリング動作は App Service のプラットフォーム動作に依存していません)。 失われたインスタンスの補填はベストエフォートで実行されるため、ゾーンがダウンするシナリオにおける追加インスタンスの要求は、成功するとは限らないことに注意する必要があります。 この記事の次のセクションで説明しているとおり、ゾーンの喪失に対応できるように App Service プランをプロビジョニングすることをソリューションとしてお勧めします。

ゾーン冗長が有効な App Service プランでデプロイされたアプリケーションは、同じリージョン内の他のゾーンで障害が起こった場合もトラフィックを実行および提供し続けます。 ただし、非実行時の動作 (App Service プランのスケーリング、アプリケーションの作成、アプリケーションの構成、およびアプリケーションの発行を含む) では、他の可用性ゾーンの障害から影響を受ける可能性があります。 App Service プランのゾーン冗長で確保されるのは、デプロイされたアプリケーションの継続的なアップタイムのみです。

App Service プラットフォームによってインスタンスがゾーン冗長 App Service プランに割り当てられると、[基になる Azure Virtual Machine Scale Sets によって提供されるベスト エフォートのゾーン負荷分散](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md#zone-balancing)が使用されます。 App Service プランの "バランスが取れる" のは、各ゾーンに、その App Service プランで使用される他のすべてのゾーンと同じ数の VM または +/- 1 個の VM がある場合です。

## <a name="how-to-deploy-a-zone-redundant-app-service"></a>ゾーン冗長アプリ サービスをデプロイする方法

現在、ゾーン冗長アプリ サービスを作成するには ARM テンプレートを使用する必要があります。 ARM テンプレートを使用して作成した App Service プランは、Azure portal および CLI ツールを使用して表示、操作できるようになります。 ARM テンプレートは、App Service プランを最初に作成するときにのみ必要となります。

ゾーン冗長アプリ サービスを指定するための ARM テンプレートでは、***zoneRedundant** _ プロパティ (必須) と、[Microsoft.Web/serverfarms](/azure/templates/microsoft.web/serverfarms?tabs=json) リソース上の App Service プラン インスタンス数 (_*_capacity_*_) (省略可能) のみ変更する必要があります。 容量を指定しない場合は、プラットフォームによって既定値 3 に設定されます。 _*_zoneRedundant_*_ プロパティを _*_true_*_ に設定する必要があります。また、ワークロード要件に基づいて _ *_capacity_** を設定する必要がありますが、3 未満にしないようにします。 大まかに言えば、インスタンスのゾーンが 1 つ失われても予想される負荷を処理するのに十分な容量が残っているように、アプリケーションに十分なインスタンスを確保することが、容量を選択するうえで大切です。

> [!TIP]
> インスタンスの容量を決定するには、次の計算を使用できます。
>
> プラットフォームによって VM が 3 つのゾーンに分散されていて、少なくとも 1 つのゾーンの障害に対応できる必要がある場合は、ピーク ワークロードのインスタンス数にゾーン数/(ゾーン数 -1)、つまり 3/2 をかけます。 たとえば、通常のピーク ワークロードに 4 つのインスタンスが必要な場合、6 つのインスタンスをプロビジョニングする必要があります: (2/3 * 6 インスタンス) = 4 インスタンス。
>

下の ARM テンプレート スニペットは、新しい ***zoneRedundant** _ プロパティと _ *_capacity_** 仕様を示しています。

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "your-appserviceplan-name-here",
    "location": "West US 3",
    "sku": {
        "name": "P1v3",
        "tier": "PremiumV3",
        "size": "P1v3",
        "family": "Pv3",
        "capacity": 3
    },
    "kind": "app",
    "properties": {
        "zoneRedundant": true
    }
  }
]
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [ARM テンプレートを作成してデプロイする方法を学習する](../azure-resource-manager/templates/quickstart-create-templates-use-visual-studio-code.md)

> [!div class="nextstepaction"]
> [ARM クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/)
