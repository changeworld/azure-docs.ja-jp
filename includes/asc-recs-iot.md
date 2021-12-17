---
author: memildin
ms.service: security-center
ms.topic: include
ms.date: 09/05/2021
ms.author: memildin
ms.custom: generated
ms.openlocfilehash: 1e6040007e345be6e1b80e7e797daf4b1f3d862a
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/07/2021
ms.locfileid: "123536897"
---
このカテゴリには **12 個** の推奨事項があります。

|推奨 |Description |重大度 |
|---|---|---|
|[ 既定の IP フィルター ポリシーを拒否にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/5a3d6cdd-8eb3-46d2-ba11-d24a0d47fe65) |IP フィルター構成では、許可されるトラフィックを定義するルールを設定し、その他のトラフィックはすべて既定で拒否する必要があります<br />(関連ポリシーはありません) |Medium |
|[IoT Hub の診断ログを有効にする必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/77785808-ce86-4e40-b45f-19110a547397) |ログを有効にし、それらを最大 1 年間保持します。 これにより、セキュリティ インシデントが発生した場合やネットワークが侵害された場合に、調査目的でアクティビティ証跡を再作成できます。<br />(関連ポリシー: [IoT Hub における診断ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f383856f8-de7f-44a2-81fc-e5135b5c2aa4)) |低 |
|[ 認証の資格情報が同一](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/9d07b7e6-2986-4964-a76c-b2689604e212) |IoT Hub に対し、複数のデバイスで同一の認証資格情報が使われています。 これは、不正なデバイスが正当なデバイスを偽装していることを示している場合があります。 攻撃者によってデバイスが偽装されているおそれもあります。<br />(関連ポリシーはありません) |高 |
|[ IoT デバイス - エージェントで低使用率のメッセージが送信されている](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/a9a59ebb-5d6f-42f5-92a1-036fd0fd1879) |現在、IoT エージェントのメッセージ サイズ キャパシティが十分に活用されておらず、送信メッセージ数が増加しています。 使用率を高めるためにメッセージ間隔を調整してください。<br />(関連ポリシーはありません) |低 |
|[ IoT デバイス - Auditd プロセスでイベントの送信が停止された](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/d74d2738-2485-4103-9919-69c7e63776ec) |Auditd プロセスによって生じたセキュリティ イベントは、このデバイスから受信されなくなりました<br />(関連ポリシーはありません) |高 |
|[ IoT デバイス - デバイス上でポートを開く](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/1a36f14a-8bd8-45f5-abe5-eef88d76ab5b) |リッスン中のエンドポイントがデバイス上に見つかりました<br />(関連ポリシーはありません) |Medium |
|[ IoT デバイス - オペレーティング システムのベースラインの検証に失敗した](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/5f65e47f-7a00-4bf3-acae-90ee441ee876) |セキュリティ関連のシステム構成の問題が識別されました<br />(関連ポリシーはありません) |Medium |
|[ IoT デバイス - チェーンのうちの 1 つに制限の緩すぎるファイアウォール ポリシーが見つかりました](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/beb62be3-5e78-49bd-ac5f-099250ef3c7c) |メインのファイアウォール チェーン (入出力) に、許可されているファイアウォール ポリシーが見つかりました。 このポリシーでは、既定ですべてのトラフィックを拒否し、デバイスとの間の必要な通信を許可するルールを定義する必要があります<br />(関連ポリシーはありません) |Medium |
|[ IoT デバイス - 入力チェーンに制限の緩すぎるファイアウォール ルールが見つかりました](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/ba975338-f956-41e7-a9f2-7614832d382d) |ファイアウォール内に、IP アドレスまたはポートの範囲が広くて制限の少ないパターンを含むルールが見つかりました<br />(関連ポリシーはありません) |Medium |
|[ IoT デバイス - 出力チェーンに制限の緩すぎるファイアウォール ルールが見つかりました](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/d5a8d84a-9ad0-42e2-80e0-d38e3d46028a) |ファイアウォール内に、IP アドレスまたはポートの範囲が広くて制限の少ないパターンを含むルールが見つかりました<br />(関連ポリシーはありません) |Medium |
|[ IoT デバイス - TLS 暗号スイートのアップグレードが必要](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/2acc27c6-5fdb-405e-9080-cb66b850c8f5) |セキュリティで保護されていない TLS 構成が検出されました。 TLS 暗号スイートをすぐにアップグレードすることをお勧めします<br />(関連ポリシーはありません) |Medium |
|[ IP フィルター ルールの IP 範囲が広い](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/d8326952-60bb-40fb-b33f-51e662708a88) |許可 IP フィルター ルールのソース IP 範囲が広すぎます。 ルールの許可が緩すぎると、IoT ハブが悪意のある攻撃者に利用される可能性があります<br />(関連ポリシーはありません) |中 |
|||
