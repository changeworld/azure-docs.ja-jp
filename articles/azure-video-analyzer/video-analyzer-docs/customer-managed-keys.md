---
title: カスタマー マネージド キー - Azure Video Analyzer
description: Azure Video Analyzer ではカスタマー マネージド キー (つまり Bring Your Own Key) を使用できます。
author: anilmur
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 11/04/2021
ms.openlocfilehash: 749c70286af3c6d182dc1e8a925ad7fb4c8e815a
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131552679"
---
# <a name="customer-managed-keys-with-azure-video-analyzer"></a>カスタマー マネージド キーと Azure Video Analyzer

Bring Your Own Key (BYOK) は、お客様のワークロードをクラウドに移行するのに役立つ Azure 全体でのイニシアティブです。 カスタマー マネージド キーを使用すると、お客様は業界のコンプライアンス規制に準拠し、サービスのテナント分離を強化できます。 お客様に暗号化キーの制御を委ねることは、不必要なアクセスや制御を最小限にして、Microsoft サービスへの信頼を育む方法となっています。

## <a name="keys-and-key-management"></a>キーとキー管理

すべての Video Analyzer アカウントに対してアカウント キーが作成されます。 このアカウントキーは既定では、Video Analyzer によって所有されているシステム キー (つまり、システムによって管理されるキー) を使用して暗号化されます。 そうしないで、Azure Video Analyzer で独自のキーを使用することもできます。 この場合、お客様のアカウント キーはご自分のキーで暗号化されます。 アクセス ポリシーとビデオ リソース メタデータは、アカウント キーを使用して暗号化されます。

Video Analyzer では、ユーザー割り当てマネージド ID を使用して、自分が所有しているキー コンテナーから自分のキーを読み取ります。 Video Analyzer アカウントの作成または更新時にユーザー割り当てマネージド ID を指定し、適切な [Azure ロールベースのアクセス制御](../../role-based-access-control/overview.md)をキー コンテナーに割り当てる必要があります。 Video Analyzer では、そのキー コンテナーがアカウントと同じリージョンにあり、そこで論理的な削除と消去保護が有効になっている必要があります。

お客様のキーには、2048、3072、または 4096 RSA キーを使用できます。また、HSM キーとソフトウェア キーの両方がサポートされています。

> [!NOTE]
> EC キーはサポートされていません。

キー名とキー バージョン、またはキー名だけを指定できます。 キー名のみを使用すると、Video Analyzer では最新のキー バージョンが使用されます。 新しいバージョンのカスタマー キーが自動的に検出され、アカウント キーが再暗号化されます。

> [!WARNING]
> Video Analyzer では、カスタマー キーへのアクセスが監視されます。 カスタマー キーにアクセスできなくなった場合 (キーが削除された場合、キー コンテナーが削除された場合、アクセスの付与が削除された場合など)、アカウントは Video Analyzer によって、"カスタマー キー アクセス不能状態" に移行されます (事実上のアカウントの無効化)。 アカウントを削除するか、アカウント キーを復元するかして、アクセスを再開できます。

## <a name="double-encryption"></a>二重暗号化

Video Analyzer では、Azure の標準プラクティスに従って二重暗号化を使用し、お客様の機密データを保護します ([Azure の二重暗号化](../../security/fundamentals/double-encryption.md)に関するページを参照)。 保存データの場合、暗号化の最初のレイヤーでは、アカウント上の `encryption` 設定に応じて、カスタマー マネージド キーまたは Microsoft マネージド キーが使用されます。 保存データ用の暗号化の 2 番目のレイヤーは、個別の Microsoft マネージド キーを使用して自動的に提供されます。

> [!NOTE]
> Video Analyzer アカウント上で二重暗号化は自動的に有効にされます。 一方、ご利用のストレージ アカウント上では、カスタマー マネージド キーと二重暗号化を別々に構成する必要があります。 [ストレージの暗号化](../../storage/common/storage-service-encryption.md)に関するページを参照してください。


## <a name="next-steps"></a>次の手順

[マネージド ID](managed-identity.md) について確認する
