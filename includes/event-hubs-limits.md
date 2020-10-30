---
title: インクルード ファイル
description: インクルード ファイル
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 09/10/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: ea1ac064799b0cede1de82851a514a2b389f20aa
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2020
ms.locfileid: "92499065"
---
次の表に、[Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) に固有のクォータと制限を示します。 Event Hubs の価格については、「[Event Hubs の価格](https://azure.microsoft.com/pricing/details/event-hubs/)」を参照してください。

### <a name="common-limits-for-all-tiers"></a>すべてのレベルに共通の制限
次の制限は、すべてのレベルで共通です。 

| 制限 |  Notes | 値 |
| --- |  --- | --- |
| サブスクリプションあたりの Event Hubs 名前空間の数 |- |100 |
| 名前空間あたりのイベント ハブの数 | 以後、新しいイベント ハブの作成要求は拒否されます。 |10 |
| イベント ハブあたりのパーティションの数 |- |32 |
| イベント ハブ名のサイズ |- | 256 文字 |
| コンシューマー グループ名のサイズ |- | 256 文字 |
| コンシューマー グループあたりの非エポック受信者の数 |- |5 |
| 名前空間ごとの承認規則の数 | 上限を超えると承認規則の作成要求が拒否されます。|12 |
| GetRuntimeInformation メソッドの呼び出し数 |  - | 1 秒あたり 50 | 
| 仮想ネットワーク (VNet) と IP 構成規則の数 | - | 128 | 


### <a name="basic-and-standard-tiers"></a>Basic レベルと Standard レベル
次の表では、Basic と Standard のレベルで異なる可能性がある制限を示しています。 

| 制限 | Notes | Basic | Standard |
| --- |  --- | -- | --- |
| Event Hubs イベントの最大サイズ| &nbsp; | 256 KB | 1 MB |
| イベント ハブあたりのコンシューマー グループの数 | &nbsp; |1 |20 |
| 名前空間あたりの AMQP 接続の数 | 追加の接続に関する後続の要求は拒否され、呼び出し元コードが例外を受け取ります。 |100 |5,000|
| イベント データの最大リテンション期間 | &nbsp; |1 日 |1 ～ 7 日間 |
| 最大スループット ユニット |スループット ユニットの制限を超えると、データが調整され、[サーバー ビジー例外](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception)が生成されます。 Standard レベルに対してより多くのスループット ユニットを要求するには、[サポート リクエスト](/azure/azure-portal/supportability/how-to-create-azure-support-request)を申請します。 [追加スループット ユニット](../articles/event-hubs/event-hubs-auto-inflate.md)は、20 単位で購入できます。 |20 | 20 | 

### <a name="dedicated-tier"></a>Dedicated レベル
Event Hubs Dedicated オファリングは固定の月額料金で課金され、最低の使用量は 4 時間です。 Dedicated レベルの機能は Standard プランとすべて同じですが、要求の厳しいワークロードを実行するお客様向けにエンタープライズ スケールの容量と制限で提供されます。 

Azure portal を使用して専用 Event Hubs クラスターを作成する方法については、この[ドキュメント](https://docs.microsoft.com/azure/event-hubs/event-hubs-dedicated-cluster-create-portal)を参照してください。

| 機能 | 制限 |
| --- | ---|
| 帯域幅 |  20 CU |
| 名前空間 | CU あたり 50 |
| Event Hubs |  名前空間あたり 1,000 |
| メッセージ サイズ | 1 MB |
| メジャー グループ | CU あたり 2,000 |
| コンシューマー グループ | CU あたりの制限なし、イベント ハブあたり 1,000 |
| 仲介型接続 | 100,000 (付属) |
| メッセージの保持期間 | 90 日、CU あたり 10 TB を含む |
| イングレス イベント | Included |
| キャプチャ | Included |


### <a name="schema-registry-limitations"></a>スキーマ レジストリに関する制限

#### <a name="limits-that-are-the-same-for-standard-and-dedicated-tiers"></a>**Standard** と **Dedicated** のレベルで同じ制限 
| 機能 | 制限 | 
| --- |  --- | -- |
| スキーマ グループ名の最大長 | 50 |  
| スキーマ名の最大長 | 100 |    
| スキーマあたりのサイズ (バイト) | 1 MB |   
| スキーマ グループあたりのプロパティの数 | 1024 |
| グループ プロパティ キーあたりのサイズ (バイト) | 256 | 
| グループ プロパティ値あたりのサイズ (バイト) | 1024 | 


#### <a name="limits-that-are-different-for-standard-and-dedicated-tiers"></a>**Standard** と **Dedicated** のレベルで異なる制限 

| 制限 | Standard | 専用 | 
| --- |  --- | -- | --- |
| スキーマ レジストリ (名前空間) のサイズ (MB) | 25 |  1024 |
| スキーマ レジストリ (名前空間) 内のスキーマ グループの数| 1 (既定のものはのぞく) | 1000 |
| スキーマ グループ全体におけるスキーマ バージョンの数 | 25 | 10000 |





