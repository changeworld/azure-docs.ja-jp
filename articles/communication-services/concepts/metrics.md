---
title: Azure Communication Services のメトリックの定義
titleSuffix: An Azure Communication Services concept document
description: このドキュメントでは、Azure portal で使用できるメトリックの定義について説明します。
author: probableprime
manager: chpalm
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: data
ms.openlocfilehash: b97c7ead1c10573dccaf260e7ab1225872d2e898
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128679884"
---
# <a name="metrics-overview"></a>メトリックの概要

Azure Communication Services では現在、チャット用と SMS 用のメトリックが提供されています。 [Azure メトリックス エクスプローラー](../../azure-monitor/essentials/metrics-getting-started.md)では、チャットと SMS の要求から生成されるメトリック データを使用することで、独自のグラフをプロットしたり、メトリック値の異常を調査したり、API トラフィックを把握したりすることができます。

## <a name="where-to-find-metrics"></a>メトリックを確認できる場所

Azure Communication Services のチャットおよび SMS サービスでは、API 要求のメトリックを生成します。 これらのメトリックは、Communication Services リソースの [メトリック] ブレードで確認できます。 Communication Services リソースのブック ブレードを使用して永続的なダッシュボードを作成することもできます。

## <a name="metric-definitions"></a>メトリック定義

Communication Services のメトリック内で表現される要求には、**チャット API 要求** と **SMS API 要求** の 2 種類があります。

チャットと SMS 両方の API 要求のメトリックには、メトリック データをフィルター処理するために使用できる 3 つのディメンションが含まれています。 これらのディメンションは、`Count` という集計の種類を使用することでまとめて集計できます。また、Azure の標準的な集計時系列 (`Sum`、`Average`、`Min`、`Max` など) をサポートしています。

サポートされる集計の種類と時系列集計について詳しくは、「[Azure メトリックス エクスプローラーの高度な機能](../../azure-monitor/essentials/metrics-charts.md#aggregation)」を参照してください。

- **操作** - ACS チャット ゲートウェイで呼び出すことができるあらゆる操作またはルート。
- **状態コード** - 要求の後に送信される状態コードの応答。
- **StatusSubClass** - 応答の後に送信される一連の状態コード。 


### <a name="chat-api-request-metric-operations"></a>チャット API 要求のメトリック操作

チャット API 要求のメトリックには、次の操作が利用できます。

| 操作またはルート    | 説明                                                                                    |
| -------------------- | ---------------------------------------------------------------------------------------------- |
| GetChatMessage       | メッセージ ID を使用してメッセージを取得します。 |
| ListChatMessages     | スレッドからチャット メッセージの一覧を取得します。 |
| SendChatMessage      | スレッドにチャット メッセージを送信します。 |
| UpdateChatMessage    | チャット メッセージを更新します。 |
| DeleteChatMessage    | チャット メッセージを削除します。 |
| GetChatThread        | チャット スレッドを取得します。 |
| ListChatThreads      | ユーザーのチャット スレッドの一覧を取得します。 |
| UpdateChatThread     | チャット スレッドのプロパティを更新します。 |
| CreateChatThread     | チャット スレッドを作成します。 |
| DeleteChatThread     | スレッドを削除します。 |
| GetReadReceipts      | スレッドの開封確認メッセージを取得します。 |
| SendReadReceipt      | ユーザーに代わって開封確認メッセージ イベントをスレッドに送信します。 |
| SendTypingIndicator           | ユーザーに代わって入力イベントをスレッドにポストします。 |
| ListChatThreadParticipants    | スレッドのメンバーを取得します。 |
| AddChatThreadParticipants     | スレッドにスレッド メンバーを追加します。 既にメンバーが存在する場合は、何も変化しません。 |
| RemoveChatThreadParticipant   | スレッドからメンバーを削除します。 |

:::image type="content" source="./media/chat-metric.png" alt-text="チャット API 要求のメトリック。":::

要求した操作が認識されない場合、応答として "無効なルート" という値が返されます。

### <a name="sms-api-requests"></a>SMS API 要求

SMS API 要求のメトリックには、次の操作が利用できます。

| 操作またはルート    | 説明                                                                                    |
| -------------------- | ---------------------------------------------------------------------------------------------- |
| SMSMessageSent       | SMS メッセージを送信します。 |
| SMSDeliveryReportsReceived     | SMS 配信レポートを取得します。 |
| SMSMessagesReceived      | SMS メッセージを取得します。 |


:::image type="content" source="./media/sms-metric.png" alt-text="SMS API 要求のメトリック。":::

### <a name="authentication-api-requests"></a>認証 API 要求

認証 API 要求のメトリックでは、次の操作を利用できます。

| 操作またはルート    | 説明                                                                                    |
| -------------------- | ---------------------------------------------------------------------------------------------- |
| CreateIdentity       | 1 人のユーザーを表す ID を作成します。 |
| DeleteIdentity       | ID を削除します。 |
| CreateToken          | アクセス トークンを作成します。 |
| RevokeToken          | 指定された時刻より前に、ID 用に作成されたすべてのアクセス トークンを取り消します。 |

:::image type="content" source="./media/acs-auth-metrics.png" alt-text="認証要求のメトリック。":::

## <a name="next-steps"></a>次の手順

- [データ プラットフォームのメトリック](../../azure-monitor/essentials/data-platform-metrics.md)の詳細をご確認ください。
