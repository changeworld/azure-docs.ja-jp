---
title: Azure Functions 言語ランタイムのサポート ポリシー
description: Azure Functions 言語ランタイムのサポート ポリシーについて説明します
ms.topic: conceptual
ms.date: 06/14/2021
ms.openlocfilehash: c9a6b7346311106080fd57ad9428648a8332c79a
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2021
ms.locfileid: "114219198"
---
# <a name="language-runtime-support-policy"></a>言語ランタイムのサポート ポリシー

この記事では、Azure Functions 言語ランタイムのサポート ポリシーについて説明します。 

## <a name="retirement-process"></a>提供終了のプロセス

Azure Functions ランタイムは、オペレーティング システム、Azure Functions ホスト、言語固有のワーカーなど、さまざまなコンポーネントを中心に構築されています。 関数アプリの完全サポートの対象範囲を維持するために、Azure Functions のサポートは、プログラミング言語のバージョンの有効期限の終了に合わせて、段階的に縮小していきます。 言語バージョンの提供終了日は、ほとんどの場合、コミュニティの有効期限と一致しています。 

### <a name="notification-phase"></a>通知フェーズ

予定されている言語バージョンの提供終了に関する通知メールが、関数アプリのユーザーに送信されます。 この通知は、少なくとも提供終了日の 1 年前に行われます。 通知を受け取ったら、お使いの関数アプリの言語バージョンを、サポートされるバージョンへのアップグレードの準備を行う必要があります。

### <a name="retirement-phase"></a>提供終了フェーズ

* __フェーズ 1:__ 言語バージョンの有効期限の終了日に、その言語バージョンを対象とする新しい関数アプリを作成できなくなります。 既存の関数アプリについては、この日から少なくとも 60 日間、引き続きその言語バージョンで実行できます。また、更新も行われます。 このフェーズ中に、影響を受ける関数アプリの言語バージョンを、サポートされているバージョンにアップグレードすることを強くお勧めしています。

* __フェーズ 2__: 言語の有効期限の終了日から 60 日が経過した時点で、プラットフォーム上でのこの言語バージョンを対象とする関数アプリの動作が保証されなくなります。 


## <a name="retirement-policy-exceptions"></a>提供終了ポリシーの例外

上記の提供終了ポリシーには、例外がいくつかあります。 ここでは、言語の有効期限の終了日が近い、または終了日に達した言語のうち、さらに通知が届くまでは、プラットフォーム上でのサポートが継続されるものを紹介します。 これらの言語バージョンの更新や修正プログラムの適用については、有効期限の終了日に達した時点で行われません。 この理由から、これらの言語バージョン上で関数アプリを開発して実行することはお勧めしません。

|言語バージョン                        |EOL 日         |提供終了予定日|
|-----------------------------------------|-----------------|----------------|
|.NET 5|2022 年 2 月|TBA|
|Node 6|2019 年 4 月 30 日|TBA| 
|ノード 8|2019 年 12 月 31 日|TBA| 
|ノード 10|2021 年 4 月 30 日|TBA| 
|PowerShell Core 6| 2020 年 9 月 4 日|TBA|
|Python 3.6 |2021 年 12 月 23 日|TBA| 
 

## <a name="language-version-support-timeline"></a>言語バージョン サポートのタイムライン

言語バージョンのサポート ポリシーの具体的なタイムラインの詳細については、次の外部リソースを参照してください。
* .NET - [dotnet.microsoft.com](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)
* Node - [github.com](https://github.com/nodejs/Release#release-schedule)
* Java - [azul.com](https://www.azul.com/products/azul-support-roadmap/)
* PowerShell - [dotnet.microsoft.com](/powershell/scripting/powershell-support-lifecycle?view=powershell-7.1&preserve-view=true#powershell-releases-end-of-life)
* Python - [devguide.python.org](https://devguide.python.org/#status-of-python-branches)

## <a name="configuring-language-versions"></a>言語バージョンの構成

|言語                         | 構成ガイド         |
|-----------------------------------------|-----------------|
|C# (クラス ライブラリ) |([リンク](./functions-dotnet-class-library.md#supported-versions))|
|ノード |([リンク](./functions-reference-node.md#setting-the-node-version))|
|PowerShell |([リンク](./functions-reference-powershell.md#changing-the-powershell-version))|
|Python |([リンク](./functions-reference-python.md#python-version))|
 

## <a name="next-steps"></a>次のステップ

お使いの関数アプリの言語バージョンをアップグレードする方法の詳細については、次のリソースを参照してください。


+ [現在サポートされている言語バージョン](./supported-languages.md#languages-by-runtime-version)
