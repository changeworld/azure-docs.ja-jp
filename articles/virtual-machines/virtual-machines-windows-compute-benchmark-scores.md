<properties
 pageTitle="Windows VM のコンピューティング ベンチマーク スコア | Microsoft Azure"
 description="Windows Server を実行する Azure VM の SPECint コンピューティング ベンチマーク スコアを比較します。"
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="infrastructure-services"
 ms.date="07/18/2016"
 ms.author="danlep"/>

# Windows VM のコンピューティング ベンチマーク スコア

次の SPECInt ベンチマーク スコアは、Windows Server を実行している高いパフォーマンスの Azure の VM ラインアップについてのコンピューティング パフォーマンスを示します。コンピューティング ベンチマーク スコアは [Linux VM](virtual-machines-linux-compute-benchmark-scores.md) にも利用できます。



## A シリーズ - コンピューティング集中型


サイズ | vCPU 数 | NUMA ノード数 | CPU | 実行 | 平均のベース レート | 標準偏差
------- | ------ | ---- | -------| ---- | ---- | -----
Standard\_A8 | 8 | 1 | Intel Xeon CPU E5-2670 0 @ 2.6 GHz | 10 | 236\.1 | 1\.1
Standard\_A9 | 16 | 2 | Intel Xeon CPU E5-2670 0 @ 2.6 GHz | 10 | 450\.3 | 7\.0
Standard\_A10 | 8 | 1 | Intel Xeon CPU E5-2670 0 @ 2.6 GHz | 5 | 235\.6 | 0\.9
Standard\_A11 | 16 | 2 | Intel Xeon CPU E5-2670 0 @ 2.6 GHz |7 | 454\.7 | 4\.8

## Dv2 シリーズ


サイズ | vCPU 数 | NUMA ノード数 | CPU | 実行 | 平均のベース レート | 標準偏差
------- | ------ | ---- | -------| ---- | ---- | -----
Standard\_D1\_v2 | 1 | 1 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 83 | 36\.6 | 2\.6
Standard\_D2\_v2 | 2 | 1 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 27 | 70\.0 | 3\.7
Standard\_D3\_v2 | 4 | 1 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 19 | 130\.5 | 4\.4.
Standard\_D4\_v2 | 8 | 1 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 19 | 238\.1 | 5\.2
Standard\_D5\_v2 | 16 | 2 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 14 | 460\.9 | 15\.4
Standard\_D11\_v2 | 2 | 1 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 19 | 70\.1 | 3\.7
Standard\_D12\_v2 | 4 | 1 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 2 | 132\.0 | 1\.4
Standard\_D13\_v2 | 8 | 1 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 17 | 235\.8 | 3\.8
Standard\_D14\_v2 | 16 | 2 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 15 | 460\.8 | 6\.5


## G シリーズ、GS シリーズ


サイズ | vCPU 数 | NUMA ノード数 | CPU | 実行 | 平均のベース レート | 標準偏差
------- | ------ | ---- | -------| ---- | ---- | -----
Standard\_G1、Standard\_GS1 | 2 | 1 | Intel Xeon E5-2698B v3 @ 2 GHz | 31 | 71\.8 | 6\.5
Standard\_G2、Standard\_GS2 | 4 | 1 | Intel Xeon E5-2698B v3 @ 2 GHz | 5 | 133\.4 | 13\.0
Standard\_G3、Standard\_GS3 | 8 | 1 | Intel Xeon E5-2698B v3 @ 2 GHz | 6 | 242\.3 | 6\.0
Standard\_G4、Standard\_GS4 | 16 | 1 | Intel Xeon E5-2698B v3 @ 2 GHz | 15 | 398\.9 | 6\.0
Standard\_G5、Standard\_GS5 | 32 | 2 | Intel Xeon E5-2698B v3 @ 2 GHz | 22 | 762\.8 | 3\.7

## SPECint について

Windows の数値は、Windows Server 上で [SPECint 2006](https://www.spec.org/cpu2006/results/rint2006.html) を実行して計算しました。SPECint は、コアあたり 1 つのコピーを使用し、ベース レート オプション (SPECint\_rate2006) を使用して実行しました。SPECint は 12 個の個別のテストで構成され、それぞれを 3 回実行します。各テストの中央値を採用し、重み付けして複合スコアを割り出します。これらが複数の VM にわたって実行され、ご覧の平均スコアが算出されました。


## 次のステップ

* ストレージの容量、ディスクの詳細、VM のサイズを選択する際のその他の考慮事項については、[仮想マシンのサイズ](virtual-machines-windows-sizes.md)に関するページをご覧ください。

<!---HONumber=AcomDC_0720_2016-->