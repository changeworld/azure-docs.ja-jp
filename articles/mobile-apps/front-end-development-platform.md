---
title: Visual Studio と Azure サービスを使用してクライアント アプリケーションを構築するためのフロントエンド開発プラットフォームを選択する
description: クライアント アプリケーションを構築するための、サポートされているネイティブおよびクロス プラットフォームの言語について説明します。
author: elamalani
manager: elamalani
ms.service: vs-appcenter
ms.assetid: 355f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: c55c9c36d84c8545f4192e1c9a65d8af0c580396
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453152"
---
# <a name="choose-mobile-development-frameworks"></a>モバイル開発フレームワークの選択
開発者は、クロスプラットフォームの手法における特定のフレームワークとパターンを利用し、クライアント側の技術を使ってモバイル アプリケーション自体を構築できます。 開発者は、決定要因に基づいて以下を構築できます。
- Objective C や Java などの言語を使用したネイティブ シングルプラットフォーム アプリケーション
- Xamarin、.NET、および C# を使用したクロスプラットフォームアプリケーション
- Cordova とそのバリエーションを使用したハイブリッド アプリケーション

## <a name="native-platforms"></a>ネイティブ プラットフォーム
ネイティブ アプリケーションを構築するには、プラットフォーム固有のプログラミング言語、SDK、開発環境、および OS ベンダーが提供するその他のツールが必要になります。

### <a name="ios"></a>iOS
Apple で作成および開発された iOS は、Apple デバイス (iPhone と iPad) でアプリを構築するために使用されます。

- **プログラミング言語**:Objective-C、Swift
- **IDE**: Xcode
- **SDK**: iOS SDK

### <a name="android"></a>Android
Google によって設計された、世界で最も普及している OS。Android は、さまざまなスマートフォンやタブレット上で実行できるアプリケーションを構築するために使用されます。

- **プログラミング言語**:Java、Kotlin 
- **IDE**: Android Studio および Android 開発者ツール 
- **SDK**: Android SDK

### <a name="windows"></a>Windows
- **プログラミング言語**:C#
- **IDE**: Visual Studio、Visual Studio Code
- **SDK**: Windows SDK

#### <a name="pros"></a>長所
- 優れたユーザー エクスペリエンス
- ハイ パフォーマンスかつネイティブ ライブラリとのインターフェイスを実現する機能を備えた、応答性の高いアプリケーション
- 高度にセキュリティ保護されたアプリケーション

#### <a name="cons"></a>短所
- アプリケーションが 1 つのプラットフォーム上でしか実行されない
- アプリケーションの構築のための開発者リソースの使用が増え、コストが高くなる
- コードの再利用性が低い

## <a name="cross-platforms-and-hybrid-applications"></a>クロス プラットフォームとハイブリッド アプリケーション
クロス プラットフォーム アプリケーションを使用すると、ネイティブ モバイル アプリケーションを 1 回作成し、コードを共有して、iOS、Android、Windows 上で実行することができます。

### <a name="xamarin"></a>Xamarin
Microsoft が所有している [Xamarin](https://visualstudio.microsoft.com/xamarin/) は、C# で堅牢なクロスプラットフォーム モバイル アプリケーションを構築するために使用されます。 Xamarin には、iOS、Android、Windows などの多くのプラットフォームで動作するクラス ライブラリとランタイムがあります。 また、ハイ パフォーマンスを提供するネイティブ (解釈されていない) アプリケーションもコンパイルします。 Xamarin では、ネイティブ プラットフォームのすべての機能を統合して、独自の優れた機能を多数追加します。

- **プログラミング言語**:C#
- **IDE**: Windows または Mac 上での Visual Studio

### <a name="react-native"></a>React Native
2015 年に Facebook からリリースされた [React Native](https://facebook.github.io/react-native/) は、実践的な記述のための[オープン ソース](https://github.com/facebook/react-native) JavaScript フレームワークであり、iOS および Android 対応のモバイル アプリケーションをネイティブにレンダリングします。 これは、ユーザー インターフェイスを構築するための Facebook の JavaScript ライブラリである React を基にしています。 ブラウザーを対象とする代わりに、モバイル プラットフォームを対象としています。 React Native では構築ブロックとして、Web コンポーネントではなく、ネイティブ コンポーネントを使用します。
 
- **プログラミング言語**:JavaScript
- **IDE**: Visual Studio Code

### <a name="unity"></a>Unity
 Unity は、ゲームを作成するために最適化されたエンジンです。 これを使用して、Windows、iOS、Android、Xbox などのプラットフォーム用に C# を使用して高品質な 2D または 3D アプリケーションを作成できます。

### <a name="cordova"></a>Cordova
Cordova では、Cordova の拡張機能によって、Visual Studio Tools for Apache Cordova または Visual Studio Code を使用したハイブリッド アプリケーションの構築が可能になります。 ハイブリッドの手法を使用すれば、コンポーネントを Web サイトと共有したり、Cordova に基づくホステッド Web アプリケーションの手法を利用して Web サーバーベースのアプリケーションを再利用したりできます。

#### <a name="pros"></a>長所
- 複数のプラットフォーム用に 1 つのコードベースを作成して、コードのユーザビリティを向上させる
- 多くのプラットフォームにわたるより広範な対象ユーザーに対応する
- 開発時間の大幅短縮
- 起動と更新が容易になる

#### <a name="cons"></a>短所
- パフォーマンスが低い
- 柔軟性に欠ける
- 各プラットフォームに、ネイティブ アプリケーションをさらにクリエイティブなものにするための一意の機能セットが備わっている
- UI の設計時間が増大する
- ツールの制限
