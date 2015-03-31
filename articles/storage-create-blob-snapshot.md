<properties 
	pageTitle="BLOB のスナップショットの作成" 
	description="Azure ストレージ BLOB のスナップショットを作成する方法について" 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="tamram"/>

#BLOB のスナップショットの作成

BLOB ではスナップショットを作成できます。スナップショットは、ある時点で作成された読み取り専用の BLOB です。作成されたスナップショットは、読み取り、コピー、削除はできますが、変更はできません。スナップショットを使用すると、BLOB をある時点での表示内容のままバックアップできます。

BLOB のスナップショットには、スナップショットの取得元となるベース BLOB と同じ名前が付いており、スナップショットが取得された時間を示す DateTime 値が付加されています。たとえば、ページ BLOB の URI が http://storagesample.core.blob.windows.net/mydrives/myvhd である場合、スナップショットの URI は http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z のようになります。この値を使用して、今後の操作でスナップショットを参照できます。BLOB のスナップショットは URI を共有しており、この DateTime 値によってのみ識別されます。クライアント ライブラリ コードでは、BLOB の Snapshot プロパティによって、ベース BLOB に基づいてスナップショットを一意に識別する DateTime 値が返されます。この値を使用して、スナップショットを詳細に操作できます。

BLOB に対するスナップショットの数に制限はありません。スナップショットは、明示的に削除されるまで保持されます。スナップショットは、コピー元 BLOB よりも長く保持できません。BLOB に関連付けられたスナップショットを列挙して、現在のスナップショットを追跡できます。

##プロパティの継承

BLOB のスナップショットを作成すると、システム プロパティが同じ値でスナップショットにコピーされます。.NET ストレージ クライアント ライブラリについてコピーされるシステム プロパティを次に示します。

- ContentType 

- ContentEncoding 

- ContentLanguage

- Length

- CacheControl

- ContentMd5 


ベース BLOB に関連付けられたリースはスナップショットにコピーされません。スナップショットはリースできません。

##スナップショットのコピー 

BLOB やスナップショットに関連するコピー操作は次のルールに従います。

- スナップショットをベース BLOB にコピーします。スナップショットをベース BLOB に昇格することにより、BLOB を以前のバージョンに復元できます。スナップショットは元のままですが、スナップショットのソースは読み取りや書き込みができるコピーによって上書きされます。

- スナップショットを別の名前でコピー先 BLOB にコピーします。結果となるコピー先 BLOB は書き込み可能な BLOB であり、スナップショットではありません。

- コピー元 BLOB をコピーしても、コピー元 BLOB のスナップショットはコピー先にコピーされません。コピー先 BLOB をコピーで上書きした場合、コピー先 BLOB に関連付けられたスナップショットは名前が変わっても元のままです。 

- ブロック BLOB のスナップショットを作成する場合、BLOB のコミットされたブロック リストもスナップショットにコピーされます。コミット前のブロックはコピーされません。

##アクセス条件の指定 

アクセス条件を指定することで、その条件を満たしている場合にのみスナップショットを作成できます。アクセス条件を指定するには、AccessCondition プロパティを使用します。指定した条件を満たしていない場合、スナップショットは作成されず、BLOB サービスはステータス コード HTTPStatusCode.PreconditionFailed を返します。

##スナップショットの削除 

スナップショットを持つ BLOB は、スナップショットが削除されてからでないと、削除できません。スナップショットは個別に削除できます。また、コピー元 BLOB を削除するとき、ストレージ サービスを利用することですべてのスナップショットを削除できます。スナップショットを持つ BLOB を削除しようとすると、エラーが返されます。

##Premium Storage のスナップショット
Premium Storage でスナップショットを使うには次のルールに従います。

- Premium Storage アカウントにおけるページ BLOB あたりのスナップショットの数は 100 が上限です。上限を超えると、スナップショット BLOB の操作でエラー コード 409 (SnapshotCountExceeded) が返されます。

- Premium Storage アカウントの ページ BLOB のスナップショットは 10 分ごとに作成されます。この頻度を超えると、スナップショット BLOB の操作でエラー コード 409 (SnaphotOperationRateExceeded) が返されます。

- Premium Storage アカウントの ページ BLOB スナップショットの読み取りは、Get Blob 経由ではサポートされていません。Premium Storage アカウントのスナップショットで Get Blob をコールすると、エラー コード 400 (不正な処理) が返されます。ただし、スナップショットに対する Get Blob プロパティや Get Blob メタデータのコールはサポートされています。

- スナップショットを読み込むには、Copy Blob を操作してアカウントの別のページ BLOB にスナップショットをコピーします。すでにスナップショットがある BLOB にはスナップショットをコピーできません。コピー先の BLOB にスナップショットがある場合は、BLOB をコピーするとエラー コード 409 (SnapshotsPresent) が返されます。

##スナップショットへの絶対 URI の作成 

次のコード例は、ベース BLOB オブジェクトからのスナップショットの絶対 URI を作成します。

C#

	var snapshot = blob.CreateSnapshot();
	var uri = Microsoft.WindowsAzure.StorageClient.Protocol.BlobRequest.Get
    (snapshot.Uri, 
    0, 
    snapshot.SnapshotTime.Value, 
    null).Address.AbsoluteUri;

<!--HONumber=47-->
