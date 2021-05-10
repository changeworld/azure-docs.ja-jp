---
title: Azure Sentinel に Windows Virtual Desktop を接続する | Microsoft Docs
description: Windows Virtual Desktop のデータを Azure Sentinel に接続する方法について説明します。
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2021
ms.author: bagol
ms.openlocfilehash: a835ea7b5e79ecc9b2d26dc6955984d0d0ff2906
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107380269"
---
# <a name="connect-windows-virtual-desktop-data-to-azure-sentinel"></a>Azure Sentinel への Windows Virtual Desktop データの接続

この記事では、Azure Sentinel を使用して Windows Virtual Desktop (WVD) 環境を監視する方法について説明します。

たとえば、Windows Virtual Desktop 環境を監視することで、組織のセキュリティ体制を維持しながら、仮想化されたデスクトップを使用してより多くのリモート作業を行うことができます。

## <a name="windows-virtual-desktop-data-in-azure-sentinel"></a>Azure Sentinel の Windows Virtual Desktop データ

Azure Sentinel の Windows Virtual Desktop データには、次の種類のデータが含まれます。


|Data  |説明  |
|---------|---------|
|**Windows イベント ログ**     |  WVD 環境からの Windows イベント ログは、WVD 環境外にある他の Windows マシンの Windows イベント ログと同じ方法で、Azure Sentinel 対応の Log Analytics ワークスペースにストリーミングされます。 <br><br>Windows コンピューターに Log Analytics エージェントをインストールし、Log Analytics ワークスペースに送信する Windows イベント ログを構成します。<br><br>詳細については、次を参照してください。<br>- [Windows コンピューターに Log Analytics エージェントをインストールする](/azure/azure-monitor/agents/agent-windows)<br>- [Log Analytics エージェントを使用して Windows イベント ログのデータ ソースを収集する](/azure/azure-monitor/agents/data-sources-windows-events)<br>- [Windows セキュリティ イベントの接続](connect-windows-security-events.md)       |
|**Microsoft Defender for Endpoint (MDE) のアラート**     |  Windows Virtual Desktop 用に MDE を構成するには、他の Windows エンドポイントと同じ手順を使用します。 <br><br>詳細については、次を参照してください。 <br>- [Microsoft Defender for Endpoint の展開をセットアップする](/windows/security/threat-protection/microsoft-defender-atp/production-deployment)<br>- [Microsoft 365 Defender から Azure Sentinel にデータを接続する](connect-microsoft-365-defender.md)       |
|**Windows Virtual Desktop 診断**     | Windows Virtual Desktop 診断は Windows Virtual Desktop PaaS サービスの機能であり、Windows Virtual Desktop ロールが割り当てられているユーザーがサービスを使用するたびに情報をログに記録します。 <br><br>各ログには、そのアクティビティで Windows Virtual Desktop ロールが関係している情報、そのセッション中に表示されるすべてのエラー メッセージ、テナント情報、ユーザー情報が格納されます。 <br><br>診断機能は、ユーザーと管理者の両方のアクションのアクティビティ ログを作成します。 <br><br>詳細については、[Windows Virtual Desktop の診断機能での Log Analytics の使用](/azure/virtual-desktop/virtual-desktop-fall-2019/diagnostics-log-analytics-2019)に関するページを参照してください。        |
|     |         |

## <a name="connect-windows-virtual-desktop-data"></a>Windows Virtual Desktop データに接続する

Azure Sentinel への Windows Virtual Desktop データの取り込みを開始するには、Windows Virtual Desktop のドキュメントに記載されている手順に従います。

詳細については、[Log Analytics ワークスペースへの Windows Virtual Desktop データのプッシュ](/azure/virtual-desktop/diagnostics-log-analytics)に関するページを参照してください。

## <a name="find-your-data"></a>データの検索

接続が正常に確立されたら、Azure Sentinel で Log Analytics データに対するクエリを実行します。

例については、[Windows Virtual Desktop のドキュメント](/azure/virtual-desktop/diagnostics-log-analytics)にあるサンプル クエリをご覧ください。


Azure Sentinel では、 **[全般]**  >  **[ログ]**  >  **[WINDOWS VIRTUAL DESKTOP]** 領域に、組み込みのクエリも用意されています。

[![Azure Sentinel にある Windows Virtual Desktop の組み込みクエリ](media/connect-windows-virtual-desktop/windows-virtual-desktop-queries.png) ](media/connect-windows-virtual-desktop/windows-virtual-desktop-queries.png#lightbox)

## <a name="next-steps"></a>次の手順


詳細については、[Azure Monitor for Windows Virtual Desktop の用語](/azure/virtual-desktop/azure-monitor-glossary)を参照してください。
