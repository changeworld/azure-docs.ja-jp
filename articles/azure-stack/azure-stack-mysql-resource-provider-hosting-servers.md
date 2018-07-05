---
title: Azure Stack での MySQL ホスティング サーバー | Microsoft Docs
description: MySQL アダプター リソースプロバイダーを使用したプロビジョニングのために MySQL インスタンスを追加する方法
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
ms.date: 06/25/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 5522eb1b8b0398aeb6f1b0dd8578b906880b4e89
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/25/2018
ms.locfileid: "36939656"
---
# <a name="add-hosting-servers-for-the-mysql-resource-provider"></a>MySQL リソース プロバイダー用のホスティング サーバーの追加

MySQL リソース プロバイダーが SQL インスタンスに接続できる限り、その MySQL インスタンスを、[Azure Stack](azure-stack-poc.md) 内の仮想マシン (VM) 上、または Azure Stack 環境の外部にある VM 上でホストできます。

## <a name="connect-to-a-mysql-hosting-server"></a>MySQL ホスティング サーバーに接続する

システム管理特権を持つアカウントの資格情報があることを確認してください。 ホスティング サーバーを追加するには、次の手順に従います。

1. Azure Stack オペレーター ポータルにサービス管理者としてサインインします
2. **[そのほかのサービス]** を選択します。
3. **[管理リソース]** > **[MySQL ホスティング サーバー]** > **[+追加]** を選択します。 これにより、次の画面キャプチャに示される **[Add a MySQL Hosting Server]\(MySQL ホスティング サーバーの追加\)** ダイアログが開きます。

   ![ホスティング サーバーを構成する](./media/azure-stack-mysql-rp-deploy/mysql-add-hosting-server-2.png)

4. MySQL サーバー インスタンスの接続詳細を指定します。

   * **[MySQL Hosting Server Name]\(MySQL ホスティング サーバー名\)** では、完全修飾ドメイン名 (FQDN) または有効な IPv4 アドレスを指定します。 短い VM 名は使用しないでください。
   * 既定の MySQL インスタンスが指定されていないため、**[ホスティング サーバーのサイズ (GB)]** を指定する必要があります。 データベース サーバーの容量に近いサイズを入力します。
   * **[サブスクリプション]** の既定の設定のままにします。
   * **[リソース グループ]** で、新しいリソース グループを作成するか、既存のグループを使用します。

   > [!NOTE]
   > テナントと管理者の Azure Resource Manager から MySQL インスタンスにアクセスできる場合は、そのインスタンスをリソース プロバイダーの管理下に置くことができます。 ただし、MySQL インスタンスは、リソース プロバイダーに排他的に割り当てる**必要があります**。

5. **[SKU]** を選択して **[Create SKU]\(SKU の作成\)** ダイアログを開きます。

   ![MySQL SKU を作成する](./media/azure-stack-mysql-rp-deploy/mysql-new-sku.png)

   ユーザーが自分のデータベースを適切な SKU にデプロイできるように、SKU の **[名前]** には SKU のプロパティが反映される必要があります。

   >[!IMPORTANT]
   >MySQL リソース プロバイダーの SKU を作成する際、**[名前]** または **[レベル]** では、スペースやピリオドなどの特殊文字はサポートされていません。

6. **[OK]** を選択して SKU を作成します。
7. **[Add a MySQL Hosting Server]\(MySQL ホスティング サーバーの追加\)** の下で、**[作成]** を選択します。

サーバーを追加する際に、サービス内容を区別するために新規または既存の SKU に割り当てます。 たとえば、増加したデータベースと自動バックアップを提供する MySQL エンタープライズ インスタンスを使用できます。 この高パフォーマンス サーバーは、組織内のさまざまな部門用に予約できます。

## <a name="increase-backend-database-capacity"></a>バックエンド データベース容量を増やす

バックエンド データベース容量を増加するには、Azure Stack ポータルで追加で MySQL サーバーをデプロイします。 これらのサーバーを新規または既存の SKU に追加します。 サーバーを既存の SKU に追加する場合は、サーバーの特性が SKU 内の他のサーバーと同じであることを確認してください。

## <a name="make-mysql-database-servers-available-to-your-users"></a>ユーザーが MySQL データベース サーバーを使用できるようにする

ユーザーが MySQL データベース サーバーを使用できるようにプランとオファーを作成します。 プランに Microsoft.MySqlAdapter サービスを追加した後、既定のクォータを追加するか、新しいクォータを作成します。

![データベースのプランとオファーを作成する](./media/azure-stack-mysql-rp-deploy/mysql-new-plan.png)

## <a name="next-steps"></a>次の手順

[MySQL データベースの作成](azure-stack-mysql-resource-provider-databases.md)