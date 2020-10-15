---
title: クエリ言語
titleSuffix: Azure Digital Twins
description: Azure Digital Twins クエリ言語の基本について理解します。
author: baanders
ms.author: baanders
ms.date: 3/26/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: d656f19f6f4030025ff1393c3e5017466b3333fd
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92044396"
---
# <a name="about-the-query-language-for-azure-digital-twins"></a>Azure Digital Twins 用のクエリ言語について

Azure Digital Twins の中心は、**デジタル ツイン**と**リレーションシップ**から構築された[**ツイン グラフ**](concepts-twins-graph.md)であることを思い出してください。 このグラフに対してクエリを実行し、デジタル ツインとそれに含まれるリレーションシップに関する情報を取得することができます。 これらのクエリは、**Azure Digital Twins クエリ言語**と呼ばれる、SQL に似たクエリ言語で記述されます。

クライアント アプリからサービスにクエリを送信するには、Azure Digital Twins の [**Query API**](/dotnet/api/azure.digitaltwins.core.digitaltwinsclient.query?view=azure-dotnet-preview) を使用します。 これにより、開発者はクエリを作成し、フィルターを適用して、ツイン グラフ内のデジタル ツインのセットや、Azure Digital Twins のシナリオに関するその他の情報を検索できます。

[!INCLUDE [digital-twins-query-operations.md](../../includes/digital-twins-query-operations.md)]

## <a name="next-steps"></a>次のステップ

クエリの作成方法を学習し、クライアント コードの例を見るには、[*方法:ツイン グラフにクエリを実行する*](how-to-query-graph.md)方法に関する記事を参照してください。