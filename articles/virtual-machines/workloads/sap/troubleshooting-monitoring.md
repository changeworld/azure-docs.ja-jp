---
title: SAP HANA on Azure (Large Instances) を監視する | Microsoft Docs
description: SAP HANA on Azure (Large Instances) の監視について説明します。
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/23/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017, contperf-fy21q4
ms.openlocfilehash: d8325a9b5a3b8adaf3f39cde791e9a00f3f897e3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131036601"
---
# <a name="monitor-sap-hana-large-instances-on-azure"></a>SAP HANA on Azure (Large Instances) を監視する

この記事では、Azure 上の SAP HANA Large Instances (BareMetal インフラストラクチャ) の監視について見ていきます。

SAP HANA on Azure (Large Instances) も、他の IaaS デプロイと変わりません。 オペレーティング システムとアプリケーションを監視することが大切です。 次のリソースがアプリケーションによってどのように使用されているかを把握する必要があります。

- CPU
- メモリ
- ネットワークの帯域幅
- ディスク領域

以上のリソースが足りているか、または消耗しているかは、SAP HANA Large Instances を監視することで確認します。 以降の各セクションでは、これらの各リソースについて詳しく説明します。

## <a name="cpu-resource-consumption"></a>CPU リソース使用量

SAP HANA ワークロードには、CPU 使用の最大しきい値が SAP によって定義されています。 このしきい値の範囲内にとどめることで、メモリに格納されているデータを処理するのに十分な CPU リソースが確保されます。 インデックスの欠落やそれに類似する問題が原因で SAP HANA サービスによってクエリが実行されると、CPU 使用量が上昇する可能性があります。 そのため、HANA Large Instances の CPU 使用量と特定の HANA サービスの CPU 使用量を監視することがきわめて重要となります。

## <a name="memory-consumption"></a>メモリ消費量 

SAP HANA L インスタンス上の HANA の内部と外部の両方のメモリ消費量を監視することが大切です。 HANA に割り当てられたメモリをデータがどう消費しているかを監視して、SAP のサイジング ガイドラインの範囲を超えないようにするのです。 HANA 以外のソフトウェアがメモリを消費しすぎないように L インスタンスのメモリ消費量を監視します。 HANA 以外のソフトウェアが HANA とメモリを奪い合う状況は好ましくありません。

## <a name="network-bandwidth"></a>ネットワーク帯域幅 

Azure Virtual Network (VNet) ゲートウェイの帯域幅は有限です。 Azure VNet に入ることができるデータは限られています。 VNet 内のすべての Azure VM の受信データを監視してください。 そうすれば、選択した Azure ゲートウェイ SKU の上限に近づいてきたタイミングがわかるようになります。 HANA L インスタンスの送受信ネットワーク トラフィックを監視して、一定期間にわたって処理されたボリュームを追跡してもよいでしょう。

## <a name="disk-space"></a>ディスク領域

ディスク領域の使用量は、通常、時間の経過と共に増加します。 一般的な原因には、次のようなものがあります。
- 時間の経過に伴うデータ ボリュームの増加
- トランザクション ログ バックアップの実行
- トレース ファイルの格納
- ストレージ スナップショットの取得 

ディスク領域の使用量を監視し、HANA L インスタンスに関連付けられているディスク領域を管理することが重要です。

## <a name="preloaded-system-diagnostic-tools"></a>事前に読み込まれるシステム診断ツール

HANA L インスタンスの **Type II SKU** の場合、サーバーには事前に読み込まれたシステム診断ツールが付属しています。 これらの診断ツールを使用して、システム正常性チェックを実行できます。
 
正常性チェックのログ ファイルを /var/log/health_check に生成するには、次のコマンドを実行します。

```
/opt/sgi/health_check/microsoft_tdi.sh
```
Microsoft サポート チームと協力して問題のトラブルシューティングを行う場合は、これらの診断ツールを使用してログ ファイルを提供するように求められることがあります。 このコマンドを使用して、ファイルを圧縮することができます。

```
tar  -czvf health_check_logs.tar.gz /var/log/health_check
```

## <a name="azure-monitor-for-sap-solutions"></a>SAP ソリューション向け Azure Monitor

Azure Monitor for SAP Solutions を使用すると、前述したすべてのリソースを含め、さまざまなリソースを監視できます。 Azure Monitor for SAP Solutions は Azure ネイティブのツールです。 Azure のインフラストラクチャやデータベースから 1 か所にデータを収集して、それらを視覚的に関連付けることで、トラブルシューティングを迅速化することができます。 詳細については、[Azure での SAP の監視](../../../virtual-machines/workloads/sap/monitor-sap-on-azure.md)に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ

SAP HANA 内から監視とトラブルシューティングを行う方法について学習します。

> [!div class="nextstepaction"]
> [HANA 側からの監視とトラブルシューティング](hana-monitor-troubleshoot.md)
