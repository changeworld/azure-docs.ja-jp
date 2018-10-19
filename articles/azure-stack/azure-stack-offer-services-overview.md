---
title: Azure Stack でのサービスの提供 | Microsoft Docs
description: クラウド オペレーターの場合は、ユーザーにサービスを提供できます。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: jeffgilb
ms.reviewer: ''
ms.openlocfilehash: e4e1701a145a36fce93db3812b67c307b342da5c
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2018
ms.locfileid: "46127484"
---
# <a name="overview-of-offering-services-in-azure-stack"></a>Azure Stack でのサービスの提供の概要

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

[Microsoft Azure Stack](azure-stack-poc.md) は、データセンターからのサービスの提供を可能にするハイブリッド クラウド プラットフォームです。 サービス プロバイダーの場合は、テナントにサービスを提供できます。 企業や政府機関では、従業員にオンプレミスのサービスを提供できます。 

[サービスとしてのインフラストラクチャ](https://azure.microsoft.com/overview/what-is-iaas/) (IaaS) サービスを提供し、ユーザーが、Azure Stack ユーザー ポータルからプロビジョニングおよび管理されるオンデマンドのコンピューティング インフラストラクチャを構築できるようにすることができます。

また、マイクロソフトおよびその他サード パーティ プロバイダーの Azure Stack 用の[サービスとしてのプラットフォーム](https://azure.microsoft.com/overview/what-is-paas/) (PaaS) サービスをデプロイすることもできます。 提供できるサービスとして次のようなものが挙げられます。

- [App Service リソース プロバイダーを Azure Stack に追加する](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-overview)

- [SQL Server リソース プロバイダーを Azure Stack に追加する](https://docs.microsoft.com/azure/azure-stack/azure-stack-sql-resource-provider-deploy)

- [MySQL サーバー リソース プロバイダーを Azure Stack に追加する](https://docs.microsoft.com/azure/azure-stack/azure-stack-mysql-resource-provider-deploy)


サービスを組み合わせて、さまざまなユーザー向けに複雑なソリューションを統合して作成することもできます。

ユーザーにこれらのサービスを提供するには、[プラン、オファー、およびクォータ](azure-stack-plan-offer-quota-overview.md)を作成する必要があります。 それによってユーザーが、オファーにサブスクライブしてサービスを使用することができます。

## <a name="plan-your-service-offers"></a>サービスのオファーの計画

オファーを計画するときは、次の点に留意してください。

**試用版**: 試用版を使用して、後で追加のサービスにアップグレードする可能性のある新しいユーザーを引き込むことができます。 試用版を作成するには、オプションの大きめのアドオン プランを含む小さい[基本プラン](azure-stack-plan-offer-quota-overview.md#base-plan)を作成します。

**キャパシティ プランニング**: 大量のリソースを取り込んで、すべてのユーザーのためのシステムを停滞させたりするユーザーのことが懸念される場合があります。 パフォーマンスの助けとなるよう、[クォータを使ってプランを構成](azure-stack-plan-offer-quota-overview.md#plans)して使用量を制限できます。

**委任されたプロバイダー**: 他のユーザーにご利用の環境でオファーを作成する権限を与えることができます。 たとえば、サービス プロバイダーの場合は、この権限を再販業者に[委任](azure-stack-delegated-provider.md)できます。 また、組織の場合は、他の部門/子会社に委任できます。

## <a name="next-steps"></a>次の手順

[Azure Stack でのオファーの作成](azure-stack-create-offer.md)
