---
title: Windows Virtual Desktop を Microsoft Sentinel に接続する | Microsoft Docs
description: Windows Virtual Desktop データを Microsoft Sentinel に接続する方法について説明します。
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: 155ae6ee08db12b49dc337e4e3f3640d7dfcd4b3
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132721634"
---
# <a name="connect-windows-virtual-desktop-data-to-microsoft-sentinel"></a>Windows Virtual Desktop データを Microsoft Sentinel に接続する

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

この記事では、Microsoft Sentinel を使用して Windows Virtual Desktop (WVD) 環境を監視する方法について説明します。

たとえば、Windows Virtual Desktop 環境を監視することで、組織のセキュリティ体制を維持しながら、仮想化されたデスクトップを使用してより多くのリモート作業を行うことができます。

## <a name="windows-virtual-desktop-data-in-microsoft-sentinel"></a>Microsoft Sentinel の Windows Virtual Desktop データ

Microsoft Sentinel の Windows Virtual Desktop データには、次の種類が含まれます。


|Data  |説明  |
|---------|---------|
|**Windows イベント ログ**     |  WVD 環境の Windows イベント ログは、WVD 環境の外部にある他の Windows マシンの Windows イベント ログと同じ方法で、Microsoft Sentinel 対応の Log Analytics ワークスペースにストリーミングされます。 <br><br>Windows コンピューターに Log Analytics エージェントをインストールし、Log Analytics ワークスペースに送信する Windows イベント ログを構成します。<br><br>詳細については、次を参照してください。<br>- [Windows コンピューターに Log Analytics エージェントをインストールする](../azure-monitor/agents/agent-windows.md)<br>- [Log Analytics エージェントを使用して Windows イベント ログのデータ ソースを収集する](../azure-monitor/agents/data-sources-windows-events.md)<br>- [Windows セキュリティ イベントの接続](connect-windows-security-events.md)       |
|**Microsoft Defender for Endpoint アラート**     |  Windows Virtual Desktop 用に Defender for Endpoint を構成するには、他の Windows エンドポイントと同じ手順を使用します。 <br><br>詳細については、次を参照してください。 <br>- [Microsoft Defender for Endpoint の展開をセットアップする](/windows/security/threat-protection/microsoft-defender-atp/production-deployment)<br>- [Microsoft 365 Defender から Microsoft Azure Sentinel にデータを接続する](connect-microsoft-365-defender.md)       |
|**Windows Virtual Desktop 診断**     | Windows Virtual Desktop 診断は Windows Virtual Desktop PaaS サービスの機能であり、Windows Virtual Desktop ロールが割り当てられているユーザーがサービスを使用するたびに情報をログに記録します。 <br><br>各ログには、そのアクティビティで Windows Virtual Desktop ロールが関係している情報、そのセッション中に表示されるすべてのエラー メッセージ、テナント情報、ユーザー情報が格納されます。 <br><br>診断機能は、ユーザーと管理者の両方のアクションのアクティビティ ログを作成します。 <br><br>詳細については、[Windows Virtual Desktop の診断機能での Log Analytics の使用](../virtual-desktop/virtual-desktop-fall-2019/diagnostics-log-analytics-2019.md)に関するページを参照してください。        |
|     |         |

## <a name="connect-windows-virtual-desktop-data"></a>Windows Virtual Desktop データに接続する

Windows Virtual Desktop データの Microsoft Sentinel への取り込みを開始するには、Windows Virtual Desktop のドキュメントの手順を使用します。

詳細については、[Log Analytics ワークスペースへの Windows Virtual Desktop データのプッシュ](../virtual-desktop/diagnostics-log-analytics.md)に関するページを参照してください。

## <a name="find-your-data"></a>データの検索

正常な接続が確立されたら、Microsoft Sentinel で Log Analytics データに対するクエリを実行します。

例については、[Windows Virtual Desktop のドキュメント](../virtual-desktop/diagnostics-log-analytics.md)にあるサンプル クエリをご覧ください。


Microsoft Sentinel ではまた、 **[全般]**  >  **[ログ]**  >  **[Windows Virtual Desktop]** 領域に組み込みのクエリも用意されています。

[![Microsoft Sentinel の Windows Virtual Desktop の組み込みのクエリ。](media/connect-windows-virtual-desktop/windows-virtual-desktop-queries.png) ](media/connect-windows-virtual-desktop/windows-virtual-desktop-queries.png#lightbox)

## <a name="next-steps"></a>次の手順


詳細については、[Azure Monitor for Windows Virtual Desktop の用語](../virtual-desktop/azure-monitor-glossary.md)を参照してください。
