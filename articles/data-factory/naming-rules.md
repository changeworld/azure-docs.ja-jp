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
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: shlo
ms.openlocfilehash: cfab1a82c7da0ad596c9989e5a9f3ed800c58e4a
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2018
ms.locfileid: "39389312"
---
# <a name="azure-data-factory---naming-rules"></a>Azure Data Factory - 名前付け規則
次の表に、Data Factory アーティファクトの名前付け規則を示します。

| Name | 名前の一意性 | 検証チェック |
|:--- |:--- |:--- |
| Data Factory |Microsoft Azure 全体で一意です。 名前の大文字と小文字を区別されません。つまり、`MyDF` と `mydf` は同じデータ ファクトリを表します。 |<ul><li>各データ ファクトリは、厳密に 1 つの Azure サブスクリプションに関連付けられます。</li><li>オブジェクト名は英文字または数字で始まり、英文字、数字、ダッシュ (-) 文字のみを含めることができます。</li><li>すべてのダッシュ (-) 文字は、その直前または直後に文字または数字が使用されている必要があります。 連続するダッシュ文字はコンテナー名では使用できません。</li><li>名前は 3 ～ 63 文字の長さにすることができます。</li></ul> |
| リンクされたサービス、データセット、パイプライン |データ ファクトリ内で一意です。 名前の大文字と小文字は区別されません。 |<ul><li>オブジェクト名は、文字、数字、アンダー スコア (_) のいずれかで始める必要があります。</li><li>次の文字は使用できません: “.”, “+”, “?”, “/”, “<”, ”>”,”*”,”%”,”&”,”:”,”\\"</li><li>リンクされたサービスとデータセットの名前では、ダッシュ ("-") は使用できません。</li></ul>  |
| リソース グループ |Microsoft Azure 全体で一意です。 名前の大文字と小文字は区別されません。 | 詳細については、「[名前付け規則と制約事項](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions)」を参照してください。 |

## <a name="next-steps"></a>次の手順
[クイック スタート: データ ファクトリの作成](quickstart-create-data-factory-powershell.md)の手順に従ってデータ ファクトリを作成する方法を確認します。 
