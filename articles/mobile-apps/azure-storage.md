---
title: 高度なセキュリティで保護され、耐久性に優れたスケーラブルなアプリを Azure Storage で構築するためのクラウド ストレージ
description: モバイル アプリケーションの大規模な構造化データおよび非構造化データをクラウドに格納するためのサービスについて説明します。
author: elamalani
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 53754976a6e1f6d893cf7d6ab73d57c95dae193f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453263"
---
# <a name="cloud-storage-for-highly-secure-durable-scalable-apps-with-azure-storage"></a>高度なセキュリティで保護され、耐久性に優れたスケーラブルなアプリを Azure Storage で実現するためのクラウド ストレージ
[Azure Storage](https://azure.microsoft.com/services/storage/) は、データ オブジェクトのための高度にスケーラブルなオブジェクト ストア、クラウドのためのファイル システム サービス、信頼性の高いメッセージングのためのメッセージング ストア、および NoSQL ストアを提供する、最新のアプリケーション向けの Microsoft のクラウド ストレージ ソリューションです。 Azure Storage の特徴を次に示します。
- **持続性と高可用性:** 冗長性により、一時的なハードウェア障害が発生した場合でも、データが安全に保たれます。 地域的な災害または自然災害から保護するために、データセンターまたは地理的リージョンにまたがってデータをレプリケートすることもできます。 この方法でレプリケートされたデータは、予期しない停止が発生した場合でも高可用性を維持します。
- **セキュリティ保護:** Azure Storage に書き込まれたすべてのデータは、サービスによって暗号化されます。 Azure Storage では、データにアクセスできるユーザーをきめ細かく制御できます。
- **スケーラブル:** サービスは、今日のアプリケーションのデータ ストレージとパフォーマンスに関するニーズを満たすために、高度にスケーラブルな設計となっています。
- **マネージド:** Microsoft Azure では、ユーザーに代わって、ハードウェアのメンテナンス、更新、および重大な問題が処理されます。
- **アクセス可能:** 世界中のどこからでも、HTTP または HTTPS 経由でデータにアクセスできます。 Microsoft では、.NET、Java、Node.js、Python、PHP、Ruby、Go などのさまざまな言語のクライアント ライブラリと、成熟した REST API を提供しています。 スクリプトは Azure PowerShell または Azure CLI でサポートされています。 Azure portal と Azure Storage Explorer により、データを操作するための使いやすい視覚的ソリューションが提供されます。

ご利用のモバイル アプリ内でクラウド ストレージを有効にするには、次のサービスを使用します。

## <a name="azure-blob-storage"></a>Azure BLOB ストレージ
[Azure BLOB ストレージ](https://azure.microsoft.com/services/storage/blobs/)は、クラウド用のオブジェクト ストレージ ソリューションを提供します。 BLOB ストレージは、テキストやバイナリなどの特定のデータ モデルまたは定義に準拠していない非構造化データを大量に格納するために最適化されています。 クライアント ライブラリが使用するさまざまな言語がサポートされています。 BLOB ストレージは、次の用途向けに設計されています。
- 画像またはドキュメントをブラウザーに直接配信する。
- 分散アクセス用にファイルを格納する。
- ビデオおよびオーディオをストリーミング配信する。
- ログ ファイルに書き込む。
- バックアップと復元、ディザスター リカバリー、アーカイブのためのデータを格納する。
- オンプレミス サービスまたは Azure ホステッド サービスで分析するデータを格納する。

**参照**
- [Azure Portal](https://portal.azure.com)
- [Azure Blob Storage のドキュメント](/azure/storage/blobs/storage-blobs-introduction)
- [クイック スタート](/azure/storage/blobs/storage-quickstart-blobs-portal)
- [サンプル](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

## <a name="azure-table-storage"></a>Azure Table Storage
[Azure Table Storage](https://azure.microsoft.com/services/storage/tables/) は、NoSQL の構造化データをクラウド内に格納するサービスです。スキーマレスのデザインでキー/属性ストアを実現します。 Azure Table Storage は、大量の構造化データを格納します。 このサービスは、Azure クラウドの内部および外部からの認証された呼び出しを受け付ける NoSQL データストアです。 Azure のテーブルは、構造化された非リレーショナル データを格納するのに最適です。 Table Storage は通常、次の目的で使用します。
- Web スケール アプリケーションにサービスを提供できる数テラバイトの構造化データを格納する。
- 複雑な結合、外部キー、またはストアド プロシージャを必要とせず、高速アクセスのために非正規化できるデータセットを格納する。
- クラスター化インデックスを使用して高速なデータのクエリを実行する。
- OData プロトコルおよび LINQ クエリと Windows Communication Foundation (WCF) Data Services .NET ライブラリを使用してデータにアクセスする。

Table Storage を使用して、構造化された非リレーショナル データの巨大なセットを格納および照会できます。 必要に応じてテーブルが拡大縮小されます。

**参照**
- [Azure Portal](https://portal.azure.com)
- [Azure Table Storage のドキュメント](/azure/storage/tables/table-storage-overview)
- [サンプル](/azure/cosmos-db/tutorial-develop-table-dotnet?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fstorage%2Ftables%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
- [クイック スタート](/azure/storage/tables/table-storage-quickstart-portal)

## <a name="azure-files"></a>Azure Files
[Azure Files](https://azure.microsoft.com/services/storage/files/) では、標準的なサーバー メッセージ ブロック (SMB) プロトコルを使用してアクセスできる可用性の高いネットワーク ファイル共有を設定することができます。 複数の VM が、読み取りアクセス権と書き込みアクセス権の両方を使用して同じファイルを共有できます。 また、REST インターフェイスまたはストレージ クライアント ライブラリを使用してファイルを読み取ることもできます。 世界中のどこからでも、ファイルを指し、共有アクセス署名 (SAS) トークンを含む URL を使用して、ファイルにアクセスできます。 SAS トークンを生成することができます。 SAS トークンを使用することで、特定の期間にわたってプライベートな資産への特定のアクセスを許可できます。

Azure ファイル共有は、以下の作業に使用できます。
- **オンプレミスのファイル サーバーの置換または補完:** Windows、macOS、Linux などの一般的なオペレーティング システムに、世界中のどこからでも、Azure ファイル共有を簡単にマウントできます。 また、Azure File Sync を使用すると、Azure ファイル共有を、オンプレミスまたはクラウドにある Windows サーバーにレプリケートすることも可能で、使用されているデータを分散キャッシュしてパフォーマンスを向上させることができます。
- **アプリケーションのリフトアンドシフト:** ファイル アプリケーションまたはユーザー データがファイル共有に格納されることを期待するアプリケーションをクラウドに移行します。
- **クラウド開発の簡略化:** また、Azure Files は、新しいクラウド開発プロジェクトを簡素化するためにさまざまな方法で使用できます。 次に例を示します。
    - **共有アプリケーションの設定:** 分散アプリケーションの一般的なパターンでは、複数のアプリケーション インスタンスからアクセスできる集中管理された場所に構成ファイルが配置されます。 アプリケーション インスタンスでは、ファイル REST API を使用して構成を読み込むことができます。 ユーザーは、SMB 共有をローカルにマウントすることで、必要に応じてそれらにアクセスできます。
    - **診断の共有:** Azure ファイル共有は、クラウド アプリケーションが、ログ、メトリック、およびクラッシュ ダンプを書き込める便利な場所です。 ファイル REST API を介して、アプリケーション インスタンスからログを書き込むことができます。 開発者は、ローカル コンピューターでファイル共有をマウントすることによってそれらにアクセスできます。 この機能により、柔軟性が向上します。 開発者は、使い慣れた既存のツールを引き続き使用して、クラウド開発を行えます。

**参照**
- [Azure Portal](https://portal.azure.com)
- [Azure Files のドキュメント](/azure/storage/files/storage-files-introduction)
- [クイック スタート](/azure/storage/files/storage-files-quick-create-use-windows)

## <a name="azure-queue-storage"></a>Azure Queue Storage
[Azure Queue storage](https://azure.microsoft.com/services/storage/queues/) は、多数のメッセージを格納するためのサービスです。 メッセージには、HTTP または HTTPS を使用して、認証された呼び出しを介して世界中のどこからでもアクセスできます。 キュー メッセージの許容される最大サイズは 64 KB です。 キューには、ストレージ アカウントの総容量の上限を超えない限り、数百万のメッセージを含めることができます。 キューは通常、非同期的な処理用に作業のバックログを作成するために使用されます。

**参照**
- [Azure Portal](https://portal.azure.com)
- [Azure Queue storage のドキュメント](/azure/storage/queues/)
- [クイック スタート](/azure/storage/queues/storage-quickstart-queues-portal)
- [サンプル](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
