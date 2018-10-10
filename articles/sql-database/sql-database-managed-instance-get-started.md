---
title: 'Azure portal: SQL マネージド インスタンスの作成 | Microsoft Docs'
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
ms.date: 09/23/2018
ms.openlocfilehash: c0c249ffe426e86049024122d9cbf786bb677220
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2018
ms.locfileid: "47160640"
---
# <a name="create-an-azure-sql-database-managed-instance"></a>Azure SQL Database マネージド インスタンスの作成

このクイック スタートでは、Azure portal で Azure SQL Database [マネージド インスタンス](sql-database-managed-instance.md)を作成する方法について説明します。 

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="sign-in-to-the-azure-portal"></a>Azure ポータルにサインインします。

[Azure Portal](https://portal.azure.com/) にサインインします。

## <a name="create-a-managed-instance"></a>マネージド インスタンスを作成する

次の手順に従って、マネージド インスタンスを作成します。

1. Azure Portal の左上隅にある **[リソースの作成]** をクリックします。
2. **[マネージド インスタンス]** を探し、**[Azure SQL Managed Instance]** を選択します。
3. **Create** をクリックしてください。

   ![マネージド インスタンスの作成](./media/sql-database-managed-instance-get-started/managed-instance-create.png)

4. 次の表の情報を参考にして、マネージド インスタンスのフォームに必要な情報を入力します。

   | Setting| 推奨値 | 説明 |
   | ------ | --------------- | ----------- |
   | **サブスクリプション** | 該当するサブスクリプション | 新しいリソースを作成するアクセス許可があるサブスクリプション |
   |**マネージド インスタンス名**|有効な名前|有効な名前については、[名前付け規則と制限事項](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)に関するページを参照してください。|
   |**マネージド インスタンス管理者ログイン**|任意の有効なユーザー名|有効な名前については、[名前付け規則と制限事項](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)に関するページを参照してください。 "serveradmin" は予約済みのサーバー レベルのロールであるため、使用しないでください。| 
   |**パスワード**|有効なパスワード|パスワードは 16 文字以上で、[定義された複雑さの要件](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)を満たす必要があります。|
   |**リソース グループ**|新規または既存のリソース グループ|有効なリソース グループ名については、[名前付け規則と制限](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)に関するページを参照してください。|
   |**場所**|マネージド インスタンスの作成先となる場所|リージョンについては、「[Azure リージョン](https://azure.microsoft.com/regions/)」を参照してください。|
   |**Virtual Network**|**[Create new virtual network]\(新しい仮想ネットワークを作成する)** を選択するか、以前にこのフォームで指定したリソース グループに作成した仮想ネットワークを選択| カスタム設定を使用するマネージド インスタンスの仮想ネットワークを構成する場合は、Github の「[Configure SQL Managed Instance virtual network environment template (SQL マネージド インスタンスの仮想ネットワーク環境テンプレートを構成する)](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment)」を参照。 マネージド インスタンスのネットワーク環境を構成するための要件については、「[Azure SQL Database Managed Instance の VNet を構成する](sql-database-managed-instance-vnet-configuration.md)」を参照 |

   ![マネージド インスタンス フォーム](./media/sql-database-managed-instance-get-started/managed-instance-create-form.png)

5. **[価格レベル]** をクリックしてコンピューティング リソースとストレージ リソースのサイズを指定し、価格レベルのオプションを確認します。 [汎用] 価格レベルでは、32 GB のメモリと 16 仮想コアが既定値となっています。
6. スライダーまたはテキスト ボックスを使用して、記憶域のサイズと仮想コアの数を指定します。 
7. 完了したら **[適用]** をクリックして選択内容を保存します。  
8. **[作成]** をクリックしてマネージド インスタンスをデプロイします。
9. **[通知]** アイコンをクリックしてデプロイの状態を表示します。

    ![マネージド インスタンスのデプロイの進行状況](./media/sql-database-managed-instance-get-started/deployment-progress.png)

10. **[デプロイを実行しています]** をクリックして [マネージド インスタンス] ウィンドウを開き、デプロイの進行状況を詳しく監視します。 

> [!IMPORTANT]
> サブネット内の最初のインスタンスの場合、通常、デプロイ時間は後続のインスタンスよりもはるかに長くなります。 デプロイ操作が予想以上に長くなった場合でも、デプロイ操作を取り消さないでください。 サブネット内に 2 つ目のマネージド インスタンスを作成するときにかかる時間はわずか数分です。

## <a name="review-resources-and-retrieve-your-fully-qualified-server-name"></a>リソースの確認と完全修飾サーバー名の取得

デプロイが正常に完了した後は、作成したリソースを確認し、以降のクイック スタートで使用する完全修飾サーバー名を取得します。

1. マネージド インスタンスのリソース グループを開き、「[マネージド インスタンスを作成する](sql-database-managed-instance-get-started.md)」クイック スタートで作成したリソースを表示します。

   ![マネージド インスタンスのリソース](./media/sql-database-managed-instance-get-started/resources.png)Azure portal で、マネージド インスタンスのリソースを開きます。

2. マネージド インスタンスをクリックします。
3. **[Overview]\(概要)** タブで **[Host]\(ホスト)** プロパティを探し、マネージド インスタンスの完全修飾ホスト アドレスをコピーします。


   ![マネージド インスタンスのリソース](./media/sql-database-managed-instance-get-started/host-name.png)

   名前は、**your_machine_name.neu15011648751ff.database.windows.net** のようになります。

## <a name="next-steps"></a>次の手順

- マネージド インスタンスへの接続の詳細について、以下をご覧ください。
  - アプリケーションの接続オプションの概要については、[マネージド インスタンスにアプリケーションを接続する](sql-database-managed-instance-connect-app.md)方法に関するページを参照してください。
  - Azure 仮想マシンからマネージド インスタンスに接続する方法を説明したクイック スタートが必要であれば、[Azure 仮想マシン接続](sql-database-managed-instance-configure-vm.md)に関するページを参照してください。
  - ポイント対サイト接続を使用して、オンプレミスのクライアント コンピューターからマネージド インスタンスに接続する方法を示すクイック スタートについては、「[ポイント対サイト接続の構成](sql-database-managed-instance-configure-p2s.md)」を参照してください。
- 既存の SQL Server データベースをオンプレミスからマネージド インスタンスに復元するには、[移行用の Azure Database Migration Service (DMS)](../dms/tutorial-sql-server-to-managed-instance.md) を使用してデータベース バックアップ ファイルから復元するか、[T-SQL RESTORE コマンド](sql-database-managed-instance-get-started-restore.md)を使用してデータベース バックアップ ファイルから復元します。
