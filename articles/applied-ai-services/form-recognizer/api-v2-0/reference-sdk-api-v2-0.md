---
title: 'リファレンス: Form Recognizer クライアント ライブラリ 3.0.0 および REST API v2.0'
titleSuffix: Azure Cognitive Services
description: Form Recognizer クライアント ライブラリ v3.0.0 または REST API v2.0 を使用して、カスタム ドキュメントからキーと値のペアとテーブル データを抽出するフォーム処理アプリを作成します。
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: reference
ms.date: 05/25/2021
ms.author: lajanuar
zone_pivot_groups: programming-languages-set-formre
ms.custom: devx-track-js, devx-track-csharp
keywords: フォーム処理, 自動データ処理
ms.openlocfilehash: cba7378dcc504e675e449dda4243149419e2726a
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130265871"
---
# <a name="reference-azure-form-recognizer-client-library-v300-and-rest-api-v20"></a>リファレンス: Azure Form Recognizer クライアント ライブラリ v3.0.0 および REST API v2.0

>[!IMPORTANT]
>
> * このガイドは、Azure Form Recognizer SDK v3.0.0 と REST API v2.0 に基づいています。 最新バージョンのドキュメントとコードサンプルについては、 **[クイックスタート](../quickstarts/try-sdk-rest-api.md)** の記事を参照してください。
>
>* この記事のコードでは、単純化するために、同期メソッドと、セキュリティで保護されていない資格情報の格納を使用しています。 運用環境では、セキュリティで保護された方法を使用して資格情報を格納し、アクセスします。 詳細については、Cognitive Services の[セキュリティ](../../../cognitive-services/cognitive-services-security.md)に関するページを参照してください。

好みの開発言語で Azure Form Recognizer の使用を開始しましょう。 Azure Form Recognizer は、機械学習テクノロジを利用して自動データ処理ソフトウェアを構築することができる [Azure Applied AI Services](../../../applied-ai-services/index.yml) です。 フォーム ドキュメント &mdash; から、テキスト、キーと値のペア、選択マーク、テーブル データなどを特定して抽出します。このサービスによって、元のファイルにおけるリレーションシップを含む構造化データが出力されます。 Form Recognizer は、REST API または SDK を介して使用できます。 以下の手順に従って、SDK パッケージをインストールし、基本タスクのコード例を試してみましょう。

::: zone pivot="programming-language-csharp"

[!INCLUDE [C# SDK v3.0.0](includes/csharp-v3-0-0.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [Java SDK v3.0.0](includes/java-v3-0-0.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [NodeJS SDK v3.0.0](includes/javascript-v3-0-0.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [Python SDK v3.0.0](includes/python-v3-0-0.md)]

::: zone-end

::: zone pivot="programming-language-rest-api"

[!INCLUDE [REST API v2.0](includes/rest-api-v2-0.md)]

::: zone-end