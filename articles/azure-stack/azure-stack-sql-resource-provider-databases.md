---
title: Azure Stack 上で SQL アダプター RP によって指定されたデータベースを使用する | Microsoft Docs
description: SQL アダプター リソースプロバイダーを使用してプロビジョニングした SQL データベースを作成し管理する方法
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 2808847642639069e60102b195ac97957c8593f0
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2018
ms.locfileid: "33203461"
---
# <a name="create-sql-databases"></a>SQL データベースの作成
セルフサービス データベースは、ユーザー ポータル エクスペリエンスを通じて提供されます。 ユーザーはデータベース サービスを含むプランのサブスクリプションが必要です。

1. [Azure Stack](azure-stack-poc.md) のユーザー ポータルにサインインします (サービス管理者は管理者ポータルも使用できます)。

2. **[+新規]** &gt;**[データ + ストレージ]** &gt; **[SQL Server データベース]** &gt; **[追加]** をクリックします。

3. **[データベース名]**、**[最大サイズ]** などのデータベースの詳細をフォームに入力し、必要に応じて他のパラメーターを変更します。 データベースの SKU の選択を求められます。 ホスト サーバーが追加されると、SKU が割り当てられます。 データベースは SKU を構成するホスト サーバーのプールに作成されます。

  ![New database](./media/azure-stack-sql-rp-deploy/newsqldb.png)

  >[!NOTE]
  > データベースのサイズは 64 MB 以上である必要があります。 サイズは設定で増やすことができます。

4. ログイン設定: **[データベース ログイン]** と **[パスワード]** を入力します。 次の設定は、このデータベースにアクセスするためにだけ作成する SQL 認証の資格情報です。 ログイン ユーザー名はグローバルに一意である必要があります。 新しいログイン設定を作成するか、既存の設定を選択します。 同じ SKU を使用して他のデータベースのログイン設定を再利用することができます。

    ![新しいデータベース ログインの作成](./media/azure-stack-sql-rp-deploy/create-new-login.png)


5. フォームを送信し、デプロイが完了するまで待ちます。

    結果のブレードで、[接続文字列] フィールドに注目してください。 Azure Stack で SQL Server アクセスを必要とする任意のアプリケーション (たとえば、Web アプリ) でその文字列を使用できます。

    ![接続文字列の取得](./media/azure-stack-sql-rp-deploy/sql-db-settings.png)

## <a name="delete-sql-alwayson-databases"></a>SQL AlwaysOn データベースの削除
SQL AlwaysOn データベースは、リソース プロバイダーから削除されるとプライマリ グループと AlwaysOn 可用性グループから正常に削除されますが、SQL 可用性グループ は削除したデータベースをすべてのレプリカで復元中の状態とし、トリガーが発生しない限りはデータベースを削除しないようになっています。 データベースが削除されない場合、セカンダリ レプリカは非同期状態になります。 同じリソースプロバイダーを通した可用性グループへの新しいデータベースの再追加は、引き続き機能します。

## <a name="verify-sql-alwayson-databases"></a>SQL AlwaysOn データベースの確認
AlwaysOn データベースは同期済みと表示され、すべてのインスタンスと可用性グループで使用できます。 フェールオーバー後は、データベースはシームレスに接続されます。 データベースが同期中であることを確認するには SQL Server Management Studio を使用できます。

![AlwaysOn の確認](./media/azure-stack-sql-rp-deploy/verifyalwayson.png)


## <a name="next-steps"></a>次の手順

[SQL Server リソース プロバイダーの維持](azure-stack-sql-resource-provider-maintain.md)
