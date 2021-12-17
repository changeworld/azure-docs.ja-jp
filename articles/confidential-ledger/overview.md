---
title: Microsoft Azure Confidential Ledger の概要
description: 安全性の高い機密データ レコード管理サービス、Azure Confidential Ledger の概要
services: confidential-ledger
author: msmbaldwin
ms.service: confidential-ledger
ms.topic: overview
ms.date: 04/15/2021
ms.author: mbaldwin
ms.openlocfilehash: 2f0636d5ef4b4e1fa3a9576912630470af7fa388
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132337796"
---
# <a name="microsoft-azure-confidential-ledger-preview"></a>Microsoft Azure Confidential Ledger (プレビュー)

Microsoft Azure Confidential Ledger (ACL) は、機密データ レコードを管理するための、安全性の高い新しいサービスです。 Azure Confidential Ledger では、許可型ブロックチェーン モデルに基づいて、独自のデータ整合性機能を提供しています。 たとえば、不変性、追加専用台帳の作成、改ざん防止などの機能があり、すべてのレコードを安全に保ちます。

Confidential Ledger は、潜在的な攻撃を防ぐために厳重に監視、隔離されたランタイム環境である、ハードウェアを基盤にしたセキュア エンクレーブ上でのみ動作します。 さらに、Microsoft も含めて、Ledger の "上位" に存在するものは何もありません。 Azure Confidential Ledger は、Microsoft をソリューションに組み入れないことにより、最小限のトラステッド コンピューティング ベース (TCB) で動作するよう設計され、Ledger サービスの開発者、データセンターの技術職員、クラウド管理者によるアクセスを防止しています。

Azure Confidential Ledger は、変更してはならない重要なメタデータ レコードがある状況で特に役に立ちます。たとえば、規制遵守のためにデータを変更せずに保持し続ける場合や、アーカイブを管理する場合に有効です。 Ledger で保管できるものの例をここにいくつか挙げます。

- 業務上のトランザクション (送金、ドキュメント編集など) に関連する記録。
- 信頼性の高い資産 (中核アプリケーション、契約書など) の更新データ。
- 管理、制御に関する変更 (アクセス権の付与など)。
- IT およびセキュリティの運用に関するイベント (Microsoft Defender for Cloud のアラートなど)。

詳細については、[Microsoft Ignite 2020 の Azure Confidential Ledger のデモ](https://mediusprodstatic.studios.ms/asset-b88de19d-4187-40c4-98f2-a65efc419e2a/OD221_1920x1080_AACAudio_1461.mp4?sv=2018-03-28&sr=b&sig=k5roi6WXnlqK1zP0fs5KYlJd4FD3Nuaf97z%2B2gV0aTs%3D&st=2020-09-22T08%3A05%3A01Z&se=2025-09-22T08%3A10%3A01Z&sp=r&rscd=filename%3DIG20-OD221-Inside%2BAzure%2BDatacenter%2BArchitecture%2Bwith%2BMark%2BRu.mp4)をご視聴ください。

## <a name="key-features"></a>主な機能

Confidential Ledger は、新しい、または既存のアプリケーションに組み込める REST API を介して公開されます。 Confidential Ledger は、管理者が Administrative API (コントロール プレーン) を使用して管理できます。 Functional API (データ プレーン) を使用してアプリケーション コードで直接呼び出すこともできます。 Administrative API は、作成、更新、取得、削除などの基本操作をサポートしています。 Functional API では Ledger のインスタンスと直接やりとりでき、データの配置や取得などの操作を実行できます。

## <a name="ledger-security"></a>台帳のセキュリティ

このセクションでは台帳のセキュリティ保護を説明します。 Ledger API ではクライアント証明書による認証を行います。 現在 Ledger は、所有者ロールを使用した、証明書による認証をサポートしています。 Azure Active Directory (AAD) による認証、ロール (所有者、閲覧者、共同作成者など) によるアクセス制御の機能も追加する予定です。

Ledger に送るデータは TLS 1.2 接続によって送信し、TLS 1.2 接続は、ハードウェアにより実装されたセキュリティ エンクレーブ (Intel® SGX enclaves) 上から終了します。 これにより、顧客のクライアントと Confidential Ledger サーバー ノード間の接続は誰にも傍受できません。

### <a name="ledger-storage"></a>Ledger ストレージ

Confidenial Ledgers は、Azure Storage アカウントに属する BLOB ストレージ コンテナー内のブロックとして作成されます。 トランザクションのデータは、必要に応じて、暗号化するか、プレーンテキストとして保存できます。 Ledger を作成するときは、「[Confidential Ledger サービス プリンシパルを登録する](register-ledger-service-principal.md)」で説明されている手順を使用して Storage アカウントを関連付けます。

Confidential Ledger は、管理者が Administrative API (コントロール プレーン) を使用して管理でき、Functional API (データ プレーン) を使用してアプリケーション コードで直接呼び出すことができます。 Administrative API は、作成、更新、取得、削除などの基本操作をサポートしています。

Functional API を使用すると、インスタンス化された Confidential Ledger と直接やりとりでき、データの配置や取得などの操作を実行できます。

## <a name="preview-limitations"></a>プレビューの制約

- Confidential Ledger の種類を作成後に変更することはできません。
- 現時点で Confidencial Ledger では、標準の Azure Disaster Recovery はサポートされていません。 ただし、Confidential Ledger は複数の独立したノード上で動作するため、Azure Confideitial Ledger では、同一の Azure リージョン内で組み込み冗長性が提供されます。
- Azure Confidential Ledger を削除すると "完全削除" が実行されるため、削除後はデータを復旧できません。
- Azure Confidential Ledger の名前はグローバルで一意である必要があります。 同名の Ledger は種類にかかわらず許可されません。

## <a name="terminology"></a>用語

| 期間 | 定義 |
|--|--|
| ACL | Azure Confidential Ledger |
| Ledger | トランザクションの不変追加レコード (別名ブロックチェーン) |
| Commit | トランザクションをローカルでノードにコミットしたことの確認。 ローカルでコミットするだけでは、トランザクションを Ledger に記録することは保証されません。 |
| グローバル コミット | トランザクションをグローバルでコミットして Ledger に記録したことの確認。 |
| Receipt | トランザクションが Leger で処理されたことの証明。 |

## <a name="next-steps"></a>次の手順

- [Microsoft Azure Confidential Ledger のアーキテクチャ](architecture.md)
- [クイック スタート: Azure portal](quickstart-portal.md)
- [クイック スタート: Python](quickstart-python.md)
- [クイック スタート: Azure Resource Manager (ARM) テンプレート](quickstart-portal.md)
