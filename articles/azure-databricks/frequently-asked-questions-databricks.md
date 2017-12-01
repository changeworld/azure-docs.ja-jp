---
title: "Azure Databricks: 一般的な質問とヘルプ | Microsoft Docs"
description: "Azure Databricks に関する一般的な質問の回答とトラブルシューティング情報を確認します。"
services: azure-databricks
documentationcenter: 
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/17/2017
ms.author: nitinme
ms.openlocfilehash: 6bb542537ec713be272f7e58e0b247763214ef4a
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2017
---
# <a name="azure-databricks-preview-common-questions-and-help"></a>Azure Databricks プレビュー: 一般的な質問とヘルプ

この記事には、Azure Databricks に関してよく寄せられる質問が記載されています。 また、Azure Databricks の使用中に発生する可能性のある一般的な問題についても記載されています。 Azure Databricks の詳細については、「[Azure Databricks とは](what-is-azure-databricks.md)」を参照してください。 

## <a name="common-questions"></a>一般的な質問

このセクションでは、Azure Databricks に関する一般的な質問をまとめています。

### <a name="can-i-use-my-own-keys-for-local-encryption"></a>ローカル暗号化に独自のキーを使用できますか。 
現在のリリースでは、Azure Keyvault の独自のキーを使用することはサポートされていません。 

### <a name="can-i-use-azure-vnets-with-azure-databricks"></a>Azure Databricks で Azure VNET を使用できますか。
Azure Databricks プロビジョニングの一環として、新しい VNET が作成されます。 このリリースの一部として、独自の Azure VNET を使用することはできません。

### <a name="how-do-i-access-azure-data-lake-store-from-a-notebook"></a>ノートブックから Azure Data Lake Store にアクセスするにはどうすればよいですか。 

1. Azure Active Directory でサービス プリンシパルをプロビジョニングし、そのキーを記録します。
2. Azure Data Lake Store のサービス プリンシパルに必要なアクセス許可を割り当てます。
3. Azure Data Lake Store 内のファイルにアクセスするには、ノートブックでこのサービス プリンシパルの資格情報を使用します。

