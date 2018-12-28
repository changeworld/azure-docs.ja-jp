---
title: Azure SQL Database Managed Instance の管理エンドポイントを検出する | Microsoft Docs
description: Azure SQL Database Managed Instance 管理エンドポイントのパブリック IP アドレスを取得し、組み込みファイアウォールの保護機能を検証する方法について説明します
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: carlrab
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 83eea565a12ee5201c42b543cdbdad72ddc28ca9
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/13/2018
ms.locfileid: "53345795"
---
# <a name="determine-the-management-endpoint-ip-address"></a>管理エンドポイント IP アドレスを確認する

Azure SQL Database Managed Instance 仮想クラスターには、Microsoft が管理操作のために使用する管理エンドポイントが含まれています。 管理エンドポイントは、ネットワーク レベルでは組み込みのファイアウォールにより保護され、アプリケーション レベルでは相互の証明書の検証により保護されます。 管理エンドポイントの IP アドレスを確認することはできますが、このエンドポイントにアクセスすることはできません。

## <a name="determine-ip-address"></a>IP アドレスを確認する

Managed Instance のホストが `mi-demo.xxxxxx.database.windows.net` であると仮定します。 ホスト名を使用して `nslookup` を実行します。

![内部ホスト名の解決](./media/sql-database-managed-instance-management-endpoint/01_find_internal_host.png)

次に、強調表示されている名前についてもう一度 `nslookup` を実行し、`.vnet.` セグメントを削除します。 このコマンドの実行結果として、パブリック IP アドレスが取得されます。

![パブリック IP アドレスの解決](./media/sql-database-managed-instance-management-endpoint/02_find_public_ip.png)


## <a name="next-steps"></a>次の手順

Managed Instances と接続の詳細については、「[Azure SQL Database Managed Instance の接続アーキテクチャ](sql-database-managed-instance-connectivity-architecture.md)」を参照してください。
