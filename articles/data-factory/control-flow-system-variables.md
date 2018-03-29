---
title: Azure Data Factory のシステム変数 | Microsoft Docs
description: この記事では、Azure Data Factory でサポートされているシステム変数について説明します。 Data Factory エンティティを定義するときに、式でこれらの変数を使用できます。
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: shlo
ms.openlocfilehash: 77285692846d2a301e091426801e20203816181a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
---
# <a name="system-variables-supported-by-azure-data-factory"></a>Azure Data Factory でサポートされているシステム変数
この記事では、Azure Data Factory でサポートされているシステム変数について説明します。 Data Factory エンティティを定義するときに、式でこれらの変数を使用できます。 

> [!NOTE]
> この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されている Data Factory サービスのバージョン 1 を使用している場合は、「[Functions and variables in Data Factory V1](v1/data-factory-functions-variables.md)」(Data Factory V1 の関数と変数)を参照してください。


## <a name="pipeline-scope"></a>パイプラインのスコープ:

| 変数名 | [説明] |
| --- | --- |
| @pipeline().DataFactory |パイプライン実行が実行されているデータ ファクトリの名前 | 
| @pipeline().Pipeline |パイプラインの名前 |
| @pipeline().RunId | 特定のパイプライン実行の ID | 
| @pipeline().TriggerType | パイプラインを呼び出したトリガーの種類 (手動、Scheduler) | 
| @pipeline（）.TriggerId| パイプラインを呼び出すトリガーの ID |
| @pipeline（）.TriggerName| パイプラインを呼び出すトリガーの名前 |
| @pipeline（）.TriggerTime| トリガーがパイプラインを呼び出した時間。 トリガーの時間は、実際の起動時間であり、スケジュールされた時間ではありません。 たとえば、`13:20:00.00Z` ではなく、`13:20:08.0149599Z` が返されます |

## <a name="trigger-scope"></a>トリガーのスコープ:

| 変数名 | [説明] |
| --- | --- |
| trigger().scheduledTime |トリガーがパイプライン実行を呼び出すようにスケジュールされた時間。 たとえば、5 分ごとに起動されるトリガーの場合、この変数はそれぞれ `2017-06-01T22:20:00Z`、`2017-06-01T22:25:00Z`、`2017-06-01T22:29:00Z` を返します。|
| trigger().startTime |トリガーが**実際に**パイプライン実行を呼び出すために起動した時間。 たとえば、5 分ごとに起動されるトリガーの場合、この変数はそれぞれ、`2017-06-01T22:20:00.4061448Z`、`2017-06-01T22:25:00.7958577Z`、`2017-06-01T22:29:00.9935483Z` のような値を返します。|

## <a name="next-steps"></a>次の手順
これらの変数を式で使用する方法については、[式言語と関数](control-flow-expression-language-functions.md)に関するページを参照してください。 
