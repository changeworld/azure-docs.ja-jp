---
author: MightyPen
ms.service: sql-database
ms.topic: include
ms.date: 01/28/2019
ms.author: genemi
ms.openlocfilehash: edeaa932abe4d1882f957d0ed26aaddd97dabe3f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2019
ms.locfileid: "55198063"
---
<!-- sql-database-connect-query-prerequisites-create-db-includes.md -->

- [論理サーバー](https://docs.microsoft.com/azure/sql-database/sql-database-single-index)または[マネージド インスタンス](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index)に配置されている Azure SQL Database。 データベースは、次のいずれかの方法で作成できます。

| 論理サーバー | マネージド インスタンス |
| --- | --- |
| [ポータル](../articles/sql-database/sql-database-get-started-portal.md) | [ポータル](../articles/sql-database/sql-database-managed-instance-get-started.md) |
| [CLI](../articles/sql-database/sql-database-get-started-cli.md) | |
| [PowerShell](../articles/sql-database/sql-database-get-started-powershell.md) | |

- **論理サーバーのみ** - 論理サーバーに接続できるようにする、構成済みのサーバー レベルのファイアウォール規則。 詳細については、「[サーバーレベルのファイアウォール規則を作成する](../articles/sql-database/sql-database-get-started-portal-firewall.md)」を参照してください。
- **マネージド インスタンスのみ** - マネージド インスタンスにアクセスしているコンピューターからの構成済みの接続。 以下のオプションを使用できます。
  - インスタンスにアクセスできるマネージド インスタンスと同じ Azure VNet 内の [Azure 仮想マシン](../articles/sql-database/sql-database-managed-instance-configure-vm.md)。
  - マネージド インスタンスが配置されている VNet にコンピューターを参加させ、ネットワーク内のその他の任意の SQL Server のようにマネージド インスタンスを使用できるようにする、コンピューター上の[ポイント対サイト接続](../articles/sql-database/sql-database-managed-instance-configure-p2s.md)。
