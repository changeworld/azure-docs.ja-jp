---
title: Azure Stack 上で SQL アダプター リソース プロバイダーによって提供されたデータベースを使用する | Microsoft Docs
description: SQL アダプター リソース プロバイダーを使用してプロビジョニングした SQL データベースを作成して管理する方法
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
ms.date: 06/18/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 2f286c48822956c82f99808092c26f6637be5cb1
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2018
ms.locfileid: "38968828"
---
# <a name="create-sql-databases"></a>SQL データベースの作成

ユーザー ポータルで、セルフサービス データベースを作成して管理できます。 Azure Stack ユーザーは、SQL データベース サービスを含むオファーがあるサブスクリプションが必要です。

1. [Azure Stack](azure-stack-poc.md) ユーザー ポータルにサインインします。

2. **[+新規]** &gt;**[データ + ストレージ]** &gt; **[SQL Server データベース]** &gt; **[追加]** を選択します。

3. **[データベースの作成]** で、**[データベース名]**、**[最大サイズ (MB)]** などの必要な情報を入力します。

   >[!NOTE]
   >データベースのサイズは少なくとも 64 MB 必要であり、データベースの配置後に増やすことができます。

   必要に応じて、環境に合うように他の設定を構成します。

4. **[データベースの作成]** で、**[SKU]** を選択します。 **[SKU の選択]** で、データベースの SKU を選択します。

   ![データベースの作成](./media/azure-stack-sql-rp-deploy/newsqldb.png)

   >[!NOTE]
   >ホスティング サーバーは、Azure Stack に追加されるときに SKU が割り当てられます。 データベースは、ホスティング サーバー プールの SKU 内に作成されます。

5. **[ログイン]** を選択します。
6. **[ログインの選択]** で、既存のログインを選択するか、**[+ 新しいログインの作成]** を選択します。
7. **[新しいログイン]** で、**データベースのログイン名**と**パスワード** を入力します。

   >[!NOTE]
   >これらの設定は、このデータベースにアクセスするためにのみ作成する SQL 認証資格情報です。 ログイン ユーザー名はグローバルに一意である必要があります。 同じ SKU を使用する他のデータベースのログイン設定を再利用できます。

   ![新しいデータベース ログインの作成](./media/azure-stack-sql-rp-deploy/create-new-login.png)

8. **[OK]** を選択して、データベースの配置を完了します。

データベースの配置後に表示される **[要点]** で、**[接続文字列]** の情報を書き留めます。 この文字列は、SQL Server データベースにアクセスする必要があるすべてのアプリケーションで使用できます。

![接続文字列の取得](./media/azure-stack-sql-rp-deploy/sql-db-settings.png)

## <a name="sql-always-on-databases"></a>SQL AlwaysOn データベース

設計により、AlwaysOn データベースの処理は、スタンドアロン サーバー環境とは異なる方法で処理されます。 詳細については、[Azure 仮想マシンの SQL Server AlwaysOn 可用性グループの概要](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)に関する記事を参照してください。

### <a name="verify-sql-always-on-databases"></a>SQL AlwaysOn データベースの確認

次の画面キャプチャは、SQL Server Management Studio を使用して、SQL AlwaysOn データベースの状態を確認する方法を示しています。

![AlwaysOn データベースの状態](./media/azure-stack-sql-rp-deploy/verifyalwayson.png)

AlwaysOn データベースは同期済みと表示され、すべての SQL インスタンスで使用でき、可用性グループ内に表示されます。 前の画面キャプチャでは、データベースの例は newdb1 であり、その状態は **newdb1 (同期済み)** です。

### <a name="delete-an-alwayson-database"></a>AlwaysOn データベースの削除

リソース プロバイダーから SQL AlwaysOn データベースを削除すると、データベースは SQL によってプライマリ レプリカと可用性グループから削除されます。

データベースは他のレプリカに復元中状態で配置され、トリガーされない限り、削除されることはありません。 データベースが削除されない場合、セカンダリ レプリカは非同期状態になります。

## <a name="next-steps"></a>次の手順

[SQL Server リソース プロバイダーの維持](azure-stack-sql-resource-provider-maintain.md)
