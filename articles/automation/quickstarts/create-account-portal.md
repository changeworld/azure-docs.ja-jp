---
title: クイックスタート - ポータルを使用して Azure Automation アカウントを作成する
description: このクイックスタートでは、ポータルを使用して Azure Automation アカウントの作成を開始する方法を説明します
services: automation
ms.date: 09/07/2021
ms.topic: quickstart
ms.subservice: process-automation
ms.custom: mvc
ms.openlocfilehash: f0f93a43fd74ba38b8be17636b695e287354ed5a
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129218277"
---
# <a name="quickstart-create-an-automation-account-using-the-azure-portal"></a>クイックスタート: Azure portal を使用して Automation アカウントを作成する

Azure [Automation アカウント](../automation-security-overview.md)は、さまざまなリソースにアクセスできるブラウザーベースのユーザー インターフェイスである Azure portal を使用して作成できます。 1 つの Automation アカウントで、特定のテナントのすべてのリージョンおよびサブスクリプションにわたってリソースを管理できます。 このクイックスタートでは、Automation アカウントを作成する手順について説明します。

## <a name="prerequisites"></a>前提条件

アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-automation-account"></a>Automation アカウントを作成する

1. [Azure portal](https://portal.azure.com) にサインインします。

1. 上部のメニューで、 **[+ リソースの作成]** を選択します。

1. **[カテゴリ]** で、 **[IT & Management Tools]\(IT & 管理ツール\)** 、 **[Automation]** の順に選択します。

   :::image type="content" source="./media/create-account-portal/automation-account-portal.png" alt-text="ポータルでの Automation アカウントの検索。":::

1. **[Automation アカウントの追加]** ページで、次の情報を指定します。

   | プロパティ | 説明 |
   |---|---|
   |名前| 場所とリソース グループに対して一意である名前を入力します。 削除された Automation アカウントの名前はすぐには使用できない場合があります。 一度ユーザー インターフェイスで入力されたアカウント名を変更することはできません。 |
   |サブスクリプション| ドロップダウン リストから、アカウントの Azure サブスクリプションを選択します。|
   |Resource group|ドロップダウン リストから既存のリソース グループを選択するか、 **[新規作成]** を選択します。|
   |場所| ドロップダウン リストから、アカウントの場所を選択します。 Automation アカウントをデプロイできる場所の最新の一覧については、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?products=automation&regions=all)」を参照してください。|
   |Azure 実行アカウントの作成| このため、 **[いいえ]** を選択します。  Automation アカウントの Azure 実行アカウントは Azure での認証に役立ちます。ただし、Automation のマネージド ID が使用できるようになっています。 [マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) により、Azure Active Directory (Azure AD) 認証をサポートするリソースに接続するときに使用される ID がアプリケーションに提供されます。 |

   :::image type="content" source="./media/create-account-portal/add-automation-account-portal.png" alt-text="Automation アカウントを追加するために必要なフィールド":::

1. **[作成]** を選択して、Automation アカウントのデプロイを開始します。 作成は約 1 分で完了します。

1. デプロイが完了すると、通知を受け取ります。 通知で **[リソースに移動]** を選択し、 **[Automation アカウント]** ページを開きます。

1. 新しい Automation アカウントを確認します。

   :::image type="content" source="./media/create-account-portal/automation-account-overview.png" alt-text="Automation アカウントの概要ページ":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Automation アカウントを引き続き使用しない場合は、 **[概要]** ページで **[削除]** を選択し、確認のメッセージが表示されたら **[はい]** を選択します。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Automation アカウントを作成しました。 Automation アカウントでマネージド ID を使用するには、次のクイックスタートに進んでください。

> [!div class="nextstepaction"]
> [クイックスタート - マネージド ID を有効にする](enable-managed-identity.md)

