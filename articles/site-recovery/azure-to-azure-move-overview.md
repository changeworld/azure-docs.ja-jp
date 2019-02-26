---
title: Azure Site Recovery サービスを使用して Azure IaaS VM を別の Azure リージョンに移動する | Microsoft Docs
description: Azure Site Recovery を使用して、異なる Azure リージョン間で Azure IaaS VM を移動します。
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 1560dad31e00b00a2d27cf1675e05901e6611825
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/15/2019
ms.locfileid: "56309864"
---
# <a name="move-azure-vms-to-another-region"></a>Azure VM を別のリージョンに移動する

Azure は顧客ベースと共に大規模に拡大しており、需要の増大に伴って、新たにサポートされるリージョンも増えています。 サービス全体で毎月追加される新しい機能も存在します。 そのため、既存の VM を別のリージョンや Availability Zones に移動して可用性を高めたい場合があります。

このドキュメントでは、VM の移動が必要になるさまざまなシナリオについて取り上げると共に、可用性を高めるために、ターゲットに対してどのようにアーキテクチャを構成すればよいかについて説明します。 
> [!div class="checklist"]
> * [Azure VM を移動する理由](#why-would-you-move-azure-vms)
> * [Azure VM を移動する方法](#how-to-move-azure-vms)
> * [一般的なアーキテクチャ](#typical-architectures-for-a-multi-tier-deployment)
> * [VM をそのままターゲット リージョンに移動する](#move-azure-vms-to-another-region)
> * [VM を移動して可用性を高める](#move-vms-to-increase-availability)


## <a name="why-would-you-move-azure-vms"></a>Azure VM を移動する理由

ユーザーが VM を移動する理由としては、次のケースが考えられます。

- 既にデプロイ済みのリージョンがあるとき、既存のアプリケーションまたはサービスのエンド ユーザーにより近いリージョンが新たにサポートされた場合、待ち時間の短縮を図るために、**既存のVM をそのまま新しいリージョン**に移動したいと考えるでしょう。 サブスクリプションを統合したい場合や、ガバナンス/組織のルール上、移動が必要となる場合にも、同じ手法が用いられます。 
- 単一インスタンスの VM として、または可用性セットの一部として VM がデプロイされているとき、可用性の SLA を強化するには、**既存の VM を可用性ゾーンに移動**することができます。 

## <a name="how-to-move-azure-vms"></a>Azure VM を移動する方法
VM の移動には、次の手順が伴います。

1. 前提条件を確認する 
2. ソース VM を準備する 
3. ターゲット リージョンを準備する 
4. ターゲット リージョンにデータをコピーする: Azure Site Recovery のレプリケーション テクノロジを使用して、ソース VM からターゲット リージョンにデータをコピーします。
5. 構成をテストする: レプリケーションが完了したら、非運用ネットワークへのテスト フェールオーバーを実行して構成をテストします。
6. 移動を実行する 
7. ソース リージョンのリソースを破棄する 


> [!IMPORTANT]
> 現在 Azure Site Recovery では、異なるリージョン間の VM の移動はサポートされますが、同じリージョン内での移動はサポートされません。 

> [!NOTE]
> これらの手順についての詳しいガイダンスは、[ここ](#next-steps)で言及されている各シナリオのドキュメントに記載されています。

## <a name="typical-architectures-for-a-multi-tier-deployment"></a>多層デプロイの一般的なアーキテクチャ
以下のセクションでは、Azure における多層アプリケーションで採用される最も一般的なデプロイ アーキテクチャについて見ていきます。 ここでは、パブリック IP を持つ 3 階層アプリケーションの例を取り上げています。 Web、アプリケーション、データベースの各階層は、それぞれ 2 つの VM を有しており、ロード バランサーによって他の階層に接続されています。 データベース層の VM 間には SQL AlwaysOn レプリケーションが存在し、高可用性 (HA) が確保されています。

1.  **単一インスタンスの VM を各種階層にデプロイ** - 階層内の各 VM は単一インスタンスの VM として構成され、ロード バランサーによって他の階層に接続されます。 これがユーザーによって採用される最もシンプルな構成になります。

       ![単一 VM](media/move-vm-overview/regular-deployment.PNG)

2. **各階層の VM を可用性セットにデプロイ** - 階層内の各 VM が可用性セット内に構成されます。 [可用性セット](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)を使うことで、Azure にデプロイする VM は、クラスター内で切り離された複数のハードウェア ノードに確実に分散されます。 これにより、Azure 内でハードウェアまたはソフトウェアの障害が発生した場合に影響を受けるのは VM のサブセットに限定され、ソリューション全体は引き続き利用可能であり、運用可能であることが保証されます。 
   
      ![avset](media/move-vm-overview/AVset.PNG)

3. **各階層の VM を可用性セットにデプロイ** - 階層内の各 VM が [Availability Zones](https://docs.microsoft.com/azure/availability-zones/az-overview) に構成されます。 Azure リージョン内の可用性ゾーンは、障害ドメインと更新ドメインを組み合わせたものです。 たとえば、Azure リージョンの 3 つのゾーンに 3 つ以上の VM を作成する場合、VM は実際には 3 つの障害ドメインと 3 つの更新ドメインに分散されます。 Azure プラットフォームは更新ドメインへのこの分散を認識し、異なるゾーン内の VM が同時に更新されないようにします。

      ![zone-deploymnt](media/move-vm-overview/zone.PNG)



 ## <a name="move-vms-as-is-to-a-target-region"></a>VM をそのままターゲット リージョンに移動する

以下に示したのは、前述の[アーキテクチャ](#typical-architectures-for-a-multi-tier-deployment)をベースに、ターゲット リージョンに VM をそのまま移動した後のデプロイの概観です。


1. **単一インスタンスの VM を各種階層にデプロイ** 

     ![single-zone.PNG](media/move-vm-overview/single-zone.PNG)

2. **各階層の VM を可用性セットにデプロイ**

     ![crossregionAset.PNG](media/move-vm-overview/crossregionAset.PNG)


3. **各階層の VM を可用性ゾーンにデプロイ**
      

     ![AzoneCross.PNG](media/move-vm-overview/AzoneCross.PNG)

## <a name="move-vms-to-increase-availability"></a>VM を移動して可用性を高める

1. **単一インスタンスの VM を各種階層にデプロイ** 

     ![single-zone.PNG](media/move-vm-overview/single-zone.PNG)

2. **各階層の VM を可用性セットにデプロイ** - Azure Site Recovery を使用した VM のレプリケーションを有効にした場合、可用性セット内の VM を別個の可用性ゾーンに配置するように構成できます。 移動操作の完了後、可用性の SLA は 99.9% になります。

     ![aset-Azone.PNG](media/move-vm-overview/aset-Azone.PNG)


## <a name="next-steps"></a>次の手順

このドキュメントでは、VM の移動に関する一般的なガイダンスについて取り上げました。 具体的な実行手順については、次の記事を参照してください。


> [!div class="nextstepaction"]
> * [Azure VM を別のリージョンに移動する](azure-to-azure-tutorial-migrate.md)

> * [Azure VM を Availability Zones に移動する](move-azure-VMs-AVset-Azone.md)

