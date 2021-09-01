---
title: UI ライブラリの概要
titleSuffix: An Azure Communication Services concept document
description: Azure Communication Services UI ライブラリについて説明します。
author: ddematheu2
manager: chrispalm
services: azure-communication-services
ms.author: dademath
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 86ef660c5d4b0f0132218df1df37c8934f27ec08
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/30/2021
ms.locfileid: "122653607"
---
# <a name="ui-library-overview"></a>UI ライブラリの概要

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

> [!NOTE]
> UI ライブラリの詳細なドキュメントについては、[UI ライブラリのストーリーブック](https://azure.github.io/communication-ui-library)を参照してください。 そこには、その他の概念説明のドキュメント、クイックスタート、例が記載されています。

Azure Communication Services - UI ライブラリを使用すると、Azure Communication Services を使用して、最新のコミュニケーション用ユーザー エクスペリエンスを簡単に構築できます。 運用環境ですぐに使用できる UI コンポーネントのライブラリが用意されており、アプリケーションに手軽にドロップできます。

- **複合** - これらのコンポーネントは、一般的な通信シナリオを実装するためのターンキー ソリューションです。
  アプリケーションにビデオ通話やチャットのエクスペリエンスをすばやく追加できます。
  複合は、UI コンポーネントを使用して構築されたオープン ソースの上位のコンポーネントです。

- **UI コンポーネント** - これらのコンポーネントは、カスタム通信エクスペリエンスを構築できる、オープンソースの構成要素です。
  コンポーネントは、エクスペリエンスの構成のために組み合わせることのできる通話とチャットの機能の両方を対象に提供されます。

これらの UI クライアント ライブラリはすべて、[Microsoft の Fluent 設計言語](https://developer.microsoft.com/fluentui/)と資産を使用しています。 Fluent UI とは、Microsoft 製品全体にわたって積極的に使用されている UI ライブラリの基本レイヤーです。

UI コンポーネントと連携し、UI ライブラリは通話とチャット用のステートフル クライアント ライブラリを公開します。
このクライアントは、特定の状態管理フレームワークに依存しないため、Redux や React Context などの一般的な状態管理ツールと統合することができます。
このステートフル クライアント ライブラリを UI コンポーネントと一緒に使用して、UI コンポーネントでデータをレンダリングするためのプロップとメソッドを渡します。 詳細については、[ステートフル クライアントの概要](https://azure.github.io/communication-ui-library/?path=/story/stateful-client-what-is-stateful--page)に関するページを参照してください。

## <a name="installing-ui-library"></a>UI ライブラリのインストール

ステートフル クライアントは、`@azure/communication-react` パッケージの一部として含まれています。 

```bash
npm i --save @azure/communication-react
```

## <a name="composites-overview"></a>複合の概要

複合は、UI コンポーネントで構成される上位レベルのコンポーネントです。このコンポーネントは、Azure Communication Services を使用して一般的な通信シナリオを実装するためのターンキー ソリューションです。
開発者は、Azure Communication Services のアクセス トークンと、通話またはチャットに必要な構成を使用して、複合を簡単にインスタンス化できます。

| Composite    | ユース ケース  | 
| ------------ | ---------- |
| [CallComposite](https://azure.github.io/communication-ui-library/?path=/story/composites-call--basic-example) | ユーザーが通話を開始したり、参加したりすることができる通話エクスペリエンス。 このエクスペリエンスにおいて、ユーザーは自分のデバイスを構成したり、ビデオ通話に参加して、他の参加者 (ビデオがオンの参加者も含む) を見ることができます。 Teams との相互運用には、許可待ちのユーザーのためのロビー機能が含まれています。 |
| [ChatComposite](https://azure.github.io/communication-ui-library/?path=/story/composites-chat--basic-example)    | ユーザーがメッセージを送受信できるチャット エクスペリエンス。 入力、読み取り、参加者の入退出などのスレッド イベントが、チャット スレッドの一部としてユーザーに表示されます。                                                                                                                          |

## <a name="ui-component-overview"></a>UI コンポーネントの概要

開発者がコミュニケーション エクスペリエンスを構築するために使用できる純粋な UI コンポーネントで、これにはリモートの参加者を一覧表示するためにビデオのタイルをグリッド状に表示したり、アプリケーションの仕様に合わせてコンポーネントを整理するなどが含まれます。
UI コンポーネントを使用すると、アプリケーションのブランディングやスタイルに合わせて、コンポーネントの外観をカスタマイズすることができます。

| 領域    | コンポーネント    | 説明       |
| ------- | ------------ | ----------------- |
| 呼び出し | [Grid レイアウト](https://azure.github.io/communication-ui-library/?path=/story/ui-components-gridlayout--grid-layout)                | ビデオ タイルを NxN のグリッドに整理するためのグリッド コンポーネント                                            |
|         | [ビデオ タイル](https://azure.github.io/communication-ui-library/?path=/story/ui-components-videotile--video-tile)                   | 使用可能な場合はビデオ ストリームを表示し、ない場合は既定の静的コンポーネントを表示するコンポーネント        |
|         | [コントロール バー](https://azure.github.io/communication-ui-library/?path=/story/ui-components-controlbar--control-bar)                | ミュートや共有画面など、特定の呼び出しアクションに接続するための DefaultButtons を整理するためのコンテナー |
|         | [VideoGallery](https://azure.github.io/communication-ui-library/?path=/story/ui-components-video-gallery--video-gallery)                                           | 参加者が増える度に動的に変更されるターンキーのビデオ ギャラリー コンポーネント               |
| チャット    | [メッセージ スレッド](https://azure.github.io/communication-ui-library/?path=/story/ui-components-messagethread--message-thread)       | チャット メッセージ、システム メッセージ、カスタム メッセージを表示するコンテナー                          |
|         | [送信ボックス](https://azure.github.io/communication-ui-library/?path=/story/ui-components-sendbox--send-box)                         | 不連続な [送信] ボタンを持つテキスト入力コンポーネント                                                   |
|         | [メッセージの状態インジケーター](https://azure.github.io/communication-ui-library/?path=/story/ui-components-messagestatusindicator--message-status-indicator)        | 送信されたメッセージの状態を表示するマルチステート読み取りの確認メッセージ コンポーネント                                   |
|         | [入力インジケーター](https://azure.github.io/communication-ui-library/?path=/story/ui-components-typingindicator--typing-indicator) | スレッドで現在入力中の参加者を表示するテキスト コンポーネント                      |
| 共通  | [参加者の項目](https://azure.github.io/communication-ui-library/?path=/story/ui-components-participantitem--participant-item) | アバターと表示名を含む、通話またはチャット参加者を表示する共通コンポーネント            |
|         | [参加者リスト](https://azure.github.io/communication-ui-library/?path=/story/ui-components-participantlist--participant-list)                                 | アバターと表示名を含む、通話またはチャット参加者の一覧を表示する共通コンポーネント       |

## <a name="what-ui-artifact-is-best-for-my-project"></a>自分のプロジェクトに最適な UI アーティファクトとは

次の要件を把握することで、適切なクライアント ライブラリを選択するのに役立ちます。

- **どの程度のカスタマイズが必要か。** Azure Communication コア クライアント ライブラリには UX が存在しておらず、任意の UX を構築できるように設計されています。 UI ライブラリのコンポーネントでは、カスタマイズの自由度は低くなりますが、UI 資産が提供されます。
- **対象とするプラットフォームは何か。** プラットフォームによって、機能は異なります。

[UI ライブラリで利用できる機能の詳細についてはこちら](https://azure.github.io/communication-ui-library/?path=/story/use-cases--page)をご覧ください。ただし、主なトレードオフについては以下にまとめます。

| クライアント ライブラリまたは SDK  | 実装の複雑さ | カスタマイズ性 | 呼び出し | チャット | [Teams との相互運用](../teams-interop.md) |
| --------------------- | ------------------------- | --------------------- | ------- | ---- | ----------------------------------------------------------------------------------------------------- |
| 複合コンポーネント  | 低                       | 低                   | ✔       | ✔    | ✔                                                                                                     |
| 基本コンポーネント       | Medium                    | Medium                | ✔       | ✔    | ✔                                                                                                     |
| コア クライアント ライブラリ | 高                      | 高                  | ✔       | ✔    | ✔                                                                                                     |

> [!div class="nextstepaction"]
> [UI Library Storybook にアクセス](https://azure.github.io/communication-ui-library)
