---
title: Azure Monitor の IT Service Management Connector
description: この記事では、ITSM 製品/サービスを Azure Monitor の IT Service Management Connector (ITSMC) に接続して、ITSM 作業項目を一元的に監視して管理する方法に関する情報を提供します。
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/12/2020
ms.openlocfilehash: a372cdcd05267f3bdb093f676948a79c473ad955
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/29/2020
ms.locfileid: "97804022"
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector"></a>ITSM 製品/サービスを IT Service Management Connector に追加する
この記事では、ITSM 製品/サービスと Log Analytics の IT Service Management Connector (ITSMC) の間の接続を構成して、作業項目を一元的に管理する方法に関する情報を提供します。 ITSMC の詳細については、[概要](./itsmc-overview.md)に関する記事をご覧ください。

次の ITSM 製品/サービスがサポートされています。 ITSMC に製品を接続する方法に関する詳細な情報を表示する製品を選択してください。

- [ServiceNow](./itsmc-connections-servicenow.md)
- [System Center Service Manager](./itsmc-connections-scsm.md)
- [Cherwell](./itsmc-connections-cherwell.md)
- [Provance](./itsmc-connections-provance.md)

> [!NOTE]
> Cherwell や Provance のお客様には、統合の別の方法として、[Webhook アクション](./action-groups.md#webhook)を使用して Cherwell と Provance のエンドポイントに接続することを提案しています。

## <a name="next-steps"></a>次のステップ

* [ITSM Connector での問題のトラブルシューティング](./itsmc-resync-servicenow.md)