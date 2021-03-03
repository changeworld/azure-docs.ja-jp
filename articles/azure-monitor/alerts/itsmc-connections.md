---
title: Azure Monitor の IT Service Management Connector
description: この記事では、ITSM 製品/サービスを Azure Monitor の IT Service Management Connector (ITSMC) に接続して、ITSM 作業項目を一元的に監視して管理する方法に関する情報を提供します。
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/12/2020
ms.openlocfilehash: 7ac842df17e80b49990d89d1623330c4e31ab566
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/17/2021
ms.locfileid: "100603539"
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector"></a>ITSM 製品/サービスを IT Service Management Connector に追加する
この記事では、ITSM 製品/サービスと Log Analytics の IT Service Management Connector (ITSMC) の間の接続を構成して、作業項目を一元的に管理する方法に関する情報を提供します。 ITSMC の詳細については、[概要](../platform/itsmc-overview.md)に関する記事をご覧ください。

次の ITSM 製品/サービスがサポートされています。 ITSMC に製品を接続する方法に関する詳細な情報を表示する製品を選択してください。

- [ServiceNow](../platform/itsmc-connections-servicenow.md)
- [System Center Service Manager](../platform/itsmc-connections-scsm.md)
- [Cherwell](../platform/itsmc-connections-cherwell.md)
- [Provance](../platform/itsmc-connections-provance.md)

> [!NOTE]
> Cherwell や Provance のお客様には、統合の別の方法として、[Webhook アクション](../platform/action-groups.md#webhook)を使用して Cherwell と Provance のエンドポイントに接続することを提案しています。

## <a name="next-steps"></a>次のステップ

* [ITSM Connector での問題のトラブルシューティング](../platform/itsmc-resync-servicenow.md)