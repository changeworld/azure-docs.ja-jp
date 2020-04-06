---
title: インクルード ファイル
description: インクルード ファイル
services: data-factory
author: memildin
ms.service: data-factory
ms.topic: include
ms.date: 03/17/2020
ms.author: memildin
ms.custom: include file
ms.openlocfilehash: 7d81799f7fbdb2b41db421daa1a85ec8cde511eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79541387"
---
### <a name="the-data-model-of-the-schema"></a>スキーマのデータ モデル

|フィールド|説明|
|----|----|
|**AlertName**|アラートの表示名|
|**AlertType**|一意のアラート識別子|
|**ConfidenceLevel**|(省略可能) このアラートの信頼レベル (高/低)|
|**ConfidenceScore**|(省略可能) セキュリティ アラートの数値信頼度インジケーター|
|**説明**|アラートの説明テキスト|
|**DisplayName**|アラートの表示名|
|**EndTime**|アラートの影響の終了時間 (アラートの原因となった最後のイベントの時刻)|
|**エンティティ**|アラートに関連するエンティティのリスト。 このリストでは、さまざまな種類のエンティティの混合を保持できます|
|**ExtendedLinks**|(省略可能) アラートに関連するすべてのリンクのバッグ。 このバッグでは、さまざまな種類のリンクの混合を保持できます|
|**ExtendedProperties**|アラートに関連する追加フィールドのバッグ|
|**IsIncident**|アラートがインシデントか標準アラートかを決定します。 インシデントとは、複数のアラートが 1 つのセキュリティ インシデントに集約されているセキュリティ アラートです|
|**ProcessingEndTime**|アラートが作成された UTC タイムスタンプ|
|**ProductComponentName**|(省略可能) アラートを生成した製品内のコンポーネントの名前。|
|**ProductName**|定数 ("Azure Security Center")|
|**ProviderName**|未使用|
|**RemediationSteps**|セキュリティの脅威を修復するために実行する手動のアクション項目|
|**ResourceId**|影響を受けるリソースの完全な識別子|
|**Severity**|アラートの重要度 (高/中/低/情報提供)|
|**SourceComputerId**|影響を受けたサーバーの一意の GUID (アラートがサーバーで生成された場合)|
|**SourceSystem**|未使用|
|**StartTime**|アラートの影響の開始時間 (アラートの原因となった最初のイベントの時刻)|
|**SystemAlertId**|このセキュリティ アラート インスタンスの一意識別子|
|**TenantId**|スキャンされたリソースが存在するサブスクリプションの親 Azure Active Directory テナントの識別子|
|**TimeGenerated**|評価が実行された UTC タイムスタンプ (Security Center のスキャン時刻) (DiscoveredTimeUTC と同じ)|
|**Type**|定数 ("SecurityAlert")|
|**VendorName**|アラートを提供したベンダーの名前 (例: "Microsoft")|
|**VendorOriginalId**|未使用|
|**WorkspaceResourceGroup**|VM、サーバー、仮想マシン スケール セット、またはワークスペースに報告する App Service インスタンスでアラートが生成された場合、そのワークスペース リソース グループの名前が含まれます|
|**WorkspaceSubscriptionId**|VM、サーバー、仮想マシン スケール セット、またはワークスペースに報告する App Service インスタンスでアラートが生成された場合、そのワークスペースのサブスクリプションが含まれます|
|||
