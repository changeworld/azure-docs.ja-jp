---
title: 保存データ向け Azure Storage Service Encryption | Microsoft Docs
description: データを保存するときにサービス側で Azure Blob Storage を暗号化し、データを取得するときに復号化するには、Azure Storage Service Encryption 機能を使用します。
services: storage
author: lakasa
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 03/06/2018
ms.author: lakasa
ms.openlocfilehash: 6b56cbb4220ce1c8767724938dd531b8ae5c3920
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2018
---
# <a name="azure-storage-service-encryption-for-data-at-rest"></a>Azure Storage Service Encryption for Data at Rest

Azure Storage Service Encryption for Data at Rest は、データを保護し、組織のセキュリティとコンプライアンスの要件を満たすお手伝いをします。 この機能を使用すると、Azure Storage は Azure Storage にデータを保存する前に自動的に暗号化し、データを取得する前に復号化します。 Storage Service Encryption での暗号化、保存時の暗号化、キー管理の処理は、ユーザーにとって透過的に行われます。 Azure Storage に書き込まれるすべてのデータは、現在利用できるブロック暗号化の中でも最強レベルの 256 ビット [AES 暗号化](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)によって暗号化されます。

Storage Service Encryption は新しいストレージ アカウントと既存のストレージ アカウントすべてに対して有効化され、無効にすることはできません。 データは既定で保護されるので、Storage Service Encryption を活用するために、コードまたはアプリケーションを変更する必要はありません。

この機能により次のデータが自動的に暗号化されます。

- パフォーマンス レベル (Standard と Premium の両方)。
- デプロイメント モデル (Azure Resource Manager とクラシックの両方)。
- すべての Azure Storage サービス (BLOB ストレージ、キュー ストレージ、テーブル ストレージ、Azure ファイル)。 

Storage Service Encryption は Azure Storage のパフォーマンスには影響しません。

Storage Service Encryption には Microsoft が管理する暗号化キーのほか、ユーザー独自の暗号化キーを使うことができます。 独自のキーを使用する方法について詳しくは、「[ユーザーが管理する Azure Key Vault キーを Storage Service Encryption に使用する](storage-service-encryption-customer-managed-keys.md)」をご覧ください。

## <a name="view-encryption-settings-in-the-azure-portal"></a>暗号化設定を Azure ポータルに表示する

Storage Service Encryption の設定を表示するには、[Azure Portal](https://portal.azure.com) にログインしてストレージ アカウントを選択します。 **[設定]** ウィンドウで **[暗号化]** 設定を選択します。

![暗号化設定が表示されたポータルのスクリーンショット](./media/storage-service-encryption/image1.png)

## <a name="faq-for-storage-service-encryption"></a>Storage Service Encryption のよくあるご質問

**Q: クラシック ストレージ アカウントがあります。これに対して Storage Service Encryption を有効にできますか?**

A: Storage Service Encryption は、既定ですべてのストレージ アカウント (クラシックと Resource Manager) に対して有効になります。

**Q: クラシック ストレージ アカウントのデータを暗号化するにはどうすればよいですか?**

A: Azure Storage は、既定で有効な暗号化を使用して新しいデータを自動的に暗号化します。 

**Q: Resource Manager ストレージ アカウントがあります。これに対して Storage Service Encryption を有効にできますか?**

A: Storage Service Encryption は、既定で既存のすべての Resource Manager ストレージ アカウントで有効になります。 これは、Blob ストレージ、テーブル ストレージ、キュー ストレージ、Azure ファイルでサポートされます。 

**Q: Resource Manager ストレージ アカウントのデータはどのように暗号化すればよいですか?**

A: Storage Service Encryption は、既定ですべてのストレージ アカウント (クラシックと Resource Manager) に対して有効になります。 ただし、既存のデータは暗号化されません。 既存のデータを暗号化するには、別の名前または別のコンテナーにコピーしてから、暗号化されていないバージョンを削除します。 

**Q: Azure PowerShell および Azure CLI を使用して、Storage Service Encryption を有効にしたストレージ アカウントを作成できますか?**

A: Storage Service Encryption は、既定でストレージ アカウント (クラシックまたは Resource Manager) の作成時に有効にされます。 Azure PowerShell と Azure CLI の両方を使用して、アカウントのプロパティを確認できます。

**Q: Storage Service Encryption を有効にすると、Azure Storage の料金はどれくらい増えますか?**

A: 追加コストはかかりません。

**Q: だれが暗号化キーを管理するのですか?**

A: マイクロソフトがキーを管理します。

**Q: 独自の暗号化キーを使用できますか?**

A: 現時点ではありません。

**Q: 暗号化キーへのアクセスを取り消すことはできますか?**

A: 現時点ではありません。 マイクロソフトが全面的にキーを管理します。

**Q: Storage Service Encryption は、ストレージ アカウントを作成すると、既定で有効になりますか?**

A: はい、Microsoft 管理キーを使用する Storage Service Encryption は、既定ですべてのストレージ アカウント (Azure Resource Manager とクラシック) に対して有効になります。 これは、すべてのサービス (BLOB ストレージ、キュー ストレージ、テーブル ストレージ、Azure ファイル) で有効になります。

**Q: この機能は Azure Disk Encryption とどのように違うのですか?**

A: Azure Disk Encryption は、IaaS VM の OS ディスクとデータ ディスクの暗号化に使用されます。 詳しくは、「[Azure Storage セキュリティ ガイド](../storage-security-guide.md)」をご覧ください。

**Q: 自分のデータ ディスクで Azure Disk Encryption を有効化するとどうなりますか?**

A: これはシームレスに機能します。 どちらの方法でもデータが暗号化されます。

**Q: ストレージ アカウントを、geo 冗長レプリケートされるように設定してあります。Storage Service Encryption では、冗長コピーも暗号化されますか?**

A: はい。ストレージ アカウントのすべてのコピーが暗号化されます。 すべての冗長コピー (ローカル冗長ストレージ、ゾーン冗長ストレージ、geo 冗長ストレージ、読み取りアクセス geo 冗長ストレージ) がサポートされます。

**Q: ストレージ アカウントで暗号化を無効にできますか?**

A: 暗号化は既定で有効になっており、ストレージ アカウントの暗号化を無効にするプロビジョニングはありません。 

**Q: Storage Service Encryption は、特定のリージョンだけで許可されますか?**

A: すべてのサービスのすべてのリージョンで Storage Service Encryption を使用できます。 

**Q: 問題やフィードバックの連絡先はどこですか?**

A: Storage Service Encryption に関する問題またはフィードバックは、[ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com) にご連絡ください。

## <a name="next-steps"></a>次の手順
Azure Storage で提供される包括的なセキュリティ機能のセットを利用して、開発者はセキュリティで保護されたアプリケーションを構築できます。 詳しくは、「[Azure Storage セキュリティ ガイド](../storage-security-guide.md)」をご覧ください。
