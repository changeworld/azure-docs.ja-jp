<properties 
	pageTitle="HDInsight での Hadoop のデバッグ: エラー メッセージ | Microsoft Azure" 
	description="PowerShell を使用して HDInsight を管理しているときに表示されることがあるエラー メッセージと、回復するために使用できる手順について説明します。" 
	services="hdinsight" 
	editor="cgronlun" 
	manager="paulettm" 
	authors="mumian" 
	documentationCenter=""/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/08/2015" 
	ms.author="jgao"/>

# HDInsight で Hadoop のデバッグをする: エラーメッセージの解釈

このトピックで取り上げるエラー メッセージは、Azure HDInsight で Hadoop のユーザーが Azure PowerShell を使用してサービスを管理する際に発生する可能性のあるエラー状況を理解するのに役立ちます。また、エラーから回復する手順も示されています。

一部のエラー メッセージは、Azure ポータルで HDinsight クラスターを管理している場合にも表示されます。しかし、その場合に遭遇する可能性のあるエラー メッセージは、その状況で可能な対応策に制約があるため、さほどきめ細かいものではありません。その他のエラー メッセージは、対応策が明白な文脈で提供されています。たとえば、パラメーターの制約違反である場合、値が入力されたボックスの右側にポップアップ形式でメッセージが表示されます。下図は、要求されたデータ ノードの数が多すぎた場合です。対応策は、許容値である 22 以下まで数を減らすことです。

![HDI.Debugging.ErrorMessages.Portal][image-hdi-debugging-error-messages-portal]

