---
title: Entity Linking Intelligence Service API の概要
titlesuffix: Azure Cognitive Services
description: エンティティ リンク設定タスクで、Entity Linking Intelligence Service API を使用する方法を説明します。
services: cognitive-services
author: DavidLiCIG
manager: cgronlun
ms.service: cognitive-services
ms.subservice: entity-linking-intelligence
ms.topic: overview
ms.date: 03/28/2016
ms.author: davl
ms.openlocfilehash: fa2e480cd2c46e6092f4b0421adca63542e54d1b
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2019
ms.locfileid: "55224634"
---
# <a name="what-is-the-entity-linking-intelligence-service-api"></a>Entity Linking Intelligence Service API の概要

Entity Linking Intelligence Service は、エンティティ リンク設定に関するタスクで開発者を支援するために作成された Web サービスです。

## <a name="entity-linking"></a>Entity Linking

単語は、さまざまなコンテキストの任意の文中で固有表現、動詞、または別の単語の形で使われることがあります。 たとえば、“times” が名前付きエンティティである場合でも、“The New York Times” や “Times Square” といった 2 つの区別可能な別個のエンティティを表すことができます。 Entity Linking Intelligence Service は、ドキュメント内の特定の段落を指定されると、文脈に基づいて、個別のエンティティそれぞれを識別します。  

次の図は、エンティティ リンク設定の例を示します。 具体的には、Wikipedia を使用するとき、Entity Linking Intelligence Service は、入力テキスト内に列挙されているすべてのエンティティを検出して、それらを関連する参照ポイントにページ ID に応じてリンクします。

 ![Mars のエンティティのエンティティ リンク設定例](./Images/EntityLinkingSample1.png)
 
## <a name="get-started"></a>作業の開始
 
エンティティ リンク設定の基本機能とサブスクリプションを簡単に確認するには、作業開始のためのチュートリアルを参照してください。
[C# での Entity Linking API の作業開始](GettingStarted.md)


