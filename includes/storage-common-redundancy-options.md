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
ms.openlocfilehash: 60912adbecbfdb4e6bebd7ddfea59eafa09b985e
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67181398"
---
ストレージ アカウントのレプリケーション オプションには、次のようなものがあります。

* [ローカル冗長ストレージ (LRS)](../articles/storage/common/storage-redundancy-lrs.md):シンプルな低コストのレプリケーション戦略です。 データは、単一のストレージ スケール ユニット内でレプリケートされます。
* [ゾーン冗長ストレージ (ZRS)](../articles/storage/common/storage-redundancy-zrs.md): 高可用性と持続性を得るためのレプリケーションです。 データは、3 つの可用性ゾーン間で同期的にレプリケートされます。 
* [geo 冗長ストレージ (GRS)](../articles/storage/common/storage-redundancy-grs.md):リージョン全体が利用不可になる事態に備えたリージョン間レプリケーションです。
* [読み取りアクセス geo 冗長ストレージ (RA-GRS)](../articles/storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage):レプリカに対する読み取りアクセス権を持つリージョン間レプリケーションです。