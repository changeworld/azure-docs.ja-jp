---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: 9b18687c0a6f3e48d94431e88be8ae8137c9dcdb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96011156"
---
| プロパティ | 説明 |
|:--- |:---|
|**accountName** | ストレージ アカウントの名前。 (例: `mystorageaccount`)。  |
|**requestUrl** | 要求された URL |
|**userAgentHeader** | 引用符で囲んだ **User-Agent ヘッダー** 値 (例: `WA-Storage/6.2.0 (.NET CLR 4.0.30319.42000; Win32NT 6.2.9200.0)`)。|
|**referrerHeader** | **Referrer** ヘッダー値 (例: `http://contoso.com/about.html`)。|
|**clientRequestId** | 要求の **x-ms-client-request-id** ヘッダー値 (例: `360b66a6-ad4f-4c4a-84a4-0ad7cb44f7a6`)。 |
|**etag** | 引用符で囲まれている、返されたオブジェクトの ETag 識別子 (例: `0x8D101F7E4B662C4`)。  |
|**serverLatencyMs** | 要求された操作の実行に要した合計の時間 (ミリ秒単位)。 この値には、ネットワークの遅延 (受信要求を読み取り、要求元に応答を送信する時間) は含まれません (例: `22`)。 |
|**serviceType** | この要求に関連付けられたサービス。 例: `blob`、`table`、`files`、`queue`。 |
|**operationCount** | 要求に関連する、ログに記録された各操作の数。 このカウントはインデックス `0` で始まります。 要求によっては、複数の操作が必要になる場合があります。 ほとんどの要求では 1 つの操作だけが実行されます (例: `1`)。 |
|**requestHeaderSize** | 要求ヘッダーのサイズ (バイト単位) (例: `578`)。 <br>要求が成功しなかった場合、この値は空になることがあります。 |
|**requestBodySize** | ストレージ サービスによって読み取られた要求パケットのサイズ (バイト単位) <br> (例: `0`)。 <br>要求が成功しなかった場合、この値は空になることがあります。  |
|**responseHeaderSize** | 応答ヘッダーのサイズ (バイト単位) (例: `216`)。 <br>要求が成功しなかった場合、この値は空になることがあります。  |
|**responseBodySize** | ストレージ サービスによって書き込まれた応答パケットのサイズ (バイト単位)。 要求が成功しなかった場合、この値は空になることがあります (例: `216`)。  |
|**requestMd5** | 要求の **Content-MD5** ヘッダーまたは **x-ms-content-md5** ヘッダーの値。 このフィールドに指定された MD5 ハッシュ値は、要求の内容を表します (例: `788815fd0198be0d275ad329cafd1830`)。 <br>このフィールドは空になる場合があります。  |
|**serverMd5** | ストレージ サービスによって計算された MD5 ハッシュの値 (例: `3228b3cf1069a5489b298446321f8521`)。 <br>このフィールドは空になる場合があります。  |
|**lastModifiedTime** | 返されたオブジェクトの最終更新日時 (LMT)  (例: `Tuesday, 09-Aug-11 21:13:26 GMT`)。 <br>複数のオブジェクトを返すことができる操作に対しては、このフィールドは空になります。 |
|**conditionsUsed** | 条件を表すキーと値のペアをセミコロンで区切った一覧。 条件は次のいずれかになります。 <li> If-Modified-Since <li> If-Unmodified-Since <li> If-Match <li> If-None-Match  <br> (例: `If-Modified-Since=Friday, 05-Aug-11 19:11:54 GMT`)。 |
|**contentLengthHeader** | ストレージ サービスに送信された要求の Content-Length ヘッダーの値。 要求が成功した場合、この値は requestBodySize と同じです。 要求が成功しなかった場合、この値は requestBodySize と同じ値にならないことや、空になることがあります。 |
|**tlsVersion** | 要求の接続で使用される TLS のバージョン (例: `TLS 1.2`)。 |
|**smbTreeConnectID** | ツリー接続時に確立したサーバー メッセージ ブロック (SMB) の **treeConnectId**。 例: `0x3` |
|**smbPersistentHandleID** | ネットワーク再接続を維持する SMB2 CREATE 要求の永続的ハンドル ID。  [MS-SMB2](/openspecs/windows_protocols/ms-smb2/f1d9b40d-e335-45fc-9d0b-199a31ede4c3) 2.2.14.1 では **SMB2_FILEID.Persistent** として参照されます。 例: `0x6003f` |
|**smbVolatileHandleID** | ネットワーク再接続時に再利用される SMB2 CREATE 要求の揮発性ハンドル ID。  [MS-SMB2](/openspecs/windows_protocols/ms-smb2/f1d9b40d-e335-45fc-9d0b-199a31ede4c3) 2.2.14.1 では **SMB2_FILEID.Volatile** として参照されます。 例: `0xFFFFFFFF00000065` |
|**smbMessageID** | 接続の相対的な **MessageId**。 例: `0x3b165` |
|**smbCreditsConsumed** | 要求で消費されるイングレスまたはエグレス (64k 単位)。 例: `0x3` |
|**smbCommandDetail** | 一般的な種類の要求ではなく、この特定の要求に関する詳細情報。 例: `0x2000 bytes at offset 0xf2000` |
|**smbFileId** | ファイルまたはディレクトリに関連付けられている **FileId**。  NTFS FileId とほぼ同じです。 例: `0x9223442405598953` |
|**smbSessionID** | セッションのセットアップ時に確立される SMB2 の **SessionId**。 例: `0x8530280128000049` |
|**smbCommandMajor  uint32** | **SMB2_HEADER.Command** の値。 現在、これは 0 から 18 まで (両端を含む) の数値です。 例: `0x6` |
|**smbCommandMinor** | 必要に応じて **SmbCommandMajor** のサブクラス。 例: `DirectoryCloseAndDelete` |