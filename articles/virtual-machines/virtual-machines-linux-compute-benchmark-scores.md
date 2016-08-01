<properties
 pageTitle="Linux VM のコンピューティング ベンチマーク スコア | Microsoft Azure"
 description="Linux を実行する Azure VM の CoreMark コンピューティング ベンチマーク スコアを比較します。"
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="07/18/2016"
 ms.author="danlep"/>

# Linux VM のコンピューティング ベンチマーク スコア

次の CoreMark ベンチマーク スコアは、Ubuntu を実行している、Azure の 高パフォーマンス VM ラインアップについてのコンピューティング パフォーマンスを示します。コンピューティング ベンチマーク スコアは [Windows VM](virtual-machines-windows-compute-benchmark-scores.md) にも利用できます。




## A シリーズ - コンピューティング集中型


サイズ | vCPU 数 | NUMA ノード数 | CPU | 実行 | イテレーション数/秒 | 標準偏差
------- | ------ | ---- | -------| ---- | ---- | -----
Standard\_A8 | 8 | 1 | Intel Xeon CPU E5-2670 0 @ 2.6 GHz| 179 | 110,294 | 554
Standard\_A9 | 16 | 2 | Intel Xeon CPU E5-2670 0 @ 2.6 GHz| 189 | 210,816| 2,126
Standard\_A10 | 8 | 1 | Intel Xeon CPU E5-2670 0 @ 2.6 GHz| 188 | 110,025 | 1,045
Standard\_A11 | 16 | 2 | Intel Xeon CPU E5-2670 0 @ 2.6 GHz| 188 | 210,727| 2,073

## Dv2 シリーズ


サイズ | vCPU 数 | NUMA ノード数 | CPU | 実行 | イテレーション数/秒 | 標準偏差
------- | ------ | ---- | -------| ---- | ---- | -----
Standard\_D1\_v2 | 1 | 1 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 140 | 14,852 | 780
Standard\_D2\_v2 | 2 | 1 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 133 | 29,467 | 1,863
Standard\_D3\_v2 | 4 | 1 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 139 | 56,205 | 1,167
Standard\_D4\_v2 | 8 | 1 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 126 | 108,543 | 3,446
Standard\_D5\_v2 | 16 | 2 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 126 | 205,332 | 9,998
Standard\_D11\_v2 | 2 | 1 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 125 | 28,598 | 1,510
Standard\_D12\_v2 | 4 | 1 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 131 | 55,673 | 1,418
Standard\_D13\_v2 | 8 | 1 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 140 | 107,986 | 3,089
Standard\_D14\_v2 | 16 | 2 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 140 | 208,186 | 8,839
Standard\_D15\_v2 | 20 | 2 | Intel Xeon E5-2673 v3 @ 2.4 GHz |28 | 268,560 | 4,667

## F シリーズ

サイズ | vCPU 数 | NUMA ノード数 | CPU | 実行 | イテレーション数/秒 | 標準偏差
------- | ------ | ---- | -------| ---- | ---- | -----
Standard\_F1 | 1 | 1 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 154 | 15,602 | 787
Standard\_F2 | 2 | 1 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 126 | 29,519 | 1,233
Standard\_F4 | 4 | 1 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 147 | 58,709 | 1,227
Standard\_F8 | 8 | 1 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 224 | 112,772 | 3,006
Standard\_F16 | 16 | 2 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 42 | 218,571 | 5,113



## G シリーズ


サイズ | vCPU 数 | NUMA ノード数 | CPU | 実行 | イテレーション数/秒 | 標準偏差
------- | ------ | ---- | -------| ---- | ---- | -----
Standard\_G1 | 2 | 1 | Intel Xeon E5-2698B v3 @ 2 GHz | 83 | 31,310 | 2,891
Standard\_G2 | 4 | 1 | Intel Xeon E5-2698B v3 @ 2 GHz | 84 | 60,112 | 3,537
Standard\_G3 | 8 | 1 | Intel Xeon E5-2698B v3 @ 2 GHz | 84 | 107,522 | 4,537
Standard\_G4 | 16 | 1 | Intel Xeon E5-2698B v3 @ 2 GHz | 83 | 195,116 | 5,024
Standard\_G5 | 32 | 2 | Intel Xeon E5-2698B v3 @ 2 GHz | 84 | 360,329 | 14,212

## GS シリーズ


サイズ | vCPU 数 | NUMA ノード数 | CPU | 実行 | イテレーション数/秒 | 標準偏差
------- | ------ | ---- | -------| ---- | ---- | -----
Standard\_GS1 | 2 | 1 | Intel Xeon E5-2698B v3 @ 2 GHz | 84 | 28,613 | 1,884
Standard\_GS2 | 4 | 1 | Intel Xeon E5-2698B v3 @ 2 GHz | 83 | 54,348 | 3,474
Standard\_GS3 | 8 | 1 | Intel Xeon E5-2698B v3 @ 2 GHz | 83 | 104,564 | 1,834
Standard\_GS4 | 16 | 1 | Intel Xeon E5-2698B v3 @ 2 GHz | 84 | 194,111 | 4,735
Standard\_GS5 | 32 | 2 | Intel Xeon E5-2698B v3 @ 2 GHz | 84 | 357,396 | 16,228


## CoreMark について

Linux の数値は、Ubuntu 上で [CoreMark](http://www.eembc.org/coremark/faq.php) を実行して計算しました。CoreMark は、スレッド数を仮想 CPU の数に設定し、同時実行数を pThread に設定して構成しました。イテレーション数の目標は、テストを実行するために要した秒数で除算した完了イテレーション数を表す最終的なスコアを使用して、少なくとも 20 秒間 (通常はもっと長い時間) のランタイムを提供するように、予想パフォーマンスに基づいて調整しました。VM ごとに各テストを少なくとも 7 回実行しました。テストは、2015 年 10 月に、実行当日に VM がサポートされているすべての Azure パブリック リージョンで、複数の VM 上で実行されました。
## 次のステップ



* ストレージの容量、ディスクの詳細、VM のサイズを選択する際のその他の考慮事項については、[仮想マシンのサイズ](virtual-machines-linux-sizes.md)に関するページをご覧ください。

* Linux VM 上で CoreMark スクリプトを実行するには、[CoreMark スクリプト パック](http://download.microsoft.com/download/3/0/5/305A3707-4D3A-4599-9670-AAEB423B4663/AzureCoreMarkScriptPack.zip)をダウンロードします。

<!---HONumber=AcomDC_0720_2016-->