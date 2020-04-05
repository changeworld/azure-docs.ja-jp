---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/14/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 73c2b742ede21a4e86d717d994f8ebc4f16389c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "77157222"
---
ストレージ アカウントの冗長オプションには、次のようなものがあります。

* ローカル冗長ストレージ (LRS):シンプルな低コストの冗長戦略。 データは、プライマリ リージョン内で 3 回、同期的にコピーされます。
* ゾーン冗長ストレージ (ZRS):高可用性を必要とするシナリオ向けの冗長性。 データは、プライマリ リージョンの 3 つの Azure 可用性ゾーン間で同期的にコピーされます。
* geo 冗長ストレージ (GRS): リージョン障害から保護するためのリージョン間冗長。 データは、プライマリ リージョンで 3 回、同期的にコピーされた後、セカンダリ リージョンに非同期的にコピーされます。 セカンダリ リージョンのデータへの読み取りアクセスのために、読み取りアクセス geo 冗長ストレージ (RA-GRS) を有効にします。
* geo ゾーン冗長ストレージ (GZRS) (プレビュー):高可用性と最大限の持続性の両方を必要とするシナリオ向けの冗長性。 データは、プライマリ リージョンの 3 つの Azure 可用性ゾーン間で同期的にコピーされた後、セカンダリ リージョンに非同期的にコピーされます。 セカンダリ リージョンのデータへの読み取りアクセスのために、読み取りアクセス geo ゾーン冗長ストレージ (RA-GZRS) を有効にします。

Azure Storage の冗長オプションの詳細については、「[Azure Storage の冗長性](../articles/storage/common/storage-redundancy.md)」を参照してください。
