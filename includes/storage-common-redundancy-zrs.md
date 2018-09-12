---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/11/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 4a43966180850645584043690b1be9d6ae232f6e
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/06/2018
ms.locfileid: "44027449"
---
ゾーン冗長ストレージ (ZRS) は、1 つのリージョン内の 3 つのストレージ クラスターにデータを同期してレプリケートします。 各ストレージ クラスターは物理的に他のストレージ クラスターと分離されており、それぞれの可用性ゾーン (AZ) 内にあります。 各可用性ゾーンとその中の ZRS クラスターは自律的であり、独立したユーティリティとネットワーク機能を備えています。

ZRS アカウンでデータを保存すると、1 つのゾーンが利用不可になった場合でもデータにアクセスして管理できるようになります。 ZRS はパフォーマンスに優れ、短い待機時間です。 ZRS は、[ローカル冗長ストレージ (LRS)](../articles/storage/common/storage-redundancy-lrs.md) と同じ[スケーラビリティ ターゲット](../articles/storage/common/storage-scalability-targets.md)を実現します。

停電や自然災害によってゾーンのデータ センターが利用不可になった場合でも強力な一貫性、高い持続性、高い可用性が必要なシナリオの場合は、ZRS を検討してください。 ZRS は、ストレージ オブジェクトに年間 99.9999999999% (トゥエルブ ナイン) 以上の持続性を実現します。

可用性ゾーンの詳細については、[Availability Zones の概要](https://docs.microsoft.com/azure/availability-zones/az-overview)に関するページを参照してください。