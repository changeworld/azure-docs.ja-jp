---
title: "Azure Database for PostgreSQL のファイアウォール規則の作成と管理 | Microsoft Docs"
description: "Azure Portal を使用した Azure Database for PostgreSQL ファイアウォール規則の作成と管理"
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 11/27/2017
ms.openlocfilehash: 248bd491bf688ff9b3ef4252c295989dc340b79c
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/28/2017
---
# <a name="create-and-manage-azure-database-for-postgresql-firewall-rules-using-the-azure-portal"></a>Azure Portal を使用した Azure Database for PostgreSQL ファイアウォール規則の作成と管理
サーバーレベルのファイアウォール規則を使用すると、管理者は、指定された IP アドレスまたは IP アドレス範囲から Azure Database for PostgreSQL サーバーにアクセスできます。 

## <a name="prerequisites"></a>前提条件
このハウツー ガイドの手順を実行するには、以下が必要です。
- [Azure Database for PostgreSQL](quickstart-create-server-database-portal.md) を作成するサーバー

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Azure Portal でサーバーレベルのファイアウォール規則を作成する
1. PostgreSQL サーバー ページの [設定] で、**[接続のセキュリティ]** をクリックして Azure Database for PostgreSQL の [接続のセキュリティ] ページを開きます。

  ![Azure Portal - [接続のセキュリティ] のクリック](./media/howto-manage-firewall-using-portal/1-connection-security.png)

2. ツール バーで、**[自分の IP を追加]** を選択します。 このアクションにより、Azure システムによって認識されたコンピューターのパブリック IP アドレスでファイアウォール規則が自動的に作成されます。

  ![Azure Portal - [自分の IP を追加] のクリック](./media/howto-manage-firewall-using-portal/2-add-my-ip.png)

3. 構成を保存する前に、IP アドレスを確認します。 場合によっては、Azure Portal で見られる IP アドレスは、インターネットおよび Azure サーバーにアクセスするときに使用する IP アドレスと異なることがあります。 そのため、規則が期待どおりに機能するように開始 IP と終了 IP の変更が必要になる場合があります。
検索エンジンまたはその他のオンライン ツールを使用して、自分の IP アドレスを確認します (たとえば、「what is my IP」を Bing 検索します)。

  ![「What is my IP」の Bing 検索](./media/howto-manage-firewall-using-portal/3-what-is-my-ip.png)

4. アドレス範囲を追加します。 Azure Database for PostgreSQL のファイアウォール規則では、単一の IP アドレスまたはアドレス範囲を指定できます。 規則を単一の IP アドレスに限定する場合は、[開始 IP] と [終了 IP] のフィールドに同じアドレスを入力します。 ファイアウォールを開くと、管理者、ユーザー、アプリケーションは、有効な資格情報を持っている PostgreSQL サーバー上の任意のデータベースにログ インできます。

  ![Azure Portal - ファイアウォール規則 ](./media/howto-manage-firewall-using-portal/4-specify-addresses.png)

5. ツール バーの **[保存]** をクリックしてこのサーバーレベルのファイアウォール規則を保存します。 ファイアウォール規則の更新が成功したという確認を待機します。

  ![Azure Portal - [保存] のクリック](./media/howto-manage-firewall-using-portal/5-save-firewall-rule.png)


## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Azure ポータルで既存のサーバー レベルのファイアウォール規則を管理する
ファイアウォール規則を管理する手順を繰り返します。
* 現在のコンピューターを追加するには、**[自分の IP を追加]** のボタンをクリックします。 **[保存]** をクリックして変更を保存します。
* さらに IP アドレスを追加するには、規則名、開始および終了 IP アドレスを入力します。 **[保存]** をクリックして変更を保存します。
* 既存の規則を変更するには、規則内の任意のフィールドをクリックし、変更します。 **[保存]** をクリックして変更を保存します。
* 既存の規則を削除するには、省略記号 [...] をクリックし、**[削除]** をクリックします。 **[保存]** をクリックして変更を保存します。

## <a name="next-steps"></a>次のステップ
- 同様に、スクリプトを作成し、[Azure CLI を使用して Azure Database for PostgreSQL ファイアウォール規則を作成および管理](howto-manage-firewall-using-cli.md)できます。
- Azure Database for PostgreSQL サーバーに接続する方法については、「[Azure Database for PostgreSQL の接続ライブラリ](concepts-connection-libraries.md)」をご覧ください。
