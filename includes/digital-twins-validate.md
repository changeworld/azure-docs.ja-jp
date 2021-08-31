---
author: baanders
description: Azure Digital Twins モデルの検証方法を説明するファイルを含める
ms.service: digital-twins
ms.topic: include
ms.date: 8/7/2020
ms.author: baanders
ms.openlocfilehash: 064f8e322d75e8c92f8ae9d0bd976ebbfb1bdb49
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114438586"
---
> [!TIP]
> モデルの作成後、モデルは、Azure Digital Twins インスタンスにアップロードする前に、オフラインで検証することが推奨されます。

言語に依存しない [DTDL 検証ツールのサンプル](/samples/azure-samples/dtdl-validator/dtdl-validator)があります。これにより、モデル ドキュメントを検証して、インスタンスにアップロードする前に DTDL が正しいことを確認できます。

DTDL 検証ツールのサンプルは、NuGet でクライアント側ライブラリとして提供されている .NET DTDL パーサー ライブラリに基づいて構築されています。[Microsoft.Azure.DigitalTwins.Parser](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/). また、このライブラリを直接使用し、自分独自の検証ソリューションを設計することもできます。 パーサー ライブラリを使用する場合は、Azure Digital Twins が実行されているバージョンと互換性のあるバージョンを使用するようにしてください。 現時点では、このバージョンは *3.12.4* です。

使用例を含む検証ツールのサンプルとパーサー ライブラリの詳細については、[モデルの解析と検証](../articles/digital-twins/how-to-parse-models.md)に関するページを参照してください。