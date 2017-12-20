---
title: "Azure Portal で Batch アカウントを作成する | Microsoft Docs"
description: "Azure ポータルで、クラウド内で大規模な並列ワークロードを実行する Azure Batch アカウントを作成する方法について説明します"
services: batch
documentationcenter: 
author: v-dotren
manager: timlt
editor: 
ms.assetid: 3fbae545-245f-4c66-aee2-e25d7d5d36db
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/14/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ebda2f11f93b04a5592d18f8e15c8fc3b560aac3
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/18/2017
---
# <a name="create-a-batch-account-with-the-azure-portal"></a>Azure Portal で Batch アカウントを作成する

> [!div class="op_single_selector"]
> * [Azure ポータル](batch-account-create-portal.md)
> * [Batch Management .NET](batch-management-dotnet.md)
>
>

[Azure Portal][azure_portal] で Azure Batch アカウントを作成し、自分のコンピューティング シナリオに適したアカウント プロパティを選ぶ方法について説明します。 アクセス キーやアカウント URL のような重要なアカウント プロパティを確認できる場所を紹介します。

Batch アカウントとシナリオの背景情報については、[機能の概要](batch-api-basics.md)に関するページを参照してください。



## <a name="create-a-batch-account"></a>Batch アカウントを作成する

> [!NOTE]
> Batch アカウントを作成する際には、通常、既定の **Batch サービス** モードを選択することをお勧めします。このモードでは、Azure で管理されたサブスクリプションにバックグラウンドでプールが割り当てられます。 別の**ユーザー サブスクリプション** モード (現在は、ほとんどのシナリオで非推奨) では、プールの作成時に、Batch VM とその他のリソースがサブスクリプションに直接作成されます。 また、ユーザー サブスクリプション モードで Batch アカウントを作成するには、ご利用のサブスクリプションを Azure Batch に登録し、アカウントを Azure Key Vault に関連付ける必要があります。

1. [Azure Portal][azure_portal] にサインインします。
2. **[新規]** をクリックして、Marketplace で **[Batch サービス]** を検索します。

    ![Marketplace での Batch][marketplace_portal]
