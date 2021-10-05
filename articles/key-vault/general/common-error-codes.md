---
title: Azure Key Vault の一般的なエラー コード | Microsoft Docs
description: Azure Key Vault の一般的なエラー コード
services: key-vault
author: sebansal
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: reference
ms.date: 09/29/2020
ms.author: mbaldwin
ms.openlocfilehash: bd66619c65dd39e32a11b81096d4b763430fc16c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128553681"
---
# <a name="common-error-codes-for-azure-key-vault"></a>Azure Key Vault の一般的なエラー コード

Azure Key Vault に対して操作を実行した結果、返される可能性があるエラー コードを次の表に示します

| エラー コード | ユーザー メッセージ |
|--|--|
| VaultAlreadyExists |  指定された名前で新しいキー コンテナーを作成しようとしましたが、その名前が既に使用されているため作成できませんでした。 この名前のキー コンテナーを最近削除した場合は、まだ論理的な削除状態である可能性があります。 論理的に削除された状態かどうかは、[こちら](./key-vault-recovery.md?tabs=azure-portal#list-recover-or-purge-a-soft-deleted-key-vault)で確認できます |
| VaultNameNotValid |  コンテナー名は、3 から 24 文字の文字列とする必要があり、含めることができるのは数字 (0-9)、文字 (a-z、A-Z)、およびハイフン (-) のみとなります。 |
| AccessDenied |  この操作を行うためのアクセス許可がアクセス ポリシーにない可能性があります。 |
| ForbiddenByFirewall |  クライアント アドレスは承認されておらず、呼び出し元は信頼できるサービスではありません。 |
| ConflictError |  同じ項目に対して複数の操作を要求しています。  |
| RegionNotSupported |  このリソースでは、指定された Azure リージョンはサポートされていません。 |
| SkuNotSupported |  このリソースでは、指定された SKU の種類はサポートされていません。 |
| ResourceNotFound |  指定した Azure リソースが見つかりません。 |
| ResourceGroupNotFound | 指定した Azure リソース グループが見つかりません。 |
| CertificateExpired |  証明書の有効期限と有効期間を確認してください。 |


## <a name="next-steps"></a>次の手順

- 「[Azure Key Vault 開発者ガイド](developers-guide.md)」を参照する
- 「[Key Vault に対する認証](authentication.md)」の詳細を参照する
