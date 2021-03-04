---
title: IT Service Management Connector - Azure Monitor のセキュア エクスポート - ServiceNow を使用した構成
description: この記事では、ITSM 製品またはサービスを、Azure Monitor のセキュア エクスポートで ServiceNow に接続する方法について説明します。
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/31/2020
ms.openlocfilehash: 02b167219a4f1604d340d72f9dc47e67919c2542
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101714138"
---
# <a name="connect-servicenow-to-azure-monitor"></a>ServiceNow を Azure Monitor に接続する

以降のセクションでは、ServiceNow 製品と Azure のセキュア エクスポートを接続する方法について詳しく説明します。

## <a name="prerequisites"></a>前提条件

次の前提条件が満たされていることを確認します。

* Azure AD が登録されている。
* サポートされているバージョンの ServiceNow Event Management - ITOM (バージョン New York 以降) がある。
* [アプリケーション](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ac4c9c57dbb1d090561b186c1396191a/1.3.1?referer=%2Fstore%2Fsearch%3Flistingtype%3Dallintegrations%25253Bancillary_app%25253Bcertified_apps%25253Bcontent%25253Bindustry_solution%25253Boem%25253Butility%26q%3DEvent%2520Management%2520Connectors&sl=sh)が ServiceNow インスタンスにインストールされている。

## <a name="configure-the-servicenow-connection"></a>ServiceNow 接続の構成

1. リンク https://<インスタンス名>.service-now.com/api/sn_em_connector/em/inbound_event?source=azuremonitor を使用します。これは、セキュア エクスポート定義の URI です。

2. バージョンに応じた手順に従ってください。
   * [パリ](https://docs.servicenow.com/bundle/paris-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
   * [Orlando](https://docs.servicenow.com/bundle/orlando-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
   * [ニューヨーク](https://docs.servicenow.com/bundle/newyork-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
