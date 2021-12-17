---
title: Synapse ワークスペースのマネージド ID にアクセス許可を付与する
description: Azure Synapse ワークスペースのマネージド ID のアクセス許可を構成する方法について説明する記事。
author: meenalsri
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: mesrivas
ms.reviewer: jrasnick
ms.custom: subject-rbac-steps
ms.openlocfilehash: 009f611e1c80f4c4e0e7bafaa80036d809b3393d
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131853075"
---
# <a name="grant-permissions-to-workspace-managed-identity"></a>ワークスペースのマネージド ID にアクセス許可を付与する

この記事では、Azure Synapse ワークスペースのマネージド ID にアクセス許可を付与する方法について説明します。 そして、アクセス許可によって、Azure portal からワークスペース内の専用 SQL プールや ADLS Gen2 ストレージ アカウントにアクセスできるようになります。

>[!NOTE]
>このドキュメントの残りの部分では、このワークスペース マネージド ID をマネージド ID と呼びます。

## <a name="grant-the-managed-identity-permissions-to-adls-gen2-storage-account"></a>マネージド ID に ADLS Gen2 ストレージ アカウントへのアクセス許可を付与する

Azure Synapse ワークスペースの作成には、ADLS Gen2 ストレージ アカウントが必要です。 Azure Synapse ワークスペースで Spark プールを正常に起動するには、Azure Synapse マネージド ID に、このストレージ アカウントに対する *ストレージ BLOB データ共同作成者* ロールが必要です。 Azure Synapse のパイプライン オーケストレーションでも、このロールが役立ちます。

### <a name="grant-permissions-to-managed-identity-during-workspace-creation"></a>ワークスペースの作成中にマネージド ID にアクセス許可を付与する

Azure portal を使用して Azure Synapse ワークスペースが作成された後、Azure Synapse では、ストレージ BLOB データ共同作成者ロールをマネージド ID に付与しようとします。 ADLS Gen2 ストレージ アカウントの詳細は、 **[基本]** タブで入力します。

![ワークスペース作成フローの [基本] タブ](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-1.png)

**[アカウント名]** と **[ファイルシステム名]** で ADLS Gen2 ストレージ アカウントとファイルシステムをそれぞれ選択します。

![ADLS Gen2 ストレージ アカウントの詳細の入力](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-2.png)

ワークスペース作成者が ADLS Gen2 ストレージ アカウントの **所有者** でもある場合、Azure Synapse では、"*ストレージ BLOB データ共同作成者*" ロールをマネージド ID に割り当てます。 入力したストレージ アカウントの詳細の下に、次のメッセージが表示されます。

![ストレージ BLOB データ共同作成者の割り当てに成功](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-3.png)

ワークスペース作成者が ADLS Gen2 ストレージ アカウントの所有者でない場合、Azure Synapse では、*ストレージ BLOB データ共同作成者* ロールをマネージド ID に割り当てません。 ストレージ アカウントの詳細の下に表示されるメッセージは、*ストレージ BLOB データ共同作成者* ロールをマネージド ID に付与するための十分なアクセス許可がないことをワークスペース作成者に通知します。

![ストレージ BLOB データ共同作成者の割り当てに失敗](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-4.png)

メッセージに示されているように、*ストレージ BLOB データ共同作成者* がマネージド ID に割り当てられていない限り、Spark プールを作成することはできません。

### <a name="grant-permissions-to-managed-identity-after-workspace-creation"></a>ワークスペースの作成後にマネージド ID にアクセス許可を付与する

ワークスペースの作成中に、"*ストレージ BLOB データ共同作成者*" をマネージド ID に割り当てていない場合は、ADLS Gen2 ストレージ アカウントの **所有者** が手動でそのロールを ID に割り当てます。 次の手順は、手動での割り当てを行う場合に役立ちます。

#### <a name="step-1-navigate-to-the-adls-gen2-storage-account-in-azure-portal"></a>手順 1:Azure portal で ADLS Gen2 ストレージ アカウントに移動する

Azure portal で、ADLS Gen2 ストレージ アカウントを開き、左側のナビゲーションから **[概要]** を選択します。 *ストレージ BLOB データ共同作成者* ロールは、コンテナーまたはファイルシステム レベルで割り当てる必要があります。 **[コンテナー]** を選択します。  
![ADLS Gen2 ストレージ アカウントの概要](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-5.png)

#### <a name="step-2-select-the-container"></a>手順 2:コンテナーを選択する

マネージド ID には、ワークスペースの作成時に指定したコンテナー (ファイルシステム) へのデータ アクセスが必要です。 このコンテナーまたはファイルシステムは Azure portal で確認できます。 Azure portal で Azure Synapse ワークスペースを開き、左側のナビゲーションから **[概要]** タブを選択します。
![ADLS Gen2 ストレージ アカウントのコンテナー](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-7.png)


それと同じコンテナーまたはファイルシステムを選択して、*ストレージ BLOB データ共同作成者* ロールをマネージド ID に付与します。
![選択する必要があるコンテナーまたはファイル システムを示すスクリーンショット。](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-6.png)

#### <a name="step-3-open-access-control-and-add-role-assignment"></a>手順 3: アクセス制御を開き、ロールの割り当てを追加するする

1. **[アクセス制御 (IAM)]** を選択します。

1. **[追加]**  >  **[ロールの割り当ての追加]** を選択して、[ロールの割り当ての追加] ページを開きます。

1. 次のロールを割り当てます。 詳細な手順については、「[Azure portal を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-portal.md)」を参照してください。
    
    | 設定 | 値 |
    | --- | --- |
    | Role | ストレージ BLOB 共同作成者 |
    | アクセスの割り当て先 | マネージド ID |
    | メンバー | マネージド ID 名  |

    > [!NOTE]
    > マネージド ID の名前は、ワークスペース名でもあります。

    ![Azure portal でロール割り当てページを追加します。](../../../includes/role-based-access-control/media/add-role-assignment-page.png)

1. **[保存]** を選択して、ロールの割り当てを追加します。

#### <a name="step-9-verify-that-the-storage-blob-data-contributor-role-is-assigned-to-the-managed-identity"></a>手順 9:ストレージ BLOB データ共同作成者ロールがマネージド ID に割り当てられていることを確認する

**[アクセス制御 (IAM)]** を選択してから、 **[ロールの割り当て]** を選択します。

![ロールの割り当てを確認する](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-14.png)

**[ストレージ BLOB データ共同作成者]** セクションの下に、*ストレージ BLOB データ共同作成者* ロールが割り当てられているマネージド ID が表示されているのを確認できます。 
![ADLS Gen2 ストレージ アカウントのコンテナーの選択](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-15.png)

## <a name="next-steps"></a>次のステップ

[ワークスペースのマネージド ID](../../data-factory/data-factory-service-identity.md?context=/azure/synapse-analytics/context/context&tabs=synapse-analytics) の詳細を確認する
