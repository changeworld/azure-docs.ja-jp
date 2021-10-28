---
title: Azure API Management コンピューティング プラットフォーム
description: API Management サービス インスタンスのホストとして使用するコンピューティング プラットフォームについて説明します
author: dlepow
ms.service: api-management
ms.topic: conceptual
ms.date: 08/23/2021
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 23d54c2725e48b1d587fb641c593be0c8d63958f
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130248642"
---
# <a name="compute-platform-for-azure-api-management"></a>Azure API Management のコンピューティング プラットフォーム

クラウドの PaaS (サービスとしてのプラットフォーム) である Azure API Management では、サービスのホストと実行に使用されるインフラストラクチャのさまざまな点が抽象化されています。 API Management インスタンスは、その基盤となるリソースについて知らなくても、ほとんどの要素を作成、管理、スケーリングすることができます。

Microsoft はサービスの機能を強化するため、いくつかの[サービス レベル](api-management-features.md)のインスタンスを対象に、API Management のコンピューティング プラットフォーム バージョン、つまりサービスのホストとなる Azure コンピューティング リソースをアップグレードしています。 この記事では、API Management のコンピューティング プラットフォームのメジャー バージョン (`stv1` および `stv2`) とアップグレードの背景について取り上げます。

このアップグレードが API Management インスタンスの操作に及ぼす影響は最小限に抑えられています。 ただし、インスタンスが [Azure 仮想ネットワーク](virtual-network-concepts.md)に接続されている場合、インスタンスを `stv2` プラットフォーム バージョンにアップグレードする際に、ネットワーク構成の設定を一部変更する必要があります。

## <a name="compute-platform-versions"></a>コンピューティング プラットフォームのバージョン

| バージョン | 説明 | Architecture | API Management レベル |
| -------| ----------| ----------- | ------- |
| `stv2` | Single-tenant v2 | [仮想マシン スケール セット](../virtual-machine-scale-sets/overview.md) | Developer、Basic、Standard、Premium |
| `stv1` |  Single-tenant v1 | [クラウド サービス (クラシック)](../cloud-services/cloud-services-choose-me.md) | Developer、Basic、Standard、Premium |
| `mtv1` | Multi-tenant v1 |  [App Service](../app-service/overview.md) | 従量課金 |


## <a name="how-do-i-know-which-platform-hosts-my-api-management-instance"></a>API Management インスタンスのホストとなっているプラットフォームは、どのようにして確認できますか?

### <a name="developer-basic-standard-and-premium-tiers"></a>Developer、Basic、Standard、Premium レベル

* **2021 年 4 月** より後に Azure portal を使用して、または API Management REST API バージョン **2021-01-01-preview** 以降を使用して作成または更新されたインスタンスと仮想ネットワーク接続は、`stv2` プラットフォームでホストされています
* Premium レベルのインスタンスで[ゾーン冗長](zone-redundancy.md)を有効にした場合、`stv2` プラットフォームでホストされています
* それ以外の場合、インスタンスは `stv1` プラットフォームでホストされています

> [!TIP]
> API バージョン `2021-04-01-preview` 以降の API Management インスタンスには、そのプラットフォーム情報を表示する読み取り専用の `PlatformVersion` プロパティが備わっています。 

### <a name="consumption-tier"></a>Consumption レベル

* すべてのインスタンスは `mtv1` プラットフォームでホストされています

## <a name="how-do-i-upgrade-to-the-stv2-platform"></a>`stv2` プラットフォームにアップグレードするにはどうすればよいですか? 

更新は、Developer、Basic、Standard、Premium レベルのインスタンスでのみ可能です。 

次の方法を使用して、API Management インスタンスの仮想ネットワーク接続 (可用性ゾーン構成) を作成または更新します。

* [Azure Portal](https://portal.azure.com)
* Azure REST API または ARM テンプレート (API バージョン **2021-01-01-preview** 以降を指定)

> [!IMPORTANT]
> Azure [仮想ネットワーク](virtual-network-concepts.md)に接続されたインスタンスのコンピューティング プラットフォームのバージョンを更新する際の注意点を次に示します。
> * Standard SKU の[パブリック IPv4 アドレス](../virtual-network/ip-services/public-ip-addresses.md#standard) リソースを指定する必要があります
> * API Management インスタンスの VIP アドレスは変化します。

## <a name="next-steps"></a>次のステップ

* API Management での[仮想ネットワーク](virtual-network-concepts.md)の使用について詳細を確認します。
* [ゾーン冗長](zone-redundancy.md)について詳細を確認します。