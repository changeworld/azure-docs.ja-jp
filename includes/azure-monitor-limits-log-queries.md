---
title: インクルード ファイル
description: インクルード ファイル
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 07/22/2019
ms.author: bwren
ms.custom: include file
ms.openlocfilehash: ed840352096f1a1739bc8f655be42096456d3c33
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405679"
---
| 制限 | 説明 |
|:---|:---|
| クエリ言語 | Azure Monitor では、Azure Data Explorer と同じ [Kusto クエリ言語](/azure/kusto/query/)が使用されます。 Azure Monitor でサポートされていない KQL 言語要素については、「[Azure Monitor ログ クエリ言語の違い](../articles/azure-monitor/log-query/data-explorer-difference.md)」を参照してください。 |
| Azure Azure リージョン | データが複数の Azure リージョンの Log Analytics ワークスペースにまたがっている場合、ログ クエリで過剰なオーバーヘッドが発生する可能性があります。 詳細については、「[クエリの制限](../articles/azure-monitor/log-query/scope.md#query-limits)」をご覧ください。 |