詳細については、[Azure Databricks での Data Lake Store の使用](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-storage.html#azure-data-lake-store)に関するページを参照してください。

## <a name="troubleshooting"></a>トラブルシューティング

このセクションでは、Azure Databricks に関する一般的な問題のトラブルシューティングを行う方法について説明します。

### <a name="issue-this-subscription-is-not-registered-to-use-the-namespace-microsoftdatabricks"></a>問題: このサブスクリプションが名前空間 ‘Microsoft.Databricks’ を使用するように登録されていない

**エラー メッセージ**

このサブスクリプションは名前空間 ‘Microsoft.Databricks’ を使用するように登録されていません。 サブスクリプションを登録する方法については https://aka.ms/rps-not-found をご覧ください。 (コード: MissingSubscriptionRegistration)

**ソリューション**

1. [Azure ポータル](https://portal.azure.com)に移動します。
2. **[サブスクリプション]** をクリックし、使用しているサブスクリプションをクリックして、**[リソースプロバイダー]** をクリックします。 
3. リソースプロバイダーの一覧で、**[Microsoft.Databricks]** に対して **[登録]** をクリックします。 リソースプロバイダーを登録するには、サブスクリプションの共同作成者または所有者のロールが必要です。


### <a name="issue-your-account-email-does-not-have-owner-or-contributor-role-on-the-databricks-workspace-resource-in-the-azure-portal"></a>問題: アカウント (電子メール) に Azure Portal の Databricks ワークスペース リソースの所有者または共同作成者ロールがない

**エラー メッセージ**

アカウント (電子メール) に、Azure Portal の Databricks ワークスペース リソースの所有者または共同作成者ロールがありません。 テナントのゲスト ユーザーである場合も、このエラーが発生する可能性があります。 アクセスを許可するか Databricks ワークスペースに直接ユーザーとして追加するよう、管理者に依頼してください。 

**ソリューション**

この問題に対するいくつかの解決策を次に示します。

* テナントを初期化するには、ゲスト ユーザーではなく、テナントの通常のユーザーとしてログインする必要があります。 Databricks ワークスペース リソースの共同作成者ロールも必要です。 Azure Portal の Azure Databricks ワークスペースの **[アクセス制御 (IAM)]** タブで、ユーザーにアクセス権を付与することができます。

* このエラーは、電子メール ドメイン名が複数の Active Directory に割り当てられている場合にも発生する可能性があります。 この問題を回避するには、Databricks ワークスペースを持つサブスクリプションを含む Active Directory で新しいユーザーを作成します。

    a. Azure Portal で Azure Active Directory に移動して、**[ユーザーとグループ]** をクリックし **[ユーザーの追加]** をクリックします。

    b. @<your_domain> 電子メールの代わりに、`@<tenant_name>.onmicrosoft.com` 電子メールでユーザーを追加します。 Azure Portal の Azure Active Directory の下の **[カスタム ドメイン]** で、お使いの Active Directory と関連付けられている <tenant_name>.onmicrosoft.com を見つけることができます。
    
    c. この新しいユーザーに Databricks ワークスペース リソースの**共同作成者**ロールを付与します。
    
    d. 新しいユーザーで Azure Portal にログインし、Databricks ワークスペースを見つけてください。
    
    e. このユーザーとして Databricks ワークスペースを起動します。


### <a name="issue-your-account-email-has-not-been-registered-in-databricks"></a>問題: アカウント (電子メール) が Databricks に登録されていない 

**ソリューション**

他のユーザーが作成したワークスペースにユーザーとして追加される場合は、Azure Databricks 管理コンソールを使用して追加するよう、ワークスペースの作成者に依頼してください。 手順については、「[Adding and managing users (ユーザーの追加と管理)](https://docs.azuredatabricks.net/administration-guide/admin-settings/users.html)」を参照してください。 自分が作成したワークスペースでこのエラーが引き続き表示される場合は、Azure Portal で [ワークスペースの初期化] を再度クリックしてください。

### <a name="issue-cloud-provider-launch-failure-publicipcountlimitreached-a-cloud-provider-error-was-encountered-while-setting-up-the-cluster"></a>問題: クラウド プロバイダーの起動エラー (PublicIPCountLimitReached): クラスターの設定中にクラウド プロバイダーのエラーが発生した

**エラー メッセージ**

クラウド プロバイダーの起動エラー: クラスターの設定中にクラウド プロバイダーのエラーが発生しました。 詳細については、Databricks ガイドを参照してください。 Azure エラー コード: PublicIPCountLimitReached。 Azure エラー メッセージ: このリージョンのこのサブスクリプションに 60 個を超えるパブリック IP アドレスを作成することはできません。

**ソリューション**

Azure Databricks クラスターは、ノードごとに 1 つのパブリック IP アドレスを使用します。 サブスクリプションがすべてのパブリック IP を既に使用している場合は、[クォータを増やすよう要求](https://docs.microsoft.com/en-us/azure/azure-supportability/resource-manager-core-quotas-request)する必要があります。 **[問題の種類]** に **[クォータ]**、**[クォータの種類]** に **[ネットワーキング: ARM]** を選択し、**[詳細]** でパブリック IP アドレス クォータを増やすように要求します。 たとえば現在の上限が 60 で、100 個のノード クラスターを作成する場合は、上限を 160 に増やすよう要求します。

### <a name="issue-cloud-provider-launch-failure-missingsubscriptionregistration-a-cloud-provider-error-was-encountered-while-setting-up-the-cluster"></a>問題: クラウド プロバイダーの起動エラー (MissingSubscriptionRegistration): クラスターの設定中にクラウド プロバイダーのエラーが発生した

**エラー メッセージ**

クラウド プロバイダーの起動エラー: クラスターの設定中にクラウド プロバイダーのエラーが発生しました。 詳細については、Databricks ガイドを参照してください。
Azure のエラー コード: MissingSubscriptionRegistration Azure エラー メッセージ: サブスクリプションが名前空間 'Microsoft.Compute' を使用するように登録されていません。 サブスクリプションを登録する方法については https://aka.ms/rps-not-found をご覧ください。

**ソリューション**

1. [Azure ポータル](https://portal.azure.com)に移動します。
2. **[サブスクリプション]** をクリックし、使用しているサブスクリプションをクリックして、**[リソースプロバイダー]** をクリックします。 
3. リソースプロバイダーの一覧で、**[Microsoft.Compute]** に対して **[登録]** をクリックします。 リソースプロバイダーを登録するには、サブスクリプションの共同作成者または所有者のロールが必要です。

詳細な手順については、「[リソースプロバイダーと種類](../azure-resource-manager/resource-manager-supported-services.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ
バージョン 2 のデータ ファクトリを作成する手順については、次のチュートリアルを参照してください。

- [クイックスタート: Azure Databricks の使用を開始する](quickstart-create-databricks-workspace-portal.md)
- [Azure Databricks とは](what-is-azure-databricks.md)

