---
title: オープンソース リレーショナル データベース用 Microsoft  Defender からのアラートの設定とアラートへの応答
description: データベースに対する潜在的なセキュリティ脅威を示す異常なデータベース アクティビティを検出するオープンソース リレーショナル データベース用 Microsoft Defender を構成する方法について説明します。
author: memildin
ms.author: memildin
ms.date: 06/17/2021
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.custom: ignite-fall-2021
ms.openlocfilehash: f7d34663e9c83e74ea25ce501a89a5a34b38677e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131056009"
---
# <a name="enable-microsoft-defender-for-open-source-relational-databases-and-respond-to-alerts"></a>オープンソース リレーショナル データベース用 Microsoft Defender を有効にしてアラートに応答する

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender により、以下のサービスのデータベースへのアクセスやその悪用を試みる、異常で有害と考えられる不自然な動作が検出されます。

- [Azure Database for PostgreSQL](../postgresql/index.yml)
- [Azure Database for MySQL](../mysql/index.yml)
- [Azure Database for MariaDB](../mariadb/index.yml)

Microsoft Defender プランからアラートを取得するには、[次に示す方法](#enable-enhanced-security)で最初に Azure Defender を有効にする必要があります。

この Microsoft Defender プランの詳細については、「[オープンソース リレーショナル データベース用 Microsoft Defender の概要](defender-for-databases-introduction.md)」を参照してください。

## <a name="enable-enhanced-security"></a>強化されたセキュリティを有効にする

1. [Azure portal](https://portal.azure.com) から、保護するデータベース サーバーの構成ページを開きます。

1. 左側のセキュリティ メニューから、 **[Microsoft Defender for Cloud]** を選択します。

1. 強化されたセキュリティが有効になっていない場合は、次のスクリーンショットに示すようなボタンが表示されます。 **[Enable Microsoft Defender for [Database type]]\(Azure Defender for [データベースの種類] の有効化)** (例: "Microsoft Defender for MySQL") を選択し、 **[保存]** を選択します。

    :::image type="content" source="media/defender-for-databases-usage/enable-defender-for-mysql.png" alt-text="Microsoft Defender for MySQL を有効にする。" lightbox="media/defender-for-databases-usage/enable-defender-for-mysql.png":::

    > [!TIP]
    > ポータルのこのページは、データベースの種類 (PostgreSQL、MySQL、MariaDB) に関係なく同じになります。

## <a name="respond-to-security-alerts"></a>セキュリティの警告への対応

データベースで Microsoft Defender を有効にすると、異常なアクティビティが検出されてアラートが生成されます。 これらのアラートは、次のような複数の場所から確認できます。

- Azure portal で次の操作を行います。
    - **Microsoft Defender for Cloud のセキュリティ アラート ページ** - 表示のためのアクセス許可を取得したサブスクリプションについて、Microsoft Defender for Cloud で保護されているすべてのリソースのアラートが表示されます。
    - リソースの **Microsoft Defender for Cloud** ページ - 上記の「[強化されたセキュリティを有効にする](#enable-enhanced-security)」に示すように、1 つの特定のリソースについてアラートとレコメンデーションが表示されます。
- [電子メール アラートを受信するよう指定されている](configure-email-notifications.md)組織内のユーザーの受信トレイ。  

> [!TIP]
> [Microsoft Defender for Cloud の概要ダッシュボード](overview-page.md)にあるライブ タイルでは、データベースを含むすべてのリソースに対してアクティブな脅威の状態が追跡されます。 このタイルを選択して Microsoft Defender for Cloud のアラート ページを起動し、データベースで検出されたアクティブな脅威の概要を取得します。
>
> セキュリティ アラートへの応答の詳細な手順と推奨される方法については、「[セキュリティ アラートへの対応](tutorial-security-incident.md#respond-to-a-security-alert)」を参照してください。

### <a name="respond-to-email-notifications-of-security-alerts"></a>セキュリティ アラートの電子メール通知に応答する

Defender for Cloud では、異常なデータベース アクティビティを検出すると、電子メール通知を送信します。 この電子メールには、異常なアクティビティの特徴、データベース名、サーバー名、アプリケーション名、イベントの時刻など、疑わしいセキュリティ イベントの詳細が含まれます。 また、データベースへの潜在的な脅威の考えられる原因と調査や緩和のための推奨されるアクションについての情報も提供されます。

1. 電子メールから **[View the full alert]\(すべてのアラートの表示)** リンクを選択して Azure portal を起動し、アラート ページを表示します。このページには、データベースで検出されたアクティブな脅威の概要が示されます。
    
    :::image type="content" source="media/defender-for-databases-usage/suspected-brute-force-attack-notification-email.png" alt-text="ブルート フォース攻撃の可能性に関する Defender for Cloud の電子メール通知。":::

    Defender for Cloud ポータル ページ内からサブスクリプション レベルでアクティブな脅威を表示します。

    :::image type="content" source="media/defender-for-databases-usage/db-alerts-page.png" alt-text="1 つ以上のサブスクリプションに対するアクティブな脅威は、Microsoft Defender for Cloud に表示されます。" lightbox="media/defender-for-databases-usage/db-alerts-page.png":::

1. 現在の脅威を調査し、将来的な脅威を修復するための追加情報と推奨されるアクションを確認するには、特定のアラートを選択します。
    
    :::image type="content" source="media/defender-for-databases-usage/specific-alert-details.png" alt-text="特定のアラートの詳細。" lightbox="media/defender-for-databases-usage/specific-alert-details.png":::


> [!TIP]
> アラートの処理方法の詳細なチュートリアルについては、「[チュートリアル: セキュリティ アラートのトリアージ、調査、対応](tutorial-security-incident.md)」を参照してください。


## <a name="next-steps"></a>次のステップ

- [Defender for Cloud のトリガーへの応答を自動化する](workflow-automation.md)
- [SIEM、SOAR、または ITSM ソリューションにアラートをストリーミングする](export-to-siem.md)
- [Defender for Cloud からのアラートを抑制する](alerts-suppression-rules.md)
