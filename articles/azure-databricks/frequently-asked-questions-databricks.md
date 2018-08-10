---
title: 'Azure Databricks: 一般的な質問とヘルプ | Microsoft Docs'
description: Azure Databricks に関する一般的な質問の回答とトラブルシューティング情報を確認します。
services: azure-databricks
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: c3ba235c60480c38a21ee3264c54b4a4dcdea340
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39434603"
---
# <a name="frequently-asked-questions-about-azure-databricks"></a>Azure Databricks についてよく寄せられる質問

この記事には、Azure Databricks に関してよく寄せられる質問が記載されています。 また、Databricks の使用中に発生する可能性がある一般的な問題の一覧も示します。 詳しくは、「[Azure Databricks とは](what-is-azure-databricks.md)」をご覧ください。 

## <a name="can-i-use-my-own-keys-for-local-encryption"></a>ローカル暗号化に独自のキーを使用できますか。 
現在のリリースでは、Azure Key Vault の独自キーの使用はサポートされていません。 

## <a name="can-i-use-azure-virtual-networks-with-databricks"></a>Databricks で Azure 仮想ネットワークを使えますか。
Databricks のプロビジョニングの一部として、新しい仮想ネットワークが作成されます。 このリリースでは、独自の Azure 仮想ネットワークを使うことはできません。

## <a name="how-do-i-access-azure-data-lake-store-from-a-notebook"></a>ノートブックから Azure Data Lake Store にアクセスするにはどうすればよいですか。 

次の手順に従います。
1. Azure Active Directory (Azure AD) でサービス プリンシパルをプロビジョニングし、そのキーを記録します。
1. Data Lake Store のサービス プリンシパルに必要なアクセス許可を割り当てます。
1. Data Lake Store 内のファイルにアクセスするには、Notebook でこのサービス プリンシパルの資格情報を使います。

詳細については、[Azure Databricks での Data Lake Store の使用](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html)に関するページを参照してください。

## <a name="fix-common-problems"></a>一般的な問題の修正

ここでは、Databricks で発生する可能性がある問題について説明します。

### <a name="issue-this-subscription-is-not-registered-to-use-the-namespace-microsoftdatabricks"></a>問題: このサブスクリプションが名前空間 ‘Microsoft.Databricks’ を使用するように登録されていない

#### <a name="error-message"></a>エラー メッセージ

"このサブスクリプションは名前空間 ‘Microsoft.Databricks’ を使用するように登録されていません。 サブスクリプションの登録方法については、 https://aka.ms/rps-not-found を参照してください。 (コード: MissingSubscriptionRegistration)"

#### <a name="solution"></a>解決策

