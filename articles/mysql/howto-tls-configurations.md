---
title: TLS 構成 - Azure portal - Azure Database for MySQL
description: Azure portal を使用して Azure Database for MySQL の TLS 構成を設定する方法について説明します。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 06/02/2020
ms.openlocfilehash: 5ecf2992fa9ea56f73748a9f1f98c75f9076c68f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104656891"
---
# <a name="configuring-tls-settings-in-azure-database-for-mysql-using-azure-portal"></a>Azure portal を使用した Azure Database for MySQL での TLS 設定の構成

この記事では、接続のために許可された TLS の最小バージョンを適用して、構成されている TLS の最小バージョンより低い TLS バージョンのすべての接続を確認して拒否することによりネットワーク セキュリティを向上させるように Azure Database for MySQL サーバーを構成する方法について説明します。

Azure Database for MySQL に接続するための TLS バージョンを適用できます。 ユーザーは、使用しているデータベース サーバーに TLS の最小バージョンを設定できるようになりました。 たとえば、この TLS の最小バージョンを 1.0 に設定することは、TLS 1.0、1.1、1.2 を使用したクライアントの接続が許可されることを示します。 あるいは、これを 1.2 に設定することは、TLS 1.2 以上を使用したクライアントの接続のみが許可され、TLS 1.0 と TLS 1.1 を使用したすべての受信接続が拒否されることを示します。

## <a name="prerequisites"></a>前提条件

このハウツー ガイドを完了するには、次が必要です。

* [Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-mysql"></a>Azure Database for MySQL の TLS 構成を設定する

MySQL サーバーの TLS の最小バージョンを設定するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) で、既存の Azure Database for MySQL サーバーを選択します。

1. MySQL サーバー ページの **[設定]** で、 **[接続のセキュリティ]** をクリックして接続のセキュリティの構成ページを開きます。

1. **[TLS の最小バージョン]** で、 **[1.2]** を選択して、MySQL サーバーへの TLS 1.2 より低い TLS バージョンでの接続を拒否します。

    :::image type="content" source="./media/howto-tls-configurations/setting-tls-value.png" alt-text="Azure Database for MySQL の TLS 構成":::

1. **[保存]** をクリックして変更を保存します。 

1. 接続のセキュリティの設定が正常に有効にされたことが通知で示され、すぐに有効になります。 サーバーの **再起動は不要** で、実行されません。 変更が保存されると、サーバーへのすべての新しい接続は、その TLS バージョンが、ポータルで設定されている最小 TLS バージョン以上の場合にのみ受け入れられます。

    :::image type="content" source="./media/howto-tls-configurations/setting-tls-value-success.png" alt-text="Azure Database for MySQL の TLS 構成の成功":::

## <a name="next-steps"></a>次のステップ

- [メトリックに関するアラートを作成する方法](howto-alert-on-metric.md)について学習します
