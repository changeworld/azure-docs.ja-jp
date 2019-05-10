---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: include
ms.date: 4/30/2019
ms.author: shants
ms.custom: include file
ms.openlocfilehash: 747fb9a38cc0c27d162192f4f3ed928e8a968f27
ms.sourcegitcommit: abeefca6cd5ca01c3e0b281832212aceff08bf3e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/02/2019
ms.locfileid: "64993114"
---
Azure は、定期的にプラットフォームを更新して、仮想マシンのホスト インフラストラクチャの信頼性、パフォーマンス、セキュリティの向上に努めています。 これらの更新は、ホスティング環境のソフトウェア コンポーネントの修正から、ネットワーク コンポーネントのアップグレード、ハードウェアの使用停止まで、広い範囲に及びます。 これらの更新のほとんどは、ホストされている仮想マシンに影響を及ぼしません。 ただし、更新プログラムが影響を及ぼす場合に Azure が最も影響の小さな更新方法を選択することがあります。

- 再起動を伴わない更新が可能な場合、VM は、ホストの更新中に一時停止するか、または更新済みのホストにライブ移行します。

- 再起動を伴うメンテナンスの場合は、メンテナンスの予定日時が知らされます。 Azure により、都合の良いときに自分でメンテナンスを開始できる時間枠も与えられます。 セルフ メンテナンスの時間枠は、メンテナンスの実行が急務でない限り、通常は 4 週間です。 また、Azure では、計画されたプラットフォーム メンテナンスのために VM を再起動する必要があるケースを減らすための技術に投資しています。 

このページでは、2 種類のメンテナンスが Azure でどのように実行されるかについて説明します。 計画外のイベント (停止) の詳細については、Azure での Windows 仮想マシンの可用性の管理 ([Windows](../articles/virtual-machines/windows/manage-availability.md) または [Linux](../articles/virtual-machines/linux/manage-availability.md)) に関する記事を参照してください。

[Windows](../articles/virtual-machines/windows/scheduled-events.md) または [Linux](../articles/virtual-machines/linux/scheduled-events.md) 向けの Scheduled Events を使用して、今後のメンテナンスに関する VM 内通知を受け取ることができます。

計画メンテナンスを管理する場合の「方法」については、[Linux](../articles/virtual-machines/linux/maintenance-notifications.md) または [Windows](../articles/virtual-machines/windows/maintenance-notifications.md) の計画メンテナンスの通知の処理に関するページを参照してください。

## <a name="maintenance-not-requiring-a-reboot"></a>再起動を必要としないメンテナンス

再起動を必要としない影響なしのメンテナンスでは、ほとんどの場合、VM の一時停止を 10 秒未満に留めることを目標としています。 Azure では、お客様の VM に対して最も影響の低い更新メカニズムを採用しています。 特定のケースでは、メモリ保持のメンテナンス メカニズムが使用されます。VM は最大で 30 秒間一時停止状態になり、RAM 内のメモリは保持されます。 VM が再開された後、クロックが自動的に同期されます。 Azure では、ライブ マイグレーション テクノロジの使用を拡大すると共に、一時停止期間を短縮するためのメモリ保持メンテナンス メカニズムの改善に努めています。  

これらの再起動を伴わないメンテナンス操作は障害ドメインごとに適用され、警告の正常性シグナルを受信した場合、進行が停止します。 

こうしたタイプの更新が一部のアプリケーションに影響を及ぼすことがあります。 VM を別のホストにライブ移行する場合、一部の機密性の高いワークロードでは、VM の一時停止に至るまでの数分間にわずかなパフォーマンスの低下が見られることがあります。 そのようなアプリケーションでは、[Windows](../articles/virtual-machines/windows/scheduled-events.md) または [Linux](../articles/virtual-machines/linux/scheduled-events.md) 向けの Scheduled Events を使用して VM のメンテナンスを準備することでメリットが得られ、Azure のメンテナンス中に影響を受けません。 Azure では、このような非常に機密性の高いアプリケーションのためのメンテナンス管理機能にも取り組んでいます。 

## <a name="live-migration"></a>ライブ マイグレーション

ライブ マイグレーションとは、VM 用のメモリを保持する再起動を伴わない操作であり、通常は 5 秒以内しか継続しない限定された一時停止または凍結になります。 今日、G、M、N、および H シリーズを除くサービスとしてのインフラストラクチャ (IaaS) 仮想マシンは、ライブ マイグレーションに対応しています。 これは Azure Fleet にデプロイされた IaaS VM の 90% 以上に相当します。 

ライブ マイグレーションは、次のシナリオで、Azure Fabric によって開始されます。
- 計画メンテナンス
- ハードウェア障害
- 割り当ての最適化