1. [Azure ポータル](https://portal.azure.com)にアクセスします。
1. **[サブスクリプション]**、使っているサブスクリプション、**[リソース プロバイダー]** の順に選びます。 
1. リソース プロバイダーの一覧で、**[Microsoft.Databricks]** に対して **[登録]** を選びます。 リソース プロバイダーを登録するには、サブスクリプションの共同作成者または所有者のロールが必要です。


### <a name="issue-your-account-email-does-not-have-the-owner-or-contributor-role-on-the-databricks-workspace-resource-in-the-azure-portal"></a>問題: アカウント (電子メール アドレス) に、Azure Portal の Databricks ワークスペース リソースの所有者または共同作成者ロールがない

#### <a name="error-message"></a>エラー メッセージ

"アカウント (電子メール) に、Azure Portal の Databricks ワークスペース リソースの所有者または共同作成者ロールがありません。 テナントのゲスト ユーザーである場合も、このエラーが発生する可能性があります。 アクセスを許可するか Databricks ワークスペースに直接ユーザーとして追加するよう、管理者に依頼してください。" 

#### <a name="solution"></a>解決策

この問題に対するいくつかの解決策を次に示します。

* テナントを初期化するには、ゲスト ユーザーではなく、テナントの通常のユーザーとしてサインインインする必要があります。 Databricks ワークスペース リソースの共同作成者ロールも必要です。 Azure Portal の Databricks ワークスペースの **[アクセス制御 (IAM)]** タブで、ユーザーにアクセス権を付与することができます。

* このエラーは、電子メール ドメイン名が Azure AD の複数のディレクトリに割り当てられている場合にも発生する可能性があります。 この問題を回避するには、Databricks ワークスペースを持つサブスクリプションを含むディレクトリで新しいユーザーを作成します。

    a. Azure Portal で、Azure AD に移動します。 **[ユーザーとグループ]** > **[ユーザーの追加]** の順に選びます。

    b. `@<your_domain>` というメール アドレスの代わりに、`@<tenant_name>.onmicrosoft.com` のメール アドレスでユーザーを追加します。 このオプションは、Azure Portal の Azure AD の **[カスタム ドメイン]** で確認できます。
    
    c. この新しいユーザーに Databricks ワークスペース リソースの**共同作成者**ロールを付与します。
    
    d. 新しいユーザーで Azure Portal にサインインし、Databricks ワークスペースを見つけます。
    
    e. このユーザーとして Databricks ワークスペースを起動します。


### <a name="issue-your-account-email-has-not-been-registered-in-databricks"></a>問題: アカウント (電子メール) が Databricks に登録されていない 

#### <a name="solution"></a>解決策

自分でワークスペースを作成していなくて、ユーザーとして追加された場合は、ワークスペースを作成した担当者に問い合わせてください。 そのユーザーに、Azure Databricks 管理者コンソールを使って追加してもらいます。 手順については、「[Adding and managing users (ユーザーの追加と管理)](https://docs.azuredatabricks.net/administration-guide/admin-settings/users.html)」を参照してください。 自分が作成したワークスペースでこのエラーが引き続き表示される場合は、Azure Portal で **[ワークスペースの初期化]** を再度選びます。

### <a name="issue-cloud-provider-launch-failure-while-setting-up-the-cluster-publicipcountlimitreached"></a>問題: クラスターのセットアップ中にクラウド プロバイダー起動エラーが発生する (PublicIPCountLimitReached)

#### <a name="error-message"></a>エラー メッセージ

"クラウド プロバイダーの起動エラー: クラスターの設定中にクラウド プロバイダーのエラーが発生しました。 詳細については、Databricks ガイドを参照してください。 Azure エラー コード: PublicIPCountLimitReached。 Azure エラー メッセージ: このリージョンのこのサブスクリプションに 60 個を超えるパブリック IP アドレスを作成することはできません。"

#### <a name="solution"></a>解決策

Databricks クラスターは、ノードごとに 1 つのパブリック IP アドレスを使用します。 サブスクリプションがすべてのパブリック IP を既に使用している場合は、[クォータを増やすよう要求](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)する必要があります。 **[問題の種類]** として **[クォータ]** を選び、**[クォータの種類]** として **[ネットワーク: ARM]** を選びます。 **[詳細]** で、パブリック IP アドレスのクォータの増量を要求します。 たとえば現在の上限が 60 で、100 個のノード クラスターを作成する場合は、上限を 160 に増やすよう要求します。

### <a name="issue-a-second-type-of-cloud-provider-launch-failure-while-setting-up-the-cluster-missingsubscriptionregistration"></a>問題: クラスターのセットアップ中に 2 番目の種類のクラウド プロバイダー起動エラーが発生する (MissingSubscriptionRegistration)

#### <a name="error-message"></a>エラー メッセージ

"クラウド プロバイダーの起動エラー: クラスターの設定中にクラウド プロバイダーのエラーが発生しました。 詳細については、Databricks ガイドを参照してください。
Azure のエラー コード: MissingSubscriptionRegistration Azure エラー メッセージ: サブスクリプションが名前空間 'Microsoft.Compute' を使用するように登録されていません。 サブスクリプションの登録方法については、 https://aka.ms/rps-not-found を参照してください。

#### <a name="solution"></a>解決策

1. [Azure ポータル](https://portal.azure.com)にアクセスします。
1. **[サブスクリプション]**、使っているサブスクリプション、**[リソース プロバイダー]** の順に選びます。 
1. リソースプロバイダーの一覧で、**[Microsoft.Compute]** に対して **[登録]** を選びます。 リソース プロバイダーを登録するには、サブスクリプションの共同作成者または所有者のロールが必要です。

詳細な手順については、「[リソースプロバイダーと種類](../azure-resource-manager/resource-manager-supported-services.md)」をご覧ください。

### <a name="issue-azure-databricks-needs-permissions-to-access-resources-in-your-organization-that-only-an-admin-can-grant"></a>問題: 組織のリソースにアクセスするためのアクセス許可が Azure Databricks に必要だが、そのアクセス許可は管理者しか付与できない。

#### <a name="background"></a>バックグラウンド

Azure Databricks は、Azure AD に完全に統合されています。 これにより、Azure AD からユーザーを指定することで、Azure Databricks 内 (たとえば、ノートブックまたはクラスター上) でアクセス許可を設定できます。 Azure Databricks が Azure AD からユーザーの名前の一覧を表示できるようにするには、その情報への読み取りアクセス許可が必要です。 これには、同意が必要です。 同意をまだ得られていない場合は、エラーが表示されます。

#### <a name="solution"></a>解決策

グローバル管理者として Azure Portal にログインします。 Azure Active Directory で、**[ユーザー設定]** タブに移動し、**[ユーザーはアプリが自身の代わりに会社のデータにアクセスすることを許可できます]** が **[はい]** に設定されていることを確認します。

## <a name="next-steps"></a>次の手順

- [クイックスタート: Azure Databricks の使用を開始する](quickstart-create-databricks-workspace-portal.md)
- [Azure Databricks とは](what-is-azure-databricks.md)

