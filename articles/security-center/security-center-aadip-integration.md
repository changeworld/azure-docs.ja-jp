---
title: Azure Active Directory Identity Protection を Azure Security Center に接続する | Microsoft Docs
description: Azure Security Center を Azure Active Directory Identity Protection と統合する方法について説明します。
services: security-center
documentationcenter: na
author: terrylan
manager: MBaldwin
editor: ''
ms.assetid: 0d4b77c2-dba4-4e46-8f55-ab04ddd92496
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2017
ms.author: yurid
ms.openlocfilehash: b15508acea92c165cba85e39ab907fa524088164
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/03/2018
ms.locfileid: "32774184"
---
# <a name="connecting-azure-active-directory-identity-protection-to-azure-security-center"></a>Azure Active Directory Identity Protection を Azure Security Center に接続する
このドキュメントは、Azure Active Directory (AD) Identity Protection と Azure Security Center 間の統合を構成する場合に役立ちます。

## <a name="why-connect-azure-ad-identity-protection"></a>Azure AD Identity Protection を接続する理由
[Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) を使用すると、組織の ID に影響する潜在的な脆弱性を検出することができます。 接続すると、Security Center で Azure AD Identity Protection のアラートを表示できるようになります。 この統合で、ハイブリッド クラウド ワークロードに関連するすべてのセキュリティ アラートの表示、関連付け、および調査を Security Center で実行できるようになります。 

## <a name="how-do-i-configure-this-integration"></a>この統合を構成する方法
組織が既に Azure AD Identity Protection を使用している場合は、以下の手順で統合を構成します。

1. **[Security Center]** ダッシュボードを開きます。
2. 左側のウィンドウで **[セキュリティ ソリューション]** をクリックします。 Security Center で、組織の Azure AD Identity Protection が有効かどうかが自動的に検出されます。

    ![AADIP](./media/security-center-aadip-integration/security-center-aadip-integration-fig1.png)

3. **[接続]** をクリックします。
4. **[Azure AD Identity Protection の統合]** ページで、スクロールして適切なワークスペースを選択します。

    ![ワークスペース](./media/security-center-aadip-integration/security-center-aadip-integration-fig2.png)

5. **[接続]** をクリックします。

この構成が完了すると、**[セキュリティ ソリューション]** の **[接続済みのソリューション]** に Azure AD Identity Protection ソリューションが表示されます。 

## <a name="next-steps"></a>次の手順
このドキュメントでは、Azure AD Identity Protection を Security Center に接続する方法について説明しました。 Security Center の詳細については、次の記事を参照してください。

* [Microsoft Advanced Threat Analytics を Azure Security Center に接続する](security-center-ata-integration.md)
* [Azure Security Center でのセキュリティ ポリシーの設定](security-center-policies.md) -- Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明しています。
* [Azure Security Center でのセキュリティに関する推奨事項の管理](security-center-recommendations.md) 」-- 推奨事項に従って Azure リソースを保護する方法について説明しています。
* [Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md) 」 -- Azure リソースの正常性を監視する方法について説明しています。
* [Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md) 」 -- セキュリティの警告の管理と対応の方法について説明しています。
* [Azure Security Center を使用したパートナー ソリューションの監視](security-center-partner-solutions.md) 」 -- パートナー ソリューションの正常性状態を監視する方法について説明しています。
- 「[Azure Security Center のデータ セキュリティ](security-center-data-security.md)」 - Security Center でデータがどのように管理および保護されているかを説明しています。
* [Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md) 」 -- このサービスの使用に関してよく寄せられる質問が記載されています。
* [Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/) -- Azure のセキュリティに関する最新のニュースと情報を入手できます。


