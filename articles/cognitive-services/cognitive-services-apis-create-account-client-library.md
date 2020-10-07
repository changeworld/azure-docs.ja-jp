---
title: Azure Management クライアント ライブラリを使用して Cognitive Services リソースを作成する
titleSuffix: Azure Cognitive Services
description: Azure Management クライアント ライブラリを使用して、Azure Cognitive Services リソースを作成および管理します。
services: cognitive-services
keywords: Cognitive Services, コグニティブ インテリジェンス, コグニティブ ソリューション, AI サービス
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 09/14/2020
ms.author: pafarley
zone_pivot_groups: programming-languages-set-ten
ms.openlocfilehash: e8628d051db7f5066a81171567f6f7e54fb0ab97
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2020
ms.locfileid: "91262452"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-the-azure-management-client-library"></a>クイック スタート:Azure Management クライアント ライブラリを使用して Cognitive Services リソースを作成する

このクイックスタートを利用すると、Azure Management クライアント ライブラリを使用して、Azure Cognitive Services リソースを作成および管理できます。

Azure Cognitive Services は、開発者が直接的な人工知能 (AI) またはデータ サイエンスのスキルや知識がなくてもコグニティブかつインテリジェントなアプリケーションを構築できる、REST API シリーズとクライアント ライブラリ SDK を含むクラウドベースのサービスです。 開発者は Azure Cognitive Services を使用して、見たり、聞いたり、話したり、理解したり、推論し始めたりできるコグニティブ ソリューションを使用したコグニティブ機能をそのアプリケーションに容易に追加することができます。

個別の AI サービスは、ご利用の Azure サブスクリプションに作成した Azure [リソース](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)によって表されます。 リソースの作成後、生成されたキーとエンドポイントを使用して、アプリケーションを認証できます。

::: zone pivot="programming-language-csharp"

[!INCLUDE [C# SDK quickstart](includes/quickstarts/management-csharp.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [Java SDK quickstart](includes/quickstarts/management-java.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [NodeJS SDK quickstart](includes/quickstarts/management-node.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [Python SDK quickstart](includes/quickstarts/management-python.md)]

::: zone-end
