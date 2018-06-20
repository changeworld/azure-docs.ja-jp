---
title: インクルード ファイル
description: インクルード ファイル
services: logic-apps
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 05/09/2018
ms.author: estfan
ms.custom: include file
ms.openlocfilehash: 524bc1d3a19ad8ecfc8d0d210e735d6a9ae0058b
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2018
ms.locfileid: "34678273"
---
### <a name="set-up-pagination"></a>改ページの設定

一部のコネクタと複数の項目を取得するアクションでは、結果が、コネクタの既定のページ サイズを超える場合があります。 この場合、アクションは、結果の最初のページのみを返します。 たとえば、**SQL Server - Get rows** アクションの既定のページ サイズは 2048 ですが、その他の設定によって異なる可能性があります。 すべてのレコードを取得することを確認し、アクションの **[改ページ]** の設定をオンにします。 この設定により、ロジック アプリは、コネクタに残りのレコードを要求しますが、アクションの終了時に、1 つのメッセージとしてすべての結果を返します。 

特定のアクションで改ページをオンにできるいくつかのコネクタを次に示します。 

* <a href="https://docs.microsoft.com/connectors/db2/" target="_blank">DB2</a>
* <a href="https://docs.microsoft.com/connectors/dynamicscrmonline/" target="_blank">Dynamics 365 CRM Online</a>
* <a href="https://docs.microsoft.com/connectors/excel/" target="_blank">Excel</a>
* <a href="https://docs.microsoft.com/connectors/oracle/" target="_blank">Oracle Database</a>
* <a href="https://docs.microsoft.com/connectors/sharepointonline/" target="_blank">SharePoint</a>
* <a href="https://docs.microsoft.com/connectors/sql/" target="_blank">SQL Server</a> 

次に **Get rows** アクションの例を示します。

1. アクションが改ページをサポートしているかどうかを検索するには、アクションの **[設定]** を開きます。 

   ![アクションの [設定] を開く](./media/connectors-pagination-bulk-data-transfer/sql-action-settings.png)

2. アクションが、改ページをサポートする場合は、**[改ページ]** 設定を**オフ**から**オン**に変更します。 アクションが、結果の最小セットを返すことを確認するには、**[制限]** の値を指定します。

   ![アクションが結果の最小数を返すことを指定する](./media/connectors-pagination-bulk-data-transfer/sql-action-settings-pagination.png)

3. 準備ができたら、**[完了]** を選択します。