---
title: UI モバイル ライブラリ
titleSuffix: An Azure Communication Services - UI Mobile Library
description: このドキュメントでは、UI モバイル ライブラリを紹介します
author: jorgegarc
ms.author: jorgegarc
ms.date: 09/14/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: fdf84a2eb2cc57081a315bbf9dc41c14ee96ae8e
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2021
ms.locfileid: "130181523"
---
# <a name="ui-mobile-library"></a>UI モバイル ライブラリ

[!INCLUDE [Public Preview Notice](../../includes/private-preview-include.md)]

Microsoft の UI モバイル ライブラリは、企業と消費者間および企業間の一般的な通話のやり取りのための UI コンポーネントを提供することに焦点を絞った、Azure Communication Services の機能です。 UI モバイル ライブラリの中心となるのは、[ビデオおよび音声通話](../voice-video-calling/calling-sdk-features.md)用のコンポーネントです。 これは、Azure の通話プリミティブを基に、モバイルでの通話および会議のエクスペリエンスのための包括的なユーザー エクスペリエンスを提供するために構築されています。

UI モバイル ライブラリの目的は、ターンキーの複合形式でこれらの機能を提供することです。 SDK をお気に入りのモバイル開発アプリのキャンバスにドロップすると、SDK によって包括的なユーザー エクスペリエンスが生成されます。 このユーザー エクスペリエンスは軽いので、開発時間の短縮と、エンジニアリングの複雑さの軽減という利点を得られます。

## <a name="composites"></a>合成

複合は、より小さいコンポーネントで構成される上位レベルのコンポーネントです。このコンポーネントは、Azure Communication Services を使用して一般的な通信シナリオを実装するためのターンキー ソリューションです。

開発者は、Azure Communication Services のアクセス トークンと、通話に必要な構成属性を使用して、複合を簡単にインスタンス化できます。

| Composite                                                                   | ユース ケース                                                                                                                                                                                                                                                                                                  |
| --------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| [CallComposite](../../quickstarts/ui-library/get-started-call.md)  | ユーザーが通話を開始したり、参加したりすることができる通話エクスペリエンス。 このエクスペリエンスにおいて、ユーザーは自分のデバイスを構成したり、ビデオ通話に参加して、他の参加者 (ビデオがオンの参加者も含む) を見ることができます。 Teams との相互運用のために、ユーザーが参加を待機できるように、ロビー機能が含まれています。 |

## <a name="scenarios"></a>シナリオ

### <a name="joining-a-videoaudio-call"></a>ビデオまたは音声通話に参加する

ユーザーは、"*Teams 会議の URL*" を使用して通話に簡単に参加できます。または、Teams アプリケーションと同様に、Azure Communication Services の通話をよりシンプルで優れたエクスペリエンスに設定できます。 ユーザーが広範なライブ ビデオまたは音声通話に参加できる機能を追加することで、シンプルなエクスペリエンスを失うことなく、本当に重要な事柄に集中できます。

### <a name="pre-call-experience"></a>通話前エクスペリエンス

通話の参加者は、名前を指定し、オーディオとビデオのデバイスの既定の構成を設定して、通話にすぐに参加できます。

:::image type="content" source="../media/mobile-ui/teams-meet.png" alt-text="会議前エクスペリエンス。":::

### <a name="call-experience"></a>通話エクスペリエンス

通話の複合は、開発時間を最適化し、クリーンなレイアウトに集中するエンド ツー エンドのエクスペリエンスを提供します。  

:::image type="content" source="../media/mobile-ui/calling-composite.png" alt-text="会議エクスペリエンス。":::

**通話エクスペリエンスでは、これらすべての機能が 1 つの複合コンポーネントで提供され、複雑なコードなしで明確なパスが提供されるため、開発時間の短縮につながります。**

### <a name="quality-and-security"></a>品質とセキュリティ

モバイル複合は、[Azure Communication Services のアクセス トークン](../../quickstarts/access-tokens.md)を使用して初期化されます。

### <a name="more-details"></a>詳細

モバイル複合の詳細については、[ユース ケース サイト](mobile-ui-use-cases.md)を参照してください。

## <a name="what-ui-artifact-is-best-for-my-project"></a>自分のプロジェクトに最適な UI 成果物とは

次の要件を把握することで、適切なクライアント ライブラリを選択するのに役立ちます。

- **どの程度のカスタマイズが必要か。** Azure Communication コア クライアント ライブラリには UX が存在しておらず、任意の UX を構築できるように設計されています。 UI ライブラリのコンポーネントでは、カスタマイズの自由度は低くなりますが、UI 資産が提供されます。

- **対象とするプラットフォームは何か。** プラットフォームによって、機能は異なります。

UI ライブラリで利用できる機能の詳細については[こちら](mobile-ui-use-cases.md)をご覧ください。ただし、主なトレードオフについては以下にまとめます。

| クライアント ライブラリまたは SDK  | 実装の複雑さ | カスタマイズ性 | 呼び出し |  [Teams との相互運用](../../concepts/teams-interop.md) |
| --------------------- | ------------------------- | --------------------- |  ---- | ----------------------------------------------------------------------------------------------- |
| 複合コンポーネント  | 低                       | 低                   |         ✔    | ✔                                                                                               |
| コア クライアント ライブラリ | 高                      | 高                  |         ✔    | ✔                                                                                               |

> [!div class="nextstepaction"]

UI モバイル ライブラリ複合の使用を開始する方法の詳細については、[クイック スタート ガイド](../../quickstarts/ui-library/get-started-call.md)に従ってください。