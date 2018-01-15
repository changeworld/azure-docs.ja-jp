---
title: "スマート検出 - Azure Application Insights によって検出される使用率が低いクラウド リソース | Microsoft Docs"
description: "Azure Application Insights を使用して、アプリケーションの使用率が低いクラウド リソースを監視します。"
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: mbullwin
ms.openlocfilehash: 8382f6047ae222a01cc0e8d6ca9dcf5593d0dff6
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2018
---
# <a name="low-utilization-of-cloud-resources-preview"></a>使用率が低いクラウド リソース (プレビュー)

Application Insights は、アプリケーションの各ロール インスタンスの CPU 使用量を自動的に分析し、CPU 使用率が低いインスタンスを検出します。 この検出を使用して、各ロールが使用するロール インスタンスの数を減らすか、ロールの数を減らすことで、Azure リソースを減らしてコストを削減できます。

この機能を使用するには、アプリの[パフォーマンス カウンターを構成する](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters)こと以外は、特別な設定は必要ありません。 それは、アプリが十分な CPU パフォーマンス カウンター テレメトリ (% プロセッサ時間) を生成しているときにアクティブになります。

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>この種類のスマート検出通知はどのような場合に取得されますか。
一般的な通知は、多数の Web/ワーカー ロール インスタンスで CPU 使用率が低くなった場合に発生します。

## <a name="does-my-app-definitely-consume-too-many-resources"></a>アプリが消費しているリソースの数が多すぎるのは確かですか
いいえ。通知は、アプリが消費しているリソースの数が間違いなく多すぎることを示すものではありません。 このような CPU 使用率が低いパターンは、通常は、リソースの消費量が減少している可能性があることを示唆していますが、この動作は、特定のロールでは一般的であったり、業務上は妥当である可能性があるため、無視することができます。 たとえば、複数のインスタンスが必要としているのが、CPU ではなく、メモリ/ネットワークなどの他のリソースである場合が考えられます。

## <a name="how-do-i-fix-it"></a>どのように修正すればよいですか
通知には、診断プロセスでサポートされる診断情報が含まれています。
1. **トリアージ**。 通知には、CPU 使用率が低くなっているアプリのロールが示されます。 この情報を基に、問題に優先順位を割り当てることができます。
2. **範囲**。 いくつのロールで CPU 使用率が低くなり、各ロールのいくつのインスタンスで CPU 使用率が低いのか?  この情報を通知から取得できます。
3. **診断**。 検出には CPU が使用された割合が含まれ、特定の期間の各インスタンスの CPU 使用率が示されます。 サポート情報 (CPU 使用の割合など) にリンクしている関連項目とレポートを使用して、問題の詳細な診断に役立てることもできます。
