---
title: Azure Application Insights を使用して要求を追跡するコードを記述する | Microsoft Docs
description: 要求のプロファイルを取得できるように、Application Insights を使用して要求を追跡するコードを記述する
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: 20f408d9dd32c3fd7a0e319e4051483e3aa54dd9
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/08/2019
ms.locfileid: "54082029"
---
# <a name="write-code-to-track-requests-with-application-insights"></a>Application Insights を使用して要求を追跡するコードを記述する

[パフォーマンス] ページでアプリケーションのプロファイルを確認するには、Application Insights がアプリケーションの要求を追跡している必要があります。 Application Insights では、既にインストルメント化されているフレームワーク (ASP.net や ASP.Net Core など) 上に構築されているアプリケーションの要求を、自動的に追跡することができます。 ただし、Azure Cloud Service の worker ロールや Service Fabric ステートレス API のようなその他のアプリケーションでは、Application Insights に要求の開始位置と終了位置を伝えるコードを記述する必要があります。 このコードを記述すると、要求テレメトリが Application Insights に送信され、[パフォーマンス] ページにテレメトリが表示され、それらの要求のプロファイルが収集されます。 

要求を手動で追跡するために必要な手順を次に示します。


  1. アプリケーションの有効期間の初期段階に次のコードを追加します。  

        ```csharp
        using Microsoft.ApplicationInsights.Extensibility;
        ...
        // Replace with your own Application Insights instrumentation key.
        TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
        ```
      このグローバル インストルメンテーション キーの構成については、「[Using Service Fabric with Application Insights](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md)」(Service Fabric と Application Insights を使用する) を参照してください。  

  1. 次の例のように、インストルメント化するすべてのコードの周辺に `StartOperation<RequestTelemetry>` **USING** ステートメントを追加します。

        ```csharp
        using Microsoft.ApplicationInsights;
        using Microsoft.ApplicationInsights.DataContracts;
        ...
        var client = new TelemetryClient();
        ...
        using (var operation = client.StartOperation<RequestTelemetry>("Insert_Your_Custom_Event_Unique_Name"))
        {
          // ... Code I want to profile.
        }
        ```

        別の `StartOperation<RequestTelemetry>` スコープ内で `StartOperation<RequestTelemetry>` を呼び出すことはサポートされていません。 代わりに、入れ子にしたスコープで `StartOperation<DependencyTelemetry>` を使用できます。 例:   
        
        ```csharp
        using (var getDetailsOperation = client.StartOperation<RequestTelemetry>("GetProductDetails"))
        {
        try
        {
          ProductDetail details = new ProductDetail() { Id = productId };
          getDetailsOperation.Telemetry.Properties["ProductId"] = productId.ToString();
        
          // By using DependencyTelemetry, 'GetProductPrice' is correctly linked as part of the 'GetProductDetails' request.
          using (var getPriceOperation = client.StartOperation<DependencyTelemetry>("GetProductPrice"))
          {
              double price = await _priceDataBase.GetAsync(productId);
              if (IsTooCheap(price))
              {
                  throw new PriceTooLowException(productId);
              }
              details.Price = price;
          }
        
          // Similarly, note how 'GetProductReviews' doesn't establish another RequestTelemetry.
          using (var getReviewsOperation = client.StartOperation<DependencyTelemetry>("GetProductReviews"))
          {
              details.Reviews = await _reviewDataBase.GetAsync(productId);
          }
        
          getDetailsOperation.Telemetry.Success = true;
          return details;
        }
        catch(Exception ex)
        {
          getDetailsOperation.Telemetry.Success = false;
        
          // This exception gets linked to the 'GetProductDetails' request telemetry.
          client.TrackException(ex);
          throw;
        }
        }
        ```
