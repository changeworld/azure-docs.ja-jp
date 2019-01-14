---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/02/2019
ms.openlocfilehash: d1416e7a6cad079be94b0a9b48d7c7c71a598cc2
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/02/2019
ms.locfileid: "53977360"
---
Fluentd は、ログの一元管理を実現するオープンソースのデータ コレクターです。 [Fluentd](https://www.fluentd.org) サーバーに対するコンテナーの接続は、`Fluentd` の設定によって管理されます。 コンテナーには、お客様のコンテナーでログ、および必要に応じてメトリック データを Fluentd サーバーに書き込むことができる Fluentd ログ プロバイダーが含まれます。

次の表に、`Fluentd` セクションでサポートされている構成設定について説明します。

| Name | データ型 | 説明 |
|------|-----------|-------------|
| `Host` | String | Fluentd サーバーの IP アドレスまたは DNS ホスト名。 |
| `Port` | 整数 | Fluentd サーバーのポート。<br/> 既定値は 24224 です。 |
| `HeartbeatMs` | 整数 | ハートビート間隔 (ミリ秒)。 この間隔が期限切れになるまでにイベント トラフィックが送信されなかった場合、ハートビートが Fluentd サーバーに送信されます。 既定値は、60000 ミリ秒 (1 分) です。 |
| `SendBufferSize` | 整数 | 送信操作用に割り当てられたネットワーク バッファー領域 (バイト数)。 既定値は、32,768 バイト (32 キロバイト) です。 |
| `TlsConnectionEstablishmentTimeoutMs` | 整数 | Fluentd サーバーとの SSL または TLS 接続を確立するためのタイムアウト (ミリ秒)。 既定値は、10000 ミリ秒 (10 秒) です。<br/> `UseTLS` が false に設定されている場合、この値は無視されます。 |
| `UseTLS` | Boolean | コンテナーで、Fluentd サーバーとの通信に SSL または TLS を使用する必要があるかどうかを示します。 既定値は false です。 |