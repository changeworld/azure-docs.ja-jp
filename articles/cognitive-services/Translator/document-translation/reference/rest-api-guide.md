---
title: Document Translation REST API リファレンス ガイド
titleSuffix: Azure Cognitive Services
description: Document Translation REST API へのリンクの一覧をご覧ください。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 06/21/2021
ms.author: lajanuar
ms.openlocfilehash: 38757efc32a90a74cdf73bc84af28b80f8402948
ms.sourcegitcommit: 5fabdc2ee2eb0bd5b588411f922ec58bc0d45962
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2021
ms.locfileid: "112543889"
---
# <a name="document-translation-rest-api-reference-guide"></a>Document Translation REST API リファレンス ガイド

ドキュメント翻訳は、Azure Translator サービスのクラウドベースの機能であり、REST API の Azure Cognitive Services ファミリの一部です。 Document Translation API は、ドキュメントの構造とデータ形式を維持しながら、[サポートされている言語および方言](../../language-support.md)との間でドキュメント翻訳を行います。 利用可能なメソッドを次の表に示します。

| Request| 説明|
|---------|--------------|
| [**サポートされるドキュメントの形式の取得**](get-supported-document-formats.md)| このメソッドでは、サポートされているドキュメント形式の一覧が返されます。|
|[**サポートされる用語集の形式の取得**](get-supported-glossary-formats.md)|このメソッドでは、サポートされている用語集形式の一覧が返されます。|
|[**サポートされるストレージ ソースの取得**](get-supported-storage-sources.md)| このメソッドでは、サポートされているストレージ ソースとオプションの一覧が返されます。|
|[**翻訳の開始 (POST)** ](start-translation.md)|このメソッドは、ドキュメント翻訳ジョブを開始します。 |
|[**ドキュメント状態の取得**](get-documents-status.md)|このメソッドでは、翻訳ジョブ内のすべてのドキュメントの状態が返されます。|
|[**ドキュメント状態の取得**](get-document-status.md)| このメソッドでは、ジョブ内の特定のドキュメントの状態が返されます。 |
|[**翻訳状態の取得**](get-translations-status.md)| このメソッドでは、ユーザーから送信されたすべての翻訳要求の一覧と各要求の状態が返されます。|
|[**翻訳状態の取得**](get-translation-status.md) | このメソッドでは、特定のドキュメント翻訳要求の状態のサマリーが返されます。 これには、要求の全体的な状態と、その要求の一部として翻訳されているドキュメントの状態が含まれます。|
|[**翻訳のキャンセル (DELETE)** ](cancel-translation.md)| このメソッドは、現在処理中またはキューに置かれているドキュメント翻訳をキャンセルします。 |

> [!div class="nextstepaction"]
> [C# と Python 用のクライアント ライブラリおよび SDK について詳しく見る。](../client-sdks.md)