Azure PowerShell または Azure ポータルで発生する可能性のあるエラーは、「[HDInsight エラー](#hdinsight-error-messages)」セクションに名前のアルファベット順に列挙されています。各エラーは、「[エラーの説明と対応策](#discription-mitigation-errors)」セクションの項目にリンクされ、以下のエラーの情報が示されています。
 	
- **説明**: ユーザーに表示されるエラー メッセージ	
- **対応策**: エラーから回復するために使用できる手順 

###HDInsight のエラー コード

[AtleastOneSqlMetastoreMustBeProvided](#AtleastOneSqlMetastoreMustBeProvided) [AzureRegionNotSupported](#AzureRegionNotSupported) [ClusterContainerRecordNotFound](#ClusterContainerRecordNotFound) [ClusterDnsNameInvalidReservedWord](#ClusterDnsNameInvalidReservedWord) [ClusterNameUnavailable](#ClusterNameUnavailable) [ClusterUserNameInvalid](#ClusterUserNameInvalid) [ClusterUserNameInvalidReservedWord](#ClusterUserNameInvalidReservedWord) [ContainerNameMisMatchWithDnsName](#ContainerNameMisMatchWithDnsName) [DataNodeDefinitionNotFound](#DataNodeDefinitionNotFound) [DeploymentDeletionFailure](#DeploymentDeletionFailure) [DnsMappingNotFound](#DnsMappingNotFound) [DuplicateClusterContainerRequest](#DuplicateClusterContainerRequest) [DuplicateClusterInHostedService](#DuplicateClusterInHostedService) [FailureToUpdateDeploymentStatus](#FailureToUpdateDeploymentStatus) [HdiRestoreClusterAltered](#HdiRestoreClusterAltered) [HeadNodeConfigNotFound](#HeadNodeConfigNotFound) [HeadNodeConfigNotFound](#HeadNodeConfigNotFound) [HostedServiceCreationFailure](#HostedServiceCreationFailure) [HostedServiceHasProductionDeployment](#HostedServiceHasProductionDeployment) [HostedServiceNotFound](#HostedServiceNotFound) [HostedServiceWithNoDeployment](#HostedServiceWithNoDeployment) [InsufficientResourcesCores](#InsufficientResourcesCores) [InsufficientResourcesHostedServices](#InsufficientResourcesHostedServices) [InternalErrorRetryRequest](#InternalErrorRetryRequest) [InvalidAzureStorageLocation](#InvalidAzureStorageLocation) [InvalidNodeSizeForDataNode](#InvalidNodeSizeForDataNode) [InvalidNodeSizeForHeadNode](#InvalidNodeSizeForHeadNode) [InvalidRightsForDeploymentDeletion](#InvalidRightsForDeploymentDeletion) [InvalidStorageAccountBlobContainerName](#InvalidStorageAccountBlobContainerName) [InvalidStorageAccountConfigurationSecretKey](#InvalidStorageAccountConfigurationSecretKey) [InvalidVersionHeaderFormat](#InvalidVersionHeaderFormat) [MoreThanOneHeadNode](#MoreThanOneHeadNode) [OperationTimedOutRetryRequest](#OperationTimedOutRetryRequest) [ParameterNullOrEmpty](#ParameterNullOrEmpty) [PreClusterCreationValidationFailure](#PreClusterCreationValidationFailure) [RegionCapabilityNotAvailable](#RegionCapabilityNotAvailable) [StorageAccountNotColocated](#StorageAccountNotColocated) [SubscriptionIdNotActive](#SubscriptionIdNotActive) [SubscriptionIdNotFound](#SubscriptionIdNotFound) [UnableToResolveDNS](#UnableToResolveDNS) [UnableToVerifyLocationOfResource](#UnableToVerifyLocationOfResource) [VersionCapabilityNotAvailable](#VersionCapabilityNotAvailable) [VersionNotSupported](#VersionNotSupported) [VersionNotSupportedInRegion](#VersionNotSupportedInRegion) [WasbAccountConfigNotFound](#WasbAccountConfigNotFound)



## <a id="discription-mitigation-errors"></a>エラーの説明と対応策 


### <a id="AtleastOneSqlMetastoreMustBeProvided"></a>AtleastOneSqlMetastoreMustBeProvided
- **説明**: Hive メタストアと Oozie メタストアにカスタム設定を使用するために、1 つ以上のコンポーネントに Azure SQL データベースの詳細を指定してください。   
- **対応策**: 有効な SQL Azure メタストアを指定して要求し直す必要があります。  

### <a id="AzureRegionNotSupported"></a>AzureRegionNotSupported
- **説明**: *nameOfYourRegion* リージョンにクラスターを作成できませんでした。有効な HDInsight リージョンを使用して要求し直してください。   
- **対応策**: 現在サポートされているリージョン (東南アジア、西ヨーロッパ、北ヨーロッパ、米国東部、米国西部) にクラスターを作成する必要があります。  

### <a id="ClusterContainerRecordNotFound"></a>ClusterContainerRecordNotFound
- **説明**: サーバーが、要求されたクラスター レコードを見つけることができませんでした。  
- **対応策**: 操作をやり直します。 

### <a id="ClusterDnsNameInvalidReservedWord"></a>ClusterDnsNameInvalidReservedWord
- **説明**: クラスターの DNS 名 *yourDnsName* が無効です。名前の先頭と末尾が英数字であり、使っている特殊文字は '-' だけであることを確認してください。  
- **対応策**: クラスターに有効な DNS 名を使用していること、つまり、先頭と末尾が英数字で、ダッシュ (-) 以外の特殊文字を含まないことを確認して、操作をやり直します。

### <a id="ClusterNameUnavailable"></a>ClusterNameUnavailable
- **説明**: クラスター名 *yourClusterName* は使用できません。別の名前を選択してください。  
- **対応策**: 既存のクラスターと重複しないクラスター名を指定して、やり直す必要があります。ポータルを使用している場合は、作成処理中にクラスター名が既に使用されていると、その時点で通知されます。 
 

### <a id="ClusterPasswordInvalid"></a>ClusterPasswordInvalid
- **説明**: クラスターのパスワードが無効です。パスワードは、数字、大文字、小文字、特殊文字を少なくとも 1 文字ずつ組み合わせて、10 文字以上とし、スペースは使わず、ユーザー名を含まないようにします。  
- **対応策**: 有効なクラスター パスワードを指定して操作をやり直します。 

### <a id="ClusterUserNameInvalid"></a>ClusterUserNameInvalid
- **説明**: クラスターのユーザー名が無効です。ユーザー名に特殊文字またはスペースが含まれていないことを確認してください。  
- **対応策**: 有効なクラスター ユーザー名を指定して操作をやり直します。

### <a id="ClusterUserNameInvalidReservedWord"></a>ClusterUserNameInvalidReservedWord
- **説明**: クラスターの DNS 名 *yourDnsClusterName* が無効です。名前の先頭と末尾が英数字であり、使っている特殊文字は '-' だけであることを確認してください。  
- **対応策**: 有効な DNS クラスター ユーザー名を指定して操作をやり直します。

### <a id="ContainerNameMisMatchWithDnsName"></a>ContainerNameMisMatchWithDnsName
- **説明**: URI *yourcontainerURI* のコンテナー名と、要求本文の DNS 名 *yourDnsName* は同じである必要があります。  
- **対応策**: コンテナー名と DNS 名が同じであることを確認して操作をやり直します。

### <a id="DataNodeDefinitionNotFound"></a>DataNodeDefinitionNotFound
- **説明**: 無効なクラスター構成です。データ ノード定義がノード サイズに見つかりません。  
- **対応策**: 操作をやり直します。

### <a id="DeploymentDeletionFailure"></a>DeploymentDeletionFailure 	
- **説明**: クラスターでデプロイの削除に失敗しました  
- **対応策**: 削除操作をやり直します。

### <a id="DnsMappingNotFound"></a>DnsMappingNotFound
- **説明**: サービスの構成エラー。必要な DNS マッピング情報が見つかりません。  
- **対応策**: クラスターを削除して新しいクラスターを作成します。

### <a id="DuplicateClusterContainerRequest"></a>DuplicateClusterContainerRequest
- **説明**: クラスター コンテナー作成操作が重複しています。*nameOfYourContainer* のレコードが存在しますが、Etag が一致しません。   
- **対応策**: 一意の名前をコンテナーに付けて、作成操作をやり直します。 

### <a id="DuplicateClusterInHostedService"></a>DuplicateClusterInHostedService
- **説明**: ホステッド サービス *nameOfYourHostedService* には既にクラスターが含まれています。ホステッド サービスに複数のクラスターを含めることはできません。  
- **対応策**: 別のホステッド サービスでクラスターをホストします。 

### <a id="FailureToUpdateDeploymentStatus"></a>FailureToUpdateDeploymentStatus
- **説明**: サーバーは、クラスターのデプロイの状態を更新できませんでした。  
- **対応策**: 操作をやり直します。これが何度も起きる場合は、CSS にお問い合わせください。 

### <a id="HdiRestoreClusterAltered"></a>HdiRestoreClusterAltered
- **説明**: クラスター *yourClusterName* がメンテナンスの一環として削除されました。クラスターを作成し直してください。     
- **対応策**: クラスターを作成し直してください。

### <a id="HeadNodeConfigNotFound"></a>HeadNodeConfigNotFound
- **説明**: 無効なクラスター構成です。必要なヘッド ノード構成がノード サイズに見つかりません。
- **対応策**: 操作をやり直します。

### <a id="HostedServiceCreationFailure"></a>HostedServiceCreationFailure
- **説明**: ホステッド サービス *nameOfYourHostedService* を作成できません。要求を再試行してください。  
- **対応策**: 要求をやり直してください。

### <a id="HostedServiceHasProductionDeployment"></a>HostedServiceHasProductionDeployment
- **説明**: ホステッド サービス *nameOfYourHostedService* には、既に運用環境が展開されています。ホステッド サービスに運用環境のデプロイを含めることはできません。別のクラスター名を使用して要求を再試行してください。   
- **対応策**: 別のクラスター名を使用して要求をやり直します。

### <a id="HostedServiceNotFound"></a>HostedServiceNotFound
- **説明**: クラスターのホステッド サービス *nameOfYourHostedService* が見つかりませんでした。  
- **対応策**: クラスターがエラー状態である場合は、クラスターを削除し、やり直します。 

### <a id="HostedServiceWithNoDeployment"></a>HostedServiceWithNoDeployment
- **説明**: ホステッド サービス *nameOfYourHostedService* には、関連付けられた展開がありません。  
- **対応策**: クラスターがエラー状態である場合は、クラスターを削除し、やり直します。 

### <a id="InsufficientResourcesCores"></a>InsufficientResourcesCores
- **説明**: サブスクリプション ID *yourSubscriptionId* に、クラスター *yourClusterName* を作成するコアが残されていません。必要: *resourcesRequired*、利用可能: *resourcesAvailable*。  
- **対応策**: サブスクリプションのリソースに空きを作るか、サブスクリプションで利用可能なリソースを増やし、クラスターの作成をやり直します。

### <a id="InsufficientResourcesHostedServices"></a>InsufficientResourcesHostedServices
- **説明**: サブスクリプション ID *yourSubscriptionId* に、新しいホステッド サービスがクラスター *yourClusterName* を作成するクォータがありません。  
- **対応策**: サブスクリプションのリソースに空きを作るか、サブスクリプションで利用可能なリソースを増やし、クラスターの作成をやり直します。

### <a id="InternalErrorRetryRequest"></a>InternalErrorRetryRequest
- **説明**: サーバーで内部エラーが発生しました。要求を再試行してください。  
- **対応策**: 要求をやり直してください。 

### <a id="InvalidAzureStorageLocation"></a>InvalidAzureStorageLocation
- **説明**: Azure ストレージの場所 *dataRegionName* が有効な場所ではありません。リージョンが正しいことを確認し、要求を再試行してください。   
- **対応策**: HDInsight をサポートするストレージの場所を選択し、クラスターが併置されていることを確認して、操作をやり直します。 

### <a id="InvalidNodeSizeForDataNode"></a>InvalidNodeSizeForDataNode
- **説明**: データ ノードの VM サイズが無効です。すべてのデータ ノードでサポートされているのは、'L VM' サイズのみです。  
- **対応策**: データ ノードでサポートされているノード サイズを指定し、操作をやり直します。 

### <a id="InvalidNodeSizeForHeadNode"></a>InvalidNodeSizeForHeadNode
- **説明**: ヘッド ノードの VM サイズが無効です。ヘッド ノードでサポートされているのは 'XL VM' サイズのみです。  
- **対応策**: ヘッド ノードでサポートされているノード サイズを指定して、操作をやり直します。

### <a id="InvalidRightsForDeploymentDeletion"></a>InvalidRightsForDeploymentDeletion
- **説明**: 使用されているサブスクリプション ID *yourSubscriptionId* に、クラスター *yourClusterName* の削除操作を実行するための十分なアクセス許可がありません。  
- **対応策**: クラスターがエラー状態である場合は、クラスターを削除してやり直します。  

### <a id="InvalidStorageAccountBlobContainerName"></a>InvalidStorageAccountBlobContainerName
- **説明**: 外部ストレージ アカウントの BLOB コンテナー名 *yourContainerName* が無効です。名前の先頭にアルファベットが使用され、名前には小文字、数字、およびダッシュのみが使用されていることを確認してください。  
- **対応策**: 有効なストレージ アカウント BLOB コンテナー名を指定して、操作をやり直します。

### <a id="InvalidStorageAccountConfigurationSecretKey"></a>InvalidStorageAccountConfigurationSecretKey
- **説明**: 秘密キーの詳細を設定するには、外部ストレージ アカウント *yourStorageAccountName* の構成が必要です。  
- **対応策**: ストレージ アカウントの有効な秘密キーを指定し、操作をやり直します。

### <a id="InvalidVersionHeaderFormat"></a>InvalidVersionHeaderFormat
- **説明**: バージョン ヘッダー *yourVersionHeader* が有効な形式 yyyy-mm-dd ではありません。  
- **対応策**: 有効な形式のバージョン ヘッダーを指定して、要求をやり直します。 

### <a id="MoreThanOneHeadNode"></a>MoreThanOneHeadNode
- **説明**: 無効なクラスター構成です。ヘッド ノード構成が複数見つかりました。  
- **対応策**: 構成を編集して、ヘッド ノードが 1 つだけ指定されるようにします。 

### <a id="OperationTimedOutRetryRequest"></a>OperationTimedOutRetryRequest
- **説明**: 許容時間または最大試行回数内に操作を完了できませんでした。要求を再試行してください。  
- **対応策**: 要求をやり直してください。 

### <a id="ParameterNullOrEmpty"></a>ParameterNullOrEmpty
- **説明**: パラメーター *yourParameterName* を null または空にすることはできません。  
- **対応策**: パラメーターの有効な値を指定します。 

### <a id="PreClusterCreationValidationFailure"></a>PreClusterCreationValidationFailure
- **説明**: クラスターの作成要求入力が 1 つ以上、無効です。入力値が正しいことを確認して、要求を再試行してください。  
- **対応策**: 入力値が正しいことを確認して、要求を再試行してください。 

### <a id="RegionCapabilityNotAvailable"></a>RegionCapabilityNotAvailable
- **説明**: リージョン *yourRegionName* およびサブスクリプション ID *yourSubscriptionId* ではリージョン能力が利用できません。  
- **対応策**: HDInsight クラスターをサポートするリージョンを指定します。パブリックにサポートされているリージョンは、東南アジア、西ヨーロッパ、北ヨーロッパ、米国東部、米国西部です。 

### <a id="StorageAccountNotColocated"></a>StorageAccountNotColocated
- **説明**: ストレージ アカウント *yourStorageAccountName* がリージョン *currentRegionName* にあります。これはクラスターのリージョン *yourClusterRegionName* と同じにする必要があります。  
- **対応策**: クラスターと同じリージョンにストレージ アカウントを指定します。または、データが既にストレージ アカウントにある場合は、既存のストレージ アカウントと同じリージョンに新しいクラスターを作成します。ポータルを使っている場合は、この問題があると、事前に通知されます。 

### <a id="SubscriptionIdNotActive"></a>SubscriptionIdNotActive
- **説明**: 指定されたサブスクリプション ID *yourSubscriptionId* がアクティブではありません。  
- **対応策**: サブスクリプションを再度アクティブにするか、新しい有効なサブスクリプションを取得します。

### <a id="SubscriptionIdNotFound"></a>SubscriptionIdNotFound
- **説明**: サブスクリプション ID *yourSubscriptionId* が見つかりません。  
- **対応策**: サブスクリプション ID が有効であることを確認し、操作をやり直します。 

### <a id="UnableToResolveDNS"></a>UnableToResolveDNS
- **説明**: DNS *yourDnsUrl* を解決できません。BLOB エンドポイントの完全修飾 URL が指定されていることを確認してください。  
- **対応策**: 有効な BLOB URL を指定します。URL は、先頭が *http://* で末尾が *.com* であることを含め、完全に有効である必要があります。完全修飾 URL は通常、manage.windowsazure.com ポータルの [ストレージ] タブに表示されています。
### <a id="UnableToVerifyLocationOfResource"></a>UnableToVerifyLocationOfResource
- **説明**: リソース *yourDnsUrl* の場所を確認できません。BLOB エンドポイントの完全修飾 URL が指定されていることを確認してください。  
- **対応策**: 有効な BLOB URL を指定します。URL は、先頭が *http://* で末尾が *.com* であることを含め、完全に有効である必要があります。完全修飾 URL は通常、manage.windowsazure.com ポータルの [ストレージ] タブに表示されています。
### <a id="VersionCapabilityNotAvailable"></a>VersionCapabilityNotAvailable
- **説明**: バージョン *specifiedVersion* およびサブスクリプション ID *yourSubscriptionId* ではバージョン能力が利用できません。  
- **対応策**: 利用可能なバージョンを選択し、操作をやり直します。 

### <a id="VersionNotSupported"></a>VersionNotSupported
- **説明**: バージョン *specifiedVersion* がサポートされていません。   
- **対応策**: サポートされているバージョンを選択し、操作をやり直します。

### <a id="VersionNotSupportedInRegion"></a>VersionNotSupportedInRegion
- **説明**: バージョン *specifiedVersion* は Azure リージョン *specifiedRegion* では使用できません。  
- **対応策**: 指定したリージョンでサポートされているバージョンを選択し、操作をやり直します。 

### <a id="WasbAccountConfigNotFound"></a>WasbAccountConfigNotFound
- **説明**: 無効なクラスター構成です。必要な WASB アカウント構成が外部アカウントに見つかりません。  
- **対応策**: アカウントが存在し、構成で適切に指定されていることを確認して、操作をやり直します。 

## <a id="resources"></a>Additional Debugging Resources

* [Azure HDInsight SDK のドキュメント][hdinsight-sdk-documentation]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[image-hdi-debugging-error-messages-portal]: ./media/hdinsight-debug-jobs/hdi-debug-errormessages-portal.png






 

<!---HONumber=July15_HO4-->