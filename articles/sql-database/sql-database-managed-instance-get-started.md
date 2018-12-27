---
title: Azure portal:SQL マネージド インスタンスの作成 | Microsoft Docs
description: SQL マネージド インスタンス、ネットワーク環境、アクセス用のクライアント VM を作成します。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: Carlrab
manager: craigg
ms.date: 11/28/2018
ms.openlocfilehash: 4b8c67cfff89b54b4776ebc8b4586cd8f52950b3
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/13/2018
ms.locfileid: "53342614"
---
# <a name="quickstart-create-an-azure-sql-database-managed-instance"></a>クイック スタート:Azure SQL Database マネージド インスタンスの作成

このクイック スタートでは、Azure portal で Azure SQL Database [マネージド インスタンス](sql-database-managed-instance.md)を作成する方法について説明します。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="sign-in-to-the-azure-portal"></a>Azure ポータルにサインインします。

[Azure Portal](https://portal.azure.com/) にサインインします。

## <a name="create-a-managed-instance"></a>マネージド インスタンスを作成する

次の手順に従って、マネージド インスタンスを作成します。

1. Azure portal の左上隅にある **[リソースの作成]** を選択します。
2. **[マネージド インスタンス]** を探し、**[Azure SQL Managed Instance]** を選択します。
3. **作成**を選択します。

   ![マネージド インスタンスの作成](./media/sql-database-managed-instance-get-started/managed-instance-create.png)

4. 次の表の情報を参考にして、**マネージド インスタンス**のフォームに必要な情報を入力します。

   | Setting| 推奨値 | 説明 |
   | ------ | --------------- | ----------- |
   | **サブスクリプション** | 該当するサブスクリプション | 新しいリソースを作成するアクセス許可があるサブスクリプション |
   |**マネージド インスタンス名**|有効な名前|有効な名前については、[名前付け規則と制限事項](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)に関するページを参照してください。|
   |**マネージド インスタンス管理者ログイン**|任意の有効なユーザー名|有効な名前については、[名前付け規則と制限事項](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)に関するページを参照してください。 "serveradmin" は予約済みのサーバー レベルのロールであるため、使用しないでください。|
   |**パスワード**|有効なパスワード|パスワードは 16 文字以上で、[定義された複雑さの要件](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)を満たす必要があります。|
   |**場所**|マネージド インスタンスの作成先となる場所|リージョンについては、「[Azure リージョン](https://azure.microsoft.com/regions/)」を参照してください。|
   |**Virtual Network**|**[新しい仮想ネットワークの作成]** または有効な仮想ネットワークとサブネットを選択します。| ネットワーク/サブネットが淡色表示されている場合は、新しいマネージド インスタンスのターゲットとして選択する前に、[ネットワーク要件を満たすように変更する](sql-database-managed-instance-configure-vnet-subnet.md)必要があります。 マネージド インスタンスのネットワーク環境を構成するための要件については、「[Azure SQL Database Managed Instance の VNet を構成する](sql-database-managed-instance-connectivity-architecture.md)」を参照。 |
   |**[リソース グループ]**|新規または既存のリソース グループ|有効なリソース グループ名については、[名前付け規則と制限](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)に関するページを参照してください。|

   ![マネージド インスタンス フォーム](./media/sql-database-managed-instance-get-started/managed-instance-create-form.png)

5. マネージド インスタンスをインスタンス フェールオーバー グループのセカンダリとして使用するには、チェックアウトを選択し、DnsAzurePartner マネージド インスタンスを指定します。 この機能はプレビュー段階のため、スクリーンショットには表示されていません。
6. **[価格レベル]** を選択してコンピューティング リソースとストレージ リソースのサイズを指定し、価格レベルのオプションを確認します。 [汎用] 価格レベルでは、32 GB のメモリと 16 仮想コアが既定値となっています。
7. スライダーまたはテキスト ボックスを使用して、記憶域のサイズと仮想コアの数を指定します。
8. 完了したら **[適用]** を選択して選択内容を保存します。  
9. **[作成]** を選択してマネージド インスタンスをデプロイします。
10. **[通知]** アイコンを選択してデプロイの状態を表示します。

    ![マネージド インスタンスのデプロイの進行状況](./media/sql-database-managed-instance-get-started/deployment-progress.png)

11. **[デプロイを実行しています]** を選択して [マネージド インスタンス] ウィンドウを開き、デプロイの進行状況を詳しく監視します。

> [!IMPORTANT]
> サブネット内の最初のインスタンスの場合、通常、デプロイ時間は後続のインスタンスよりもはるかに長くなります。 デプロイ操作が予想以上に長くなった場合でも、デプロイ操作を取り消さないでください。 サブネット内に 2 つ目のマネージド インスタンスを作成するときにかかる時間はわずか数分です。

## <a name="review-resources-and-retrieve-your-fully-qualified-server-name"></a>リソースの確認と完全修飾サーバー名の取得

デプロイが正常に完了した後は、作成したリソースを確認し、以降のクイック スタートで使用する完全修飾サーバー名を取得します。

1. マネージド インスタンスのリソース グループを開き、「[マネージド インスタンスを作成する](#create-a-managed-instance)」クイック スタートで作成したリソースを表示します。

2. マネージド インスタンスを選択します。

   ![マネージド インスタンスのリソース](./media/sql-database-managed-instance-get-started/resources.png)

3. **[Overview]\(概要)** タブで **[Host]\(ホスト)** プロパティを探し、マネージド インスタンスの完全修飾ホスト アドレスをコピーします。

   ![マネージド インスタンスのリソース](./media/sql-database-managed-instance-get-started/host-name.png)

   名前は、**your_machine_name.a1b2c3d4e5f6.database.windows.net** のようになります。

## <a name="next-steps"></a>次の手順

- マネージド インスタンスへの接続の詳細について、以下をご覧ください。
  - アプリケーションの接続オプションの概要については、[マネージド インスタンスにアプリケーションを接続する](sql-database-managed-instance-connect-app.md)方法に関するページを参照してください。
  - Azure 仮想マシンからマネージド インスタンスに接続する方法を説明したクイック スタートが必要であれば、[Azure 仮想マシン接続](sql-database-managed-instance-configure-vm.md)に関するページを参照してください。
  - ポイント対サイト接続を使用して、オンプレミスのクライアント コンピューターからマネージド インスタンスに接続する方法を示すクイック スタートについては、「[ポイント対サイト接続の構成](sql-database-managed-instance-configure-p2s.md)」を参照してください。
- 既存の SQL Server データベースをオンプレミスからマネージド インスタンスに復元するには、[移行用の Azure Database Migration Service (DMS)](../dms/tutorial-sql-server-to-managed-instance.md) を使用してデータベース バックアップ ファイルから復元するか、[T-SQL RESTORE コマンド](sql-database-managed-instance-get-started-restore.md)を使用してデータベース バックアップ ファイルから復元します。
- 組み込みのトラブルシューティング インテリジェンスを使用したマネージド インスタンス データベースの高度なパフォーマンス監視については、[Azure SQL Analytics を使用した Azure SQL Database の監視](../azure-monitor/insights/azure-sql.md)に関するページを参照してください
