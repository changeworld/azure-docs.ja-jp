---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 05/13/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: d5915d8628254f24343571c1adc254c548558415
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2021
ms.locfileid: "110077710"
---
- 現在、この機能は Premium SSD でのみサポートされています。
- ディスクのレベルを変更する前に、VM の割り当てを解除するか、実行中の VM からディスクを切断する必要があります。
- P60、P70、P80 のパフォーマンス レベルは、4,096 GiB より大きなディスクでのみ利用できます。
- ディスクのパフォーマンス レベルは、12 時間ごとに 1 回だけダウングレードできます。

## <a name="change-performance-tier-without-downtime-preview"></a>ダウンタイムなしでパフォーマンス レベルを変更する (プレビュー)

通常、パフォーマンス レベルを変更するには、VM の割り当てを解除するか、ディスクをデタッチする必要があります。 しかし、このプレビュー機能を有効にすれば、レベルを変更するために VM の割り当てを解除したり、ディスクをデタッチしたりする必要はありません。

このプレビューには、次の制限事項があります。
- 米国中西部リージョンでのみご利用いただけます。
- 現在、共有ディスクでは使用できません。
- ダウンタイムなしでレベルを変更するには、次のいずれかの方法を使用する必要があります。
    - `2020-12-01` API を含む Azure Resource Manager テンプレートを使用してダウンタイムなしでパフォーマンス レベルを変更する。
    - 次のリンクを使用して Azure portal にアクセスします[https://aka.ms/diskPerfTiersPreview](https://aka.ms/diskPerfTiersPreview)。
    - 最新の Azure CLI。
- 現在、Azure PowerShell モジュールでは使用できません。