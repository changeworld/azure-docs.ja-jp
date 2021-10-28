---
title: Azure Functions でサポートされている言語
description: サポートされている言語 (GA) とプレビュー段階の言語、および Functions の開発を他の言語に拡張する方法について説明します。
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: 91dcd26895ad41d79606458287f6aabab9594f79
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130224312"
---
# <a name="supported-languages-in-azure-functions"></a>Azure Functions でサポートされている言語

この記事では、Azure Functions で使用できる言語のサポートのレベルについて説明します。 また、ネイティブでサポートされていない言語を使用して関数を作成する方法についても説明します。

[!INCLUDE [functions-support-levels](../../includes/functions-support-levels.md)]

## <a name="languages-by-runtime-version"></a>言語 (ランタイム バージョン別) 

[複数のバージョンの Azure Functions ランタイム](functions-versions.md)を利用できます。 次の表は、各ランタイム バージョンでどの言語がサポートされているかを示しています。

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

[!INCLUDE [functions-portal-language-support](../../includes/functions-portal-language-support.md)]

### <a name="language-major-version-support"></a>言語のメジャー バージョンのサポート

Azure Functions では、サポートされているプログラミング言語のメジャー バージョンのサポートを保証しています。 ほとんどの言語では、サポートされているメジャー バージョンを更新するために、マイナー バージョンまたはパッチ バージョンがリリースされています。 マイナー バージョンやパッチ バージョンの例としては、Python 3.9.1 や Node 14.17 などがあります。 サポートされている言語の新しいマイナー バージョンが使用可能になると、関数アプリで使用されるマイナー バージョンは、それらの新しいマイナー バージョンまたはパッチ バージョンに自動的にアップグレードされます。 

> [!NOTE]
>Azure Functions では、新しいマイナー バージョンが使用可能になった後いつでも古いマイナー バージョンのサポートが削除されることがあるため、関数アプリをプログラミング言語の特定のマイナーまたはパッチ バージョンに固定しないでください。  
>

## <a name="custom-handlers"></a>カスタム ハンドラー

カスタム ハンドラーは、Azure Functions ホストからイベントを受信する軽量の Web サーバーです。 HTTP プリミティブをサポートするすべての言語で、カスタム ハンドラーを実装できます。 つまり、カスタム ハンドラーを使用して、正式にサポートされていない言語で関数を作成できることを意味します。 詳細については、「[Azure Functions のカスタム ハンドラー](functions-custom-handlers.md)」を参照してください。

## <a name="language-extensibility"></a>言語拡張

2\.x 以降、ランタイムは、[言語拡張](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility)を提供するように設計されています。 2\.x ランタイムの JavaScript および Java 言語は、この拡張機能で構築されています。

## <a name="next-steps"></a>次のステップ

サポートされている言語で関数を開発する方法の詳細については、次のリソースを参照してください。

+ [C# クラス ライブラリ開発者向けリファレンス](functions-dotnet-class-library.md)
+ [C# 開発者向けリファレンス](functions-reference-csharp.md)
+ [Java 開発者用リファレンス](functions-reference-java.md)
+ [JavaScript 開発者向けリファレンス](functions-reference-node.md)
+ [PowerShell 開発者向けリファレンス](functions-reference-powershell.md)
+ [Python 開発者向けリファレンス](functions-reference-python.md)
+ [TypeScript 開発者向けリファレンス](functions-reference-node.md#typescript)
