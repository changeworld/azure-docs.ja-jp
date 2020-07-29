---
title: Illusive 攻撃管理システム データを Azure Sentinel に接続する| Microsoft Docs
description: Illusive 攻撃管理システム データを Azure Sentinel に接続する方法について説明します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: 7d2d3871dd9836e2c68155aa82ce01dced128bf3
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86528260"
---
# <a name="connect-your-illusive-attack-management-system-to-azure-sentinel"></a>Illusive 攻撃管理システムを Azure Sentinel に接続する

この記事では、[Illusive 攻撃管理システム](https://www.illusivenetworks.com/technology/platform/attack-detection-system)を Azure Sentinel に接続する方法について説明します。 Illusive 攻撃管理システム データ コネクタを使用すると、Illusive の攻撃対象領域分析データとインシデント ログを Azure Sentinel と共有し、この情報を専用のダッシュボードに表示できます。組織の攻撃対象領域リスクに対する分析情報の入手 (ASM ダッシュボード) や、組織のネットワークにおける承認されていない横移動の追跡 (ADS ダッシュボード) を行えます。

> [!NOTE]
> データは、Azure Sentinel を実行しているワークスペースの地理的な場所に格納されます。

## <a name="forward-illusive-attack-management-system-logs-to-the-syslog-agent"></a>Illusive 攻撃管理システム ログを Syslog エージェントに転送する  

Syslog エージェントを介して、CEF 形式で Syslog メッセージを Azure ワークスペースに転送するように攻撃管理システムを構成します。

1. Illusive Console にログオンし、[Settings]\(設定\) -> [Reporting]\(レポート\) に移動します。

1. Syslog サーバーを見つけます。

1. 次の情報を指定します。
   - ホスト名:Linux Syslog エージェントの IP アドレスまたは FQDN ホスト名
   - ポート: 514
   - プロトコル:TCP
   - 監査メッセージ:サーバーに監査メッセージを送信する

1. Syslog サーバーを追加するには、[Add]\(追加\) をクリックします。

1. Log Analytics で Illusive 攻撃管理システムに関連するスキーマを使用するために、CommonSecurityLog を検索します。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Illusive 攻撃管理システムを Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。

- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。
