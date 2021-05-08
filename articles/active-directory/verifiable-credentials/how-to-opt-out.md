---
title: Azure Active Directory Verifiable Credentials (プレビュー) をオプトアウトする
description: Verifiable Credentials のプレビューをオプトアウトする方法について説明します
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: d6e72b6d6f566fcf3f52e1c48ab6824c0e9a968e
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222794"
---
# <a name="opt-out-of-the-verifiable-credentials-preview"></a>Verifiable Credentials (プレビュー) をオプトアウトする

この記事の内容:

- オプトアウトが必要になる理由。
- 必要な手順。
- データはどうなるか?
- 既存の検証可能な資格情報に対する影響。

> [!IMPORTANT]
> Azure Active Directory Verifiable Credentials は、現在パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="prerequisites"></a>前提条件

- 検証可能な資格情報のオンボードを完了していること。

## <a name="potential-reasons-for-opting-out"></a>考えられるオプトアウトする理由

現時点では、ドメイン情報を変更することはできません。 その結果、間違えた場合や変更を加える必要がある場合、オプトアウトしてからもう一度開始する以外の方法がありません。

## <a name="the-steps-required"></a>必要な手順

1. Azure portal から、検証可能な資格情報を検索します。
2. 左側のメニューで **[設定]** を選択します。
3. **[Reset your organizatio]\(組織のリセット\)** セクションで、 **[Delete all credentials, and opt out of preview]\(すべての資格情報を削除してプレビューを無効にする\)** を選択します。

   ![組織のリセットの設定](media/how-to-opt-out/settings-reset.png)

4. 警告メッセージを読み、続行する場合は **[Delete and opt out]\(削除とオプトアウト\)** を選択します。

   ![削除とオプトアウトの設定](media/how-to-opt-out/delete-and-opt-out.png)

これで、Verifiable Credentials プレビューをオプトアウトしました。 続きを読んで、内部で何が起きているか理解してください。

## <a name="what-happens-to-your-data"></a>データはどうなるか?

Azure Active Directory Verifiable Credentials サービスのオプトアウトを完了すると、次のアクションが実行されます。

- Key Vault の DID キーが[論理的に削除](../../key-vault/general/soft-delete-overview.md)されます。
- 発行者オブジェクトがデータベースから削除されます。
- テナント識別子がデータベースから削除されます。 
- すべてのコントラクト オブジェクトがデータベースから削除されます。

オプトアウトが行われると、DID を復旧したり、DID に対して操作を実行したりすることはできなくなります。 このステップは一方向の操作であり、もう一度オプトインする必要があります。これにより、新しい DID が作成されます。  

## <a name="effect-on-existing-verifiable-credentials"></a>既存の検証可能な資格情報に対する影響

既に発行済みのすべての検証可能な資格情報は、引き続き存在します。 これらは暗号として無効化されません。これは、DID が ION を通じて引き続き解決可能であるためです。
ただし、証明書利用者が状態 API を呼び出すと、常にエラー メッセージが返されます。

## <a name="next-steps"></a>次の手順

- [Azure テナント](get-started-verifiable-credentials.md)で検証可能な資格情報を設定する