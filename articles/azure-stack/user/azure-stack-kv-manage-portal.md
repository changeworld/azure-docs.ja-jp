---
title: ポータルを使って Azure Stack の Key Vault を管理する | Microsoft Docs
description: ポータルを使って Azure Stack の Key Vault を管理する方法を説明します。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: D4300668-461F-45F6-BF3B-33B502C39D17
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2018
ms.author: sethm
ms.openlocfilehash: 91035f84d02810d838127ecf6a2f6424ef5df6cf
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2018
ms.locfileid: "41948135"
---
# <a name="manage-key-vault-in-azure-stack-by-using-the-portal"></a>ポータルを使って Azure Stack の Key Vault を管理する

Azure Stack ポータルを使用して Azure Stack の Key Vault を管理できます。 この記事では、Azure Stack で Key Vault を作成および管理する手順について説明します。

## <a name="prerequisites"></a>前提条件

Azure Key Vault サービスを含むプランをサブスクライブする必要があります。

## <a name="create-a-key-vault"></a>Key Vault を作成します

1. [ユーザー ポータル](https://portal.local.azurestack.external)にサインインします。

2. ダッシュボードで、**[新規]** > **[セキュリティ + ID]** > **[Key Vault]** の順に選択します。

    ![Key Vault 画面](media/azure-stack-kv-manage-portal/image1.png)

3. **[Key Vault の作成]** ウィンドウで、Vault に**名前**を割り当てます。 Vault の名前には、英数字と特殊文字のハイフン (-) のみを含めることができます。 名前を数字で始めることはできません。

4. 使用可能なサブスクリプションの一覧から**サブスクリプション**を選択します。 Key Vault サービスを提供するすべてのサブスクリプションがドロップダウン リストに表示されます。

5. 既存の**リソース グループ**を選択するか、新しいリソース グループを作成します。

6. **[価格レベル]** を選択します。
    >[!NOTE]
    > Azure Stack Development Kit の Key Vault では、**Standard** SKU のみがサポートされます。

7. 既存の**アクセス ポリシー**のいずれかを選択するか、新しいアクセス ポリシーを作成します。 アクセス ポリシーでは、この Vault で操作を実行する権限を、ユーザー、アプリケーション、またはセキュリティ グループに付与できます。

8. 必要に応じて、**[高度なアクセス ポリシー]** を選んで機能へのアクセスを有効にします。 例: デプロイのための仮想マシン (VM)、テンプレートのデプロイのための Resource Manager、ボリューム暗号化のための Azure Disk Encryption へのアクセス。

9. 設定を構成したら、**[OK]** を選択し、**[作成]** を選択します。 これにより、キー コンテナーのデプロイが開始されます。

## <a name="manage-keys-and-secrets"></a>キーとシークレットの管理

キー コンテナーを作成したら、次の手順を実行し、キー コンテナーでキーとシークレットを作成および管理します。

### <a name="create-a-key"></a>キーの作成

1. [ユーザー ポータル](https://portal.local.azurestack.external)にサインインします。

2. ダッシュボードで **[すべてのリソース]** をクリックし、先ほど作成した Key Vault を選択して、**[キー]** タイルを選択します。

3. **[キー]** ウィンドウで **[追加]** を選択します。

4. **[キーの作成]** ウィンドウで、**[オプション]** 一覧から、キーの作成に使用する方法を選択します。 新しいキーを**生成**したり、既存のキーを**アップロード**したり、キーのバックアップを選択して**バックアップを復元**したりできます。

5. キーの**名前**を入力します。 キーの名前には、英数字と特殊文字のハイフン (-) のみを含めることができます。

6. 必要に応じて、キーの **[アクティブ化する日を設定する]** と **[有効期限を設定する]** の値を構成します。

7. **[作成]** を選択してデプロイを開始します。

キーが正常に作成されたら、**[キー]** で選択してプロパティを表示したり変更したりできます。 プロパティ セクションには **[キー識別子]** が含まれます。これは、外部アプリケーションがこのキーにアクセスするために使う Uniform Resource Identifier (URI) です。 このキーでの操作を制限するには、**[許可された操作]** で設定を構成します。

![キーの URI](media/azure-stack-kv-manage-portal/image4.png)

### <a name="create-a-secret"></a>シークレットの作成

1. [ユーザー ポータル](https://portal.local.azurestack.external)にサインインします。
2. ダッシュボードで **[すべてのリソース]** を選択し、先ほど作成した Key Vault を選択して、**[シークレット]** タイルを選択します。

3. **[シークレット]** で **[追加]** を選択します。

4. **[シークレットの作成]** で、**[アップロード オプション]** 一覧から、シークレットの作成に使用するオプションを選択します。 シークレットの値を入力して**手動で**シークレットを作成したり、お使いのローカル コンピューターから**証明書**をアップロードしてシークレットを作成したりできます。

5. シークレットの**名前**を入力します。 シークレットの名前には、英数字と特殊文字のハイフン (-) のみを含めることができます。

6. 必要に応じて、**コンテンツの種類**を指定し、シークレットの **[アクティブ化する日を設定する]** と **[有効期限を設定する]** の値を構成します。

7. **[作成]** を選択してデプロイを開始します。

シークレットが正常に作成されたら、**[シークレット]** で選択してプロパティを表示したり変更したりできます。 **[シークレット識別子]** は、外部アプリケーションがこのシークレットへのアクセスに使用できる URI です。

![シークレットの URI](media/azure-stack-kv-manage-portal/image5.png)

## <a name="next-steps"></a>次の手順

* [Key Vault に格納されているパスワードを取得して VM をデプロイする](azure-stack-kv-deploy-vm-with-secret.md)
* [キー コンテナーに格納されている証明書を使用した VM のデプロイ](azure-stack-kv-push-secret-into-vm.md)
