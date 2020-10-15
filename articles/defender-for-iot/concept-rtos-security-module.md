---
title: Azure RTOS 用のセキュリティ モジュールの基本についての概念的な説明
description: Azure RTOS 用のセキュリティ モジュールの概念とワークフローについての基本を説明します。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 9f816b35fd79d3f158ea12fd2c7e1d811823dc8a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "90931454"
---
# <a name="security-module-for-azure-rtos-preview"></a>Azure RTOS 用のセキュリティ モジュール (プレビュー)

この記事を使用して、機能と利点に加え、関連する構成と参照リソースへのリンクなど、Azure RTOS 用のセキュリティ モジュールについて理解を深めてください。 

## <a name="azure-rtos-iot-security-module"></a>Azure RTOS IoT セキュリティ モジュール

Azure RTOS 用のセキュリティ モジュールでは、NetX Duo オファリングの一部として、Azure RTOS デバイス向けの包括的なセキュリティ ソリューションが提供されます。 NetX Duo オファリングには Azure RTOS と組み込みの Azure IoT セキュリティ モジュールが付属しており、アクティブ化されると、リアルタイムのオペレーティング システム デバイスでの一般的な脅威への対応が提供されます。 

Azure RTOS 用のセキュリティ モジュールはバックグラウンドで実行され、各顧客の IoT Hub への一意の接続を使用してセキュリティ メッセージが送信されると同時に、シームレスなユーザー エクスペリエンスが提供されます。 Azure RTOS 用のセキュリティ モジュールは、既定で有効になっています。  

## <a name="azure-rtos-netx-duo"></a>Azure RTOS NetX Duo

Azure RTOS NetX Duo は、特に深く埋め込まれるリアルタイムの IoT アプリケーション用に設計された高度な商用 TCP/IP ネットワーク スタックです。 Azure RTOS NetX Duo は、セキュリティやクラウドなどの豊富なプロトコル セットを備えたデュアル IPv4 および IPv6 ネットワーク スタックです。 詳しくは、[Azure RTOS NetX Duo](https://aka.ms/netxduo) ソリューションを参照してください。

このモジュールには、次の機能があります。

- **悪意のあるネットワーク アクティビティを検出する**
- **カスタム アラートに基づくデバイスの動作のベースライン**
- **デバイス セキュリティの検疫の強化**

## <a name="security-module-for-azure-rtos-architecture"></a>Azure RTOS 用のセキュリティ モジュールのアーキテクチャ

Azure RTOS 用のセキュリティ モジュールは Azure IoT ミドルウェア プラットフォームによって初期化され、IoT Hub クライアントを使用してセキュリティ テレメトリをハブに送信します。

:::image type="content" source="media/architecture/security-module-state-diagram.png" alt-text="Azure IoT セキュリティ モジュールの状態の図と情報フロー":::

Azure RTOS 用のセキュリティ モジュールでは、3 つのコレクターを使用して、次のデバイスのアクティビティと情報が監視されます。
- デバイス ネットワーク アクティビティ **TCP**、**UDP**、および **ICM**
- **Threadx** と **NetX Duo** のバージョンとしてのシステム情報
- ハートビートのイベント

各コレクターは優先順位グループにリンクされ、各優先順位グループには独自の間隔 (可能な値は **[低]** 、 **[中]** 、 **[高]** ) があります。 間隔は、データの収集と送信が実行される時間間隔に影響します。

各時間間隔は構成可能であり、さらに[ソリューションをカスタマイズする](how-to-azure-rtos-security-module.md)ために IoT コネクタを有効または無効にすることができます。 

## <a name="supported-security-alerts-and-recommendations"></a>サポートされているセキュリティ アラートと推奨事項

Azure RTOS 用のセキュリティ モジュールでは、特定のセキュリティ アラートと推奨事項がサポートされています。 初期構成を完了した後、サービスに[関連するアラートと推奨事項の値を確認してカスタマイズ](concept-rtos-security-alerts-recommendations.md)します。

## <a name="ready-to-begin"></a>開始する準備ができましたか?

Azure RTOS 用のセキュリティ モジュールは、IoT デバイス用の無料ダウンロードとして提供されています。 Defender for IoT クラウド サービスは、Azure サブスクリプションごとに 30 日間の試用版で利用できます。 [セキュリティ モジュールを今すぐダウンロード](https://github.com/azure-rtos/azure-iot-preview/releases)して開始しましょう。 

## <a name="next-steps"></a>次の手順

- Azure RTOS 用のセキュリティ モジュールを使用して作業を開始する ([前提条件と設定](quickstart-azure-rtos-security-module.md))。
- Azure RTOS 用のセキュリティ モジュールの詳細を確認する ([セキュリティ アラートと推奨事項のサポート](concept-rtos-security-alerts-recommendations.md))。 
- Azure RTOS 用のセキュリティ モジュールの[リファレンス API](azure-rtos-security-module-api.md) を使用する。

