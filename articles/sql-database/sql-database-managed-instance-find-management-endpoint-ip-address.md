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
ms.reviewer: carlrab
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 542db0200229b4fea4ac67ad19c468f2b9dc67d1
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57772781"
---
# <a name="determine-the-management-endpoint-ip-address"></a>管理エンドポイント IP アドレスを確認する

Azure SQL Database Managed Instance 仮想クラスターには、Microsoft が管理操作のために使用する管理エンドポイントが含まれています。 管理エンドポイントは、ネットワーク レベルでは組み込みのファイアウォールにより保護され、アプリケーション レベルでは相互の証明書の検証により保護されます。 管理エンドポイントの IP アドレスを確認することはできますが、このエンドポイントにアクセスすることはできません。

## <a name="determine-ip-address"></a>IP アドレスを確認する

Managed Instance のホストが `mi-demo.xxxxxx.database.windows.net` であると仮定します。 ホスト名を使用して `nslookup` を実行します。

![内部ホスト名の解決](./media/sql-database-managed-instance-management-endpoint/01_find_internal_host.png)

次に、強調表示されている名前についてもう一度 `nslookup` を実行し、`.vnet.` セグメントを削除します。 このコマンドを実行すると、パブリック IP アドレスが取得されます。

![パブリック IP アドレスの解決](./media/sql-database-managed-instance-management-endpoint/02_find_public_ip.png)


## <a name="next-steps"></a>次の手順

Managed Instances と接続の詳細については、「[Azure SQL Database Managed Instance の接続アーキテクチャ](sql-database-managed-instance-connectivity-architecture.md)」を参照してください。
