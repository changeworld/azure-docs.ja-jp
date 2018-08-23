---
title: Advisor の高可用性に関する推奨事項 | Microsoft Docs
description: Azure Advisor を使用して、Azure のデプロイの高可用性を向上させます。
services: advisor
documentationcenter: NA
author: manbeenkohli
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: makohli
ms.openlocfilehash: 7bd0737e7fb26af95eed63696d1ac07c88a9dec4
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2018
ms.locfileid: "42140506"
---
# <a name="advisor-high-availability-recommendations"></a>Advisor の高可用性に関する推奨事項

Azure Advisor を使用して、ビジネスに不可欠なアプリケーションの継続稼働を保証し、さらに向上させることができます。 Advisor による高可用性に関する推奨事項は、Advisor ダッシュボードの **[高可用性]** タブで取得できます。

## <a name="ensure-virtual-machine-fault-tolerance"></a>仮想マシンのフォールト トレランスを確保する

アプリケーションに冗長性をもたらすには、可用性セット内に 2 つ以上の仮想マシンをグループ化することをお勧めします。 Advisor は、可用性セットの一部ではない仮想マシンを識別し、それらを可用性セットに移動することを推奨します。 このような構成により、計画済みのメンテナンス イベント中でも計画外のメンテナンス イベント中でも、少なくとも 1 台の仮想マシンが利用可能となり、Azure 仮想マシンの SLA が満たされます。 仮想マシンの可用性セットを作成するか、既存の可用性セットに仮想マシンを追加するかを選択できます。

> [!NOTE]
> 可用性セットを作成する場合は、少なくとも 1 台以上の仮想マシンを可用性セットに追加する必要があります。 2 台以上の仮想マシンを 1 つの可用性セットにグループ化して、障害の発生時に少なくとも 1 台のマシンを使用できるようにしておくことをお勧めします。

## <a name="ensure-availability-set-fault-tolerance"></a>可用性セットのフォールト トレランスを確保する 

アプリケーションに冗長性をもたらすには、可用性セット内に 2 つ以上の仮想マシンをグループ化することをお勧めします。 Advisor は、1 台の仮想マシンのみを含む可用性セットを識別し、そのセットにさらに仮想マシンを追加することを推奨します。 このような構成により、計画済みのメンテナンス イベント中でも計画外のメンテナンス イベント中でも、少なくとも 1 台の仮想マシンが利用可能となり、Azure 仮想マシンの SLA が満たされます。 仮想マシンを作成するか、既存の仮想マシンを可用性セットに追加するかを選択できます。  

## <a name="ensure-application-gateway-fault-tolerance"></a>アプリケーション ゲートウェイのフォールト トレランスを確保する
アプリケーション ゲートウェイを備えるミッション クリティカルなアプリケーションのビジネス継続性を確保するために、Advisor は、フォールト トレランス用に構成されていないアプリケーション ゲートウェイ インスタンスを識別し、実施できる修復アクションを提案します。 Advisor は、中規模または大規模な単一インスタンス アプリケーション ゲートウェイを識別し、少なくとも 1 つ以上のインスタンスを追加することを推奨します。 また、1 つまたは複数の小規模アプリケーション ゲートウェイを識別し、中規模または大規模な SKU に移行することを推奨します。 これらの修復アクションでは、アプリケーション ゲートウェイ インスタンスがこれらのリソースの現在の SLA 要件を満たすように構成されていることを確認します。

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks"></a>仮想マシンのディスクのパフォーマンスと信頼性の向上

Advisor は、Standard ディスクを使用している仮想マシンを識別し、Premium ディスクにアップグレードすることを推奨します。
 
Azure Premium Storage は、高負荷の I/O ワークロードを実行する仮想マシン向けに高パフォーマンスで待ち時間の少ないディスク サポートを提供します。 Premium Storage アカウントを使用する仮想マシンのディスクでは、ソリッド ステート ドライブ (SSD) にデータを格納します。 アプリケーションで最適なパフォーマンスを実現するには、高い IOPS を必要とする仮想マシン ディスクは Premium Storage に移行することをお勧めします。 

