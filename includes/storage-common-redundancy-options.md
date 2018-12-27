---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/26/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: b965fea3d4f166b1a801dda7cafd8e4190790c68
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2018
ms.locfileid: "45739506"
---
ストレージ アカウントのレプリケーション オプションには、次のようなものがあります。

* [ローカル冗長ストレージ (LRS)](../articles/storage/common/storage-redundancy-lrs.md): 単純な低コストのレプリケーション戦略。 データは、単一のストレージ スケール ユニット内でレプリケートされます。
* [ゾーン冗長ストレージ (ZRS)](../articles/storage/common/storage-redundancy-zrs.md): 高可用性と持続性を備えたレプリケーション。 データは、3 つの可用性ゾーン間で同期的にレプリケートされます。 
* [Geo 冗長ストレージ (GRS)](../articles/storage/common/storage-redundancy-grs.md): リージョン全体が利用不可になる事態に備えたリージョン間レプリケーション。
* [読み取りアクセス geo 冗長ストレージ (RA-GRS)](../articles/storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage): レプリカへの読み取りアクセス権を持つリージョン間レプリケーション。