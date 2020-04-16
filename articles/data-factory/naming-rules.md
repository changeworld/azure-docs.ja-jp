---
title: Azure Data Factory エンティティの名前付け規則
description: Data Factory エンティティの名前付け規則について説明します。
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2018
ms.openlocfilehash: f922ada663391cf65a61f4e18bba53668f9c4a1a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419410"
---
# <a name="azure-data-factory---naming-rules"></a>Azure Data Factory - 名前付け規則

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

次の表に、Data Factory アーティファクトの名前付け規則を示します。

| Name | 名前の一意性 | 検証チェック |
|:--- |:--- |:--- |
| Data Factory |Microsoft Azure 全体で一意です。 名前の大文字と小文字を区別されません。つまり、`MyDF` と `mydf` は同じデータ ファクトリを表します。 |<ul><li>各データ ファクトリは、厳密に 1 つの Azure サブスクリプションに関連付けられます。</li><li>オブジェクト名は英文字または数字で始まり、英文字、数字、ダッシュ (-) 文字のみを含めることができます。</li><li>すべてのダッシュ (-) 文字は、その直前または直後に文字または数字が使用されている必要があります。 連続するダッシュ文字はコンテナー名では使用できません。</li><li>名前は 3 ～ 63 文字の長さにすることができます。</li></ul> |
| リンクされたサービス、データセット、パイプライン |データ ファクトリ内で一意です。 名前の大文字と小文字は区別されません。 |<ul><li>オブジェクト名は、文字、数字、アンダー スコア (_) のいずれかで始める必要があります。</li><li>次の文字は使用できません: “.”, “+”, “?”, “/”, “<”, ”>”,”*”,”%”,”&”,”:”,”\\"</li><li>リンクされたサービスとデータセットの名前では、ダッシュ ("-") は使用できません。</li></ul>  |
| リソース グループ |Microsoft Azure 全体で一意です。 名前の大文字と小文字は区別されません。 | 詳細については、「[名前付け規則と制約事項](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming)」を参照してください。 |

## <a name="next-steps"></a>次のステップ
[クイック スタート: データ ファクトリの作成](quickstart-create-data-factory-powershell.md)の手順に従ってデータ ファクトリを作成する方法を確認します。 
