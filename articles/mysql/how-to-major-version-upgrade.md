---
title: メジャー バージョンのアップグレード - Azure portal - Azure Database for MySQL - 単一サーバー
description: この記事では、Azure portal を使用して Azure Database for MySQL - 単一サーバーのメジャー バージョンをアップグレードする方法について説明します
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 11/16/2020
ms.openlocfilehash: 080d7c09b180d5943216793119718eb6a2add79e
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/24/2020
ms.locfileid: "95753053"
---
# <a name="major-version-upgrade-in-azure-database-for-mysql-single-server-using-the-azure-portal"></a>Azure portal を使用した Azure Database for MySQL 単一サーバーのメジャー バージョンのアップグレード

> [!IMPORTANT]
> Azure database for MySQL 単一サーバーのメジャー バージョンのアップグレードは、パブリック プレビュー段階にあります。

この記事では、Azure Database for MySQL 単一サーバーで MySQL のメジャー バージョンをインプレース アップグレードする方法について説明します。

この機能を使用すると、データを移動したり、アプリケーションの接続文字列を変更したりしなくても、ボタンをクリックするだけで MySQL 5.6 サーバーを MySQL 5.7 にインプレース アップグレードできます。

> [!Note]
> * メジャー バージョンのアップグレードは、MySQL 5.6 から MySQL 5.7 へのメジャー バージョンのアップグレードにのみ使用できます。<br>
> * メジャー バージョンのアップグレードは、レプリカ サーバーではまだサポートされていません。
> * アップグレード操作中は、サーバーを使用できなくなります。 したがって、計画メンテナンス期間中にアップグレードを実行することをお勧めします。

## <a name="prerequisites"></a>前提条件
このハウツー ガイドを完了するには、次が必要です。
- [Azure Database for MySQL 単一サーバー](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57"></a>MySQL 5.6 から MySQL 5.7 へのメジャー バージョンのアップグレードを実行する

Azure Database of MySQL 5.6 サーバーのメジャー バージョンのアップグレードを実行するには、次の手順に従います。

> [!IMPORTANT]
> 直接、運用環境をアップグレードするのではなく、最初に、サーバーの復元済みコピーにアップグレードを実行することをお勧めします。 [ポイントインタイム リストアを実行する方法](howto-restore-server-portal.md#point-in-time-restore)を参照してください。 

1. [Azure portal](https://portal.azure.com/) で、既存の Azure Database for MySQL 5.6 サーバーを選択します。

2. **[概要]** ページのツール バーで **[アップグレード]** ボタンをクリックします。

3. **[アップグレード]** セクションで、 **[OK]** を選択して、Azure Database for MySQL 5.6 サーバーを 5.7 サーバーにアップグレードします。

    :::image type="content" source="./media/how-to-major-version-upgrade-portal/upgrade.png" alt-text="Azure Database for MySQL - 概要 - アップグレード":::

4. アップグレードが成功したことを確認する通知が表示されます。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

**1.アップグレードが必要な MySQL v5.6 を運用環境で使用しているのですが、このアップグレード機能が GA になるのは、いつですか。**

この機能は、MySQL v5.6 の提供終了前に GA となる予定です。 ただし、この機能は、実稼働可能であり、Azure で完全にサポートされているため、安心して環境で実行してください。 推奨されるベスト プラクティスとして、最初に、サーバーの復元済みコピーで実行し、テストすることを強くお勧めします。これにより、アップグレード中のダウンタイムを予測し、運用環境での実行前にアプリケーションの互換性テストを実行することができます。 詳細については、[ポイントインタイム リストアを実行する方法](howto-restore-server-portal.md#point-in-time-restore)を参照して、特定の時点のサーバーのコピーを作成してください。 

**2.これにより、ダウンタイムが発生しますか。発生する場合は、どのくらいですか。**

はい、アップグレード プロセス中はサーバーを使用できなくなります。ですから、この操作は、計画メンテナンス期間中に実行することをお勧めします。 推定ダウンタイムは、データベースのサイズ、プロビジョニングされたストレージのサイズ (プロビジョニングされた IOPS)、およびデータベース上のテーブルの数によって異なります。 アップグレード時間は、サーバー上のテーブルの数に直接比例します。Basic SKU サーバーは、標準ストレージ プラットフォームにあるため、アップグレード時間が長くなることが予想されます。 サーバー環境のダウンタイムを推定するために、最初にサーバーの復元済みコピーにアップグレードを実行することをお勧めします。  

**3.それはレプリカ サーバーではまだサポートされていないと記載されています。これは、どういう意味でしょうか。**

現在、メジャーバージョンのアップグレードはレプリカ サーバーではサポートされていません。つまり、レプリケーションに使用されるサーバー (ソース サーバーまたはレプリカ サーバー) には実行しないでください。 レプリカ サーバーでのアップグレード機能のサポートが追加される前に、レプリケーションに使用されるサーバーのアップグレードをテストする場合は、次の手順を実行することをお勧めします。

1. 計画メンテナンス中に、[レプリケーションを停止します。そして、レプリカ サーバーの名前とすべての構成情報 (ファイアウォール設定、サーバー パラメーター構成 (ソース サーバーとは異なる場合)) を取得した後にレプリカ サーバー](howto-read-replicas-portal.md) を削除します。
2. ソース サーバーのアップグレードを実行します。
3. 手順 1. で取得したものと同じ名前と構成設定を使用して、新しい読み取りレプリカ サーバーをプロビジョニングします。 新しいレプリカ サーバーは、ソース サーバーが v5.7 にアップグレードされた後に、自動的に v5.7 になります。

**4.2021 年 2 月 5 日より前に MySQL v5.6 サーバーをアップグレードしないことにした場合はどうなりますか。**

引き続き、MySQL v5.6 サーバーを実行できます。 Azure **では、サーバーに強制アップグレードが実行されることはありません**。 ただし、[Azure Database for MySQL のバージョン管理ポリシー](concepts-version-policy.md)に記載されている制限が適用されます。

## <a name="next-steps"></a>次のステップ

[Azure Database for MySQL バージョン管理ポリシー](concepts-version-policy.md)について説明します。
