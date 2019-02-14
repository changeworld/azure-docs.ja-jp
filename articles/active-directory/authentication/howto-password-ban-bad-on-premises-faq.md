---
title: オンプレミスの Azure AD パスワード保護に関する FAQ
description: オンプレミスの Azure AD パスワード保護に関する FAQ
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.openlocfilehash: 62bda3a1c9cb9d53578c2d471b9e63d1f0873234
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/02/2019
ms.locfileid: "55663300"
---
# <a name="preview-azure-ad-password-protection-on-premises---frequently-asked-questions"></a>更新:オンプレミスの Azure AD パスワード保護 - よく寄せられる質問

|     |
| --- |
| Azure AD のパスワード保護は、Azure Active Directory のパブリック プレビュー機能です。 詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。|
|     |

## <a name="general-questions"></a>一般的な質問

**Q:Azure AD パスワード保護はいつ一般提供 (GA) されますか?**

GA は、Q1 CY2019 を予定しています (2019 年 3 月末より前)。 今までこの機能に関するフィードバックを提供してくださった皆さん、どうもありがとうございました。感謝しております。

**Q:オンプレミスの Azure AD パスワード保護はパブリックでないクラウドでサポートされますか?**

いいえ。オンプレミスの Azure AD パスワード保護は、パブリック クラウドでのみサポートされます。

**Q:どのようにして Azure AD パスワード保護の利点を自分のオンプレミスのユーザーのサブセットに適用できますか?**

サポートされていません。 デプロイして有効にされると、Azure AD パスワード保護では識別を行いません。すべてのユーザーがセキュリティの利点を等しく受け取ります。

**Q:その他のパスワード フィルター ベースの製品とサイド バイ サイドで Azure AD パスワード保護をインストールすることはサポートされていますか?**

はい。 複数の登録されたパスワード フィルター DLL に対するサポートは、コア Windows 機能であり、Azure AD パスワード保護に固有のものではありません。 登録されたパスワード フィルター DLL はすべて、パスワードを受け入れる前に一致している必要があります。

**Q:sysvol レプリケーションに DFSR が必要なのはなぜですか?**

FRS (DFSR に対する先行テクノロジ) には、多くの既知の問題があり、より新しいバージョンの Windows Server Active Directory ではまったくサポートされていません。 Azure AD パスワード保護のゼロ テストは、FRS で構成されたドメインで行われます。

詳細については、次の記事を参照してください。

[sysvol レプリケーションを DFSR に移行するケース](https://blogs.technet.microsoft.com/askds/2010/04/22/the-case-for-migrating-sysvol-to-dfsr)

[FRS の終了が近づいています](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs)

**Q:DC エージェント ソフトウェアのインストールまたはアップグレードに再起動が必要なのはなぜですか?**

これはコア Windows 動作によって必要になります。

**Q:特定のプロキシ サーバーを使用するように DC エージェントを構成する方法はありますか?**

いいえ。

## <a name="next-steps"></a>次の手順

ここでは回答されていないオンプレミスの Azure AD パスワード保護に関するご質問がある場合、以下のフィードバック項目を送信してください。

[Azure AD のパスワード保護をデプロイする](howto-password-ban-bad-on-premises-deploy.md)
