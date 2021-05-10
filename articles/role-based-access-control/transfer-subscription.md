---
title: Azure サブスクリプションを別の Azure AD ディレクトリに移転する
description: Azure サブスクリプションと既知の関連リソースを別の Azure Active Directory (Azure AD) ディレクトリに移転する方法について説明します。
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.workload: identity
ms.date: 04/06/2021
ms.author: rolyon
ms.openlocfilehash: 5baf5f503542f31b26c4c210741f1ce986f6a549
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "106580123"
---
# <a name="transfer-an-azure-subscription-to-a-different-azure-ad-directory"></a>Azure サブスクリプションを別の Azure AD ディレクトリに移転する

組織には複数の Azure サブスクリプションがある場合があります。 各サブスクリプションは、特定の Azure Active Directory (Azure AD) ディレクトリと関連付けられています。 管理を容易にするために、サブスクリプションを別の Azure AD ディレクトリに移転することが必要になる場合があります。 サブスクリプションを別の Azure AD ディレクトリに移転する場合、一部のリソースはターゲット ディレクトリに移転されません。 たとえば、Azure のロールベースのアクセス制御 (Azure RBAC) でのすべてのロールの割り当てとカスタム ロールは、ソース ディレクトリからは **完全に** 削除され、ターゲット ディレクトリには移転されません。

この記事では、サブスクリプションを別の Azure AD ディレクトリに移転し、移転後にいくつかのリソースを再作成するために実行できる基本的な手順について説明します。

> [!NOTE]
> Azure クラウド ソリューション プロバイダー (CSP) のサブスクリプションでは、サブスクリプションに対する Azure AD ディレクトリの変更はサポートされていません。

## <a name="overview"></a>概要

別の Azure AD ディレクトリへの Azure サブスクリプションの移転は、慎重に計画して実行する必要がある複雑なプロセスです。 多くの Azure サービスでは、セキュリティ プリンシパル (ID) が通常どおりに動作するか、他の Azure リソースを管理する必要があります。 この記事では、セキュリティ プリンシパルに大きく依存する Azure サービスのほとんどについて説明しますが、これは包括的なものではありません。

> [!IMPORTANT]
> 一部のシナリオでサブスクリプションを移転する場合、プロセスの完了にダウンタイムが必要なことがあります。 自分の移転でダウンタイムが必要になるかどうかを評価するには、慎重に計画する必要があります。

次の図では、サブスクリプションを別のディレクトリに移転するときに従う必要がある基本的な手順を示します。

1. 移転を準備する

1. Azure サブスクリプションを別のディレクトリに移転する

1. ロールの割り当て、カスタム ロール、マネージド ID などのリソースをターゲット ディレクトリで再作成する

    ![サブスクリプションの移転の図](./media/transfer-subscription/transfer-subscription.png)

### <a name="deciding-whether-to-transfer-a-subscription-to-a-different-directory"></a>サブスクリプションを別のディレクトリに移転するかどうかを決定する

サブスクリプションを移転する理由には、次のようなものがあります。

- 会社の合併や買収のため、取得したサブスクリプションをプライマリ Azure AD ディレクトリで管理する必要があります。
- 組織内のだれかが作成したサブスクリプションの管理を、特定の Azure AD ディレクトリに統合する必要があります。
- 特定のサブスクリプション ID または URL に依存するアプリケーションがあり、アプリケーションの構成やコードを簡単に変更することができません。
- ビジネスの一部が別の会社に分割され、一部のリソースを別の Azure AD ディレクトリに移動する必要があります。
- セキュリティを分離するため、リソースの一部を別の Azure AD ディレクトリで管理する必要があります。

### <a name="alternate-approaches"></a>別のアプローチ

サブスクリプションを移転するには、プロセスを完了するためにダウンタイムが必要です。 シナリオによっては、次の代替方法を検討できます。

