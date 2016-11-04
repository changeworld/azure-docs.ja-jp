---
title: Azure Data Lake Store と Azure BLOB ストレージの比較 | Microsoft Docs
description: Azure Data Lake Store と Azure BLOB ストレージの比較
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun

ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/15/2016
ms.author: nitinme

---
# Azure Data Lake Store と Azure BLOB ストレージの比較
この記事の表には、ビッグ データ処理の重要な側面に沿った Azure Data Lake Store と Azure BLOB ストレージの違いがまとめられています。Azure BLOB ストレージは、さまざまなストレージ シナリオ向けに設計されたスケーラブルな汎用オブジェクト ストアです。Azure Data Lake Store は、ビッグ データ分析ワークロードに最適化されたハイパースケール リポジトリです。

|  | Azure Data Lake Store | Azure BLOB ストレージ |
| --- | --- | --- |
| 目的 |ビッグ データ分析ワークロードに最適化されたストレージ |さまざまなストレージ シナリオに対応する汎用オブジェクト ストア |
| ユース ケース |バッチ、対話型、ストリーミング分析、および機械学習データ (ログ ファイル、IoT データ、クリック ストリーム、大規模なデータセットなど) |あらゆる種類のテキスト データまたはバイナリ データ (アプリケーション バックエンド、バックアップ データ、ストリーミング用メディア ストレージ、汎用データなど) |
| 主要な概念 |Data Lake Store アカウントにはフォルダーが含まれ、フォルダーにはファイルとして保存されたデータが含まれます。 |ストレージ アカウントにはコンテナーが含まれ、コンテナーには BLOB の形でデータが含まれます。 |
| Structure |階層型ファイル システム |フラットな名前空間を使用するオブジェクト ストア |
| API |HTTPS 経由の REST API |HTTP/HTTPS 経由の REST API |
| サーバー側 API |[WebHDFS 互換の REST API](https://msdn.microsoft.com/library/azure/mt693424.aspx) |[Azure BLOB ストレージ REST API](https://msdn.microsoft.com/library/azure/dd135733.aspx) |
| Hadoop ファイル システム クライアント |はい |はい |
| データ操作 - 認証 |[Azure Active Directory ID](../active-directory/active-directory-authentication-scenarios.md) に基づきます。 |共有シークレット ([アカウント アクセス キー](../storage/storage-create-storage-account.md#manage-your-storage-account)と [Shared Access Signature キー](../storage/storage-dotnet-shared-access-signature-part-1.md)) に基づきます。 |
| データ操作 - 認証プロトコル |OAuth 2.0。呼び出しには、Azure Active Directory によって発行された有効な JWT (JSON Web トークン) が含まれている必要があります。 |ハッシュベース メッセージ認証コード (HMAC)。呼び出しには、HTTP 要求の一部に対する Base64 でエンコードされた SHA-256 ハッシュが含まれている必要があります。 |
| データ操作 - 承認 |POSIX アクセス制御リスト (ACL)。Azure Active Directory ID に基づく ACL は、ファイルおよびフォルダー レベルで設定できます。 |アカウント レベルの承認 - [アカウント アクセス キー](../storage/storage-create-storage-account.md#manage-your-storage-account)を使用<br>アカウント、コンテナー、または BLOB の承認 - [Shared Access Signature キー](../storage/storage-dotnet-shared-access-signature-part-1.md)を使用 |
| データ操作 - 監査 |使用可能。詳細については、[こちら](data-lake-store-diagnostic-logs.md)をご覧ください。 |使用可能 |
| 保存データの暗号化 |透過的、サーバー側 (近日対応予定)<ul><li>サービスによって管理されたキーを使用</li><li>Azure KeyVault の顧客によって管理されたキーを使用</li></ul> |<ul><li>透過的、サーバー側</li> <ul><li>サービスによって管理されたキーを使用</li><li>Azure KeyVault の顧客によって管理されたキーを使用 (近日対応予定)</li></ul><li>クライアント側の暗号化</li></ul> |
| 管理操作 (アカウントの作成など) |アカウント管理に Azure が提供する[ロールベースのアクセス制御 ](../active-directory/role-based-access-control-what-is.md) (RBAC) |アカウント管理に Azure が提供する[ロールベースのアクセス制御 ](../active-directory/role-based-access-control-what-is.md) (RBAC) |
| Developer SDK |.NET、Java、Node.js |.Net、Java、Python、Node.js、C++、Ruby |
| 分析ワークロードのパフォーマンス |並列分析ワークロードに最適化されたパフォーマンス。高スループットおよび高 IOPS。 |分析ワークロードに最適化されていません。 |
| サイズ制限 |アカウント サイズ、ファイル サイズ、ファイル数に制限はありません。 |具体的な制限については、[こちら](../azure-subscription-service-limits.md#storage-limits)をご覧ください。 |
| geo 冗長 |ローカル冗長 (1 つの Azure リージョンにデータの複数のコピー) |ローカル冗長 (LRS)、geo 冗長 (GRS)、読み取りアクセス geo 冗長 (RA-GRS)。詳細については、[こちら](../storage/storage-redundancy.md)をご覧ください。 |
| サービスの状態 |パブリック プレビュー |一般公開 |
| リージョン別の提供状況 |[こちら](https://azure.microsoft.com/regions/#services)をご覧ください。 |[こちら](https://azure.microsoft.com/regions/#services)をご覧ください。 |
| 料金 |[価格](https://azure.microsoft.com/pricing/details/data-lake-store/)に関するページをご覧ください。 |[価格](https://azure.microsoft.com/pricing/details/storage/)に関するページをご覧ください。 |

### 次のステップ
* [Azure Data Lake Store の概要](data-lake-store-overview.md)
* [Data Lake Store の使用](data-lake-store-get-started-portal.md)

<!---HONumber=AcomDC_0914_2016-->