---
title: クラスター エラー ディクショナリを作成する
description: クラスター エラー ディクショナリを作成する方法について説明します。
ms.reviewer: hrasheed
author: karkrish
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: troubleshooting
ms.date: 11/19/2019
ms.author: v-todmc
ms.openlocfilehash: e537014f741196024c24dd6ee98af0d8af2e1b31
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75475933"
---
# <a name="hdinsight-cluster-creation-errors"></a>HDInsight:クラスター作成時のエラー

この記事では、クラスターの作成時に発生するエラーの解決方法について説明します。 

> [!NOTE]
> 以下の一覧の最初の 3 つのエラーは、HDInsight 製品によって CsmDocument_2_0 クラスが使用された場合の検証エラーが原因で発生します。



## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Error Code: DeploymentDocument 'CsmDocument_2_0' で検証に失敗しました

### <a name="error-script-action-location-cannot-be-accessed-uriscript-action-url"></a>**Error**: スクリプト操作の場所を次のアクセスされた URI にすることはできません: \<SCRIPT_ACTION_URL\>

**エラー メッセージ:"リモート サーバーがエラーを返しました: (404) 見つかりません"**

### <a name="cause"></a>原因
HDInsight サービスから、クラスター作成要求の一部として指定されたスクリプト操作 URL にアクセスできません。 スクリプト操作にアクセスしようとすると、"ErrorMessage" が表示されます。

### <a name="resolution"></a>解決策 
  - http/https URL の場合は、incognito ブラウザー ウィンドウから URL を開くことで確認できます。 
  - WASB URL の場合は、要求に指定されているストレージ アカウントにスクリプトが存在することを確認します。 このストレージ アカウントのストレージ キーが正確であることを確認してください。 
  - ADLS URL の場合は、ストレージ アカウントにスクリプトが存在することを確認します。

---

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Error Code: DeploymentDocument 'CsmDocument_2_0' で検証に失敗しました

### <a name="error-script-action-location-cannot-be-accessed-uri-script-action-url"></a>**Error**: スクリプト操作の場所を次のアクセスされた URI にすることはできません: \<SCRIPT_ACTION_URL\>

**エラー メッセージ:指定されたスクリプト URI \<SCRIPT_URI\> は ADLS 上にありますが、このクラスターには Data Lake Storage プリンシパルがありません**

### <a name="cause"></a>原因
HDInsight サービスから、クラスター作成要求の一部として指定されたスクリプト操作 URL にアクセスできません。 スクリプト操作にアクセスしようとすると、"ErrorMessage" が表示されます。 

### <a name="resolution"></a>解決策

対応する Azure Data Lake Store Gen 1 アカウントがクラスターに追加されていることを確認します。 Azure Data Lake Store Gen 1 アカウントにアクセスするために使用されるサービス プリンシパルも、クラスターに追加されます。 

---

## <a name="error-code-deploymentdocument-csmdocument_2_0-failed-the-validation"></a>Error Code:DeploymentDocument 'CsmDocument_2_0' で検証に失敗しました

### <a name="error-vm-size-customer-specified-vm-size-provided-in-the-request-is-invalid-or-not-supported-for-role-role-valid-values-are-valid-vm-size-for-role"></a>**Error**: 要求で指定された VM サイズ '\<CUSTOMER_SPECIFIED_VM_SIZE\>' が無効であるか、'\<ROLE\>' ロールではサポートされていません。 有効な値は次のとおりです。\<VALID_VM_SIZE_FOR_ROLE\>

### <a name="cause"></a>原因
お客様が指定した VM サイズは、このロールでは許可されていません。 これは、VM サイズの値が想定どおりに動作していないか、このコンピューター ロールには適していないことが原因である可能性があります。 

### <a name="resolution"></a>解決策
エラー メッセージには、VM サイズの有効な値が表示されます。 これらの有効な値のいずれかを選択し、クラスター作成要求を再試行してください。 

---

## <a name="error-codeinvalidvirtualnetworkid"></a>Error Code: InvalidVirtualNetworkId  

### <a name="error-the-virtualnetworkid-is-not-valid-virtualnetworkid-user_virtualnetworkid"></a>**Error**: VirtualNetworkId が無効です。 VirtualNetworkId '\<USER_VIRTUALNETWORKID\>'*

### <a name="cause"></a>原因
クラスター作成時に指定された **VirtualNetworkId** の値が正しい形式ではありません。 

### <a name="resolution"></a>解決策
**VirtualNetworkId** とサブネットの形式が正しいことを確認してください。 **VirtualnetworkId** 値を取得するには、Azure portal にアクセスし、お使いの仮想ネットワークを選択してから、メニューの **[プロパティ]** を選択します。 **ResourceID** プロパティが **VirtualNetworkId** 値です。 

仮想ネットワーク ID の例を次に示します。 

“/subscriptions/c15fd9b8-e2b8-1d4e-aa85-2e668040233b/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet” 

---