- リソースを再作成し、ターゲット ディレクトリおよびサブスクリプションにデータをコピーする。
- マルチディレクトリ アーキテクチャを採用し、サブスクリプションをソース ディレクトリに残す。 ターゲット ディレクトリのユーザーがソース ディレクトリ内のサブスクリプションにアクセスできるように、Azure Lighthouse を使用してリソースを委任します。 詳しくは、「[エンタープライズ シナリオにおける Azure Lighthouse](../lighthouse/concepts/enterprise.md)」をご覧ください。

### <a name="understand-the-impact-of-transferring-a-subscription"></a>サブスクリプションの移転による影響を把握する

いくつかの Azure リソースは、サブスクリプションまたはディレクトリに依存しています。 次の表では、状況に応じたサブスクリプションの移転による既知の影響を示します。 この記事の手順を実行することで、サブスクリプションの移転前に存在していたリソースの一部を再作成できます。

> [!IMPORTANT]
> このセクションでは、サブスクリプションに依存する既知の Azure サービスまたはリソースの一覧を示します。 Azure のリソースの種類は絶えず進化しているため、ここに記載されていない追加の依存関係で、環境の破壊的変更の原因になるものがある可能性があります。 

| サービスまたはリソース | 影響を受ける | 復旧可能 | 影響を受けるかどうか? | 実行可能な操作 |
| --------- | --------- | --------- | --------- | --------- |
| ロールの割り当て | はい | はい | [ロールの割り当てを一覧表示する](#save-all-role-assignments) | すべてのロールの割り当ては完全に削除されます。 ユーザー、グループ、サービス プリンシパルを、ターゲット ディレクトリ内の対応するオブジェクトにマップする必要があります。 ロールの割り当てを再作成する必要があります。 |
| カスタム ロール | はい | はい | [カスタム ロールを一覧表示する](#save-custom-roles) | すべてのカスタム ロールは完全に削除されます。 カスタム ロールとロールの割り当てを再作成する必要があります。 |
| システム割り当てのマネージド ID | はい | はい | [マネージド ID を一覧表示する](#list-role-assignments-for-managed-identities) | マネージド ID を無効にして、再度有効にする必要があります。 ロールの割り当てを再作成する必要があります。 |
| ユーザー割り当て済みマネージド ID | はい | はい | [マネージド ID を一覧表示する](#list-role-assignments-for-managed-identities) | マネージド ID を削除して再作成し、適切なリソースにアタッチする必要があります。 ロールの割り当てを再作成する必要があります。 |
| Azure Key Vault | はい | はい | [Key Vault アクセス ポリシーを一覧表示する](#list-key-vaults) | キー コンテナーに関連付けられているテナント ID を更新する必要があります。 アクセス ポリシーを削除して、新しく追加する必要があります。 |
| Azure AD 認証が統合された Azure SQL データベース | はい | いいえ | [Azure SQL データベースと Azure AD 認証を確認する](#list-azure-sql-databases-with-azure-ad-authentication) | Azure AD 認証が有効になっている Azure SQL データベースを別のディレクトリに転送することはできません。 詳細については、「[Azure Active Directory 認証を使用する](../azure-sql/database/authentication-aad-overview.md)」を参照してください。 | 
| Azure Storage と Azure Data Lake Storage Gen2 | はい | はい |  | すべての ACL を再作成する必要があります。 |
| Azure Data Lake Storage Gen1 | はい | はい |  | すべての ACL を再作成する必要があります。 |
| Azure Files | はい | はい |  | すべての ACL を再作成する必要があります。 |
| Azure File Sync | はい | はい |  | ストレージ同期サービスやストレージ アカウントは、別のディレクトリに移動できます。 詳細については、「[Azure Files についてよく寄せられる質問 (FAQ)](../storage/files/storage-files-faq.md#azure-file-sync)」を参照してください。 |
| Azure Managed Disks | はい | はい |  |  ディスク暗号化セットを使用して、カスタマー マネージド キーで Managed Disks を暗号化する場合は、ディスク暗号化セットに関連付けられているシステム割り当て ID を無効にしてから、再度有効にする必要があります。 また、ロールの割り当てを再作成する必要があります。つまり、Key Vault 内にあるディスク暗号化セットに対し、必要なアクセス許可を再度付与します。 |
| Azure Kubernetes Service | はい | いいえ |  | AKS クラスターとそれに関連付けられているリソースを別のディレクトリに転送することはできません。 詳細については、「[Azure Kubernetes Service (AKS) についてよく寄せられる質問](../aks/faq.md)」を参照してください。 |
| Azure Policy | はい | いいえ | カスタム定義、割り当て、除外、コンプライアンス データなど、すべての Azure Policy オブジェクト。 | 定義を[エクスポート](../governance/policy/how-to/export-resources.md)、インポート、および再割り当てする必要があります。 次に、新しいポリシー割り当てと、必要な[ポリシーの除外](../governance/policy/concepts/exemption-structure.md)を作成します。 |
| Azure Active Directory Domain Services | はい | いいえ |  | Azure AD Domain Services の管理対象ドメインを別のディレクトリに転送することはできません。 詳細については、「[Azure Active Directory (AD) Domain Services に関してよく寄せられる質問 (FAQ)](../active-directory-domain-services/faqs.md)」を参照してください。 |
| アプリの登録 | はい | はい |  |  |

> [!WARNING]
> ストレージ アカウントや SQL データベースなどのリソースに対して保存時の暗号化を使用していて、それが移転されるの **ではない** サブスクリプションのキー コンテナーに依存している場合、復旧不可能なシナリオが発生する可能性があります。 このような状況が発生した場合は、別のキー コンテナーを使用するか、ユーザーが管理するキーを一時的に無効にして、この復旧不可能なシナリオを回避する必要があります。

## <a name="prerequisites"></a>前提条件

これらの手順を完了するには、以下が必要です。

- [Azure Cloud Shell の Bash](../cloud-shell/overview.md) または [Azure CLI](/cli/azure)
- ソース ディレクトリ内の移転するサブスクリプションのアカウント管理者
- ターゲット ディレクトリの[所有者](built-in-roles.md#owner) ロール

## <a name="step-1-prepare-for-the-transfer"></a>手順 1:移転を準備する

### <a name="sign-in-to-source-directory"></a>ソース ディレクトリにサインインする

1. 管理者として Azure にサインインします。

1. [az account list](/cli/azure/account#az_account_list) コマンドを使用して、サブスクリプションの一覧を取得します。

    ```azurecli
    az account list --output table
    ```

1. [az account set](/cli/azure/account#az_account_set) を使用して、移転するアクティブなサブスクリプションを設定します。

    ```azurecli
    az account set --subscription "Marketing"
    ```

### <a name="install-the-azure-resource-graph-extension"></a>Azure Resource Graph 拡張機能をインストールする

 [Azure Resource Graph](../governance/resource-graph/index.yml) の Azure CLI 拡張機能である *resource-graph* を使用すると、[az graph](/cli/azure/ext/resource-graph/graph) コマンドを使用して、Azure Resource Manager によって管理されているリソースを照会できます。 このコマンドは、後の手順で使用します。

1. [az extension list](/cli/azure/extension#az_extension_list) を使用して、*resource-graph* 拡張機能がインストールされているかどうかを確認します。

    ```azurecli
    az extension list
    ```

1. ない場合、*resource-graph* 拡張機能をインストールします。

    ```azurecli
    az extension add --name resource-graph
    ```

### <a name="save-all-role-assignments"></a>すべてのロールの割り当てを保存する

1. [az role assignment list](/cli/azure/role/assignment#az_role_assignment_list) を使用して、すべてのロールの割り当ての一覧を表示します (継承されたロールの割り当てを含む)。

    一覧を簡単に確認できるように、出力を JSON、TSV、またはテーブルとしてエクスポートできます。 詳細については、「[Azure RBAC と Azure CLI を使用してロールの割り当てを一覧表示する](role-assignments-list-cli.md)」を参照してください。

    ```azurecli
    az role assignment list --all --include-inherited --output json > roleassignments.json
    az role assignment list --all --include-inherited --output tsv > roleassignments.tsv
    az role assignment list --all --include-inherited --output table > roleassignments.txt
    ```

1. ロールの割り当ての一覧を保存します。

    サブスクリプションを移転すると、すべてのロールの割り当てが **完全に** 削除されます。そのため、コピーを保存しておくことが重要です。

1. ロールの割り当ての一覧を確認します。 ターゲット ディレクトリに必要のないロールの割り当てが存在する可能性があります。

### <a name="save-custom-roles"></a>カスタムロールを保存する

1. カスタム ロールの一覧を表示するには、[az role definition list](/cli/azure/role/definition#az_role_definition_list) を使用します。 詳細については、「[Azure CLIを使用して Azure カスタム ロールを作成または更新する](custom-roles-cli.md)」を参照してください。

    ```azurecli
    az role definition list --custom-role-only true --output json --query '[].{roleName:roleName, roleType:roleType}'
    ```

1. ターゲット ディレクトリに必要な各カスタム ロールを、別の JSON ファイルとして保存します。

    ```azurecli
    az role definition list --name <custom_role_name> > customrolename.json
    ```

1. カスタム ロール ファイルのコピーを作成します。

1. 次の形式を使用するように各コピーを変更します。

    後でこれらのファイルを使用して、ターゲット ディレクトリにカスタム ロールを再作成します。

    ```json
    {
      "Name": "",
      "Description": "",
      "Actions": [],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": []
    }
    ```

### <a name="determine-user-group-and-service-principal-mappings"></a>ユーザー、グループ、サービス プリンシパルのマッピングを決定する

1. ロールの割り当ての一覧に基づいて、ターゲット ディレクトリでマップするユーザー、グループ、サービス プリンシパルを決定します。

    各ロールの割り当ての `principalType` プロパティを参照することで、プリンシパルの種類を特定できます。

1. 必要に応じて、ターゲット ディレクトリに、必要なユーザー、グループ、またはサービス プリンシパルを作成します。

### <a name="list-role-assignments-for-managed-identities"></a>マネージド ID のロールの割り当ての一覧を表示する

サブスクリプションを別のディレクトリに移転したとき、マネージド ID は更新されません。 その結果、既存のシステム割り当てマネージド ID やユーザー割り当てマネージド ID は破損します。 移転後、システム割り当てマネージド ID を再び有効にすることができます。 ユーザー割り当てマネージド ID の場合は、ターゲット ディレクトリで再作成してアタッチする必要があります。

1. [マネージド ID がサポートされている Azure サービスの一覧](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)を確認して、マネージド ID を使用している可能性がある場所を記録します。

1. システム割り当てとユーザー割り当てのマネージド ID の一覧を表示するには、[az ad sp list](/cli/azure/ad/sp#az_ad_sp_list) を使用します。

    ```azurecli
    az ad sp list --all --filter "servicePrincipalType eq 'ManagedIdentity'"
    ```

1. マネージド ID の一覧で、システム割り当てのものとユーザー割り当てのものを特定します。 次の条件を使用して、種類を特定できます。

    | 条件 | マネージド ID の種類 |
    | --- | --- |
    | `alternativeNames` プロパティに `isExplicit=False` が含まれる | システム割り当て |
    | `alternativeNames` プロパティに `isExplicit` が含まれない | システム割り当て |
    | `alternativeNames` プロパティに `isExplicit=True` が含まれる | ユーザー割り当て |

    また、[az identity list](/cli/azure/identity#az_identity_list) コマンドを使用して、ユーザー割り当てマネージド ID だけの一覧を表示することもできます。 詳細については、「[Azure CLI を使用してユーザー割り当てマネージド ID を作成、一覧表示、または削除する](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)」を参照してください。

    ```azurecli
    az identity list
    ```

1. マネージド ID の `objectId` 値の一覧を取得します。

1. ロールの割り当ての一覧を検索して、マネージド ID に対するロールの割り当てがあるかどうかを確認します。

### <a name="list-key-vaults"></a>Key Vault のリスト

キー コンテナーを作成すると、そのキー コンテナーは、それが作成されたサブスクリプションの既定の Azure Active Directory テナント ID に自動的に関連付けられます。 また、すべてのアクセス ポリシー エントリがこのテナント ID に関連付けられます。 詳細については、「[Azure Key Vault を別のサブスクリプションに移動する](../key-vault/general/move-subscription.md)」を参照してください。

> [!WARNING]
> ストレージ アカウントや SQL データベースなどのリソースに対して保存時の暗号化を使用していて、それが移転されるの **ではない** サブスクリプションのキー コンテナーに依存している場合、復旧不可能なシナリオが発生する可能性があります。 このような状況が発生した場合は、別のキー コンテナーを使用するか、ユーザーが管理するキーを一時的に無効にして、この復旧不可能なシナリオを回避する必要があります。

- キー コンテナーがある場合は、[az keyvault show](/cli/azure/keyvault#az_keyvault_show) を使用してアクセス ポリシーの一覧を表示します。 詳細については、[キー コンテナーへのアクセス ポリシーの割り当て](../key-vault/general/assign-access-policy-cli.md)に関するページを参照してください。

    ```azurecli
    az keyvault show --name MyKeyVault
    ```

### <a name="list-azure-sql-databases-with-azure-ad-authentication"></a>Azure SQL データベースと Azure AD 認証の一覧を表示する

- Azure AD 認証が統合されている Azure SQL データベースを使用しているかどうかを確認するには、[az sql server ad-admin list](/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_list) と [az graph](/cli/azure/ext/resource-graph/graph) 拡張機能を使用します。 詳細については、「[SQL による Azure Active Directory 認証の構成と管理](../azure-sql/database/authentication-aad-configure.md)」を参照してください。

    ```azurecli
    az sql server ad-admin list --ids $(az graph query -q 'resources | where type == "microsoft.sql/servers" | project id' -o tsv | cut -f1)
    ```

### <a name="list-acls"></a>ACL の一覧を表示する

1. Azure Data Lake Storage Gen1 を使用している場合は、Azure portal または PowerShell を使用して、ファイルに適用されている ACL の一覧を表示します。

1. Azure Data Lake Storage Gen2 を使用している場合は、Azure portal または PowerShell を使用して、ファイルに適用されている ACL の一覧を表示します。

1. Azure Files を使用している場合は、ファイルに適用されている ACL の一覧を表示します。

### <a name="list-other-known-resources"></a>他の既知のリソースの一覧を表示する

1. [az account show](/cli/azure/account#az_account_show) を使用して、サブスクリプション ID を取得します。

    ```azurecli
    subscriptionId=$(az account show --query id | sed -e 's/^"//' -e 's/"$//')
    ```

1. [az graph](/cli/azure/ext/resource-graph/graph) 拡張機能を使用して、Azure AD ディレクトリの既知の依存関係がある他の Azure リソースの一覧を表示します。

    ```azurecli
    az graph query -q \
    'resources | where type != "microsoft.azureactivedirectory/b2cdirectories" | where  identity <> "" or properties.tenantId <> "" or properties.encryptionSettingsCollection.enabled == true | project name, type, kind, identity, tenantId, properties.tenantId' \
    --subscriptions $subscriptionId --output table
    ```

## <a name="step-2-transfer-the-subscription"></a>手順 2:サブスクリプションを移転する

この手順では、サブスクリプションを、ソース ディレクトリからターゲット ディレクトリに移転します。 この手順は、課金所有権も移転するかどうかによって変わります。

> [!WARNING]
> サブスクリプションを移転すると、ソース ディレクトリ内のすべてのロールの割り当てが **完全に** 削除されて復元できなくなります。 サブスクリプションの移転は、元に戻すことはできません。 このステップを実行する前に、これまでの手順を完了していることを確認してください。

1. 課金所有権も別のアカウントに移転するかどうかを決定します。

1. サブスクリプションを別のディレクトリに移転します。

    - 現在の課金所有権を維持する場合は、「[Azure サブスクリプションを Azure Active Directory テナントに関連付けるまたは追加する](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)」の手順に従ってください。
    - 課金所有権も移転する場合は、「[Azure サブスクリプションの課金所有権を別のアカウントに譲渡する](../cost-management-billing/manage/billing-subscription-transfer.md)」の手順に従ってください。 サブスクリプションを別のディレクトリに移転するには、 **[サブスクリプション Azure AD テナント]** チェック ボックスをオンにする必要があります。

1. サブスクリプションの移転が完了したら、この記事に戻り、ターゲット ディレクトリにリソースを再作成します。

## <a name="step-3-re-create-resources"></a>手順 3:リソースを再作成する

### <a name="sign-in-to-target-directory"></a>ターゲット ディレクトリにサインインする

1. ターゲット ディレクトリで、移転要求を受け入れたユーザーとしてサインインします。

    移転要求を受け入れた新しいアカウントのユーザーだけが、リソースにアクセスして管理できます。

1. [az account list](/cli/azure/account#az_account_list) コマンドを使用して、サブスクリプションの一覧を取得します。

    ```azurecli
    az account list --output table
    ```

1. [az account set](/cli/azure/account#az_account_set) を使用して、使用するアクティブなサブスクリプションを設定します。

    ```azurecli
    az account set --subscription "Contoso"
    ```

### <a name="create-custom-roles"></a>カスタム ロールを作成する
        
- [az role definition create](/cli/azure/role/definition#az_role_definition_create) を使用して、前に作成したファイルから各カスタム ロールを作成します。 詳細については、「[Azure CLIを使用して Azure カスタム ロールを作成または更新する](custom-roles-cli.md)」を参照してください。

    ```azurecli
    az role definition create --role-definition <role_definition>
    ```

### <a name="assign-roles"></a>ロールを割り当てる

- [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) を使用して、ユーザー、グループ、サービス プリンシパルにロールを割り当てます。 詳細については、「[Azure CLI を使用して Azure ロールを割り当てる](role-assignments-cli.md)」を参照してください。

    ```azurecli
    az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
    ```

### <a name="update-system-assigned-managed-identities"></a>システム割り当てのマネージド ID を更新する

1. システム割り当てのマネージドID を無効にした後、再び有効にします。

    | Azure サービス | 詳細情報 | 
    | --- | --- |
    | 仮想マシン | [Azure CLI を使用して Azure VM 上に Azure リソースのマネージド ID を構成する](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#system-assigned-managed-identity) |
    | 仮想マシン スケール セット | [Azure CLI を使用して仮想マシン スケール セットで Azure リソースのマネージド ID を構成する](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vmss.md#system-assigned-managed-identity) |
    | その他のサービス | [Azure リソースのマネージド ID をサポートするサービス](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) |

1. [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) を使用して、システム割り当てマネージド ID にロールを割り当てます。 詳細については、「[Azure CLI を使用して、リソースにマネージド ID アクセスを割り当てる](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)」を参照してください。

    ```azurecli
    az role assignment create --assignee <objectid> --role '<role_name_or_id>' --scope <scope>
    ```

### <a name="update-user-assigned-managed-identities"></a>ユーザー割り当てのマネージド ID を更新する

1. ユーザー割り当てのマネージド ID を削除し、再作成して、アタッチします。

    | Azure サービス | 詳細情報 | 
    | --- | --- |
    | 仮想マシン | [Azure CLI を使用して Azure VM 上に Azure リソースのマネージド ID を構成する](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) |
    | 仮想マシン スケール セット | [Azure CLI を使用して仮想マシン スケール セットで Azure リソースのマネージド ID を構成する](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vmss.md#user-assigned-managed-identity) |
    | その他のサービス | [Azure リソースのマネージド ID をサポートするサービス](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)<br/>[Azure CLI を使用してユーザー割り当てマネージド ID を作成、一覧表示、または削除する](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) |

1. [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) を使用して、ユーザー割り当てマネージド ID にロールを割り当てます。 詳細については、「[Azure CLI を使用して、リソースにマネージド ID アクセスを割り当てる](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)」を参照してください。

    ```azurecli
    az role assignment create --assignee <objectid> --role '<role_name_or_id>' --scope <scope>
    ```

### <a name="update-key-vaults"></a>キー コンテナーを更新する

ここでは、キー コンテナーを更新する基本的な手順について説明します。 詳細については、「[Azure Key Vault を別のサブスクリプションに移動する](../key-vault/general/move-subscription.md)」を参照してください。

1. サブスクリプションで既存のすべてのキー コンテナーに関連付けられているテナント ID を、ターゲット ディレクトリに更新します。

1. すべての既存のアクセス ポリシー エントリを削除する。

1. ターゲット ディレクトリに関連付けられた新しいアクセス ポリシー エントリを追加します。

### <a name="update-acls"></a>ACL を更新する

1. Azure Data Lake Storage Gen1 を使用している場合は、適切な ACL を割り当てます。 詳細については、「[Securing data stored in Azure Data Lake Storage Gen1 (Azure Data Lake Storage Gen1 に格納されているデータのセキュリティ保護)](../data-lake-store/data-lake-store-secure-data.md)」をご覧ください。

1. Azure Data Lake Storage Gen2 を使用している場合は、適切な ACL を割り当てます。 詳細については、[Azure Data Lake Storage Gen2 でのアクセス制御](../storage/blobs/data-lake-storage-access-control.md)に関するページを参照してください。

1. Azure Files を使用している場合は、適切な ACL を割り当てます。

### <a name="review-other-security-methods"></a>他のセキュリティ メソッドを確認する

ロールの割り当ては移転の間に削除されますが、元の所有者アカウントのユーザーが、次のような他のセキュリティ メソッドを使用して、引き続きサブスクリプションにアクセスできる場合があります。

- Storage などのサービス用のアクセス キー。
- サブスクリプションのリソースに対する管理者アクセス権をユーザーに付与する[管理証明書](../cloud-services/cloud-services-certs-create.md)。
- Azure Virtual Machines などのサービス用のリモート アクセス資格情報。

ソース ディレクトリのユーザーがターゲット ディレクトリにアクセスできないように、アクセス権を削除する場合は、すべての資格情報のローテーションを検討する必要があります。 資格情報が更新されるまで、移転後もユーザーは引き続きアクセスできます。

1. ストレージ アカウントのアクセス キーをローテーションします。 詳細については、「[ストレージ アカウントのアクセス キーの管理](../storage/common/storage-account-keys-manage.md)」を参照してください。

1. Azure SQL Database や Azure Service Bus メッセージングなどの他のサービスにアクセス キーを使用している場合は、アクセス キーをローテーションします。

1. シークレットを使用しているリソースについては、リソースの設定を開き、シークレットを更新します。

1. 証明書を使用しているリソースについては、証明書を更新します。

## <a name="next-steps"></a>次のステップ

- [Azure サブスクリプションの課金所有権を別のアカウントに譲渡する](../cost-management-billing/manage/billing-subscription-transfer.md)
- [サブスクライバーと CSP の間で Azure サブスクリプションを譲渡する](../cost-management-billing/manage/transfer-subscriptions-subscribers-csp.md)
- [Azure サブスクリプションを Azure Active Directory テナントに関連付けるまたは追加する](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
- [エンタープライズ シナリオにおける Azure Lighthouse](../lighthouse/concepts/enterprise.md)
