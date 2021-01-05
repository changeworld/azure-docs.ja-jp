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
ms.openlocfilehash: 65729934ea7c4037d6857aec10b14cdddd616368
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/29/2020
ms.locfileid: "97805644"
---
ストレージ アカウントの冗長オプションには、次のようなものがあります。

* ローカル冗長ストレージ (LRS):シンプルな低コストの冗長戦略。 データは、プライマリ リージョンの 1 つの物理的な場所内で、同期的に 3 回コピーされます。
* ゾーン冗長ストレージ (ZRS):高可用性を必要とするシナリオ向けの冗長性。 データは、プライマリ リージョンの 3 つの Azure 可用性ゾーン間で同期的にコピーされます。
* geo 冗長ストレージ (GRS): リージョン障害から保護するためのリージョン間冗長。 データは、プライマリ リージョンで 3 回、同期的にコピーされた後、セカンダリ リージョンに非同期的にコピーされます。 セカンダリ リージョンのデータへの読み取りアクセスのために、読み取りアクセス geo 冗長ストレージ (RA-GRS) を有効にします。
* geo ゾーン冗長ストレージ (GZRS) (プレビュー):高可用性と最大限の持続性の両方を必要とするシナリオ向けの冗長性。 データは、プライマリ リージョンの 3 つの Azure 可用性ゾーン間で同期的にコピーされた後、セカンダリ リージョンに非同期的にコピーされます。 セカンダリ リージョンのデータへの読み取りアクセスのために、読み取りアクセス geo ゾーン冗長ストレージ (RA-GZRS) を有効にします。

Azure Storage の冗長オプションの詳細については、「[Azure Storage の冗長性](../articles/storage/common/storage-redundancy.md)」を参照してください。
