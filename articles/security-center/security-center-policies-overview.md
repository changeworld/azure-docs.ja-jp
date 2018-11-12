---
title: Azure Security Center のセキュリティ ポリシー設定 | Microsoft Docs
description: Azure Security Center のセキュリティ ポリシー設定を構成します。
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: f24b1e4a-cc36-4542-b21e-041453cdfcd8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/3/2018
ms.author: rkarlin
ms.openlocfilehash: 3d2687f56b69174fde783060d994e3c01763ec94
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2018
ms.locfileid: "51256631"
---
# <a name="security-policy-settings"></a>セキュリティ ポリシー設定
この記事では、Security Center でのセキュリティ ポリシー設定の概要を示します。

## <a name="what-are-security-policies"></a>セキュリティ ポリシーとは
セキュリティ ポリシーは、ワークロードの必要な構成を定義し、会社や規制のセキュリティ要件に確実に準拠できるようにします。 Azure Security Center では、Azure サブスクリプションのポリシーを定義でき、ワークロードの種類やデータの機密性に合わせて調整できます。 たとえば、個人を特定できる情報のような、規制されたデータが使用されるアプリケーションには、他のワークロードより高いレベルのセキュリティが必要です。

セキュリティ ポリシーでは、以下を設定できます。

- **データ収集**: エージェントのプロビジョニングと[データ収集](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection)の設定を決定します。
- **セキュリティ ポリシー**: Security Center がどのコントロールを監視し、推奨するかを決定します。 Security Center.の[セキュリティ ポリシー](security-center-policies.md)を編集できます。 [Azure Policy](security-center-azure-policy.md) を使用し、新しい定義の作成、追加のポリシーの定義、管理グループ間でのポリシーの割り当てを行うこともできます。 
- **電子メール通知**: セキュリティ連絡先と[電子メール通知](security-center-provide-security-contact-details.md)の設定を指定します。
- **価格レベル**: 無料または標準[価格選択](security-center-pricing.md)を定義します。 選択したレベルでは、スコープ内のリソースに使用できる Security Center の機能を決定します。 サブスクリプション、リソース グループ、およびワークスペースのレベルを指定できます。

> [!NOTE]
> これらはすべてサブスクリプションごとに設定できます。 ワークスペースについては、データ収集と価格レベルのみを設定できます。 リソース グループについては、価格レベルのみを設定できます。
>


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
* [Azure セキュリティ ブログ](https://blogs.msdn.com/b/azuresecurity/): Azure のセキュリティに関する最新のニュースと情報を入手できます。
