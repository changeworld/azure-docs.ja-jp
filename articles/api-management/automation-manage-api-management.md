---
title: Azure Automation を使用した Azure API Management の管理
description: Azure Automation サービスを使用して Azure API Management を管理する方法について説明します。
services: api-management, automation
documentationcenter: ''
author: vladvino
manager: eamono
editor: ''
ms.assetid: 2e53c9af-f738-47f8-b1b6-593050a7c51b
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2018
ms.author: apimpm
ms.openlocfilehash: 10b483c70f7b5a3d767815306d8a690b1b9a5faf
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
---
# <a name="managing-azure-api-management-using-azure-automation"></a>Azure Automation を使用した Azure API Management の管理
このガイドでは、Azure Automation サービスと、このサービスを使用して Azure API Management の管理を簡略化する方法について紹介します。

## <a name="what-is-azure-automation"></a>Azure Automation とは
[Azure Automation](https://azure.microsoft.com/services/automation/) は、プロセスの自動化によってクラウド管理を簡略化するための Azure サービスです。 Azure Automation を使用して手動タスク、繰り返しタスク、実行時間の長いタスク、エラーが発生しやすいタスクを自動化し、信頼性と効率性を向上して組織のゴール達成までの時間を短縮できます。

Azure Automation は、ニーズに対応可能な信頼性と可用性の高いワークフロー実行エンジンを提供します。 Azure Automation では、サード パーティ製のシステムによって手動でプロセスを開始したり、必要なときに正確にタスクが起動されるようにスケジュールされた間隔でプロセスを開始できます。

Azure Automation によってクラウド管理タスクを自動的に実行することにより、運用上のオーバーヘッドが削減され、ビジネス価値の向上に重点的に取り組む IT と DevOps スタッフの負担が軽くなります。

## <a name="how-can-azure-automation-help-manage-azure-api-management"></a>Azure Automation を Azure API Management の管理に役立てる方法
[Azure API Management API 用 Windows PowerShell コマンドレット](https://docs.microsoft.com/powershell/module/azurerm.apimanagement/?view=azurermps-5.5.0#api_management/)を使用することによって、Azure Automation で API Management を管理できます。 Azure Automation 内では、コマンドレットを使用して API Management タスクの多くを実行する PowerShell ワークフロー スクリプトを記述できます。 Azure Automation 内のこれらのコマンドレットと別の Azure サービスのコマンドレットを組み合わせて、Azure サービスおよびサード パーティ システム全体の複雑なタスクを自動化することもできます。

Automation で API Management を使用する例については、次を参照してください。

* [Azure API Management – Using PowerShell for backup and restore (Azure API Management – バックアップと復元への PowerShell の使用)](https://blogs.msdn.microsoft.com/katriend/2015/10/02/azure-api-management-using-powershell-for-backup-and-restore/)

## <a name="next-steps"></a>次の手順
ここまでは、Azure Automation の基本と Azure Automation を使用して Azure API Management を管理する方法について説明しました。詳細については、次の各リンクを参照してください。

* Azure Automation の [作業開始のチュートリアル](../automation/automation-first-runbook-graphical.md)に関するページを参照してください。

