---
title: IT Service Management Connector - Azure Monitor のセキュア エクスポート - ServiceNow を使用した構成
description: この記事では、ITSM 製品またはサービスを、Azure Monitor のセキュア エクスポートで ServiceNow に接続する方法について説明します。
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/31/2020
ms.openlocfilehash: 6b65c0bfb3781a5f78c92f533e126c640347f06d
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131455266"
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
   * [Rome](https://docs.servicenow.com/bundle/rome-it-operations-management/page/product/event-management/concept/azure-integration.html)
   * [ケベック](https://docs.servicenow.com/bundle/quebec-it-operations-management/page/product/event-management/concept/azure-integration.html)
   * [パリ](https://docs.servicenow.com/bundle/paris-it-operations-management/page/product/event-management/concept/azure-integration.html)
