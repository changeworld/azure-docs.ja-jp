---
title: セルフサービス パスワード リセットのライセンス - Azure Active Directory
description: Azure Active Directory のさまざまなセルフサービス パスワード リセット ライセンス要件について説明します
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/08/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d332c831cc764c61a4672ea5ad1db231b68e106
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104952373"
---
# <a name="licensing-requirements-for-azure-active-directory-self-service-password-reset"></a>Azure Active Directory のセルフサービス パスワード リセットのライセンス要件

ユーザーがデバイスまたはアプリケーションにサインインできないときにヘルプ デスクへの問い合わせと生産性の低下を減らすには、Azure Active Directory (Azure AD) のユーザーアカウントでセルフサービス パスワード リセット (SSPR) を有効にすることができます。 SSPR を構成する機能には、パスワードの変更、リセット、ロック解除、オンプレミスのディレクトリへの書き戻しが含まれます。 Basic SSPR の機能は Microsoft 365 Business Standard 以上、すべての Azure AD Premium SKU で無料でご利用いただけます。

この記事では、セルフサービス パスワード リセットのライセンスを取得して使用するさまざまな方法について詳しく説明します。 価格と課金の詳細については、[Azure AD の価格に関するページ](https://azure.microsoft.com/pricing/details/active-directory/)を参照してください。

## <a name="compare-editions-and-features"></a>エディションと機能の比較

SSPR では、テナントについてのみライセンスが必要です。 

次の表では、パスワードの変更、リセット、またはオンプレミスの書き戻しのさまざまな SSPR シナリオと、機能が提供される SKU の概要を示します。

| 機能 | Azure AD Free | Microsoft 365 Business Standard | Microsoft 365 Business Premium | Azure AD Premium P1 または P2 |
| --- |:---:|:---:|:---:|:---:|
| **クラウドのみのユーザー パスワードの変更**<br />Azure AD のユーザーが自分のパスワードを知っていて、新しいパスワードへの変更を希望する場合。 | ● | ● | ● | ● |
| **クラウドのみのユーザー パスワードのリセット**<br />Azure AD のユーザーがパスワードを忘れて、リセットする必要がある場合。 | | ● | ● | ● |
| **オンプレミスの書き戻しを含む、ハイブリッド ユーザーのパスワードの変更またはリセット**<br />Azure AD Connect を使用してオンプレミスのディレクトリから同期されている Azure AD のユーザーが、パスワードを変更またはリセットし、オンプレミスに新しいパスワードを書き戻す場合。 | | | ● | ● |

> [!WARNING]
> スタンドアロンの Microsoft 365 Basic および Standard ライセンス プランでは、オンプレミスの書き戻しのある SSPR はサポートされていません。 オンプレミスの書き戻し機能には Azure AD Premium P1、Premium P2、または Microsoft 365 Business Premium が必要です。

追加のライセンス情報 (コストを含む) については、以下のページをご覧ください。

* [Azure Active Directory の料金](https://azure.microsoft.com/pricing/details/active-directory/)
* [Azure Active Directory の機能と働き](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Enterprise](https://www.microsoft.com/microsoft-365/enterprise)
* [Microsoft 365 Business](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)

## <a name="next-steps"></a>次のステップ

SSPR の使用を開始するには、次のチュートリアルをご覧ください。

> [!div class="nextstepaction"]
> [チュートリアル:セルフサービス パスワード リセット (SSPR) を有効にする](tutorial-enable-sspr.md)