---
title: Teams Embed SDK
titleSuffix: An Azure Communication Services - Teams Embed SDK description
description: このドキュメントでは、Teams Embed の機能と、アプリケーションでの動作について確認します
author: tophpalmer
ms.author: chpalm
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 14a063bc6b274c0c93ff700648e670f1c740730d
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2021
ms.locfileid: "114401029"
---
# <a name="teams-embed"></a>Teams Embed

[!INCLUDE [Public Preview Notice](../../includes/private-preview-include.md)]

Teams Embed は、一般的な企業-消費者間と企業間の呼び出しのやり取りに焦点を絞った、Azure Communication Services の機能です。 Teams Embed システムの中核となるのは[ビデオと音声呼び出し](../voice-video-calling/calling-sdk-features.md)であり、これは Azure の呼び出しプリミティブを基に構築され、Microsoft Teams の会議に基づく包括的なユーザー エクスペリエンスを提供します。

Teams Embed SDK はクローズドソースであり、これらの機能をターンキーの複合形式で使用できるようにします。 Teams Embed をアプリのキャンバスにドロップすると、SDK によって包括的なユーザー エクスペリエンスが生成されます。 このユーザー エクスペリエンスは Microsoft Teams の会議とよく似ているため、以下を活用できます。

- 開発時間とエンジニアリングの複雑さの軽減。
- Teams エクスペリエンスに関するエンド ユーザーの知識。
- [Teams のエンドユーザーのトレーニング コンテンツ](https://support.microsoft.com/office/meetings-in-teams-e0b0ae21-53ee-4462-a50d-ca9b9e217b67)を再利用できること。

以下を含め、Teams の会議でサポートされるほとんどの機能は Teams Embed SDK によって提供されています。

## <a name="joining-a-meeting"></a>ミーティングに参加する

ユーザーは、Teams アプリケーションと同様に、Teams 会議の URL を使用して会議に簡単に参加して、よりシンプルで優れたエクスペリエンスを利用できます。 Teams アプリケーションのシンプルなエクスペリエンスを失わずに、ユーザーが広範なライブ会議に参加できる機能を追加します。

## <a name="pre-meeting-experience"></a>会議前エクスペリエンス

どの会議の参加者としても、オーディオおよびビデオ デバイスの既定の構成を設定できます。 自分の名前を追加し、自分の画像アバターを取り込みます。

## <a name="meeting-experience"></a>会議エクスペリエンス

ユーザー エクスペリエンスをカスタマイズし、ニーズに応じて機能を調整します。 会議中の全体的なエクスペリエンスを制御します。

- [**コンテンツ共有**](https://support.microsoft.com/office/share-content-in-a-meeting-in-teams-fcc2bf59-aecd-4481-8f99-ce55dd836ce8): ユーザーはビデオ、写真、または画面全体を共有できます。ユーザーには共有コンテンツが表示されます。

- [**ビデオ ギャラリーの複数のレイアウト オプション**](https://support.microsoft.com/office/using-video-in-microsoft-teams-3647fc29-7b92-4c26-8c2d-8a596904cdae): 会議中にレイアウトの既定のオプション (大型ギャラリー、集合モード、フォーカス、ピン留め、スポットライト) を選択する機能を利用できます。 また、デバイスの解像度に応じてレイアウトを調整します。

- [**ビデオのオン/オフ**](https://support.microsoft.com/office/using-video-in-microsoft-teams-3647fc29-7b92-4c26-8c2d-8a596904cdae#bkmk_turnvideoonoff): 会議中にユーザーがビデオを管理できます。

- **参加者のアクション**: ユーザーは、["挙手"](https://support.microsoft.com/en-us/office/raise-your-hand-in-a-teams-meeting-bb2dd8e1-e6bd-43a6-85cf-30822667b372)、マイクのミュートとミュート解除、カメラまたはオーディオの構成の変更、切断、その他の多くのアクションを実行できます。

- [**多言語サポート**](https://support.microsoft.com/topic/languages-supported-in-microsoft-teams-for-education-293792c3-352e-4b24-9fc2-4c28b5de2db8): チーム エクスペリエンス全体で 56 の言語がサポートされます。

## <a name="quality-and-security"></a>品質とセキュリティ

Teams Embed SDK は Teams 品質標準の下に構築されているので、ビデオ品質について[帯域幅の要件](/microsoftteams/prepare-network#bandwidth-requirements)があります。

Azure Communication Service アクセス トークンを使用できます。詳細については、[アクセス トークンを生成および管理する方法](../../quickstarts/access-tokens.md)に関するページを参照してください。

## <a name="capabilities"></a>機能

| SDK の機能                                                        | 可用性 |
|---------------------------------------------------------------------|--------------|
| *会議アクション*                                                   |              |
| 通話への参加                                                         | はい          |
| "*通話状態*" (接続状態、参加者の数、マイクやカメラの状態などのモダリティが含まれます) の処理                                           | はい          |
| ユーザー イベント「手を挙げました」、「ミュート状態」、および「ビデオの送信」イベントの生成                                                                 | はい          |
| 不安定なネットワークの処理のサポート                                      | はい          |
| 会議からの参加者の削除                                    | はい          |
| 56 言語のサポート                                               | はい          |
| 会議中のチャットと 1n1 チャット                                    | いいえ           |
| PSTN 通話                                                        | いいえ           |
| 記録とトランスクリプト                                            | いいえ           |
| ホワイトボードの共有                                                  | いいえ           |
| ルームへの参入                                                 | いいえ           |
| *会議参加エクスペリエンス*                                        |              |
| GUID と ACS トークンを使用したグループ通話への参加                             | はい          |
| ライブ会議 URL と ACS トークンを使用した会議への参加                    | はい          |
| 会議 URL と ACS トークンを使用した会議への参加                         | はい          |
| ロビーでの待機を介した参加                                           | はい          |

| ユーザー エクスペリエンスのカスタマイズ                                       | 可用性 |
|---------------------------------------------------------------------|--------------|
| *会議アクション*                                                   |              |
| 呼び出し名簿の表示                                             | はい          |
| レイアウトのカスタマイズ: 色、アイコン、ボタン                        | 部分的    |
| 呼び出し画面アイコンのカスタマイズ                                     | はい          |
| 会議ビューのレイアウトの変更                                         | はい          |
| 動的呼び出し NxN レイアウトの変更                                    | はい          |
| 手の上げ下ろし                                                     | はい          |
| ミュート/ミュート解除                                                        | はい          |
| 保留                                                         | はい          |
| オーディオ ルーティングの選択                                                | はい          |
| カメラの選択                                                       | はい          |
| 写真、画面、ビデオの共有                                       | はい          |
| ビデオの開始/停止                                                    | はい          |
| ユーザー タイルの押下イベント                                               | はい          |
| ネーム プレートの押下イベント                                              | はい          |
| 背景ぼかし                                                     | はい          |
| 画面の背景色のカスタマイズ                               | いいえ           |
| 上部/下部のバーの色のカスタマイズ                                  | いいえ           |
| ホワイトボードの共有                                                  | いいえ           |
| *会議前エクスペリエンス*                                            |              |
| 参加の表示名を構成し、写真の共有を有効にできる            | はい          |
| 参加で通話ステージング ビューを表示するように構成できる (呼び出し前の画面)   | はい          |
| 参加で呼び出し画面にネーム プレートを表示するように構成できる             | はい          |
| ロビー画面のカスタマイズ                                          | 部分的    |

Teams Embed SDK の使用を開始する方法の詳細については、[ファースト ステップ ガイド](../../quickstarts/meeting/getting-started-with-teams-embed.md)に従ってください。 SDK 機能の詳細については、[サンプル ガイド](../../quickstarts/meeting/samples-for-teams-embed.md)を参照してください。