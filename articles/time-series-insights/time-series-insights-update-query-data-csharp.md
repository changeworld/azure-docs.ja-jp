---
title: C# を使用してプレビュー環境からデータを照会する - Azure Time Series Insights | Microsoft Docs
description: C# で記述されたアプリを使用して Azure Time Series Insights 環境からデータを照会する方法を学びます。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 12/05/2019
ms.custom: seodec18
ms.openlocfilehash: a1d32bf7ea296ed2c4ed9351fcefe400c03effa5
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/10/2020
ms.locfileid: "75861440"
---
# <a name="query-data-from-the-azure-time-series-insights-preview-environment-using-c"></a>C# を使用して Azure Time Series Insights プレビュー環境からデータを照会する

この C# サンプルでは、Azure Time Series Insights プレビュー環境からデータを照会する方法について説明します。

このサンプルでは、クエリ API の基本的な使用例をいくつか示します。

1. 準備手順として、Azure Active Directory API を使用してアクセス トークンを取得します。 このトークンをすべてのクエリ API 要求の `Authorization` ヘッダーで渡します。 非対話型アプリケーションの設定については、[認証と承認](time-series-insights-authentication-and-authorization.md)に関する記事をお読みください。 また、サンプルの先頭で定義されているすべての定数を正しく設定されていることを確認します。
1. ユーザーがアクセスできる環境の一覧を取得します。 環境の 1 つを関心のある環境として選択し、この環境のデータを照会します。
1. HTTPS 要求の例としては、関心のある環境の可用性データを要求します。
1. [Azure AutoRest](https://github.com/Azure/AutoRest) からの SDK の自動生成サポートの例を示します。

> [!NOTE]
> コード例およびコンパイルして実行する手順については、[https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-preview-sample) を参照してください。

## <a name="c-example"></a>C# の例

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-preview-sample/DataPlaneClientSampleApp/Program.cs)]

> [!NOTE]
> 上記のコード サンプルは、既定の環境値を変更せずに実行できます。

## <a name="next-steps"></a>次のステップ

- クエリの詳細については、[クエリ API リファレンス](https://docs.microsoft.com/rest/api/time-series-insights/preview-query)を参照してください。

- Time Series Insights に [クライアント SDK を使用して JavaScript アプリを接続する](https://github.com/microsoft/tsiclient)方法について確認します。
