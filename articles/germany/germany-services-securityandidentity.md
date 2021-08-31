---
title: Azure Germany のセキュリティ サービスと ID サービス | Microsoft Docs
description: この記事では、Azure Germany のセキュリティ サービスと ID サービスを比較します。
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfdocs
ms.openlocfilehash: 1d66a1f9a1fd2aea17d28d52cbcaa102ec3cb6cc
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/21/2020
ms.locfileid: "117028985"
---
# <a name="azure-germany-security-and-identity-services"></a>Azure Germany のセキュリティ サービスと ID サービス

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

## <a name="key-vault"></a>Key Vault
Azure Key Vault サービスとその使用方法について詳しくは、[Key Vault のグローバル ドキュメント](../key-vault/index.yml)をご覧ください。

Key Vault は、Azure Germany で一般提供されています。 グローバル Azure と同様に、拡張機能は存在しません。Key Vault は、PowerShell と CLI を介してのみ利用できます。

## <a name="azure-active-directory"></a>Azure Active Directory
Azure Active Directory は、Microsoft Azure で実行されている情報システムに ID およびアクセス機能を提供します。 ディレクトリ サービス、セキュリティ グループ、グループ ポリシーを使用して、Azure Active Directory を使用するマシンのアクセス ポリシーとセキュリティ ポリシーを制御できます。 アカウントとセキュリティ グループを Azure ロールベースのアクセス制御 (Azure RBAC) と共に使用することで、情報システムへのアクセスを管理できます。 

Azure Active Directory は、Azure Germany で一般提供されています。

### <a name="variations"></a>バリエーション

* Azure Germany の Azure Active Directory は、グローバル Azure の Azure Active Directory から完全に分離されています。 
* Microsoft アカウントを使用して Azure Germany にサインインすることはできません。
* Azure Germany のログイン サフィックスは *onmicrosoft.de* です (グローバル Azure などで使用されている *onmicrosoft.com* ではありません)。
* Azure Germany を使用するには別個のサブスクリプションが必要になります。
* Azure Germany のお客様は、グローバル Azure のサブスクリプションや ID を必要とするリソースにアクセスできません。
* グローバル Azure のお客様は、Azure Germany のサブスクリプションや ID を必要とするリソースにアクセスできません。
* 追加ドメインは、いずれかのクラウド環境でのみ、追加したり、検証したりすることができます。
 
> [!NOTE]
> *Azure Germany 内に両方のテナント* を持つ他のテナントからのユーザーに権限を割り当てることはまだできません。


## <a name="next-steps"></a>次のステップ
補足情報と更新情報については、[Azure Germany のブログ](/archive/blogs/azuregermany/)を参照してください。