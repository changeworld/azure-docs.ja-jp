---
title: Azure Data Factory エンティティに名前を付けるときの規則 | Microsoft Docs
description: Data Factory エンティティの名前付け規則について説明します。
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: bc5e801d-0b3b-48ec-9501-bb4146ea17f1
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 0311c2e2223c60861d2b6c87bfa7747dc079856d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
---
# <a name="azure-data-factory---naming-rules"></a>Azure Data Factory - 名前付け規則
> [!NOTE]
> この記事は、一般公開 (GA) されている Data Factory のバージョン 1 に適用されます。 プレビュー段階の Data Factory サービスのバージョン 2 を使用している場合は、[Data Factory バージョン 2 の名前付け規則](../naming-rules.md)に関するページを参照してください。

次の表に、Data Factory アーティファクトの名前付け規則を示します。

| Name | 名前の一意性 | 検証チェック |
|:--- |:--- |:--- |
| Data Factory |Microsoft Azure 全体で一意です。 名前の大文字と小文字を区別されません。つまり、`MyDF` と `mydf` は同じデータ ファクトリを表します。 |<ul><li>各データ ファクトリは、厳密に 1 つの Azure サブスクリプションに関連付けられます。</li><li>オブジェクト名は英文字または数字で始まり、英文字、数字、ダッシュ (-) 文字のみを含めることができます。</li><li>すべてのダッシュ (-) 文字は、その直前または直後に文字または数字が使用されている必要があります。 連続するダッシュ文字はコンテナー名では使用できません。</li><li>名前は 3 ～ 63 文字の長さにすることができます。</li></ul> |
| リンクされたサービス/テーブル/パイプライン |データ ファクトリ内で一意です。 名前の大文字と小文字は区別されません。 |<ul><li>テーブル名の最大文字数: 260。</li><li>オブジェクト名は、文字、数字、アンダー スコア (_) のいずれかで始める必要があります。</li><li>次の文字は使用できません: “.”, “+”, “?”, “/”, “<”, ”>”,”*”,”%”,”&”,”:”,”\\"</li></ul> |
| リソース グループ |Microsoft Azure 全体で一意です。 名前の大文字と小文字は区別されません。 |<ul><li>最大文字数: 1,000。</li><li>名前には、文字、数字、"-"、"_"、"," および "." を文字を含めることができます。</li></ul> |

