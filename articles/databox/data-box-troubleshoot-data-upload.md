---
title: Azure Data Box、Azure Data Box Heavy デバイスからのアップロードのコピー エラーを確認する
description: Azure Data Box または Azure Data Box Heavy デバイスからのファイルのアップロードを妨げる、再試行不可能なエラーの確認とフォローアップについて説明します。
services: databox
author: v-dalc
ms.service: databox
ms.subservice: pod
ms.topic: troubleshooting
ms.date: 10/21/2021
ms.author: alkohli
ms.openlocfilehash: 019d4da7654c3cdf2eec7fc3b6f2434e1f42cd47
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130218168"
---
# <a name="review-copy-errors-in-uploads-from-azure-data-box-and-azure-data-box-heavy-devices"></a>Azure Data Box および Azure Data Box Heavy デバイスからのアップロードのコピー エラーを確認する

この記事では、Azure Data Box または Azure Data Box Heavy デバイスからクラウドへのファイルのアップロードを妨げることがある、再試行不可能なエラーの確認とフォローアップについて説明します。

> [!NOTE]
> この記事の情報はインポート注文にのみ適用されます。

## <a name="upload-errors-notification"></a>アップロード エラーの通知

デバイスから Azure にデータがアップロードされる際、再試行では解決できない構成エラーが原因で一部のファイルのアップロードが失敗することがあります。 この場合、通知が送信され、今後のアップロードのためにエラーを確認して修正する機会が与えられます。

