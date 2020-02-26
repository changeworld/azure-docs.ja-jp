---
title: Azure Database for MySQL のデータ暗号化のトラブルシューティング
description: Azure Database for MySQL のデータ暗号化のトラブルシューティングを行う方法を学習する
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 4b517a463ec949d804798787ad4b35b53145a4a8
ms.sourcegitcommit: f255f869c1dc451fd71e0cab340af629a1b5fb6b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2020
ms.locfileid: "77371489"
---
# <a name="troubleshooting-data-encryption-with-customer-managed-keys-in-azure-database-for-mysql"></a>Azure Database for MySQL でのカスタマーマネージド キーを使用するデータ暗号化のトラブルシューティング
この記事では、カスタマー マネージド キーを使用するデータ暗号化が構成されている Azure Database for MySQL で発生する一般的な問題やエラーを特定し、解決する方法について説明します。

## <a name="introduction"></a>はじめに
Azure Key Vault でカスタマー マネージド キーを使用するようにデータ暗号化が構成されている場合、サーバーを使用可能な状態に保つには、このキーへの継続的なアクセスが必要です。 サーバーでは、Azure Key Vault のカスタマー マネージド キーにアクセスできなくなった場合、該当するエラー メッセージが表示されてすべての接続の拒否が始まり、Azure portal でその状態が "***アクセス不可***" に変わります。

アクセスできない Azure Database for MySQL サーバーが不要になった場合は、すぐに削除してコストを抑えることができます。 Azure Key Vault へのアクセスが復元され、サーバーが再び使用できるようになるまで、サーバーに対する他のすべての操作は許可されません。 サーバーがカスタマー マネージドで暗号化されている間は、アクセスできないサーバーでデータの暗号化オプションを [はい] (カスタマー マネージド) から [いいえ] (サービス マネージド) に変更することもできません。 サーバーを再び使用できるようにするには、キーを手動で再検証する必要があります。 これは、カスタマー マネージド キーへのアクセス許可が取り消されているときに、データを不正アクセスから保護するために必要です。

## <a name="common-errors-causing-server-to-become-inaccessible"></a>サーバーにアクセスできなくなる原因となる一般的なエラー

Azure Key Vault によるデータ暗号化を使用しているときに発生する問題のほとんどは、次の構成ミスのいずれかが原因です。

キー コンテナーが使用できない、または存在しない

* キー コンテナーが誤って削除された。
* 間欠的なネットワーク エラーのために、キー コンテナーを使用できない。

キー コンテナーへのアクセス許可がない、またはキーが存在しない

* キーが誤って削除された、無効にされた、またはキーの有効期限が切れた。
* Azure Database for MySQL のインスタンス マネージド ID が誤って削除された。
* キー用の Azure Database for MySQL サーバー マネージド ID に付与されているアクセス許可が十分ではない (取得、ラップ、ラップ解除が含まれていない)。
* Azure Database for MySQL サーバーのインスタンス マネージド ID のアクセス許可が取り消された。

## <a name="identify-and-resolve-common-errors"></a>一般的なエラーの識別と解決
### <a name="errors-on-the-key-vault"></a>Key Vault でのエラー

#### <a name="disabled-key-vault"></a>無効な Key Vault
* AzureKeyVaultKeyDisabledMessage
* **説明**:Azure Key Vault キーが無効になっているため、サーバーで操作を完了できませんでした。

#### <a name="missing-key-vault-permissions"></a>Key Vault アクセス許可がない
* AzureKeyVaultMissingPermissionsMessage
* サーバーに、Azure Key Vault アクセス許可に対して必要な取得、ラップ、およびラップ解除のアクセス許可がありません。 ID でサービス プリンシパルに欠落しているアクセス許可を付与します。

### <a name="mitigation"></a>対応策
* カスタマー マネージド キーが Key Vault に存在することを確認します。
* キー コンテナーを識別した後、Azure portal でそのキー コンテナーに移動します。
* キー URI によって識別されたキーが存在することを確認します。


## <a name="next-steps"></a>次のステップ
[Azure portal を使用して、Azure Database for MySQL のカスタマー マネージド キーによるデータ暗号化を設定します](howto-data-encryption-portal.md)。