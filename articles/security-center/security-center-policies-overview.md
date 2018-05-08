---
title: Azure Security Center セキュリティ ポリシーの紹介 | Microsoft Docs
description: Azure Security Center セキュリティ ポリシーの主な機能について説明します。
services: security-center
documentationcenter: na
author: terrylan
manager: MBaldwin
editor: ''
ms.assetid: f24b1e4a-cc36-4542-b21e-041453cdfcd8
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2017
ms.author: yurid
ms.openlocfilehash: f552164edc22b450c4ace57cce5c051d1c93e780
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/03/2018
---
# <a name="security-policies-overview"></a>セキュリティ ポリシーの概要
この記事では、Security Center のセキュリティ ポリシーの概要を示します。

## <a name="what-are-security-policies"></a>セキュリティ ポリシーとは
セキュリティ ポリシーは、ワークロードの必要な構成を定義し、会社や規制のセキュリティ要件に確実に準拠できるようにします。 Azure Security Center では、Azure サブスクリプションのポリシーを定義でき、ワークロードの種類やデータの機密性に合わせて調整できます。 たとえば、個人を特定できる情報のような、規制されたデータが使用されるアプリケーションには、他のワークロードより高いレベルのセキュリティが必要です。 

Security Center のポリシーには、次のコンポーネントが含まれています。

- **データ収集**: エージェントのプロビジョニングと[データ収集](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection)の設定を決定します。
- **セキュリティ ポリシー**: Security Center がどのコントロールを監視し、推奨するかを決定します。 Security Center.の[セキュリティ ポリシー](https://docs.microsoft.com/azure/security-center/security-center-policies)を編集できます。 [Azure Policy](security-center-azure-policy.md) を (制限付きのプレビューで) 使用し、新しい定義の作成、追加のポリシーの定義、管理グループ間でのポリシーの割り当てを行うこともできます。
- **電子メール通知**: セキュリティ連絡先と[電子メール通知](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)の設定を決定します。
- **価格レベル**: 無料または標準[価格選択](https://docs.microsoft.com/azure/security-center/security-center-pricing)を定義します。 選択したレベルでは、スコープ内のリソースに使用できる Security Center の機能を決定します。 サブスクリプション、リソース グループ、およびワークスペースのレベルを指定できます。 


## <a name="who-can-edit-security-policies"></a>セキュリティ ポリシーを編集できるユーザーは誰ですか。
Security Center ではロールベースのアクセス制御 (RBAC) が使用されています。RBAC が提供する組み込みのロールは、Azure でユーザー、グループ、サービスに割り当てることができます。 ユーザーが Security Center を開くと、アクセス権のあるリソースに関する情報のみが表示されます。 これは、サブスクリプションまたはリソースが属するリソース グループについて、*所有者*、*共同作成者*、*閲覧者*のいずれかのロールがユーザーに割り当てられていることを意味します。 これらのロールに加え、Security Center には、次の 2 つの固有のロールがあります。

- **セキュリティ閲覧者**: Security Center に対する閲覧権限を持ちます。推奨事項、アラート、ポリシー、および正常性を確認できますが、変更を加えることはできません。
- **セキュリティ管理者**: *セキュリティ閲覧者*と同じ閲覧権限を持ちますが、セキュリティ ポリシーの更新と推奨事項とアラートの解除を実行することもできます。


## <a name="next-steps"></a>次の手順
この記事では、Azure Security Center のセキュリティ ポリシーについて説明しました。 Azure Security Center の詳細については、次の記事をご覧ください。

* [Azure Security Center でのセキュリティ ポリシーの設定](security-center-policies.md): Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明しています。
* [Azure Security Center でのセキュリティに関する推奨事項の管理](security-center-recommendations.md): Security Center の推奨事項に従って Azure リソースを保護する方法について説明しています。
* [Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md): Azure リソースの正常性を監視する方法について説明しています。
* 「[Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md)」: セキュリティの警告の管理と対応の方法について説明しています。
* 「[Azure Security Center を使用したパートナー ソリューションの監視](security-center-partner-solutions.md)」: パートナー ソリューションの正常性状態を監視する方法について説明しています。
- [Azure Security Center のデータ セキュリティ](security-center-data-security.md): Security Center でデータがどのように管理および保護されているかを説明しています。
* [Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md): このサービスの使用に関してよく寄せられる質問に対する回答を確認します。
* [Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/): Azure のセキュリティに関する最新のニュースと情報を入手できます。


