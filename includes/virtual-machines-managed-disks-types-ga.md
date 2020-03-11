---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/28/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e145cf481dd165144b188e6205e4b78cc61359fd
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/29/2020
ms.locfileid: "78202454"
---
## <a name="premium-ssd"></a>Premium SSD

Azure Premium SSD では、IO (入出力) を集中的に行うワークロードが存在する仮想マシン (VM) 向けに、高パフォーマンスで待ち時間の少ないディスク サポートが提供されます。 Premium Storage ディスクの速度とパフォーマンスを活用するために、既存の VM ディスクを Premium SSD に移行することができます。 Premium SSD は、ミッション クリティカルな運用アプリケーションに適しています。 Premium SSD は、Premium Storage に互換性のある VM シリーズでのみ使用できます。

Premium Storage に互換性のあるサイズなど、Windows 用の Azure 内の個別の VM の種類とサイズについて詳しくは、[Windows VM のサイズ](../articles/virtual-machines/windows/sizes.md)に関するページを参照してください。 Premium Storage に互換性のあるサイズなど、Linux 用の Azure 内の個別の VM の種類とサイズについて詳しくは、[Linux VM のサイズ](../articles/virtual-machines/linux/sizes.md)に関するページを参照してください。 これらのいずれかの記事から、個々の VM サイズの記事を確認し、Premium ストレージと互換性があるかどうかを判断する必要があります。