Azure portal に、次の通知が表示されます。 エラーはデータ コピー ログに一覧表示され、 **[データのコピー パス]** を使用して開くことができます。 エラーを解決するためのガイダンスについては、「[再試行不可能なアップロード エラーの概要](#summary-of-non-retryable-upload-errors)」を参照してください。

![アップロード中に発生したエラーの通知](media/data-box-troubleshoot-data-upload/copy-completed-with-errors-notification-01.png)

これらのエラーは修正できません。 アップロードは完了しましたが、エラーが発生しました。 この通知は、ネットワーク転送または新しいインポート注文を使用して別のアップロードを実行する前に修正する必要がある構成の問題について通知しています。

エラーを確認し、続行する準備ができたことを確認して応答すると、データがデバイスから安全に消去されます。 通知に応答しない場合、注文は 14 日後に自動的に完了します。 詳細な手順については、「[エラーを確認して続行する](#review-errors-and-proceed)」を参照してください。


## <a name="review-errors-and-proceed"></a>エラーを確認して続行する

注文は 14 日後に自動的に完了します。 通知に応答することで、より迅速に進めることができます。

[!INCLUDE [data-box-review-nonretryable-errors](../../includes/data-box-review-nonretryable-errors.md)]


## <a name="summary-of-non-retryable-upload-errors"></a>再試行不可能なアップロード エラーの概要

次の再試行不可能なエラーが発生すると、通知が生成されます。

|エラーのカテゴリ                    |エラー コード |エラー メッセージ                                                                             |
|----------------------------------|-----------|------------------------------------------------------------------------------------------|
|UploadErrorCloudHttp              |400        |Bad Request (file name not valid) (無効な要求 (ファイル名が無効)) [詳細情報](#bad-request-file-name-not-valid)。|
|UploadErrorCloudHttp              |400        |The value for one of the HTTP headers is not in the correct format. (いずれかの HTTP ヘッダーの値の形式が正しくありません。) [詳細については、こちらを参照してください](#the-value-for-one-of-the-http-headers-is-not-in-the-correct-format)。|
|UploadErrorCloudHttp              |409        |This operation is not permitted as the blob is immutable due to a policy. (この操作は、ポリシーにより BLOB が不変であるため、許可されていません。) [詳細については、こちらを参照してください](#this-operation-is-not-permitted-as-the-blob-is-immutable-due-to-policy)。|
|UploadErrorCloudHttp              |409        |The total provisioned capacity of the shares cannot exceed the account maximum size limit. (共有のプロビジョニングされた容量の合計は、アカウントの最大サイズ制限を超えることはできません。) [詳細については、こちらを参照してください](#the-total-provisioned-capacity-of-the-shares-cannot-exceed-the-account-maximum-size-limit)。|
|UploadErrorCloudHttp              |409        |The blob type is invalid for this operation. (この操作では、この BLOB の種類は無効です。) [詳細については、こちらを参照してください](#the-blob-type-is-invalid-for-this-operation)。|
|UploadErrorCloudHttp              |409        |There is currently a lease on the blob and no lease ID was specified in the request. (現在、BLOB にリースがありますが、リクエストでリース ID が指定されていませんでした。) [詳細については、こちらを参照してください](#there-is-currently-a-lease-on-the-blob-and-no-lease-id-was-specified-in-the-request)。|
|UploadErrorManagedConversionError |409        |インポート中の BLOB のサイズが無効です。 BLOB サイズは `<blob-size>` バイトです。 サポートされているサイズは、20,971,520 バイトから 8,192 GiB までです。 [詳細情報](#the-size-of-the-blob-being-imported-is-invalid-the-blob-size-is-blob-size-bytes-supported-sizes-are-between-20971520-bytes-and-8192-gib)|
<!--Temporarily removed from table: Bad Request (file property failure for Azure Files)-->

データ コピー ログの内容の詳細については、「[Azure Data Box と Azure Data Box Heavy のインポート注文の追跡とイベント ログ記録](data-box-logs.md)」を参照してください。

データのアップロード中には、他の REST API のエラーが発生する可能性があります。 詳細については、[一般的な REST API のエラー コード](/rest/api/storageservices/common-rest-api-error-codes)に関するページを参照してください。

> [!NOTE]
> エラーの説明にある「**補足情報**」のセクションでは、新しいインポート注文を作成したり、ネットワーク転送を実行する前に、データ構成を更新する方法が説明されています。 現在のアップロードでは、このエラーを修正することはできません。


### <a name="bad-request-file-name-not-valid"></a>Bad Request (file name not valid) (無効な要求 (ファイル名が無効))

**エラー カテゴリ:** UploadErrorCloudHttp 

**エラー コード:** 400

**エラーの説明:** ファイルの名前付けに関する問題の大部分は、**発送準備** フェーズ中に検出されるか、アップロード中に自動的に修正されます (結果として **[Copy with warnings]\(警告ありでコピー\)** 状態になります)。 無効なファイル名が検出できなかった場合、ファイルは Azure にアップロードされません。

**補足情報:** 現在のアップロードでは、このエラーを修正することはできません。 アップロードは完了しましたが、エラーが発生しました。 ネットワーク転送を実行したり、または新しい注文を開始する前に、Azure Files の名前付けに関する要件を満たすよう一覧表示されたファイルの名前を変更します。 名前付けに関する要件については、「[ディレクトリ名とファイル名](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)」を参照してください。


<!--TEMPORARILY REMOVED. Product team may restore later. ### Bad Request (File property failure for Azure Files)

**Error category:** UploadErrorCloudHttp 

**Error code:** 400

**Error description:** Data import will fail if the upload of file properties fails for Azure Files.  

**Follow-up:** You can't fix this error in the current upload. The upload will complete with errors. Before you do a network transfer or start a new import order, *GET TROUBLESHOOTING*.-->


### <a name="the-value-for-one-of-the-http-headers-is-not-in-the-correct-format"></a>The value for one of the HTTP headers is not in the correct format (いずれかの HTTP ヘッダーの値の形式が正しくありません)

**エラー カテゴリ:** UploadErrorCloudHttp 

**エラー コード:** 400

**エラーの説明:** Azure storage における BLOB の形式またはサイズに関する要件を満たしていないため、一覧表示されている BLOB をアップロードできませんでした。

**補足情報:** 現在のアップロードでは、このエラーを修正することはできません。 アップロードは完了しましたが、エラーが発生しました。 ネットワーク転送を実行したり、新しいインポート注文を開始する前に、次のことを確認してください。

- 一覧表示されているページ BLOB が、512 バイトのページ境界に揃えられている。

- 一覧表示されているブロック BLOB が、4.75 から TiB の最大サイズを超えていない。


### <a name="this-operation-is-not-permitted-as-the-blob-is-immutable-due-to-policy"></a>This operation is not permitted as the blob is immutable due to a policy (この操作は、ポリシーにより BLOB が不変であるため、許可されていません)

**エラー カテゴリ:** UploadErrorCloudHttp 

**エラー コード:** 409

**エラーの説明:** Blob ストレージ コンテナーが Write Once Read Many (WORM) として構成されている場合、コンテナーに既に格納されている BLOB のアップロードは失敗します。

**補足情報:** 現在のアップロードでは、このエラーを修正することはできません。 アップロードは完了しましたが、エラーが発生しました。 ネットワーク転送を実行したり、新しいインポート注文を開始する前に、一覧表示されている BLOB が不変なストレージ コンテナーに含まれていないことを確認してください。 詳細については、「[不変ストレージを使用してビジネスに不可欠な BLOB データを保存する](../storage/blobs/immutable-storage-overview.md)」を参照してください。


### <a name="the-total-provisioned-capacity-of-the-shares-cannot-exceed-the-account-maximum-size-limit"></a>The total provisioned capacity of the shares cannot exceed the account maximum size limit (共有のプロビジョニングされた容量の合計は、アカウントの最大サイズ制限を超えることはできません)

**エラー カテゴリ:** UploadErrorCloudHttp 

**エラー コード:** 409

**エラーの説明:** データの合計サイズがストレージ アカウントのサイズ制限を超えているため、アップロードに失敗しました。 たとえば、FileStorage アカウントの最大容量は 100 TiB です。 データの合計サイズが 100 TiB を超えると、アップロードは失敗します。  

**補足情報:** 現在のアップロードでは、このエラーを修正することはできません。 アップロードは完了しましたが、エラーが発生しました。 ネットワーク転送を実行したり、新しいインポート注文を開始する前に、ストレージ アカウント内のすべての共有の合計容量がストレージ アカウントのサイズ制限を超えていないことを確認してください。 詳細については、「[Azure ストレージ アカウントのサイズ制限](data-box-limits.md#azure-storage-account-size-limits)」を参照してください。


### <a name="the-blob-type-is-invalid-for-this-operation"></a>The blob type is invalid for this operation (この操作では、この BLOB の種類は無効です)

**エラー カテゴリ:** UploadErrorCloudHttp 

**エラー コード:** 409

**エラーの説明:** アップロード先 BLOB のデータまたはプロパティが変更されている場合、クラウド上の BLOB へのデータ インポートは失敗します。

**補足情報:** 現在のアップロードでは、このエラーを修正することはできません。 アップロードは完了しましたが、エラーが発生しました。 ネットワーク転送を実行したり、新しいインポート注文を開始する前に、一覧表示されている BLOB またはそれらのプロパティが、アップロード中に同時に変更されていないことを確認してください。

### <a name="there-is-currently-a-lease-on-the-blob-and-no-lease-id-was-specified-in-the-request"></a>There is currently a lease on the blob and no lease ID was specified in the request (現在、BLOB にリースがありますが、リクエストでリース ID が指定されていませんでした)

**エラー カテゴリ:** UploadErrorCloudHttp 

**エラー コード:** 409

**エラーの説明:** アップロード先 BLOB にアクティブなリースが含まれている場合、クラウド上の BLOB へのデータ インポートは失敗します。

**補足情報:** 現在のアップロードでは、このエラーを修正することはできません。 アップロードは完了しましたが、エラーが発生しました。 ネットワーク転送を実行したり、新しいインポート注文を開始する前に、一覧表示されている BLOB にアクティブなリースが存在していないことを確認してください。 詳細については、「[BLOB でのペシミスティック コンカレンシー制御](../storage/blobs/concurrency-manage.md?tabs=dotnet#pessimistic-concurrency-for-blobs)」を参照してください。


### <a name="the-size-of-the-blob-being-imported-is-invalid-the-blob-size-is-blob-size-bytes-supported-sizes-are-between-20971520-bytes-and-8192-gib"></a>インポート中の BLOB のサイズが無効です。 BLOB サイズは `<blob-size>` バイトです。 サポートされているサイズは、20,971,520 バイトから 8,192 GiB までです。

**エラー カテゴリ:** UploadErrorManagedConversionError

**エラー コード:** 409

**エラーの説明:** マネージド ディスクに変換できるサイズではないため、表示されているページ BLOB をアップロードできませんでした。 マネージド ディスクに変換するには、ページ BLOB のサイズが 20 MB (20,971,520 バイト) から 8,192 GiB である必要があります。

**補足情報:** 現在のアップロードでは、このエラーを修正することはできません。 アップロードは完了しましたが、エラーが発生しました。 ネットワーク転送を実行したり、新しいインポート注文を開始する前に、一覧表示されている各 BLOB のサイズが 20 MB から 8,192 GiB の範囲に収まっていることを確認してください。


## <a name="next-steps"></a>次のステップ

- [一般的な REST API のエラーを確認する](/rest/api/storageservices/common-rest-api-error-codes)。
- [Azure へのデータのアップロードを確認する](data-box-deploy-picked-up.md#verify-data-upload-to-azure)