## <a name="error-code-customizationfailederrorcode"></a>Error Code:CustomizationFailedErrorCode

### <a name="error-cluster-deployment-failed-due-to-an-error-in-the-custom-script-action-failed-actions-script_name-please-go-to-ambari-ui-to-further-debug-the-failure"></a>**Error**: カスタム スクリプト操作でエラーが発生したため、クラスターのデプロイに失敗しました。 Failed Actions:\<SCRIPT_NAME\>。Ambari UI に移動して、エラーをさらにデバックしてください。

### <a name="cause"></a>原因
クラスター作成要求時に指定されたユーザーのカスタム スクリプトは、クラスターが正常にデプロイされた後に実行されます。 このエラー コードは、\<SCRIPT_NAME\> という名前のカスタム スクリプトの実行中にエラーが発生したことを示しています。   

### <a name="resolution"></a>解決策
これはユーザーのカスタム スクリプトなので、ユーザーが問題のトラブルシューティングを行い、必要に応じてスクリプトを再実行する必要があります。 スクリプト エラーのトラブルシューティングを行うには、/var/lib/ambari-agent/* フォルダー内のログを調べます。 または、Ambari UI の [Operations]\(操作\) ページを開き、**run_customscriptionaction** 操作を選択して、エラーの詳細を表示します。 

---

## <a name="error-codeinvaliddocumenterrorcode"></a>Error Code: InvalidDocumentErrorCode

### <a name="error-the-meta_store_type-metastore-schema-version-metastore_major_version-in-database-database_name-is-incompatible-with-cluster-version-cluster_version"></a>**Error**: データベース \<DATABASE_NAME\> の \<META_STORE_TYPE\> Metastore スキーマのバージョン \<METASTORE_MAJOR_VERSION\> とクラスターのバージョン \<CLUSTER_VERSION\> に互換性がありません

### <a name="cause"></a>原因
カスタム メタストアと選択された HDInsight クラスターのバージョンに互換性がありません。 現時点では、HDInsight 4.0 クラスターでは Metastore バージョン 3.*x* のみがサポートされ、HDInsight 3.6 では Metastore version 3.*x* 以降はサポートされていません。 

### <a name="resolution"></a>解決策
各 HDInsight クラスターのバージョンでサポートされている Metastore のバージョンのみを使用するようにします。 カスタム メタストアが指定されていない場合は、HDInsight によって内部的にメタストアが作成されることに注意してください。 ただし、このメタストアは、クラスターの削除時に自動的に削除されます。 

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>Error Code:FailedToConnectWithClusterErrorCode 

### <a name="error-unable-to-connect-to-cluster-management-endpoint-to-perform-scaling-operation-verify-that-network-security-rules-are-not-blocking-external-access-to-the-cluster-and-that-the-cluster-manager-ambari-ui-can-be-successfully-accessed"></a>**Error**: クラスター管理エンドポイントに接続してスケーリング操作を実行することができません。 ネットワーク セキュリティ ルールによってクラスターへの外部アクセスがブロックされていないこと、およびクラスター マネージャー (Ambari) の UI に正常にアクセスできることを確認してください。

### <a name="cause"></a>原因
ネットワーク セキュリティ グループ (NSG) に、重要な Azure の正常性および管理サービスとのクラスター通信をブロックするファイアウォール規則があります。 

### <a name="resolution"></a>解決策
 **ネットワーク セキュリティ グループ** を使用してネットワーク トラフィックを制御する予定の場合は、HDInsight をインストールする前に、次の操作を実行します。 
  - HDInsight を使用する予定の Azure リージョンを特定します。 
  - HDInsight が必要とする IP アドレスを特定します。 詳細については、[HDInsight 管理 IP アドレス](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses)に関する記事を参照してください。 
    - HDInsight をインストールする予定のサブネットのネットワーク セキュリティ グループを作成または変更します。 
    - **ネットワーク セキュリティ グループ:** ポート  **443**  での IP アドレスからの **受信** トラフィックを許可します。 これにより、仮想ネットワークの外部から HDInsight 管理サービスがクラスターに確実に到達できます。 

---

## <a name="error-code-storagepermissionsblockedformsi"></a>Error Code:StoragePermissionsBlockedForMsi  

### <a name="error-the-managed-identity-does-not-have-permissions-on-the-storage-account-please-verify-that-storage-blob-data-owner-role-is-assigned-to-the-managed-identity-for-the-storage-account-storage-subscriptions-subscription-id-resourcegroups-resource-group-name-providersmicrosoftstoragestorageaccounts-storage-account-name-managed-identity-subscriptions-subscription-id-resourcegroups--resource-group-name-providersmicrosoftmanagedidentityuserassignedidentities-user-managed-identity-name"></a>**Error**: マネージド ID にストレージ アカウントに対するアクセス許可がありません。 ストレージ アカウントのマネージド ID に対して、"ストレージ BLOB データ所有者" ロールが割り当てられていることを確認してください。 ストレージ: /subscriptions/\<サブスクリプション ID\>/resourceGroups/\<リソース グループ名\>/providers/Microsoft.Storage/storageAccounts/\<ストレージ アカウント名\>、マネージド ID: /subscriptions/\<サブスクリプション ID\>/resourceGroups/ /\<リソース グループ名\>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/\<ユーザーのマネージド ID 名\>

### <a name="cause"></a>原因
必要なアクセス許可が**マネージド ID** に提供されていませんでした。 **ユーザー割り当てマネージド ID** には、ADLS Gen2 ストレージ アカウントに関する Blob Storage 共同作成者ロールがありませんでした。 

### <a name="resolution"></a>解決策

Azure portal を開いて、お使いのストレージ アカウントに移動します。**Access Control (IAM)** を調べて、ストレージ BLOB データ共同作成者またはストレージ BLOB データ所有者ロールに、サブスクリプションの **ユーザー割り当てマネージド ID** へのアクセス権が "割り当てられている" ことを確認します。 詳細については、「[Data Lake Storage Gen2 アカウントにマネージド ID のアクセス許可を設定する](hdinsight-hadoop-use-data-lake-storage-gen2.md)」を参照してください。 

---

## <a name="error-code-invalidnetworksecuritygroupsecurityrules"></a>Error Code:InvalidNetworkSecurityGroupSecurityRules  

### <a name="error-the-security-rules-in-the-network-security-group-subscriptionssubscriptionidresourcegroupsresource-group-name-defaultprovidersmicrosoftnetworknetworksecuritygroupsnetwork-security-group-name-configured-with-subnet-subscriptionssubscriptionidresourcegroupsresource-group-name-rg-westeurope-vnet-tomtom-defaultprovidersmicrosoftnetworkvirtualnetworksvirtual-network-namesubnetssubnet-name"></a>**Error**: サブネット /subscriptions/\<SubscriptionID>\/resourceGroups/\<リソース グループ名> RG-westeurope-vnet-tomtom-default\/providers/Microsoft.Network/virtualNetworks/\<仮想ネットワーク名>\/subnets/\<サブネット名\> に構成されているネットワーク セキュリティ グループ /subscriptions/\<サブスクリプション ID>\/resourceGroups/<Resource Group name> default/providers/Microsoft.Network/networkSecurityGroups/\<ネットワーク セキュリティ グループ名\> 内のセキュリティ規則で、 
必要な受信接続または送信接続、あるいはその両方が許可されていません。 詳細については、「[Azure HDInsight 用の仮想ネットワークを計画する](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment)」にアクセスするか、サポートにお問い合わせください。**

### <a name="cause"></a>原因
ネットワーク セキュリティ グループ (NSG) またはユーザー定義ルート (UDR) を使用して HDInsight クラスターへの受信トラフィックを制御する場合は、クラスターが Azure の重要な正常性サービスおよび管理サービスと通信できるようにする必要があります。

### <a name="resolution"></a>解決策
 **ネットワーク セキュリティ グループ** を使用してネットワーク トラフィックを制御する予定の場合は、HDInsight をインストールする前に、次の操作を実行します。 
  - HDInsight で使用する予定の Azure リージョンを特定し、リージョンの安全な IP アドレスの一覧を作成します。[正常性サービスと管理サービス:特定のリージョン](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses#health-and-management-services-specific-regions)」を参照してください。
  - HDInsight が必要とする IP アドレスを特定します。 詳細については、「 [HDInsight の管理 IP アドレス](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses)」を参照してください。 
  - HDInsight をインストールする予定のサブネットのネットワーク セキュリティ グループを作成または変更します。 **ネットワーク セキュリティ グループ**: ポート  **443**  でのIP アドレスからの受信トラフィックを許可します。 これにより、HDInsight 管理サービスが仮想ネットワークの外部から、クラスターに確実に到達できます。
  
---

## <a name="error-code-cluster-setup-failed-to-install-components-on-one-or-more-hosts"></a>Error Code:クラスターのセットアップで、1 つまたは複数のホストにコンポーネントをインストールできませんでした

###  <a name="errorcluster-setup-failed-to-install-components-on-one-or-more-hosts-please-retry-your-request"></a>**Error**:  クラスターのセットアップで、1 つまたは複数のホストにコンポーネントをインストールできませんでした。 要求を再試行してください。 

### <a name="cause"></a>原因 
通常、このエラーは、一時的な問題が発生した場合または Azure の停止が発生した場合に生成されます。 

### <a name="resolution"></a>解決策

[Azure の状態](https://status.azure.com/status)ページで、クラスターのデプロイに影響する可能性がある Azure の停止の可能性をチェックしてください。 停止が発生していない場合は、クラスターのデプロイを再試行してください。 

## <a name="next-steps"></a>次の手順

クラスター作成に関するエラーのトラブルシューティングの詳細については、「[Azure HDInsight のクラスター作成に失敗する問題のトラブルシューティング](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-troubleshoot-cluster-creation-fails)」を参照してください。
