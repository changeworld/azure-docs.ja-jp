---
title: "Azure Security Center セキュリティー ポリシーの紹介 | Microsoft Docs"
description: "Azure Security Center キュリティー ポリシーの主な機能について説明します。"
services: security-center
documentationcenter: na
author: YuriDio
manager: MBaldwin
editor: 
ms.assetid: f24b1e4a-cc36-4542-b21e-041453cdfcd8
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2017
ms.author: yurid
ms.openlocfilehash: f85f7077e5227818d062d114370e7344601a998e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="security-policies-overview"></a>セキュリティ ポリシーの概要
このドキュメントでは、Security Center のセキュリティ ポリシーの概要を示します。

## <a name="what-are-security-policies"></a>セキュリティ ポリシーとは
セキュリティ ポリシーは、ワークロードの必要な構成を定義し、会社や規制のセキュリティ要件に確実に準拠できるようにします。 Security Center では、Azure サブスクリプションのポリシーを定義できます。これらのポリシーは、ワークロードの種類やデータの機密性に合わせて調整できます。 たとえば、個人情報のような規制されたデータが使用されるアプリケーションには、他のワークロードより高いレベルのセキュリティが必要です。 

Security Center のポリシーには、次のコンポーネントが含まれています。

- [データ収集](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection): エージェントのプロビジョニングとデータ収集の設定。
- セキュリティ ポリシー: Security Center で監視および推奨されるコントロールを決定する [Azure Policy](https://docs.microsoft.com/azure/azure-policy/azure-policy-introduction) (Security Center で編集するか、または、Azure Policy を使用して、新しい定義の作成、追加ポリシーの定義、管理グループ間でのポリシーの割り当てを行います)。
- 電子メール通知: セキュリティ連絡先と[電子メール通知設定](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)。
- [価格レベル](https://docs.microsoft.com/azure/security-center/security-center-pricing): Free または Standard の価格の選択。これにより、(サブスクリプション、リソース グループ、ワークスペースに指定できる) スコープ内のリソースに使用できる Security Center の機能が決まります。 


## <a name="who-can-edit-security-policies"></a>セキュリティ ポリシーを編集できるユーザーは誰ですか。
Security Center ではロールベースのアクセス制御 (RBAC) が使用されています。RBAC が提供する組み込みのロールは、Azure でユーザー、グループ、サービスに割り当てることができます。 ユーザーが Security Center を開くと、アクセス権のあるリソースに関する情報のみが表示されます。 これは、サブスクリプションまたはリソースが属するリソース グループについて、所有者、共同作業者、閲覧者のいずれかのロールがユーザーに割り当てられていることを意味します。 これらのロールに加え、Security Center には、次の 2 つの固有のロールがあります。

- セキュリティ閲覧者: このロールに属しているユーザーは、Security Center に対する権限を表示できます。推奨事項、アラート、ポリシー、および正常性を表示できますが、変更を加えることはできません。
- セキュリティ管理者: セキュリティ閲覧者と同じですが、セキュリティ ポリシーの更新と、推奨事項とアラートの解除を実行することもできます。


## <a name="next-steps"></a>次のステップ
このドキュメントでは、Azure Security Center のセキュリティ ポリシーについて説明しました。 Azure セキュリティ センターの詳細については、次を参照してください。

* [Azure Security Center でのセキュリティ ポリシーの設定](security-center-policies.md) -- Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明しています。
* [Azure Security Center でのセキュリティに関する推奨事項の管理](security-center-recommendations.md) 」-- 推奨事項に従って Azure リソースを保護する方法について説明しています。
* [Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md) 」 -- Azure リソースの正常性を監視する方法について説明しています。
* [Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md) 」 -- セキュリティの警告の管理と対応の方法について説明しています。
* [Azure Security Center を使用したパートナー ソリューションの監視](security-center-partner-solutions.md) 」 -- パートナー ソリューションの正常性状態を監視する方法について説明しています。
- 「[Azure Security Center のデータ セキュリティ](security-center-data-security.md)」 - Security Center でデータがどのように管理および保護されているかを説明しています。
* [Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md) 」 -- このサービスの使用に関してよく寄せられる質問が記載されています。
* [Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/) -- Azure のセキュリティに関する最新のニュースと情報を入手できます。


