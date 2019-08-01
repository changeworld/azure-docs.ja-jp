---
title: Azure SQL Database Managed Instance の管理エンドポイントを検出する | Microsoft Docs
description: Azure SQL Database Managed Instance 管理エンドポイントのパブリック IP アドレスを取得し、組み込みファイアウォールの保護機能を検証する方法について説明します
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab
ms.date: 12/04/2018
ms.openlocfilehash: a0d47c61bf84cfb22c767fd09b3feed74f55b7fc
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567482"
---
# <a name="determine-the-management-endpoint-ip-address"></a>管理エンドポイント IP アドレスを確認する

Azure SQL Database Managed Instance 仮想クラスターには、Microsoft が管理操作のために使用する管理エンドポイントが含まれています。 管理エンドポイントは、ネットワーク レベルでは組み込みのファイアウォールにより保護され、アプリケーション レベルでは相互の証明書の検証により保護されます。 管理エンドポイントの IP アドレスを確認することはできますが、このエンドポイントにアクセスすることはできません。

管理 IP アドレスを決定するには、マネージド インスタンスの FQDN `mi-name.zone_id.database.windows.net` で DNS 参照を実行します。 これにより、`trx.region-a.worker.vnet.database.windows.net` のような DNS エントリが返されます。 その後、".vnet" を削除した状態で、この FQDN で DNS 参照を実行できます。 これにより、管理 IP アドレスが返されます。 

\<MI FQDN\> をお使いのマネージド インスタンスの DNS エントリ `mi-name.zone_id.database.windows.net` に置き換えると、この PowerShell で以上の作業がすべて自動的に行われます。
  
``` powershell
  $MIFQDN = "<MI FQDN>"
  resolve-dnsname $MIFQDN | select -first 1  | %{ resolve-dnsname $_.NameHost.Replace(".vnet","")}
```

Managed Instances と接続の詳細については、「[Azure SQL Database Managed Instance の接続アーキテクチャ](sql-database-managed-instance-connectivity-architecture.md)」を参照してください。
