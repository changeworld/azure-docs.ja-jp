---
title: Azure Defender for IoT で管理コンソールをデプロイする
description: Azure Defender for IoT で管理コンソールをデプロイする方法について説明します。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/14/2020
ms.author: rkarlin
ms.openlocfilehash: 6af943951b2dba0c4a11b08ad1bcd961cf1c0fc2
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2020
ms.locfileid: "92094192"
---
# <a name="deploy-the-management-console"></a>管理コンソールをデプロイする
この記事では、Azure Defender for IoT のオンプレミス管理コンソールをデプロイする方法について説明します。

## <a name="the-on-premises-management-console"></a>オンプレミス管理コンソール

オンプレミス管理コンソールでは、すべてのネットワーク資産の統合ビューが提供されます。また、すべての施設にわたって、IoT および OT の主要なリスク インジケーターとアラートがリアルタイムで表示されます。 SOC ワークフローと Runbook に緊密に統合されているため、軽減アクティビティの優先順位付けと脅威のクロスサイト関連付けを簡単に行うことができます。

- 包括的 - 資産管理、リスクと脆弱性の管理、および脅威監視とインシデント対応のための単一の統合プラットフォームにより、複雑さを軽減します。

- 集計と関連付け - すべてのサイトから収集されたデータとアラートを表示、集計、および分析します。

- すべてのセンサーの制御 - 1 つの場所からすべてのセンサーを構成および監視します。

   ![Azure Defender for IoT のサイト管理ビュー](media/updates/image2.png)

## <a name="deploy-the-on-premises-management-console-appliance"></a>オンプレミス管理コンソール アプライアンスをデプロイする

このプロセスでは、独自のハードウェアを入手し、ソフトウェアをインストールする必要があります。 このソリューションは、認定済みのアプライアンス上で動作します。 認定済みのアプライアンスを購入する際は、「[Azure Defender for IoT ハードウェア仕様ガイド](https://aka.ms/AzureDefenderforIoTBareMetalAppliance)」を参考にしてください。

ISO イメージのダウンロードとセンサー ソフトウェアのインストールについては、[Azure Defender for IoT のインストール ガイド](https://aka.ms/AzureDefenderforIoTInstallSensorISO)を参照してください。

**オンプレミス管理コンソールをデプロイするには:**

1. Microsoft Azure Defender for IoT に移動します。

2. **[On-premises management console]\(オンプレミス管理コンソール\)** を選択します。

   ![Azure Defender for IoT のオンプレミス管理コンソール ビュー](media/updates/image15.png)

3. **[Select version]\(バージョンの選択\)** メニューからバージョン 3.1 を選択します。

4. **[Download]\(ダウンロード\)** を選択してファイルを保存します。

5. ソフトウェアがインストールされたら、ネットワークのセットアップ タスクを実行します。 詳細については、[Azure Defender for IoT のネットワーク セットアップ ガイド](https://aka.ms/AzureDefenderForIoTNetworkSetup)を参照してください。

## <a name="next-steps"></a>次のステップ

構成オプションの詳細については、モジュール構成のハウツー ガイドに進んでください。
> [!div class="nextstepaction"]
> [モジュール構成のハウツー ガイド](./how-to-agent-configuration.md)
