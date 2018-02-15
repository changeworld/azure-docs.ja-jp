---
title: "Azure Functions ランタイム バージョンの概要"
description: "Azure Functions では、複数のバージョンのランタイムがサポートされます。 バージョン間の違いと、適切なバージョンを選択する方法について説明します。"
services: functions
documentationcenter: 
author: ggailey777
manager: cfowler
editor: 
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2018
ms.author: glenga
ms.openlocfilehash: 9f916aaa8032ff519709d73a1c1f51195f811686
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2018
---
# <a name="azure-functions-runtime-versions-overview"></a>Azure Functions ランタイム バージョンの概要

 Azure Functions ランタイムには、2 つのメジャー バージョン 1.x と 2.x があります。 この記事では、使用するメジャー バージョンを選択する方法について説明します。

> [!IMPORTANT] 
> ランタイム 1.x は、運用環境用に承認された唯一のバージョンです。

| ランタイム | 状態 |
|---------|---------|
|1.x|一般公開 (GA)|
|2.x|プレビュー|

関数アプリまたは特定のバージョン用の開発環境を構成する方法については、「[Azure Functions ランタイム バージョンをターゲットにする方法](set-runtime-version.md)」および「[Azure Functions をローカルでコーディングしてテストする](functions-run-local.md)」をご覧ください。

## <a name="cross-platform-development"></a>クロスプラットフォーム開発

ランタイム 1.x では、ポータルまたは Windows のみで開発とホスティングがサポートされます。 ランタイム 2.x は .NET Core で実行されます。つまり、ランタイム 2.x は、macOS や Linux など、.NET Core でサポートされるすべてのプラットフォームで実行できます。 これにより、1.x ではできなかったクロスプラットフォーム開発とホスティング シナリオが可能になります。

## <a name="languages"></a>言語

ランタイム 2.x では、新しい言語拡張モデルが使用されます。 最初に、JavaScript と Java でこの新しいモデルが活用されます。 Azure Functions 1.x の試験段階の言語は、新しいモデルを使用するよう更新されていないため、2.x ではサポートされません。 次の表は、各ランタイム バージョンでどのプログラミング言語がサポートされているかを示しています。

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

詳細については、[サポートされている言語](supported-languages.md)に関するページを参照してください。

## <a name="bindings"></a>バインド 

ランタイム 2.x で使用される新しい[バインド拡張モデル](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview)の利点は、次のとおりです。

* サード パーティのバインド拡張のサポート。
* ランタイムとバインドの分離。 これにより、バインド拡張を個別にバージョン管理したり、解放したりできます。 たとえば、基になる SDK の新しいバージョンに依存する拡張のバージョンにアップグレードするよう選択できます。
* より軽量な実行環境。ここでは、使用中のバインドのみがランタイムによって識別され、読み込まれます。

タイマー トリガーと HTTP トリガーを除いて、すべての組み込み Azure Functions バインドでこのモデルが採用され、既定では含まれなくなりました。 Visual Studio などのツールまたはポータルを通じて関数を作成するときに、これらの拡張が自動的にインストールされます。

各ランタイム バージョンでサポートされるバインドを次の表に示します。

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

## <a name="known-issues-in-2x"></a>2.x の既知の問題

2.x でのバインドのサポートおよびその他の機能的な差異については、[ランタイム 2.0 の既知の問題](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Azure-Functions-runtime-2.0-known-issues)に関するページをご覧ください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [ローカル開発環境で 2.0 ランタイムをターゲットにする](functions-run-local.md)

> [!div class="nextstepaction"]
> [ランタイムのバージョンについては、リリース ノートを参照してください](https://github.com/Azure/azure-webjobs-sdk-script/releases)