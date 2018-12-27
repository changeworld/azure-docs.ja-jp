---
title: Azure Stack 上で MySQL アダプター RP によって指定されたデータベースを使用する | Microsoft Docs
description: MySQL アダプター リソースプロバイダーを使用してプロビジョニングした MySQL データベースを作成し管理する方法
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
ms.date: 10/16/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.openlocfilehash: 78c4e7c6cdc8cf118a351d9030416318e51c69af
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2018
ms.locfileid: "49365103"
---
# <a name="create-mysql-databases"></a>MySQL データベースの作成

ユーザー ポータルで、セルフサービス データベースを作成して管理できます。 Azure Stack ユーザーは、MySQL データベース サービスを含むオファーがあるサブスクリプションが必要です。

## <a name="test-your-deployment-by-creating-a-mysql-database"></a>MySQL データベースを作成してデプロイをテストする

1. Azure Stack ユーザー ポータルにサインインします。
2. **[+ リソースの作成]** > **[データ + ストレージ]** > **[MySQL データベース]** > **[追加]** の順に選択します。
3. **[MySQL Database の作成]** でデータベース名を入力し、環境で必要な他の設定を構成します。

    ![テスト MySQL データベースの作成](./media/azure-stack-mysql-rp-deploy/mysql-create-db.png)

4. **[データベースの作成]** で、**[SKU]** を選択します。 **[MySQL SKU の選択]** で、データベースで使用する SKU を選択します。

    ![MySQL SKU を選択する](./media/azure-stack-mysql-rp-deploy/mysql-select-sku.png)

    >[!Note]
    >ホスティング サーバーは、Azure Stack に追加されるときに SKU が割り当てられます。 データベースは、ホスティング サーバー プールの SKU 内に作成されます。

5. **[ログイン]** で ***[必要な設定の構成]*** を選択します。
6. **[ログインの選択]** で、既存のログインを選択するか、**[+ 新しいログインの作成]** を選択します。  **[データベース ログイン]** 名と **[パスワード]** を入力して **[OK]** を選択します。

    ![新しいデータベース ログインの作成](./media/azure-stack-mysql-rp-deploy/create-new-login.png)

    >[!NOTE]
    >MySQL 5.7 で使用できるデータベース ログイン名は 32 文字以内です。 前のエディションでは、16 文字を超えることはできません。

7. **[作成]** を選択してデータベースの設定を完了します。

データベースの配置後、**[要点]** に表示される **[接続文字列]** の情報を書き留めます。 この文字列は、MySQL データベースにアクセスする必要があるすべてのアプリケーションで使用できます。

![MySQL データベースの接続文字列の取得](./media/azure-stack-mysql-rp-deploy/mysql-db-created.png)

## <a name="update-the-administrative-password"></a>管理パスワードの更新

パスワードは、MySQL サーバー インスタンス上で変更することで変更できます。

1. **[管理リソース]** > **[MySQL ホスティング サーバー]** を選択します。 ホスティング サーバーを選択します。
2. **[設定]** で **[パスワード]** を選択します。
3. **[パスワード]** に新しいパスワードを入力し、**[保存]** を選択します。

![管理パスワードの更新](./media/azure-stack-mysql-rp-deploy/mysql-update-password.png)

## <a name="next-steps"></a>次の手順

[MySQL リソースプロバイダーを更新する](azure-stack-mysql-resource-provider-update.md)
