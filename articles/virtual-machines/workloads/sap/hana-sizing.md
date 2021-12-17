---
title: SAP HANA on Azure (L インスタンス) のサイズ変更 | Microsoft Docs
description: SAP HANA on Azure (L インスタンス) のサイズ変更について説明します。
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
ms.date: 07/16/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4050a986c98b492f71383bd5c677d68288882611
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2021
ms.locfileid: "114404661"
---
# <a name="sizing"></a>サイズ変更

この記事では、HANA Large Instances のサイズ変更に役立つ情報を確認します。 通常、HANA Large Instances のサイズ設定は HANA のサイズ変更とまったく同じです。 

## <a name="moving-an-existing-system-to-sap-hana-large-instances"></a>SAP HANA (L インスタンス) への既存システムの移動

たとえば、既存のデプロイ済みシステムを別のリレーショナル データベース管理システム (RDBMS) から HANA に移動するとします。 SAP では、既存の SAP システムで実行するためのレポートを提供しています。 データベースを HANA に移行する場合、これらのレポートによってデータが確認され、HANA インスタンスのメモリ要件が計算されます。 

これらのレポートを実行する方法と、レポートの最新の修正プログラムまたはバージョンを入手する方法の詳細については、次の SAP ノートをご覧ください。

- [SAP Note #1793345 - Sizing for SAP Suite on HANA (SAP ノート #1793345 - HANA での SAP Suite のサイズ変更)](https://launchpad.support.sap.com/#/notes/1793345)
- [SAP Note #1872170 - Suite on HANA and S/4 HANA sizing report (SAP ノート #1872170 - Suite on HANA および S/4 HANA のサイズ変更レポート)](https://launchpad.support.sap.com/#/notes/1872170)
- [SAP Note #2121330 - FAQ: SAP BW on HANA Sizing Report (SAP ノート #2121330 - FAQ: SAP BW on HANA のサイズ変更レポート)](https://launchpad.support.sap.com/#/notes/2121330)
- [SAP Note #1736976 - Sizing Report for BW on HANA (SAP ノート #1736976 - BW on HANA のサイズ変更レポート)](https://launchpad.support.sap.com/#/notes/1736976)
- [SAP Note #2296290 - New Sizing Report for BW on HANA (SAP ノート #2296290 - BW on HANA の新しいサイズ変更レポート)](https://launchpad.support.sap.com/#/notes/2296290)

## <a name="sizing-greenfield-implementations"></a>グリーンフィールド実装のサイズ変更

実装をゼロから開始する場合、SAP Quick Sizer を使用すると、HANA 上の SAP ソフトウェア実装のメモリ要件を計算できます。

## <a name="memory-requirements"></a>メモリ要件

データ量の増加に伴って、HANA のメモリ要件が増加します。 将来的に必要になるメモリ容量を予測できるように、現在のメモリ使用量に注意してください。 メモリ要件に基づいて、いずれかの HANA L インスタンス SKU に要求をマップできます。

## <a name="next-steps"></a>次の手順

HANA Large Instances のオンボードの要件について確認します。

> [!div class="nextstepaction"]
> [オンボードの要件](hana-onboarding-requirements.md)
