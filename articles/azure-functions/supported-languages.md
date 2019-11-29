---
title: Azure Functions でサポートされている言語
description: どの言語がサポートされているか (GA) と、どの言語が試験段階またはプレビューの段階であるかを説明します。
ms.topic: conceptual
ms.date: 08/02/2018
ms.openlocfilehash: 3b61dd83e481d42dd15f089247d016c6b71ff0de
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230278"
---
# <a name="supported-languages-in-azure-functions"></a>Azure Functions でサポートされている言語

この記事では、Azure Functions で使用できる言語のサポートのレベルについて説明します。

## <a name="levels-of-support"></a>サポートのレベル

サポートのレベルは、次の 3 つです。

* **一般公開 (GA)** - 完全にサポートされ、運用環境用に承認されています。
* **プレビュー** - まだサポートされていませんが、今後 GA 状態に達すると想定されています。
* **試験段階** - サポートされておらず、今後廃止される可能性があります。最終的にプレビューまたは GA 状態になる保証はありません。

## <a name="languages-by-runtime-version"></a>言語 (ランタイム バージョン別) 

[2 つのバージョンの Azure Functions ランタイム](functions-versions.md)を利用できます。 次の表は、各ランタイム バージョンでどの言語がサポートされているかを示しています。

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

### <a name="experimental-languages"></a>試験段階の言語

バージョン 1.x の試験段階の言語は、スケーリングが適切に行われず、一部のバインドがサポートされていません。

信頼性が大切である場合には、試験段階の機能は使用しないでください。それらの言語には公式なサポートがありません。 実験段階の言語で発生した問題については、サポート ケースを開かないでください。 

バージョン 2.x ランタイムでは、試験段階の言語はサポートされていません。 新しい言語のサポートは、運用環境でその言語をサポートできる場合にのみ追加されます。 

### <a name="language-extensibility"></a>言語拡張

2\.x 以降、ランタイムは、[言語拡張](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility)を提供するように設計されています。 2\.x ランタイムの JavaScript および Java 言語は、この拡張機能で構築されています。

## <a name="next-steps"></a>次の手順

サポートされている言語で関数を開発する方法の詳細については、次のリソースを参照してください。

+ [C# クラス ライブラリ開発者向けリファレンス](functions-dotnet-class-library.md)
+ [C# 開発者向けリファレンス](functions-reference-csharp.md)
+ [Java 開発者用リファレンス](functions-reference-java.md)
+ [JavaScript 開発者向けリファレンス](functions-reference-node.md)
+ [PowerShell 開発者向けリファレンス](functions-reference-powershell.md)
+ [Python 開発者向けリファレンス](functions-reference-python.md)
+ [TypeScript 開発者向けリファレンス](functions-reference-node.md#typescript)