### <a name="disk-size"></a>ディスク サイズ
[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

Premium Storage ディスクをプロビジョニングすると、Standard Storage とは異なり、対象のディスクの容量、IOPS、スループットが保証されます。 たとえば、P50 ディスクを作成した場合、対象のディスクに 4,095 GB のストレージ容量、7,500 IOPS、および 250 MB/秒のスループットがプロビジョニングされます。 アプリケーションでは、容量とパフォーマンスのすべてまたは一部を使用できます。 Premium SSD ディスクは、1 桁のミリ秒の低遅延と、前出の表に示した目標 IOPS とスループットを 99.9% の時間で提供するように設計されています。

## <a name="bursting-preview"></a>バースティング (プレビュー)

P30 より小さいサイズの Premium SSD では、ディスク バースティング (プレビュー) を提供しており、ディスクあたり IOPS を最高 3500 まで、帯域幅を最高 170 Mbps までバーストできます。 バーストは自動化され、クレジット システムに基づいて動作します。 ディスク トラフィックがプロビジョニングされたパフォーマンス ターゲットを下回る場合、クレジットはバースト バケットに自動的に蓄積されます。また、トラフィック バーストがターゲットを超えた場合、最大バースト制限まで自動的にクレジットが消費されます。 最大バースト制限は、消費されるバースト クレジットがある場合でも、ディスク IOPS および帯域幅の上限を定義します。 ディスク バースティングは、IO パターンの予測できない変更に対して優れた耐性を示します。 これは、OS ディスクの起動や激しいトラフィックを含むアプリケーションにおいて、最大限に活用できます。    

既定では、ディスク バースティング サポートは[プレビュー領域](https://docs.microsoft.com/azure/virtual-machines/linux/disk-bursting#regional-availability)の適用可能なディスク サイズを新しくデプロイする際に有効になります。ユーザーの操作は必要ありません。 既存の適用可能なサイズのディスクの場合、ディスクのデタッチと再アタッチまたは接続済み VM の停止と再起動という、2 つのオプションのいずれかを使用してバースティングを有効にすることができます。 バーストが適用可能なすべてのディスク サイズは、ディスクが 30 分のピーク バースト制限での最大期間をサポートする仮想マシンに接続されている場合、最大のバースト クレジット バケットを使用して起動されます。 Azure ディスクでのバースティングの動作の詳細については、[Premium SSD バースティング](../articles/virtual-machines/linux/disk-bursting.md)に関する記事を参照してください。 

### <a name="transactions"></a>トランザクション

Premium SSD では、スループットが 256 KiB 以下の I/O 操作は、それぞれ単一の I/O 操作とみなされます。 スループットが 256 KiB を超える I/O 操作は、サイズが 256 KiB の複数の I/O とみなされます。

## <a name="standard-ssd"></a>Standard SSD

Azure Standard SSD は、比較的低い IOPS レベルで一貫したパフォーマンスを必要とするワークロード向けに最適化された、コスト効果に優れたストレージ オプションです。 Standard SSD は、クラウドへの移行を希望している方、特にオンプレミスの HDD ソリューションで実行されているワークロードのばらつきによって問題が発生している場合に、エントリ レベルの優れたエクスペリエンスを提供します。 Standard HDD に比べて、Standard SSD は、可用性、一貫性、信頼性、遅延が優れています。 Standard SSD は、Web サーバー、低い IOPS のアプリケーション サーバー、使用の少ないエンタープライズ アプリケーション、開発/テストのワークロードに適しています。 Standard HDD のように、Standard SSD はすべての Azure VM で使用できます。

### <a name="disk-size"></a>ディスク サイズ
[!INCLUDE [disk-storage-standard-ssd-sizes](disk-storage-standard-ssd-sizes.md)]

Standard SSD は、1 桁のミリ秒の遅延と、前出の表に示されている上限までの IOPS とスループットを 99% の時間で提供するように設計されています。 実際の IOPS とスループットは、トラフィック パターンによって異なる場合があります。 Standard SSD は、HDD ディスクよりも一貫したパフォーマンスを提供し、遅延が少なくなります。

### <a name="transactions"></a>トランザクション

Standard SSD では、スループットが 256 KiB 以下の I/O 操作は、それぞれ単一の I/O 操作とみなされます。 スループットが 256 KiB を超える I/O 操作は、サイズが 256 KiB の複数の I/O とみなされます。 これらのトランザクションは、課金への影響があります。

## <a name="standard-hdd"></a>Standard HDD

Azure Standard HDD では、遅延の影響を受けないワークロードを実行する VM 向けの信頼性の高い低コストのディスク サポートが提供されます。 Standard Storage では、データはハード ディスク ドライブ (HDD) に格納されます。 Standard HDD ディスクの遅延、IOPS、およびスループットは、SSD ベースのディスクと比較してより大きく異なる可能性があります。 Standard HDD ディスクは、ほとんどの IO 操作で 10 ミリ秒未満の書き込み待機時間と 20 ミリ秒未満の読み取り待機時間を実現するように設計されていますが、実際のパフォーマンスは IO サイズやワークロードのパターンによって異なる可能性があります。 VM を使用するとき、開発/テスト シナリオや重要度の低いワークロードには Standard HDD ディスクを使用できます。 Standard HDD はすべての Azure リージョンで使用可能であり、すべての Azure VM で使用できます。

### <a name="disk-size"></a>ディスク サイズ
[!INCLUDE [disk-storage-standard-hdd-sizes](disk-storage-standard-hdd-sizes.md)]

### <a name="transactions"></a>トランザクション

Standard HDD では、各 IO 操作は、I/O サイズに関係なく、単一のトランザクションとみなされます。 これらのトランザクションは、課金への影響があります。

## <a name="billing"></a>課金

マネージド ディスクを使用するときには、課金に関する次の考慮事項が適用されます。

- ディスクの種類
- マネージド ディスクのサイズ
- スナップショット
- 送信データ転送
- トランザクション数

**マネージド ディスクのサイズ**: マネージド ディスクは、プロビジョニング済みサイズに対して課金されます。 Azure では、プロビジョニング済みサイズ (切り上げたもの) は、提供される最も近いディスク サイズにマップされます。 提供されるディスク サイズの詳細については、前の表を参照してください。 各ディスクは、サポートされているプロビジョニング済みディスク サイズのオファリングにマップされ、それに応じて課金されます。 たとえば、200 GiB の Standard SSD をプロビジョニングした場合、E15 (256 GiB) のディスク サイズのプランにマップされます。 プロビジョニングされたディスクには、Premium Storage プランの月額料金を使用して、時間割りで計算して課金されます。 たとえば、E10 ディスクをプロビジョニングし、20 時間後にそのディスクを削除した場合は、E10 製品の 20 時間分に対して課金されます。 ディスクに書き込まれた実際のデータ量には関係ありません。

**スナップショット**: スナップショットは、使用されているサイズに基づいて請求されます。 たとえば、64 GiB のプロビジョニング済み容量でマネージド ディスクのスナップショットを作成し、実際に使用されたデータ サイズが 10 GiB である場合、スナップショットは使用されたデータ サイズである 10 GiB 分のみ課金されます。