ディスクが高い IOPS を必要としない場合は、Standard Storage で管理することでコストを制限できます。 Standard Storage は、仮想マシンのディスク データを、SSD ではなくハード ディスク ドライブ (HDD) に格納します。 仮想マシンのディスクを Premium ディスクに移行することを選択できます。 Premium ディスクは、ほとんどの仮想マシンの SKU でサポートされます。 ただし、場合によっては、Premium ディスクを使用するには、仮想マシンの SKU もアップグレードする必要があります。

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>誤って削除されないように、仮想マシンのデータを保護する

仮想マシンのバックアップを設定することにより、ビジネス クリティカルなデータの可用性が確保され、偶発的な削除または破損からデータを保護します。  Advisor は、バックアップが有効になっていない仮想マシンを識別子、バックアップを有効にすることを推奨します。 

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>必要な時に Azure クラウドの専門家を利用できるようにする

ビジネスに不可欠なワークロードを実行しているとき、必要時にはテクニカル サポートを受けることができるようにしておくことが重要です。 Advisor は、サポート計画にテクニカル サポートが含まれないビジネスに不可欠なサブスクリプションを識別し、テクニカル サポートが含まれるオプションへのアップグレードを勧めします。

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>Azure の問題による影響を受けたときに通知する Azure Service Health アラートを作成する

Azure サービスの問題による影響を受けたときに通知する Azure Service Health アラートを設定することをお勧めします。 [Azure Service Health](https://azure.microsoft.com/features/service-health/) は、Azure サービスの問題による影響を受けた場合に、カスタマイズされたガイダンスとサポートを提供する無料サービスです。 Advisor はアラートが構成されていないサブスクリプションを識別し、アラートの作成を推奨します。

## <a name="configure-traffic-manager-endpoints-for-resiliency"></a>回復性を考慮して Traffic Manager エンドポイントを構成する

複数のエンドポイントを含む Traffic Manager プロファイルは、いずれかのエンドポイントに障害が発生した場合でも高い可用性が確保されます。 エンドポイントを複数の異なるリージョンに配置すると、サービスの信頼性がさらに向上します。 Advisor はエンドポイントが 1 つだけの Traffic Manger プロファイルを識別し、別のリージョンに少なくとも 1 つのエンドポイントを追加することを推奨します。

近接ルーティング用に構成されている Traffic Manager プロファイル内のエンドポイントがすべて同じリージョン内にある場合、他のリージョンのユーザーに対しては接続の遅延が発生する可能性があります。 別のリージョンにエンドポイントを追加または移動すると、一方のリージョン内のすべてのエンドポイントに障害が発生した場合でも全体的なパフォーマンスと可用性が向上します。 Advisor は近接ルーティング用に構成されていて、すべてのエンドポイントが同じリージョン内にある Traffic Manager プロファイルを識別し、別の Azure リージョンへのエンドポイントの追加または移動を推奨します。

Traffic Manager プロファイルが地理的なルーティング用に構成されている場合は、定義済みのリージョンに基づいてトラフィックがエンドポイントにルーティングされます。 リージョンで障害が発生した場合、定義済みのフェールオーバーはありません。 エンドポイントで [リージョンのグループ化] を [すべて (世界)] に構成すると、トラフィックのドロップが回避され、サービスの可用性が向上します。 Advisor は地理的なルーティング用に構成されていて、エンドポイントで [リージョンのグループ化] が [すべて (世界)] に構成されていない Traffic Manager プロファイルを識別し、その構成を変更することを推奨します。

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>Advisor の高可用性に関する推奨事項にアクセスする方法

1. [Azure Portal](https://portal.azure.com) にサインインし、[Advisor](https://aka.ms/azureadvisordashboard) を開きます。

2.  Advisor ダッシュボードの **[高可用性]** タブをクリックします。

## <a name="next-steps"></a>次の手順

Advisor の推奨事項について詳しくは、以下を参照してください。
* [Azure Advisor の概要](advisor-overview.md)
* [Advisor の使用を開始する](advisor-get-started.md)
* [Advisor のコストに関する推奨事項](advisor-performance-recommendations.md)
* [Advisor のパフォーマンスに関する推奨事項](advisor-performance-recommendations.md)
* [Advisor のセキュリティに関する推奨事項](advisor-security-recommendations.md)

