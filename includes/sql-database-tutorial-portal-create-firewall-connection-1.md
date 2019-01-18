---
author: MightyPen
ms.service: sql-database
ms.topic: include
ms.date: 12/10/2018
ms.author: genemi
ms.openlocfilehash: ab31ee82e8035fe888fa70b5796aef2c2b2939b2
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2018
ms.locfileid: "53728563"
---
## <a name="sign-in-to-the-azure-portal"></a>Azure ポータルにサインインします。

 [Azure ポータル](https://portal.azure.com/)

## <a name="create-a-blank-sql-database"></a>空の SQL データベースを作成する

Azure SQL データベースは、定義済みの一連の[コンピューティング リソースとストレージ リソース](../articles/sql-database/sql-database-service-tiers-dtu.md)内に存在します。 データベースは、[Azure リソース グループ](../articles/azure-resource-manager/resource-group-overview.md)と [Azure SQL データベース論理サーバー](../articles/sql-database/sql-database-features.md)の下で動作します。

空の SQL Database を作成するには、次の手順に従います。

1. Azure Portal の左上隅にある **[リソースの作成]** をクリックします。

1. **[新規]** ページで、**[データベース]** > **[SQL Database]** の順に選択します。

   ![空のデータベースを作成](../articles/sql-database/media/sql-database-design-first-database/create-empty-database.png)

1. **[SQL Database]** ウィンドウで、次の値を入力または選択します。

   | Setting       | 推奨値 | 説明 |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **データベース名** | *yourDatabase* | 有効なデータベース名については、「[データベース識別子](/sql/relational-databases/databases/database-identifiers)」を参照してください。 |
   | **サブスクリプション** | *yourSubscription*  | サブスクリプションの詳細については、[サブスクリプション](https://account.windowsazure.com/Subscriptions)に関するページを参照してください。 |
   | **[リソース グループ]** | *yourResourceGroup* | 有効なリソース グループ名については、[名前付け規則と制限](/azure/architecture/best-practices/naming-conventions)に関するページを参照してください。 |
   | **[ソースの選択]** | 空のデータベース | 空のデータベースを作成するように指定します。 |

   ![データベースの作成](../articles/sql-database/media/sql-database-design-first-database/create-database.png)

   1. **[サーバー]** を選択して、新しいデータベース用のサーバーを構成します。 次に、以下の値を入力または選択します。

      | Setting       | 推奨値 | 説明 |
      | ------------ | ------------------ | ------------------------------------------------- |
      | **サーバー名** | グローバルに一意の名前 | 有効なサーバー名については、[名前付け規則と制限](/azure/architecture/best-practices/naming-conventions)に関するページを参照してください。 |
      | **サーバー管理者ログイン** | 有効な名前 | 有効なログイン名については、「[データベース識別子](/sql/relational-databases/databases/database-identifiers)」を参照してください。|
      | **パスワード** | 有効なパスワード | パスワードは 8 文字以上で、大文字、小文字、数字、英数字以外の文字のうち、3 つのカテゴリの文字が使用されている必要があります。 |
      | **場所** | 有効な場所 | リージョンについては、「[Azure リージョン](https://azure.microsoft.com/regions/)」を参照してください。 |

      **[選択]** を選択します。

      ![データベース サーバーの作成](../articles/sql-database/media/sql-database-design-first-database/create-database-server.png)

   1. **[価格レベル]** を選択して、サービス レベル、DTU 数、ストレージの容量を指定します。 DTU とストレージに関して、サービス レベルごとに利用できるオプションを調べます。

      サーバーのレベル、DTU 数、ストレージの容量を選択したら、**[適用]** を選択します。

   1. 空のデータベースの**照合順序**を入力します (このチュートリアルでは既定値を使用)。 照合順序の詳細については、[照合順序](/sql/t-sql/statements/collations)に関するページを参照してください。

1. これで **SQL Database** フォームの入力が完了したので、**[作成]** を選択して、データベースを作成します。 この操作は、完了までに 1 分 30 秒程度かかります。

1. ツール バーの **[通知]** をクリックして、デプロイ プロセスを監視します。

     ![通知](../articles/sql-database/media/sql-database-design-first-database/notification.png)

## <a name="create-a-firewall-rule"></a>ファイアウォール規則の作成

SQL データベース サービスでは、外部のアプリケーションやツールにサーバーまたはサーバー上のすべてのデータベースへの接続を禁止するファイアウォールが、サーバーレベルで作成されます。 次の手順に従って、クライアントの IP アドレスに対する [SQL データベース サーバーレベルのファイアウォール規則](../articles/sql-database/sql-database-firewall-configure.md)を作成します。 このプロセスにより、ご利用の IP アドレスに対してのみ、SQL データベース ファイアウォールを介した外部接続が可能になります。

> [!NOTE]
> SQL データベースの通信は、ポート 1433 で行われます。 企業ネットワーク内から接続しようとしても、ポート 1433 での送信トラフィックがネットワークのファイアウォールで禁止されている場合があります。 その場合、管理者によってポート 1433 が開放されない限り、Azure SQL データベース サーバーに接続することはできません。

1. デプロイが完了したら、左側のメニューから **[SQL データベース]** を選択し、**SQL データベース** ページで、*yourDatabase* を選択します。 データベースの **[概要]** ページが開くと、完全修飾サーバー名 (*yourserver.database.windows.net* など) や、追加の構成オプションが表示されます。

1. この完全修飾サーバー名をコピーします。後の手順でサーバーとそのデータベースに接続する際に必要となります。

   ![サーバー名](../articles/sql-database/media/sql-database-design-first-database/server-name.png)

1. ツール バーで **[サーバー ファイアウォールの設定]** を選択します。 SQL データベース サーバーの **[ファイアウォール設定]** ページが開きます。

   ![サーバーのファイアウォール規則](../articles/sql-database/media/sql-database-design-first-database/server-firewall-rule.png)

   1. ツール バーの **[クライアント IP の追加]** を選択し、現在の IP アドレスを新しいファイアウォール規則に追加します。 ファイアウォール規則は、単一の IP アドレスまたは IP アドレスの範囲に対して、ポート 1433 を開くことができます。

   1. **[保存]** を選択します。 論理サーバーでポート 1433 を開いている現在の IP アドレスに対して、サーバーレベルのファイアウォール規則が作成されます。

   1. **[OK]** を選択し、**[ファイアウォール設定]** ページを閉じます。

これで、ご利用の IP アドレスがファイアウォールをパススルーできるようになり、SSMS や他の任意のツールを使用して SQL データベース サーバーとそのデータベースに接続できるようになりました。 必ず、先ほど作成したサーバー管理者アカウントを使用してください。

> [!IMPORTANT]
> 既定では、すべての Azure サービスで、SQL データベース ファイアウォール経由のアクセスが有効になります。 すべての Azure サービスに対して無効にするには、このページの **[オフ]** を選択します。
