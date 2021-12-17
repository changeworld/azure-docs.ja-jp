---
title: Dynamics 365、Dataverse (Common Data Service)、Dynamics CRM のコネクタのトラブルシューティング
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory と Azure Synapse Analytics で Dynamics 365、Dataverse (Common Data Service)、Dynamics CRM のコネクタの問題を解決する方法について説明します。
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/13/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 28aa7fee3ab7cf2bbc8f10d1ba2f5ea54a792cd6
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130066674"
---
# <a name="troubleshoot-the-dynamics-365-dataverse-common-data-service-and-dynamics-crm-connectors-in-azure-data-factory-and-azure-synapse"></a>Azure Data Factory と Azure Synapse で Dynamics 365、Dataverse (Common Data Service)、Dynamics CRM のコネクタの問題を解決します。

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory と Azure Synapse で Dynamics 365、Dataverse (Common Data Service)、Dynamics CRM のコネクタの一般的な問題を解決する方法について提案します。

## <a name="error-code-dynamicscreateserviceclienterror"></a>エラー コード:DynamicsCreateServiceClientError

- **メッセージ**: `This is a transient issue on Dynamics server side. Try to rerun the pipeline.`

- **原因**:この問題は、Dynamics サーバー側での一時的な問題です。

- **推奨事項**:パイプラインを再実行してください。 再度失敗した場合は、並列処理を減らしてみてください。 問題が解決しない場合は、Dynamics サポートにお問い合わせください。


## <a name="missing-columns-when-you-import-a-schema-or-preview-data"></a>スキーマをインポートするとき、またはデータをプレビューするときに列が見つからない

- **現象**:スキーマをインポートするとき、またはデータをプレビューするときに、いくつかの列が欠落しています。 エラー メッセージ: `The valid structure information (column name and type) are required for Dynamics source.`

- **原因**: この問題は仕様によるものです。最初の 10 個のレコード内の値が含まれていない列を Azure Data Factory および Synapse のパイプラインでは表示できないためです。 追加した列が正しい形式であることを確認します。 

- **推奨事項**:マッピング タブで列を手動で追加します。


## <a name="error-code-dynamicsmissingtargetformultitargetlookupfield"></a>エラー コード:DynamicsMissingTargetForMultiTargetLookupField

- **メッセージ**: `Cannot find the target column for multi-target lookup field: '%fieldName;'.`

- **原因**:ターゲット列がソースまたは列マッピングに存在しません。

- **推奨事項**:  
  1. ソースにターゲット列が含まれていることを確認します。 
  2. 列マッピングにターゲット列を追加します。 シンク列が *{fieldName}@EntityReference* の形式であることを確認してください。


## <a name="error-code-dynamicsinvalidtargetformultitargetlookupfield"></a>エラー コード:DynamicsInvalidTargetForMultiTargetLookupField

- **メッセージ**: `The provided target: '%targetName;' is not a valid target of field: '%fieldName;'. Valid targets are: '%validTargetNames;'`

- **原因**:複数ターゲット検索フィールドのターゲット エンティティとして不正なエンティティ名が指定されています。

- **推奨事項**:複数ターゲット検索フィールドの有効なエンティティ名を指定します。


## <a name="error-code-dynamicsinvalidtypeformultitargetlookupfield"></a>エラー コード:DynamicsInvalidTypeForMultiTargetLookupField

- **メッセージ**: `The provided target type is not a valid string. Field: '%fieldName;'.`

- **原因**:ターゲット列の値が文字列ではありません。

- **推奨事項**:複数ターゲット検索フィールドに有効な文字列を指定します。


## <a name="error-code-dynamicsfailedtorequetserver"></a>エラー コード:DynamicsFailedToRequetServer

- **メッセージ**: `The Dynamics server or the network is experiencing issues. Check network connectivity or check Dynamics server log for more details.`

- **原因**:Dynamics サーバーが不安定であるか、アクセスできないか、ネットワークに問題が発生しています。

- **推奨事項**:詳細については、ネットワーク接続または Dynamics サーバーのログを確認してください。 さらに支援が必要な場合は、Dynamics サポートにお問い合わせください。


