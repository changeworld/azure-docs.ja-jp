---
title: 高可用性とディザスター リカバリー
titleSuffix: Azure Digital Twins
description: ディザスター リカバリー機能を持つ高可用性 Azure IoT ソリューションの構築を支援する Azure および Azure Digital Twins の機能について説明します。
author: baanders
ms.author: baanders
ms.date: 10/14/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 85fd5a4246e891ef6640438b07e12a9c32ad12fa
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2020
ms.locfileid: "92094203"
---
# <a name="azure-digital-twins-high-availability-and-disaster-recovery"></a>Azure Digital Twins の高可用性とディザスター リカバリー

回復性がある IoT ソリューションで考慮すべき主な領域は、事業継続とディザスター リカバリーです。 **高可用性 (HA)** と **ディザスター リカバリー (DR)** を設計すると、お客様のソリューションに適したアップタイム目標を定義し、達成するのに役立ちます。

この記事では、特に Azure Digital Twins サービスによって提供されている HA および DR の機能について説明します。

Azure Digital Twins では、次の機能オプションがサポートされています。
* *リージョン内 HA* -サービスのアップタイムを実現するために組み込まれている冗長性
* *リージョン間 DR* -予期しないデータ センターの障害が発生した場合の Azure geo ペア リージョンへのフェールオーバー

HA/DR の設計に関する一般的な Azure ガイダンスについては、 [*ベスト プラクティス*](#best-practices)に関するセクションも参照してください。

## <a name="intra-region-ha"></a>リージョン内 HA
 
Azure Digital Twins では、サービス内で冗長性を実装することで、リージョン内 HA が実現されます。 **これらの HA 機能を利用するために、Azure Digital Twins ソリューションの開発者が追加の作業を行う必要はありません。** Azure Digital Twins ではかなり高いアップタイムが保証されていますが、分散コンピューティング プラットフォームの場合と同様に一時的な障害が発生する可能性があります。 一時的な障害に対処するには、クラウド アプリケーションと対話するコンポーネントに適切な再試行ポリシーを組み込む必要があります。

## <a name="cross-region-dr"></a>リージョン間 DR

リージョン内の停電やその他のイベントにより、データセンターに長時間の停止が発生する場合があります。 このようなイベントはまれであり、障害時に、前述のリージョン内 HA 機能があっても役立つとは限りません。 Azure Digital Twins では、Microsoft が開始するフェールオーバーを使用して、これに対処します。

**Microsoft が開始するフェールオーバー** は、Microsoft によって実行されます。まれではありますが、影響を受けるリージョンのすべての Azure Digital Twins インスタンスは、対応する geo ペア リージョンにフェールオーバーされます。 このプロセスは既定のオプションであり (ユーザーがオプトアウトする方法はありません)、ユーザー操作は必要はありません。 Microsoft は、このオプションを実行するタイミングを決定する権利を留保します。 このメカニズムでは、ユーザーのインスタンスがフェールオーバーされる前にユーザーの同意を必要としません。

>[!NOTE]
> 一部の Azure サービスには、 **顧客が開始するフェールオーバー** と呼ばれる追加のオプションも用意されています。たとえば、DR ドリルを実行するために、顧客が自分のインスタンスのフェールオーバーのみを開始できます。 現時点で Azure Digital Twins では、このメカニズムが **サポートされていません** 。 

## <a name="best-practices"></a>ベスト プラクティス

HA/DR のベスト プラクティスについては、このトピックの次の Azure ガイダンスを参照してください。 
* [*Azure ビジネス継続性テクニカル ガイダンス*](/azure/architecture/framework/resiliency/overview)に関する記事では、ビジネス継続性とディザスター リカバリーについて考慮する際に役立つ一般的なフレームワークについて説明しています。 
* 「 [*Disaster recovery and high availability for Azure applications*](/azure/architecture/framework/resiliency/backup-and-recovery)」(Azure アプリケーションのディザスター リカバリーと高可用性) では、高可用性 (HA) とディザスター リカバリー (DR) を実現するための Azure アプリケーションの戦略に関するアーキテクチャのガイダンスを確認できます。

## <a name="next-steps"></a>次の手順 

Azure Digital Twins ソリューションの概要については、次を参照してください。
 
* [*Azure Digital Twins とは*](overview.md)
* [*クイック スタート:サンプル シナリオを精査する*](quickstart-adt-explorer.md)