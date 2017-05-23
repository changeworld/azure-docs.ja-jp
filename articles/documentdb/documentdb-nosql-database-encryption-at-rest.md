---
title: "保存時の NoSQL データベースの暗号化 - Azure DocumentDB | Microsoft Docs"
description: "Azure DocumentDB ですべての NoSQL データが既定で暗号化されるしくみについて説明します。"
services: documentdb
author: voellm
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 99725c52-d7ca-4bfa-888b-19b1569754d3
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/31/2017
ms.author: voellm
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f291186c6a68dea8aa00b846a2e6f3ad0d7996c
ms.openlocfilehash: 9c97f76d581935cb85f33b0016c192ded3b6f880
ms.contentlocale: ja-jp
ms.lasthandoff: 04/28/2017


---

# <a name="documentdb-nosql-database-encryption-at-rest"></a>保存時の DocumentDB NoSQL データベースの暗号化

保存時の暗号化という語句は、一般的にはソリッド ステート ドライブ (SSD) やハード ディスク (HDD) などの不揮発性ストレージ デバイスでデータを暗号化することを意味します。  DocumentDB は、プライマリ データベースを SSD に格納し、メディア添付ファイルとバックアップを、通常は HDD でバックアップされる Azure BLOB に格納します。  DocumentDB の保存時の暗号化のリリースにより、すべてのデータベース、メディア添付ファイル、バックアップが暗号化されるようになりました。  つまり、データが転送中 (ネットワーク経由) と保存時 (非揮発性ストレージ) に暗号化されることで、エンド ツー エンドの暗号化が実現されました。

Microsoft は、DocumentDB を PaaS サービスとして簡単に使用できるようにするために努力してきました。  その目的に沿って、DocumentDB に格納されるすべてのユーザー データは、ユーザーが何も操作しなくても、保存時と転送時に暗号化されます。  つまり、保存時の暗号化機能は、既定で "オン" になっています。  この機能をオフまたはオンにするコントロールはなく、[可用性とパフォーマンスの SLA](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/) の条件は引き続き満たされています。

## <a name="how-does-encryption-at-rest-work"></a>保存時の暗号化のしくみ

保存時の暗号化は、セキュリティで保護されたキー ストレージ システム、暗号化ネットワーク、暗号化 API など、多くのセキュリティ テクノロジを使用して実装されています。  次の図は、暗号化されたデータの格納とキーの管理がどのように分離されているかを示しています。  データの暗号化を解除して処理するシステムは、キーを管理するシステムと通信する必要があります。

![設計の図](./media/documentdb-nosql-database-encryption-at-rest/design-diagram.png)

ユーザー要求の基本的な流れは、次のとおりです。
- ユーザー データベース アカウントが準備され、ストレージ キーが管理サービス リソース プロバイダー (RP) への要求を通じて取得されます。
- ユーザーは、HTTPS/セキュリティで保護された転送を通じて、DocumentDB への接続を作成します (詳細は SDK が抽象化します)。
- ユーザーは、前に作成したセキュリティで保護された接続経由で、格納される JSON ドキュメントを送信します。
- ユーザーがインデックスを無効にしない限り、JSON ドキュメントのインデックスが作成されます。
- JSON ドキュメントとインデックス データの両方が、セキュリティで保護されたストレージに書き込まれます。
- データはセキュリティで保護されたストレージから定期的に読み取られ、Azure の暗号化された BLOB ストアにバックアップされます。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="q-how-much-more-does-azure-storage-cost-if-sse-is-enabled"></a>Q: SSE を有効にすると、Azure Storage の料金はどれくらい増えますか。
A: 追加コストはかかりません。

### <a name="q-who-manages-the-encryption-keys"></a>Q: だれが暗号化キーを管理するのですか。
A: キーは Microsoft によって管理されます。

### <a name="q-can-i-use-my-own-encryption-keys"></a>Q: 独自の暗号化キーを使用できますか。
A: DocumentDB は PaaS サービスであり、Microsoft はこのサービスの使いやすさを維持するために努力してきました。  この質問は、PCI-DSS などのコンプライアンスに準拠するための代わりの質問として頻繁に寄せられることがわかりました。  この機能を構築する際に、コンプライアンス監査者の協力を得て、DocumentDB を使用するお客様が自身でキーを管理しなくても要件を満たすことができるようにしました。
そのため、現時点ではお客様自身でキーを管理するオプションは提供していません。

### <a name="q-what-regions-have-encryption-turned-on"></a>Q: どのリージョンで暗号化が有効になっていますか。
A: すべての DocumentDB リージョンで、すべてのユーザー データに対して暗号化が有効になっています。

## <a name="next-steps"></a>次のステップ

DocumentDB セキュリティと最新の機能強化の概要については、「[DocumentDB NoSQL データベースのセキュリティ](documentdb-nosql-database-security.md)」を参照してください。

マイクロソフトが取得している認証の詳細については、[Azure セキュリティ センター](https://azure.microsoft.com/en-us/support/trust-center/)に関するページを参照してください。

