---
title: バックアップ センター - FAQ
description: この記事では、バックアップ センターについてよく寄せられる質問に回答します。
ms.topic: conceptual
ms.date: 09/08/2020
ms.openlocfilehash: c5b23a32b60f651cd3ff91819155d83a7465491a
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2020
ms.locfileid: "92173823"
---
# <a name="backup-center---frequently-asked-questions"></a>バックアップ センター - よく寄せられる質問

## <a name="management"></a>管理

### <a name="can-backup-center-be-used-across-tenants"></a>テナントにまたがってバックアップ センターを使用できますか?

はい。[Azure Lighthouse](../lighthouse/overview.md) を使用し、異なるテナントにまたがるサブスクリプションへの委任されたアクセス権を持っている場合は、バックアップ センターを 1 つのウィンドウとして使用して、テナントにまたがってバックアップを管理できます。

### <a name="can-backup-center-be-used-to-manage-both-recovery-services-vaults-and-backup-vaults"></a>バックアップ センターを使用して Recovery Services コンテナーとバックアップ コンテナーの両方を管理できますか?

はい。バックアップ センターでは、[Recovery Services コンテナー](./backup-azure-recovery-services-vault-overview.md)と[バックアップ コンテナー](backup-vault-overview.md)の両方を管理できます。

### <a name="is-there-a-delay-before-data-surfaces-in-backup-center"></a>バックアップ センターでデータが表示されるまでに遅延が発生しますか?

バックアップ センターは、リアルタイム情報を提供することを目的としています。 個々のコンテナー画面でエンティティが表示されてから、バックアップ センターで同じエンティティが表示されるまでに数秒の遅延が発生する可能性があります。

## <a name="configuration"></a>構成

### <a name="do-i-need-to-configure-anything-to-see-data-in-backup-center"></a>バックアップ センターでデータを表示するには、何か構成する必要がありますか?

いいえ。 バックアップ センターは、そのままですぐに使用できます。 ただし、バックアップ センターで[バックアップ レポート](./configure-reports.md)を表示するには、コンテナーのレポートを構成する必要があります。

### <a name="do-i-need-to-have-any-special-permissions-to-use-backup-center"></a>バックアップ センターを使用するには、何か特殊なアクセス許可が必要ですか?

バックアップ センター自体に新しいアクセス許可は何も必要ありません。 管理しているリソースに対する適切なレベルの Azure RBAC アクセス権を持っている限り、これらのリソースに対してバックアップ センターを使用できます。 たとえば、バックアップに関する情報を表示するには、コンテナーへの **閲覧者** アクセス権が必要になります。 バックアップを構成し、その他のバックアップ関連のアクションを実行するには、 **バックアップ共同作成者** または **バックアップ オペレーター** のロールが必要になります。 Azure Backup の Azure ロールの詳細については、[ここ](./backup-rbac-rs-vault.md) を参照してください。

## <a name="pricing"></a>価格

### <a name="do-i-need-to-pay-anything-extra-to-use-backup-explorer"></a>バックアップ エクスプローラーを使用するには、追加料金を支払う必要がありますか?

現在、バックアップ センターを使用するために (バックアップ コスト以外の) 追加コストは発生しません。 ただし、バックアップ センターで[バックアップ レポート](./configure-reports.md)を使用している場合は、バックアップ レポートに Azure Monitor ログを使用するための[コスト](https://azure.microsoft.com/pricing/details/monitor/)が発生します。

## <a name="next-steps"></a>次のステップ

その他のよく寄せられる質問をお読みください。

* [Recovery Services コンテナーに関する一般的な質問](./backup-azure-backup-faq.md)
* [Azure VM バックアップに関する一般的な質問](./backup-azure-vm-backup-faq.md)