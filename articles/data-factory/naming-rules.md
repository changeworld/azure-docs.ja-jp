---
title: Azure Data Factory エンティティの名前付け規則
description: Data Factory エンティティの名前付け規則について説明します。
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: f0d14760ce3e6403c9b6fe8cc7a2100aeb3f39a6
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2021
ms.locfileid: "100372911"
---
# <a name="azure-data-factory---naming-rules"></a>Azure Data Factory - 名前付け規則

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

次の表に、Data Factory アーティファクトの名前付け規則を示します。

| 名前 | 名前の一意性 | 検証チェック |
|:--- |:--- |:--- |
| Data Factory | Microsoft Azure 全体で一意です。 名前の大文字と小文字を区別されません。つまり、`MyDF` と `mydf` は同じデータ ファクトリを表します。 |<ul><li>各データ ファクトリは、厳密に 1 つの Azure サブスクリプションに関連付けられます。</li><li>オブジェクト名は英文字または数字で始まり、英文字、数字、ダッシュ (-) 文字のみを含めることができます。</li><li>すべてのダッシュ (-) 文字は、その直前または直後に文字または数字が使用されている必要があります。 連続するダッシュ文字はコンテナー名では使用できません。</li><li>名前は 3 ～ 63 文字の長さにすることができます。</li></ul> |
| リンクされたサービス、データセット、パイプライン、データ フロー | データ ファクトリ内で一意です。 名前の大文字と小文字は区別されません。 |<ul><li>オブジェクト名は英字で始める必要があります。</li><li>次の文字は入力できません: “.”、“+”、“?”、“/”、“<”、”>”、”*”、”%”、”&”、”:”、”\\”</li><li>リンクされたサービス、データ フロー、データセットの名前では、ダッシュ ("-") は使用できません。</li></ul>  |
| 統合ランタイム |データ ファクトリ内で一意です。 名前の大文字と小文字は区別されません。 |<ul><li>統合ランタイムの名前には、アルファベット、数字、およびダッシュ (-) 文字のみを含めることができます。</li><li>先頭と末尾の文字は、アルファベットまたは数字にする必要があります。 すべてのダッシュ (-) 文字は、その直前または直後に文字または数字が使用されている必要があります。</li><li>統合ランタイム名に連続するダッシュを使用することはできません。 </li></ul> |
| データ フローの変換 | データ フロー内で一意です。 名前では大文字と小文字は区別されません | <ul><li>データ フローの変換名には、文字と数字のみを含めることができます</li><li>先頭の文字は英字にする必要があります。 </li></ul> |
| リソース グループ |Microsoft Azure 全体で一意です。 名前の大文字と小文字は区別されません。 | 詳細については、「[名前付け規則と制約事項](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming)」を参照してください。 |

## <a name="next-steps"></a>次のステップ
[クイック スタート: データ ファクトリの作成](quickstart-create-data-factory-powershell.md)の手順に従ってデータ ファクトリを作成する方法を確認します。 
