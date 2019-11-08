---
title: Hyper-V を使用しているお客様所有のサイト間の DR と SCVMM によって Azure で管理されているサイト間の DR の廃止予定 | Microsoft Docs
description: Hyper-V を使用しているお客様所有のサイト間の DR と SCVMM によって Azure で管理されているサイト間の DR の廃止予定と代替オプションの詳細
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 09/13/2019
ms.author: rajanaki
ms.openlocfilehash: 68881d57f251d2f26996b2a837780106326ec1d3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492277"
---
# <a name="upcoming-deprecation-of-dr-between-customer-owned-sites-using-hyper-v-and-between-sites-managed-by-scvmm-to-azure"></a>Hyper-V を使用しているお客様所有のサイト間の DR と SCVMM によって Azure で管理されているサイト間の DR の廃止予定

この記事では、予定されている廃止計画、付随する影響、および Azure Site Recovery でサポートされていた次のシナリオに対してお客様が利用できる代替オプションについて説明します。 

- お客様所有のサイト間で SCVMM によって管理されている Hyper-V VM 間の DR 
- SCVMM によって Azure で管理されている Hyper-V VM の DR

> [!IMPORTANT]
> これらのシナリオは、現在非推奨とマークされており、お客様は、環境の中断を回避するためにできるだけ早く修復手順を実行することが期待されています。 
 

## <a name="what-changes-should-you-expect"></a>予期される変更

- 2019 年 11 月以降、これらのシナリオでは、新しいユーザーのオンボードは許可されません。 **既存のレプリケーション操作と管理操作** (フェールオーバー、フェールオーバー テスト、監視など) に**影響はありません**。

- シナリオが非推奨になった後、お客様が推奨される手順に従っていない場合は、次のような影響があります。

    - お客様所有のサイト間で SCVMM によって管理されている Hyper-V VM 間の DR: Hyper-V レプリカの基本的な能力は機能し続けるため、レプリケーションは引き続き機能しますが、お客様は、Azure portal で Azure Sire Recovery エクスペリエンスを通じて、DR 関連の操作を表示、管理、または実行することはできなくなります。 
    - SCVMM によって Azure で管理されている Hyper-V VM の DR: 既存のレプリケーションは中断され、お客様は、Azure Site Recovery を使用して DR 関連の操作を表示、管理、または実行することはできなくなります。


## <a name="recommended-actions-to-be-taken"></a>実行を推奨されるアクション

シナリオが非推奨になった後で DR 戦略に影響が出ないようにするためのお客様のオプションを次に示します。 

- [Hyper-V ホスト上の VM の DR ターゲットとして Azure の使用を開始する](hyper-v-azure-tutorial.md)ことを選択します。

> [!IMPORTANT]
> オンプレミス環境には引き続き SCVMMM を存在させることができますが、Hyper-V ホストのみを参照する ASR を構成することに注意してください。

- サイト間レプリケーションを、基本的な [Hyper-Hyper-V レプリカ ソリューション](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/set-up-hyper-v-replica)を使用して続行することを選択します。ただし、Azure portal で Azure Site Recovery を使用して DR 構成を管理することはできません。 


## <a name="next-steps"></a>次の手順
廃止に備えた計画を立て、ご自身のインフラストラクチャとビジネスに最適な代替オプションを選択します。 これに関してご質問がおありの場合は、Microsoft サポートにお問い合わせください。