3. **[Batch サービス]** を選択し、**[作成]** をクリックして、**[新しい Batch アカウント]** の設定を入力します。 次の詳細を確認してください。

    ![Batch アカウントを作成する][account_portal]

    a. **[アカウント名]**: 選択する名前は、アカウントが作成される Azure リージョン内で一意である必要があります (下の **[場所]** を参照)。 アカウント名に含めることができるのは、英小文字と数字のみで、文字数は 3 ～ 24 文字にする必要があります。

    b. **サブスクリプション**: Batch アカウントを作成するサブスクリプション。 サブスクリプションが 1 つのみの場合は、既定でそのサブスクリプションが選択されます。

    c. **[プール割り当てモード]**: この設定が表示される場合は、既定の **[Batch サービス]** をそのまま使用してください。

    c. **リソース グループ**: 新しい Batch アカウント用の既存のリソース グループを選択します。必要に応じて、新しく作成することもできます。

    d. **場所**: Batch アカウントを作成する Azure リージョン。 サブスクリプションとリソース グループでサポートされているリージョンのみがオプションとして表示されます。

    e. **ストレージ アカウント** (省略可能): Batch アカウントに関連付ける汎用の Azure Storage アカウント。 ほとんどの Batch アカウントでこれをお勧めします。 詳細については、この記事で後に出てくる「[リンクされた Azure ストレージ アカウント](#linked-azure-storage-account)」を参照してください。

4. **[作成]** をクリックしてアカウントを作成します。



## <a name="view-batch-account-properties"></a>Batch アカウントのプロパティを表示する
アカウントが作成されたら、そのアカウントをクリックして設定とプロパティにアクセスします。 左側のメニューを使用すると、アカウントの設定およびプロパティすべてにアクセスできます。

![Azure Portal の [Batch アカウント] ブレード][account_blade]

* **Batch アカウント URL**: [Batch API](batch-apis-tools.md#azure-accounts-for-batch-development) を使用してアプリケーションを開発する場合は、Batch リソースにアクセスするためにアカウント URL が必要になります。 Batch アカウント URL の形式を次に示します。

    `https://<account_name>.<region>.batch.azure.com`

![Batch account URL in portal][account_url]

* **アクセス キー**: アプリケーションから Batch アカウントへのアクセスを認証するには、アカウント アクセス キーを使用できます  (Batch では Azure Active Directory 認証もサポートされます)。

    アクセス キーを表示または再生成するには、**[キー]** を選択します。

    ![Batch account keys in Azure portal][account_keys]

[!INCLUDE [batch-pricing-include](../../includes/batch-pricing-include.md)]

## <a name="linked-azure-storage-account"></a>リンクされた Azure Storage アカウント

汎用の Azure ストレージ アカウントを Batch アカウントにリンクすることができます。これは、多くのシナリオで役に立ちます。 Batch の[アプリケーション パッケージ](batch-application-packages.md)機能は、[Batch File Conventions .NET](batch-task-output.md) ライブラリと同様に、Azure Blob Storage を使用します。 これらのオプション機能は、Batch タスクで実行するアプリケーションのデプロイや、そのアプリケーションによって生成されるデータの保持に役立ちます。

Batch アカウント専用として使用する新しいストレージ アカウントを作成することをお勧めします。 Azure Batch で現在サポートされているのは、汎用的なストレージ アカウントの種類のみです。 このアカウントの種類については、「[Azure ストレージ アカウントについて](../storage/common/storage-create-storage-account.md)」の「[ストレージ アカウントの作成](../storage/common/storage-create-storage-account.md#create-a-storage-account)」の手順 5. で説明されています。

![汎用ストレージ アカウントの作成][storage_account]

> [!NOTE]
> リンクされた Storage アカウントのアクセス キーを再生成する際は次の点に注意してください。 Storage アカウントのキーは 1 つだけ再生成し、リンクされた Storage アカウントのページにある **[キーの同期]** をクリックします。 キーがプール内のコンピューティング ノードに反映されるまで 5 分待ってから、必要に応じて他のキーの再生成と同期を行います。 両方のキーを同時に再生成すると、コンピューティング ノードはどちらのキーも同期できず、Storage アカウントにアクセスできなくなります。
>
>

![ストレージ アカウント キーの再生成][4]

## <a name="additional-configuration-for-user-subscription-mode"></a>ユーザー サブスクリプション モードのための追加構成

ユーザー サブスクリプション モードで Batch アカウントを作成することを選択した場合は、アカウントを作成する前に別途、次の手順を実行してください。

### <a name="allow-azure-batch-to-access-the-subscription-one-time-operation"></a>Azure Batch によるサブスクリプションへのアクセスを許可する (1 回限りの操作)
ユーザー サブスクリプション モードで Batch アカウントを初めて作成する場合は、Batch にサブスクリプションを登録する必要があります  (既に実行済みの場合は、次のセクションに移ってください)。

1. [Azure Portal][azure_portal] にサインインします。

2. **[その他のサービス]** > **[サブスクリプション]** の順にクリックし、Batch アカウントに使用するサブスクリプションをクリックします。

3. **[サブスクリプション]** ページで、**[アクセス制御 (IAM)]** > **[追加]** の順にクリックします。

    ![サブスクリプションのアクセスの制御][subscription_access]

4. **[アクセス許可の追加]** ページで、**[共同作成者]** ロールを選択し、Batch API を探します。 API が見つかるまで、次の各文字列を検索します。
    1. **MicrosoftAzureBatch**。
    2. **Microsoft Azure Batch**。 新しい Azure AD テナントでは、この名前が使用される場合があります。
    3. **ddbf3205-c6bd-46ae-8127-60eb93363864** は Batch API の ID です。 

5. Batch API を見つけたら選択して、**[保存]** をクリックします。

    ![Batch のアクセス許可を追加する][add_permission]

### <a name="create-a-key-vault"></a>Key Vault を作成します
ユーザー サブスクリプション モードでは、作成する Batch アカウントと同じリソース グループに属する Azure キー コンテナーが必要です。 Batch を[利用でき](https://azure.microsoft.com/regions/services/)、お使いのサブスクリプションでサポートされているリージョンにそのリソース グループが属していることを確認してください。

1. [Azure Portal][azure_portal] で、**[新規]** > **[セキュリティ + ID]** > **[Key Vault]** の順にクリックします。

2. **[Key Vault の作成]** ページで、キー コンテナーの名前を入力し、Batch アカウント用のリージョンでリソース グループを作成します。 残りの設定については既定値のままにして、**[作成]** をクリックします。




## <a name="batch-service-quotas-and-limits"></a>Batch サービスのクォータと制限
Azure サブスクリプションやその他の Azure サービスと同様に、Batch アカウントにも特定の[クォータと制限](batch-quota-limit.md)が適用されます。 Batch アカウントの現在のクォータは、**[クォータ]** に表示されます。

![Batch account quotas in Azure portal][quotas]



また、これらのクォータの多くは、Azure Portal で無料の製品サポート要求を送信して増やすことができます。 クォータ引き上げ要求の詳細については、「 [Azure Batch サービスのクォータと制限](batch-quota-limit.md) 」を参照してください。

## <a name="other-batch-account-management-options"></a>その他の Batch アカウント管理オプション
Azure Portal を利用する方法に加えて、次の方法で Batch アカウントを作成および管理できます。

* [Batch PowerShell コマンドレット](batch-powershell-cmdlets-get-started.md)
* [Azure CLI](batch-cli-get-started.md)
* [Batch Management .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>次のステップ
* Batch サービスの概念と機能の詳細については、[Batch 機能の概要](batch-api-basics.md)に関するページを参照してください。 この記事では、プール、コンピューティング ノード、ジョブ、タスクなど、主要な Batch リソースについて説明するほか、大規模なコンピューティング ワークロード向けのこのサービスの機能の概要について説明します。
* [Batch .NET クライアント ライブラリ](batch-dotnet-get-started.md)または [Python](batch-python-tutorial.md) を使用した Batch 対応アプリケーションの開発に関する基本事項を確認してください。 これらの入門記事では、Batch サービスを使用して複数のコンピューティング ノードでワークロードを実行する実用アプリケーションの開発手順を説明しています。また、Azure Storage を使用してワークロード ファイルのステージングと取得を行う方法についても取り上げています。

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[4]: ./media/batch-account-create-portal/batch_acct_04.png "ストレージ アカウント キーの再生成"
[marketplace_portal]: ./media/batch-account-create-portal/marketplace_batch.PNG
[account_blade]: ./media/batch-account-create-portal/batch_blade.png
[account_portal]: ./media/batch-account-create-portal/batch_acct_portal.png
[account_keys]: ./media/batch-account-create-portal/account_keys.PNG
[account_url]: ./media/batch-account-create-portal/account_url.png
[storage_account]: ./media/batch-account-create-portal/storage_account.png
[quotas]: ./media/batch-account-create-portal/quotas.png
[subscription_access]: ./media/batch-account-create-portal/subscription_iam.png
[add_permission]: ./media/batch-account-create-portal/add_permission.png

