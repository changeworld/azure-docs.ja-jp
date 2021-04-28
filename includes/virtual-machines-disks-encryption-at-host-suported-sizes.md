---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e9d7b39ce267202503b90e84e934d31501d45478
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/19/2021
ms.locfileid: "107732257"
---
最新世代のすべての VM サイズで、ホストでの暗号化がサポートされています。

|Type  |サポートされていません  |サポートされています  |
|---------|---------|---------|
|汎用     | Dv3、Dv2、Av2        | B、DSv2、Dsv3、DC、DCv2、Dav4、Dasv4、Ddv4、Ddsv4       |
|コンピューティング最適化     |         | Fsv2        |
|メモリ最適化     | Ev3        | Esv3、M、Mv2、Eav4、Easv4、Edv4、Edsv4        |
|ストレージ最適化     |         | Ls、Lsv2 (NVMe ディスクは暗号化されていません)        |
|GPU     | NC、NV        | NCv2、NCv3、ND、NVv3、NVv4、NDv2 (プレビュー)        |
|ハイ パフォーマンス コンピューティング     | H        | HB、HC、HBv2        |
|旧世代     | F、A、D、L、G        | DS、GS、Fs、NVv2        |
