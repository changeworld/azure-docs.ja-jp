---
title: Azure Stack デプロイ ネットワーク トラフィック | Microsoft Docs
description: この記事では、Azure Stack デプロイ ネットワーキング プロセスで予想されることについて説明します。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.author: jeffgilb
ms.reviewer: wamota
ms.openlocfilehash: b808875e66e867b84e2971c6a5bd031d108d003b
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2018
ms.locfileid: "34652610"
---
# <a name="about-deployment-network-traffic"></a>デプロイ ネットワーク トラフィックについて
デプロイを成功させるためには、Azure Stack デプロイ中のネットワーク トラフィック フローのしくみを理解することが重要です。 この記事では、デプロイ プロセス中に予想されるネットワーク トラフィックについて理解できるよう、段階的に説明します。

この図には、デプロイ プロセスに関連するすべてのコンポーネントと接続が示されています。

![Azure Stack デプロイ ネットワーク トポロジ](media/deployment-networking/figure1.png)

> [!NOTE]
> この記事では、接続デプロイの要件について説明します。他のデプロイ方法については、[Azure Stack デプロイ接続モデル](azure-stack-connection-models.md)に関するページを参照してください。

### <a name="the-deployment-vm"></a>デプロイ VM
Azure Stack ソリューションには、Azure Stack コンポーネントをホストするためのサーバーのグループと、Hardware Lifecycle Host (HLH) という名前の特別なサーバーが含まれています。 このサーバーは、ソリューションをデプロイし、そのライフサイクルを管理するために使用されます。デプロイ中、デプロイ VM (DVM) をホストします。

## <a name="deployment-requirements"></a>デプロイ要件
デプロイを正常に完了するための最小要件があります。デプロイを開始する前に、その要件を OEM で検証できます。 次の要件を理解することで、環境を整え、検証に合格できます。

-   [証明書](azure-stack-pki-certs.md)
-   [Azure サブスクリプション](https://azure.microsoft.com/free/?b=17.06)
-   インターネットへのアクセス
-   DNS
-   NTP

> [!NOTE]
> この記事では、最後の 3 つの要件について説明します。 最初の 2 つの詳細については、リンク先のページをご覧ください。

## <a name="deployment-network-traffic"></a>デプロイ ネットワーク トラフィック
DVM は BMC ネットワークからの IP で構成され、インターネットへのネットワーク アクセスを必要とします。 BMC ネットワークのすべてのコンポーネントで外部ルーティングまたはインターネットへのアクセスが必要になるわけではありませんが、このネットワークからの IP を利用する OEM 固有のコンポーネントでも必要になることがあります。

デプロイ中、DVM はお使いのサブスクリプションの Azure アカウントを利用し、Azure Active Directory (Azure AD) に対して認証を行います。 そのために、DVM は一覧にある特定のポートや URL にインターネット アクセスする必要があります。 完全な一覧は、[エンドポイントの公開](azure-stack-integrate-endpoints.md)に関する文書にあります。 DVM は DNS サーバーを利用し、内部コンポーネントによって行われた DNS 要求を外部 URL に転送します。 内部 DNS は転送されてきた要求を DNS フォワーダー アドレスに転送します。このアドレスは、デプロイ前に OEM に指定します。 同じことは NTP サーバーにも当てはまります。このサーバーは、すべての Azure Stack コンポーネントで一貫性を維持し、時刻を同期するために必要な、信頼性のあるタイム サーバーです。

デプロイ中、DVM が必要とするインターネット アクセスは外向きのみです。デプロイ中、内向きの呼び出しは行われません。 DVM はその IP をソースとして利用すること、Azure Stack はプロキシ構成に対応していないことにご注意ください。 そのため、必要であれば、インターネットにアクセスするために透過型プロキシまたは NAT を指定する必要があります。 デプロイの完了後、Azure と Azure Stack の間の全通信はパブリック VIP を利用した外部ネットワーク経由で行われます。

Azure Stack スイッチのネットワーク構成には、特定のネットワーク ソースと接続先の間のトラフィックを制限するアクセス制御リスト (ACL) が含まれます。 DVM は、アクセスに制限がない唯一のコンポーネントです。HLH でさえ、厳しい制限があります。 ご利用のネットワークからの管理とアクセスを簡単にするためのカスタマイズ オプションについては、OEM にお問い合わせください。 このような ACL が存在するため、デプロイ時には、DNS サーバーと NTP サーバーのアドレスを変更しないことが重要です。 変更する場合、このソリューションのすべてのスイッチを再構成する必要があります。

デプロイの完了後、指定した DNS サーバー アドレスと NTP サーバー アドレスは、システムのコンポーネントによって直接、引き続き使用されます。 たとえば、デプロイの完了後に DNS 要求を確認する場合、DVM IP から外部ネットワーク範囲のアドレスにソースが変わります。

Azure Stack が正常にデプロイされると、OEM パートナーはデプロイ後の追加タスクに DVM を使用することがあります。 ただし、デプロイ タスクとデプロイ後の構成がすべて完了したら、OEM は HLH から DVM を取り除き、削除する必要があります。

## <a name="next-steps"></a>次の手順
[Azure の登録を検証する](azure-stack-validate-registration.md)
