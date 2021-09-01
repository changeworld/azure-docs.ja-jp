---
title: オープンソース リレーショナル データベース用 Azure Defender からのアラートの設定とアラートへの応答
description: データベースに対する潜在的なセキュリティ脅威を示す異常なデータベース アクティビティを検出するオープンソース リレーショナル データベース用 Azure Defender を構成する方法について説明します。
author: memildin
ms.author: memildin
ms.date: 06/17/2021
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 1300a8ad11d7091085c95205b435a2d3fb077cd3
ms.sourcegitcommit: 4f185f97599da236cbed0b5daef27ec95a2bb85f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/19/2021
ms.locfileid: "112369971"
---
# <a name="enable-azure-defender-for-open-source-relational-databases-and-respond-to-alerts"></a>オープンソース リレーショナル データベース用 Azure Defender を有効にしてアラートに応答する

Azure Defender により、以下のサービスのデータベースへのアクセスやその悪用を試みる、異常で有害と考えられる不自然な動作が検出されます。

- [Azure Database for PostgreSQL](../postgresql/index.yml)
- [Azure Database for MySQL](../mysql/index.yml)
- [Azure Database for MariaDB](../mariadb/index.yml)

Azure Defender プランからアラートを取得するには、[次に示す方法](#enable-azure-defender)で最初に Azure Defender を有効にする必要があります。

この Azure Defender プランの詳細については、「[オープンソース リレーショナル データベース用 Azure Defender の概要](defender-for-databases-introduction.md)」を参照してください。

## <a name="enable-azure-defender"></a>Azure Defender を有効にする

1. [Azure portal](https://portal.azure.com) から、保護するデータベース サーバーの構成ページを開きます。

1. 左側の [セキュリティ] メニューで、 **[Security Center]** を選択します。

1. Azure Defender が有効になっていない場合は、次のスクリーンショットに示すようなボタンが表示されます。 **[Enable Azure Defender for [Database type]]\(Azure Defender for [データベースの種類] の有効化\)** (例: "Azure Defender for MySQL") を選択し、 **[保存]** を選択します。

    :::image type="content" source="media/defender-for-databases-usage/enable-defender-for-mysql.png" alt-text="Azure Defender for MySQL の有効化。" lightbox="media/defender-for-databases-usage/enable-defender-for-mysql.png":::

    > [!TIP]
    > ポータルのこのページは、データベースの種類 (PostgreSQL、MySQL、MariaDB) に関係なく同じになります。

## <a name="respond-to-security-alerts"></a>セキュリティの警告への対応

データベースで Azure Defender を有効にすると、異常なアクティビティが検出されてアラートが生成されます。 これらのアラートは、次のような複数の場所から確認できます。

- Azure portal で次の操作を行います。
    - **Azure Defender のセキュリティ アラート ページ** - 表示のためのアクセス許可を取得したサブスクリプションについて、Azure Defender で保護されているすべてのリソースのアラートが表示されます。
    - リソースの **Security Center** ページ - 上記の「[Azure Defender を有効にする](#enable-azure-defender)」に示すように、1 つの特定のリソースについてアラートと推奨事項が表示されます。
- [電子メール アラートを受信するよう指定されている](security-center-provide-security-contact-details.md)組織内のユーザーの受信トレイ。  

> [!TIP]
> [Azure Security Center の概要ダッシュボード](overview-page.md)にあるライブ タイルでは、データベースを含むすべてのリソースに対してアクティブな脅威の状態が追跡されます。 このタイルを選択して Azure Defender のアラート ページを起動し、データベースで検出されたアクティブな脅威の概要を取得します。
>
> Azure Defender アラートへの応答の詳細な手順と推奨される方法については、「[セキュリティ アラートへの対応](tutorial-security-incident.md#respond-to-a-security-alert)」を参照してください。


### <a name="respond-to-email-notifications-of-security-alerts"></a>セキュリティ アラートの電子メール通知に応答する

Azure Defender では、異常なデータベース アクティビティを検出すると、電子メール通知を送信します。 この電子メールには、異常なアクティビティの特徴、データベース名、サーバー名、アプリケーション名、イベントの時刻など、疑わしいセキュリティ イベントの詳細が含まれます。 また、データベースへの潜在的な脅威の考えられる原因と調査や緩和のための推奨されるアクションについての情報も提供されます。

1. 電子メールの **[View the full alert]\(すべてのアラートの表示\)** リンクを選択して Azure portal を起動し、アラート ページを表示します。このページには、データベースで検出されたアクティブな脅威の概要が示されます。
    
    :::image type="content" source="media/defender-for-databases-usage/suspected-brute-force-attack-notification-email.png" alt-text="ブルート フォース攻撃の可能性に関する Azure Defender の電子メール通知。":::

    Security Center ポータル ページ内からサブスクリプション レベルでアクティブな脅威を表示します。

    :::image type="content" source="media/defender-for-databases-usage/db-alerts-page.png" alt-text="1 つ以上のサブスクリプションに関するアクティブな脅威が Azure Security Center に表示されます。" lightbox="media/defender-for-databases-usage/db-alerts-page.png":::

1. 現在の脅威を調査し、将来的な脅威を修復するための追加情報と推奨されるアクションを確認するには、特定のアラートを選択します。
    
    :::image type="content" source="media/defender-for-databases-usage/specific-alert-details.png" alt-text="特定のアラートの詳細。" lightbox="media/defender-for-databases-usage/specific-alert-details.png":::


> [!TIP]
> アラートの処理方法の詳細なチュートリアルについては、「[チュートリアル: セキュリティ アラートのトリアージ、調査、対応](tutorial-security-incident.md)」を参照してください。


## <a name="next-steps"></a>次のステップ

- [Security Center のトリガーへの応答を自動化する](workflow-automation.md)
- [SIEM、SOAR、または ITSM ソリューションにアラートをストリーミングする](export-to-siem.md)
- [Azure Defender のアラートの抑制](alerts-suppression-rules.md)