計画メンテナンスの一部のシナリオではライブ マイグレーションが活用されており、ライブ マイグレーションの操作を開始する時点を事前に把握するために、Scheduled Events を使用することができます。

また、ライブ マイグレーションは、予測される障害の兆候が Machine Learning アルゴリズムによって検出された場合、該当のハードウェアから仮想マシンを移行し、仮想マシンの割り当てを最適化するためにも使用されます。 機能低下しているハードウェアのインスタンスを検出する予測モデリングについて詳しくは、「[Improving Azure Virtual Machine resiliency with predictive ML and live migration (予測 ML およびライブ マイグレーションによる Azure Virtual Machine の回復性の改善)](https://azure.microsoft.com/blog/improving-azure-virtual-machine-resiliency-with-predictive-ml-and-live-migration/?WT.mc_id=thomasmaurer-blog-thmaure)」をご覧ください。 お客様は常に、監視 / サービス正常性のログに加えて、これらが使用中の場合には Scheduled Events を介して、Azure portal でライブ マイグレーションの通知を受け取ります。

## <a name="maintenance-requiring-a-reboot"></a>再起動を伴うメンテナンス

計画メンテナンスで VM の再起動が必要になるまれなケースでは、事前に通知が届きます。 計画済みメンテナンスには、"セルフサービス期間" と "予定メンテナンス期間" の 2 つのフェーズがあります。

**セルフサービス期間**では、ご利用の VM に対するメンテナンスをユーザーが開始することができます。 通常は 4 週間になっているこの期間中に、個々の VM にその状態を照会し、自分が最後に行ったメンテナンス要求の結果を確認します。

セルフサービス メンテナンスを開始すると、VM は、既に更新済みのノードに再デプロイされます。 VM が再起動されるため、一時ディスクは失われ、仮想ネットワーク インターフェイスに関連付けられた動的 IP アドレスは更新されます。

セルフサービス メンテナンスを開始したときに、そのプロセス中にエラーが発生して操作が停止された場合、VM は更新されませんセルフサービス メンテナンスを再試行するオプションが提供されます。 

セルフサービス期間が過ぎると、**予定メンテナンス期間**が始まります。 この期間中、ユーザーはメンテナンス期間について照会することはできますが、メンテナンスを開始することはできません。

再起動を必要とするメンテナンスの管理については、[Linux](../articles/virtual-machines/linux/maintenance-notifications.md) または [Windows](../articles/virtual-machines/windows/maintenance-notifications.md) の計画メンテナンスの通知の処理に関するページを参照してください。 

### <a name="availability-considerations-during-scheduled-maintenance"></a>予定メンテナンス中の可用性に関する考慮事項 

予定メンテナンス期間まで待つ場合、ご利用の VM の可用性を最大限に保つために考慮すべき事柄がいくつかあります。 

#### <a name="paired-regions"></a>ペアになっているリージョン

各 Azure リージョンは、同じ geo 内の別のリージョンと組み合わせて、リージョン ペアにして使用します。 予定メンテナンス フェーズでは、リージョン ペアの一方のリージョンの VM だけが更新されます。 たとえば、米国中北部の VM が更新されるとき、同時に米国中南部の VM が更新されることはありません。 ただし、北ヨーロッパなどのその他のリージョンは、米国東部と同時にメンテナンスされる可能性があります。 各リージョンに対して適切に VM を分散させるためには、リージョン ペアの動作を理解することが大切です。 詳細については、[Azure リージョン ペア](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)に関するページを参照してください。

#### <a name="availability-sets-and-scale-sets"></a>可用性セットとスケール セット

Azure VM にワークロードをデプロイするとき、可用性セット内に VM を作成することで、アプリケーションの高可用性を確保することができます。 これにより、障害発生時や再起動が多いメンテナンス イベントの間であっても、少なくとも 1 つの VM を確実に使用できるようになります。

可用性セット内の個々の VM は最大 20 個の更新ドメイン (UD) に分散されます。 予定メンテナンス中は、どの時点において 1 つの更新ドメインのみが更新されます。 更新ドメインが更新される順序は、必ずしも順番どおりではありません。 

Virtual Machine Scale Sets は、同一の VM のセットをデプロイして管理するために使用できる Azure コンピューティング リソースです。 スケール セットは、可用性セット内の VM と同じように、複数の更新ドメインに対して分散するように自動的にデプロイされます。 可用性セットと同様、スケール セットの場合も、予定メンテナンス中に更新される更新ドメインは一度に 1 つだけです。

高可用性を実現するための VM の構成について詳しくは、[Windows](../articles/virtual-machines/windows/manage-availability.md) 仮想マシンの可用性管理に関するページまたは [Linux](../articles/virtual-machines/linux/manage-availability.md) 仮想マシンの可用性管理に関するページを参照してください。
