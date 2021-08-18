---
title: SAP HANA on Azure (L インスタンス) のデータ階層化と拡張ノード | Microsoft Docs
description: SAP HANA on Azure (Large Instances) のデータ階層化と拡張ノードについて説明します。
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/17/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6d13edf6e2512f6f70af8265e1859b106e75885c
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2021
ms.locfileid: "110577551"
---
# <a name="use-sap-hana-data-tiering-and-extension-nodes"></a>SAP HANA のデータ階層化と拡張ノードの使用

SAP は、さまざまな SAP NetWeaver リリースの SAP Business Warehouse (BW) および SAP BW/4HANA のデータ階層化モデルをサポートしています。 データ階層化モデルの詳細については、「[SAP HANA 拡張ノードを持つ SAP BW/4HANA および SAP BW-on-HANA](https://www.sap.com/documents/2017/05/ac051285-bc7c-0010-82c7-eda71af511fa.html#)」をご覧ください。

Hana Large Instances を使用すると、FAQ および SAP ブログ ドキュメントで説明する SAP HANA 拡張ノードのオプション 1 の構成をご利用いただけます。 オプション 2 の構成は次の HANA Large Instances SKU を使用して設定できます: S72m、S192、S192m、S384、S384m。

## <a name="advantages-of-sap-hana-extension-nodes"></a>SAP HANA 拡張ノードの利点

SAP HANA 拡張ノードのオプション 1 または 2 を使用すると、SAP HANA メモリを簡単に有効活用できます。 SAP のサイズ設定ガイドラインを確認すると、SAP HANA 拡張ノードの利点が明らかになります。 次に例をいくつか示します。

- SAP HANA のサイズ変更ガイドラインでは通常、メモリと比べて 2 倍のデータ ボリュームが必要です。 ホット データを使用して SAP HANA インスタンスを実行すると、50% 以下のメモリだけでデータが格納されます。 残りのメモリは SAP HANA の作業のために保持されるのが理想的です。
- つまり、2 TB のメモリを持つ Hana Large Instances S192 ユニットで SAP BW データベースを実行すると、データ ボリューム内には 1 TB しかありません。
- もう 1 つの SAP HANA 拡張ノード オプション 1 と、S192 HANA Large Instances SKU を使用した場合、データ ボリューム内にさらに 2 TB の容量が追加されます。 オプション 2 の構成では、ウォーム データ ボリューム用にさらに 4 TB が提供されます。 ホット ノードと比較した場合、オプション 1 ではデータの格納に "ウォーム" 拡張ノードの全メモリ容量をご利用いただけます。 オプション 2 の SAP HANA 拡張ノード構成では、データ ボリュームに 2 倍のメモリをご利用いただけます。
- その結果、オプション 1 ではデータ容量が 3 TB、ホットとウォームの割合が 1:2 になります。 オプション 2 の拡張ノード構成では、データ容量が 5 TB、ホットとウォームの割合が 1:4 になります。

メモリに対してデータ ボリュームが大きいほど、求めるウォーム データがディスクに保存されている可能性が高くなります。

## <a name="next-steps"></a>次の手順

SAP HANA on Azure (Large Instances) の操作モデルとその役割について説明します。

> [!div class="nextstepaction"]
> [運用モデルと責任](hana-operations-model.md)
