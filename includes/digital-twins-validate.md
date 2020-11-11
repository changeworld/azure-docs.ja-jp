---
author: baanders
description: Azure Digital Twins モデルの検証方法を説明するファイルを含める
ms.service: digital-twins
ms.topic: include
ms.date: 8/7/2020
ms.author: baanders
ms.openlocfilehash: bccf92c6c912747e28b3075f12ac1558cced30c9
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130383"
---
> [!TIP]
> モデルの作成後、モデルは、Azure Digital Twins インスタンスにアップロードする前に、オフラインで検証することが推奨されます。

モデル ドキュメントを検証してインスタンスにアップロードする前に DTDL が正しいことを確認するための、言語に依存しないサンプルがあります。 これはこちらにあります。 [**DTDL 検証ツール サンプル**](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator)

* DTDL 検証ツールのサンプルは、NuGet でクライアント側ライブラリとして提供されている .NET DTDL パーサー ライブラリに基づいて構築されています。 [**Microsoft.Azure.DigitalTwins.Parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/). また、このライブラリを直接使用し、自分独自の検証ソリューションを設計することもできます。 パーサー ライブラリを使用する場合は、Azure Digital Twins が実行されているバージョンと互換性のあるバージョンを使用するようにしてください。 現時点では、このバージョンは *3.12.4* です。

使用例を含む検証ツールのサンプルとパーサー ライブラリの詳細については、" [*モデルを解析および検証する方法*](../articles/digital-twins/how-to-parse-models.md)" に関するページを参照してください。