## <a name="error-code-dynamicsfailedtoconnect"></a>エラー コード: DynamicsFailedToConnect 
 
 - **メッセージ**: `Failed to connect to Dynamics: %message;` 
 
 - **原因と推奨事項**: このエラーはさまざまな原因により発生する可能性があります。 考えられる原因の分析および関連する推奨事項については、以下の一覧を確認してください。

    | 原因分析                                               | 推奨                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | `ERROR REQUESTING ORGS FROM THE DISCOVERY SERVERFCB 'EnableRegionalDisco' is disabled.` が表示されます。あるいはユースケースが次の 3 つの条件の **すべて** に一致する場合は `Unable to Login to Dynamics CRM, message:ERROR REQUESTING Token FROM THE Authentication context - USER intervention required but not permitted by prompt behavior AADSTS50079: Due to a configuration change made by your administrator, or because you moved to a new location, you must enroll in multi-factor authentication to access '00000007-0000-0000-c000-000000000000'` が表示されます。 <li>Dynamics 365、Common Data Service、または Dynamics CRM に接続している。</li><li>Office365 認証を使用している。</li><li>テナントおよびユーザーが Azure Active Directory で[条件付きアクセス](../active-directory/conditional-access/overview.md)に構成されている、あるいは多要素認証が必要である、あるいはその両方 (この Dataverse ドキュメントの[リンク](/powerapps/developer/data-platform/authenticate-office365-deprecation)を参照)。</li>  これらの状況下で、接続は 2021 年 6 月 8 日までは成功しました。 2021 年 6 月 9 日から、リージョンの Discovery Service の廃止に伴い、接続が失敗し始めます (この[リンク](/power-platform/important-changes-coming#regional-discovery-service-is-deprecated)を参照)。| テナントおよびユーザーが Azure Active Directory で[条件付きアクセス](../active-directory/conditional-access/overview.md)に構成されている場合、または多要素認証が必要な場合、およびその両方の場合は、2021 年 6 月 8 日以降は ‘Azure AD サービス プリンシパル’ を使用して認証する必要があります。 詳細な手順についてこちらの[リンク](./connector-dynamics-crm-office-365.md#prerequisites)を参照してください。|
    |エラー メッセージに `Office 365 auth with OAuth failed` が表示された場合は、OAuth と互換性のない構成がサーバーに存在している可能性があることを意味します。| <li>エラー メッセージの詳細については、Dynamics サポート チームにお問い合わせください。</li><li>サービス プリンシパル認証を使用し、「[例: Azure AD サービス プリンシパルと証明書認証を使用した Dynamics Online](./connector-dynamics-crm-office-365.md#example-dynamics-online-using-azure-ad-service-principal-and-certificate-authentication)」を参照してください。</li>
    |エラー メッセージに `Unable to retrieve authentication parameters from the serviceUri` が表示された場合は、誤った Dynamics サービスの URL またはプロキシ/ファイアウォールを入力してトラフィックを遮断していることを意味します。 |<li>リンクされたサービスに正しいサービス URI が指定されていることを確認してください。</li><li>自己ホスト型 IR を使用する場合は、ファイアウォール/プロキシによって Dynamics サーバーへの要求が遮断されていないことを確認してください。</li> |
    |エラー メッセージに `An unsecured or incorrectly secured fault was received from the other party` が表示された場合は、サーバー側から予期しない応答を取得したことを意味します。  | <li>Office 365 認証を使用する場合は、ユーザー名とパスワードが正しいことを確認してください。 </li><li> 正しいサービス URI が入力されていることを確認してください。</li><li>リージョンの CRM URL ("crm" の後に数字が含まれている URL) を使用する場合は、正しいリージョン識別子を使用していることを確認してください。</li><li>Dynamics サポート チームにお問い合わせください。</li>|
    |エラー メッセージに `No Organizations Found` が表示された場合は、組織名が間違っているか、サービス URL で誤った CRM リージョン識別子が使用されていることを意味します。|<li>正しいサービス URI が入力されていることを確認してください。</li><li>リージョンの CRM URL ("crm" の後に数字が含まれている URL) を使用する場合は、正しいリージョン識別子を使用していることを確認してください。</li><li>Dynamics サポート チームにお問い合わせください。</li>|
    | `401 Unauthorized` および AAD 関連のエラー メッセージが表示された場合は、サービス プリンシパルに問題があることを意味します。 |エラー メッセージのガイダンスに従って、サービス プリンシパルの問題を修正してください。 |
   |他のエラーの場合、通常はサーバー側に問題があります。 |[XrmToolBox](https://www.xrmtoolbox.com/) を使用して接続します。 エラーが引き続き発生する場合は、Dynamics サポート チームに問い合わせてください。 |

## <a name="error-code-dynamicsoperationfailed"></a>エラー コード: DynamicsOperationFailed 
 
- **メッセージ**: `Dynamics operation failed with error code: %code;, error message: %message;.` 

- **原因**: サーバー側で操作が失敗しました。 

- **推奨事項**: エラー メッセージ (`Dynamics operation failed with error code: {code}`) から Dynamics 操作のエラー コードを抽出し、「[Web サービス エラー コード](/powerapps/developer/data-platform/org-service/web-service-error-codes)」で詳細を参照してください。 必要に応じて、Dynamics サポート チームに問い合わせることができます。 
 
 
## <a name="error-code-dynamicsinvalidfetchxml"></a>エラー コード: DynamicsInvalidFetchXml 
  
- **メッセージ**: `The Fetch Xml query specified is invalid.` 

- **原因**: FetchXML にエラーが存在します。  

- **推奨事項**: FetchXML でエラーを修正してください。 
 
 
## <a name="error-code-dynamicsmissingkeycolumns"></a>エラー コード: DynamicsMissingKeyColumns 
 
- **メッセージ**: `Input DataSet must contain keycolumn(s) in Upsert/Update scenario. Missing key column(s): %column;`
 
- **原因**: ソース データにシンク エンティティのキー列が含まれていません。 

- **推奨事項**: ソース データ内にキー列があることを確認するか、ソース列をシンク エンティティのキー列にマップします。 
 
 
## <a name="error-code-dynamicsprimarykeymustbeguid"></a>エラー コード: DynamicsPrimaryKeyMustBeGuid 
 
- **メッセージ**: `The primary key attribute '%attribute;' must be of type guid.` 
 
- **原因**: 主キー列が 'Guid' 型ではありません。 
 
- **推奨事項**: ソース データの主キー列が 'Guid' 型であることを確認してください。 
 

## <a name="error-code-dynamicsalternatekeynotfound"></a>エラー コード: DynamicsAlternateKeyNotFound 
 
- **メッセージ**: `Cannot retrieve key information of alternate key '%key;' for entity '%entity;'.` 
 
- **原因**: 指定された代替キーが存在しません。キー名が間違っているか、アクセス許可が不足している可能性があります。 
 
- **推奨事項**: <br/> 
    - キー名の入力ミスを修正します。<br/> 
    - エンティティに対する十分なアクセス許可があることを確認します。 
 
 
## <a name="error-code-dynamicsinvalidschemadefinition"></a>エラー コード: DynamicsInvalidSchemaDefinition 
 
- **メッセージ**: `The valid structure information (column name and type) are required for Dynamics source.` 
 
- **原因**: 列マッピングのシンク列に 'type' プロパティがありません。 
 
- **推奨事項**: ポータルの JSON エディターを使用して、列マッピングのこれらの列に 'type' プロパティを追加できます。 

## <a name="the-copy-activity-from-the-dynamics-365-reads-more-rows-than-the-actual-number"></a>Dynamics 365 の Copy アクティビティで、実際の数よりも多くの行が読み込まれる

- **現象**: Dynamics 365 の Copy アクティビティで、実際の数よりも多くの行が読み込まれます。

- **原因**: Dynamics 365 サーバーでは、利用可能なレコード数が常に多く表示されます。 

- **推奨事項**: **XrmToolBox** を使用して、ページングを使用した FetchXML をテストします。 **XrmToolBox** と一部のインストールされているツールでレコード数を取得できます。 詳細については、[XrmToolBox](https://www.xrmtoolbox.com/) を参照してください。

## <a name="cannot-access-virtual-columns-from-dynamics-sources-in-the-copy-activity"></a>Copy アクティビティで Dynamics ソースの仮想列にアクセスできない

- **現象**: Copy アクティビティで Dynamics ソースの仮想列にアクセスできません。

- **原因**: 仮想列は現在サポートされていません。 

- **推奨事項**: オプション セットの値については、次のオプションに従って取得します。
  - オブジェクト タイプ コードは、[任意のエンティティのオブジェクト タイプ コードを取得する方法](https://powerobjects.com/tips-and-tricks/find-object-type-code-entity/)に関するページと、[Dynamics 365 のブログ](https://dynamicscrmdotblog.wordpress.com/)を参考にして取得することができます。
  - StringMap エンティティをターゲット エンティティにリンクし、関連付けられている値を取得できます。

## <a name="the-parallel-copy-in-a-dynamics-crm-data-store"></a>Dynamics CRM データ ストアの並列コピー

- **現象**: Dynamics CRM のデータ ストアで並列コピーを設定できるかどうか、または、[Degree of copy parallelism]\(コピーの並列度\) に設定できる値の範囲がわかりません。

- **推奨事項**: 並列コピーは並列処理を制御し、[Degree of copy parallelism]\(コピーの並列度\) セクションは 0 以外の値に設定できます。 数値が大きいと Dynamics サーバー側でスロットリングが発生し、スループットが低下する場合がありましたが、現在は公開 SDK を使用してスロットリングが処理されます。

  :::image type="content" source="./media/connector-troubleshoot-guide/degree-of-copy-parallelism-section.png" alt-text="コピーの並列度セクションの図。":::

## <a name="dynamics-type-conversion"></a>Dynamics 型の変換

- **現象**: GUID を Dynamics ソースの文字列に変換しようとしましたが、エラーが発生します。

- **原因**: Dynamics をソースとして使用した場合、型変換はサポートされません。

- **推奨事項**: ステージングを有効にして再試行します。

## <a name="next-steps"></a>次のステップ

トラブルシューティングのその他のヘルプについては、次のリソースを参照してください。

- [コネクタのトラブルシューティング ガイド](connector-troubleshoot-guide.md)
- [Data Factory ブログ](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory の機能のリクエスト](/answers/topics/azure-data-factory.html)
- [Azure のビデオ](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Microsoft Q&A ページ](/answers/topics/azure-data-factory.html)
- [Data Factory の Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Data Factory に関する Twitter 情報](https://twitter.com/hashtag/DataFactory)
