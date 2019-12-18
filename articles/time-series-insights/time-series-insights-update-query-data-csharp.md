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
ms.openlocfilehash: 20c1f1f9a8b0b0ef105893e44c9daaeae68604db
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74889755"
---
# <a name="query-data-from-the-azure-time-series-insights-preview-environment-using-c"></a>C# を使用して Azure Time Series Insights プレビュー環境からデータを照会する

この C# サンプルでは、Azure Time Series Insights プレビュー環境からデータを照会する方法について説明します。

このサンプルでは、クエリ API の基本的な使用例をいくつか示します。

1. 準備手順として、Azure Active Directory API を使用してアクセス トークンを取得します。 このトークンをすべてのクエリ API 要求の `Authorization` ヘッダーで渡します。 非対話型アプリケーションのセットアップについては、「[Azure Time Series Insights API の認証と承認](time-series-insights-authentication-and-authorization.md)」を参照してください。 また、サンプルの先頭で定義されているすべての定数を正しく設定されていることを確認します。
1. ユーザーがアクセスできる環境の一覧を取得します。 環境の 1 つを関心のある環境として選択し、この環境のデータを照会します。
1. HTTPS 要求の例としては、関心のある環境の可用性データを要求します。
1. [Azure AutoRest](https://github.com/Azure/AutoRest) からの SDK の自動生成サポートの例を示します。

> [!NOTE]
> コード例およびコンパイルして実行する手順については、[https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-preview-sample) を参照してください。

## <a name="c-example"></a>C# の例

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-preview-sample/DataPlaneClientSampleApp/Program.cs)]

> [!NOTE]
> 上記のコード サンプルは、既定の環境値を変更せずに実行できます。

## <a name="next-steps"></a>次の手順

- クエリの詳細については、[クエリ API リファレンス](https://docs.microsoft.com/rest/api/time-series-insights/preview-query)を参照してください。

- Time Series Insights に [クライアント SDK を使用して JavaScript アプリを接続する](https://github.com/microsoft/tsiclient)方法について確認します。
