---
title: UI モバイル ライブラリのユース ケースのシナリオ
titleSuffix: An Azure Communication Services - UI Mobile Library use cases scenarios
description: このドキュメントでは、UI モバイル ライブラリの機能とアプリケーションでの動作について説明します
author: jorgegarc
ms.author: jorgegarc
ms.date: 09/14/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: cc849bb98faea5cbf7f3ec7828cc8318061dc5a5
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2021
ms.locfileid: "130181529"
---
# <a name="ui-library-ios-and-android-capabilities"></a>UI ライブラリ (iOS および Android) の機能

[!INCLUDE [Public Preview Notice](../../includes/private-preview-include.md)]

iOS および Android 用 UI ライブラリでは、**通話の複合** を使用した通話のユース ケースがサポートされています。
複合を使用すると、開発者はわずか数行のコードを使って、通話エクスペリエンス全体をアプリケーションに簡単に統合できます。複合によって、セットアップから通話終了までの通話のライフサイクル全体が管理されます。

## <a name="calling"></a>呼び出し

| 領域                                                                                            | ユース ケース                                              |
| ----------------------------------------------------------------------------------------------- | ------------------------------------------------------ |
| 呼び出しの種類                                                                                      | Teams の会議に参加する                                     |
|                                                                                                 | グループ ID を使用して Azure Communication Services の通話に参加する   |
| [Teams との相互運用](../../concepts/teams-interop.md) | 通話ロビー                                             |
|                                                                                                 | 文字起こしとレコーディングのアラート バナー               |
| 参加者ギャラリー                                                                             | リモート参加者がグリッド状に表示される              |
|                                                                                                 | ローカル ユーザーの通話を通して利用可能なビデオ プレビュー |
|                                                                                                 | ビデオがオフのときに使用できる既定のアバター            |
|                                                                                                 | 参加者ギャラリーに表示される共有画面コンテンツ |
|                                                                                     | 参加者一覧                                     |
| 呼び出しの構成                                                                              | マイク デバイスの管理                           |
|                                                                                                 | カメラ デバイスの管理                               |
|                                                                                                 | スピーカー デバイスの管理                              |
|                                                                                                 | ユーザーがビデオを確認するために使用できるローカル プレビュー        |
| 呼び出しコントロール                                                                                   | 通話をミュートまたはミュート解除する                                       |
|                                                                                                 | 通話時のビデオのオンまたはオフ                                   |
|                                                                                                 | 通話の終了                                               |

## <a name="supported-identities"></a>サポートされている ID

複合を初期化し、サービスに対して認証を行うには、Azure Communication Services ID が必要です。
認証の詳細については、[認証](../authentication.md)と[アクセス トークン](../../quickstarts/access-tokens.md)に関するページを参照してください。

## <a name="teams-interop"></a>Teams との相互運用

![通話とチャットのための Teams との相互運用パターン](../media/mobile-ui/teams-interop-diagram.png)

[Teams との相互運用](../teams-interop.md)のシナリオでは、開発者は UI モバイル ライブラリ コンポーネントを使用して、Azure Communication Services を通じて Teams ミーティングに参加することができます。
Teams との相互運用を有効にするために、開発者は通話の複合を使用できます。これによって、Teams との相互運用の通話に参加するライフサイクルが管理されます。

:::image type="content" source="../media/mobile-ui/teams-meet.png" alt-text="会議前エクスペリエンス":::

## <a name="theming"></a>テーマ

iOS および Android 用の UI ライブラリ通話複合には、プライマリ カラーを渡すことによってエクスペリエンスのテーマを変更する開発者向けのインターフェイスが用意されています。 そのプライマリ カラーが複合で使用され、エクスペリエンス全体に適切なテーマが提供されます。

| Android                            | iOS                                     |
| -------------------------------------------------------- | --------------------------------------------------------------- |
| :::image type="content" source="../media/mobile-ui/android-color.png" alt-text="android のテーマ"::: | :::image type="content" source="../media/mobile-ui/ios-dark.png" alt-text="iOS のテーマ":::  |


## <a name="screen-size"></a>画面のサイズ

通話の複合により、任意の画面サイズに適応できるようになり、5 インチの画面からタブレットまでのサポートが提供されます。また、動的な参加者の参加者一覧レイアウトを取得し、ビューを簡潔にし、会話に集中できるようになります。

|分割モード | タブレット モード|
|---------|---------|
| :::image type="content" source="../media/mobile-ui/meet-splitscreen.png" alt-text="分割画面"::: |  :::image type="content" source="../media/mobile-ui/tablet-landscape.png" alt-text="タブレット モード"::: |

## <a name="recommended-architecture"></a>推奨アーキテクチャ

複合は、Azure Communication Services のアクセス トークンを使用して初期化されます。 アクセス トークンは、管理対象の信頼できるサービスを介して Azure Communication Services から調達する必要があります。 「[クイック スタート:詳細については、アクセストークンの作成に関するクイックスタート](../../quickstarts/access-tokens.md)と[信頼できるサービスのチュートリアル](../../tutorials/trusted-service-tutorial.md)に関するページを参照してください。

:::image type="content" source="../media/mobile-ui/ui-library-architecture.png" alt-text="推奨アーキテクチャの図":::

これらのクライアント ライブラリでは、参加する通話のコンテキストも必要になります。 ユーザー アクセス トークンと同様、このコンテキストも信頼できる独自サービスを介してクライアントに配布する必要があります。 次の一覧は、運用化する必要がある初期化機能とリソース管理機能をまとめたものです。

| Contoso の役割                                 | UI ライブラリの役割                                     |
| -------------------------------------------------------- | --------------------------------------------------------------- |
| Azure からアクセス トークンを提供する                          | 与えられたアクセス トークンをパススルーしてコンポーネントを初期化する        |
| 更新機能を提供する                                 | 開発者から提供された機能を使用してアクセス トークンを更新する          |
| 通話またはチャットの参加情報を取得する、または渡す          | 通話とチャットの情報をパススルーしてコンポーネントを初期化する |
| 任意のカスタム データ モデルのユーザー情報を取得する、または渡す | レンダリングの対象となるコンポーネントにカスタム データ モデルをパススルーする          |

## <a name="platform-support"></a>プラットフォームのサポート

|プラットフォーム | バージョン|
|---------|---------|
| iOS     | iOS 13 以降 |
| Android | v23 以降    |

## <a name="accessibility"></a>ユーザー補助

- 設計によるアクセシビリティは、Microsoft 製品全体の原則です。
- UI ライブラリは、すべての UI コンポーネントに完全にアクセスできるようにするために、この原則に従っています。
- パブリック プレビュー期間中、UI ライブラリは引き続き改善され、ユーザー補助機能が UI コンポーネントに追加されます。

## <a name="localization"></a>ローカリゼーション

- ローカライズは、世界中で、異なる言語を話す人々が使用できる製品を作るための重要な鍵となります。
- UI ライブラリでは、一部の言語や RTL などの機能に対して、すぐに使用できるサポートが提供されます。
- 開発者は、UI ライブラリで使用する独自のローカライズ ファイルを提供することがでます。

> [!div class="nextstepaction"]

UI モバイル ライブラリ複合の使用を開始する方法の詳細については、[クイック スタート ガイド](../../quickstarts/ui-library/get-started-call.md)に従ってください。