---
title: セキュリティ ポリシーの操作 | Microsoft Docs
description: この記事では、Azure Security Center でセキュリティ ポリシーを操作する方法について説明します。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2d248817-ae97-4c10-8f5d-5c207a8019ea
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: c98ae7c95ac3fc186786612dd3d8d8bd55fa816f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "82024882"
---
# <a name="working-with-security-policies"></a>セキュリティ ポリシーの操作

この記事では、セキュリティ ポリシーの構成方法と、それの Security Center での表示方法について説明します。 

## <a name="introduction-to-security-policies"></a>セキュリティ ポリシーの概要

セキュリティ ポリシーは、ワークロードの必要な構成を定義し、会社や規制当局のセキュリティ要件に確実に準拠できるようにします。

Azure Security Center では、選択したポリシーに基づいてセキュリティに関する推奨事項が作成されます。 Security Center のポリシーは、Azure Policy で作成されたポリシー イニシアティブに基づいています。 [Azure Policy](../governance/policy/overview.md) を使用して、ポリシーを管理したり、管理グループや複数のサブスクリプションでポリシーを設定したりできます。

Security Center では、セキュリティ ポリシーを操作するための次のオプションが提供されます。

* **組み込みの既定のポリシーの表示と編集** - Security Center を有効にすると、"ASC の既定値" という名前の組み込みイニシアティブが、Security Center のすべての登録済みサブスクリプション (Free または Standard レベル) に自動的に割り当てられます。 このイニシアティブをカスタマイズするために、イニシアティブ内の個々のポリシーを有効または無効にすることができます。 [組み込みのセキュリティ ポリシー](security-center-policy-definitions.md)の一覧を参照して、すぐに使用できるオプションを確認してください。

* **独自のカスタム ポリシーの追加** - サブスクリプションに適用されているセキュリティ イニシアティブをカスタマイズする場合は、Security Center で行うことができます。 作成したポリシーにマシンが従っていない場合は、推奨事項が提供されます。 カスタム ポリシーを作成して割り当てる手順については、[カスタム セキュリティ ポリシーの使用](custom-security-policies.md)に関する記事をご覧ください。

* **規制コンプライアンス ポリシーの追加** - Security Center の規制コンプライアンス ダッシュボードには、特定の標準または規制 (Azure CIS、NIST SP 800-53 R4、SWIFT CSP CSCF-v2020 など) のコンテキストにおける環境内のすべての評価の状態が表示されます。 詳細については、[規制コンプライアンスの向上](security-center-compliance-dashboard.md)に関する記事をご覧ください。


## <a name="managing-your-security-policies"></a>セキュリティ ポリシーの管理

Security Center でセキュリティ ポリシーを表示するには:

1. **Security Center** ダッシュボードで **[セキュリティ ポリシー]** を選択します。

    ![[ポリシー管理] ウィンドウ](./media/security-center-policies/security-center-policy-mgt.png)

   **[ポリシー管理]** 画面には、管理グループ、サブスクリプション、およびワークスペースの数、および管理グループの構造が表示されます。

1. ポリシーを参照するサブスクリプションまたは管理グループを選択します。

1. そのサブスクリプションまたは管理グループのセキュリティ ポリシー ページが表示されます。 利用可能な割り当て済みのポリシーが表示されます。

   ![ポリシー画面](./media/tutorial-security-policy/security-policy-page.png)

    > [!NOTE]
    > 既定のポリシーの横に "MG 継承済み" というラベルがある場合、そのポリシーが管理グループに割り当てられており、表示しているサブスクリプションに継承されていることを意味します。


1. このページで使用可能なオプションから選択します。

    1. 業界のポリシーを使用するには、 **[標準をさらに追加]** を選択します。 詳細については、[動的コンプライアンス パッケージへの更新](update-regulatory-compliance-packages.md)に関する記事をご覧ください。

    1. カスタム イニシアティブを割り当てて管理するには、 **[カスタム イニシアティブの追加]** を選択します。 詳細については、[カスタム セキュリティ ポリシーの使用](custom-security-policies.md)に関する記事をご覧ください。

    1. 既定のポリシーを表示して編集するには、 **[有効なポリシーの表示]** を選択し、下記の説明に従って操作を進めます。 

       ![ポリシー画面](./media/security-center-policies/policy-screen.png)
       
       この **[セキュリティ ポリシー]** 画面には、選択したサブスクリプションまたは管理グループに割り当てられているポリシーによって実行されたアクションが反映されます。
       
       * 上部のリンクを使用して、サブスクリプションまたは管理グループに適用されているポリシー**割り当て**を開きます。 これらのリンクを使用すると、割り当てにアクセスしたり、ポリシーを編集または無効化したりすることができます。 たとえば、特定のポリシー割り当てによってエンドポイントが実質的に保護されないようになっていることがわかった場合は、リンクを使用してそのポリシーを編集または無効化します。
       
       * ポリシーの一覧では、サブスクリプションまたは管理グループに対して実際に適用されているポリシーを確認できます。 スコープに適用されている各ポリシーの設定が考慮され、ポリシーによって実行されたアクションの累積的な結果が表示されます。 たとえば、ポリシーのある割り当てでは無効になっていても、別の割り当てでは AuditIfNotExist に設定されている場合、累積的な効果によって AuditIfNotExist が適用されます。 よりアクティブな効果が常に優先されます。
       
       * 設定できるポリシーの効果は、Append、Audit、AuditIfNotExists、Deny、DeployIfNotExists、Disabled です。 効果が適用されるしくみの詳細については、[Policy の効果](../governance/policy/concepts/effects.md)に関するページを参照してください。

       > [!NOTE]
       > 割り当てられているポリシーを確認すると、複数の割り当てが表示され、各割り当てのそれぞれの構成を参照できます。


## <a name="who-can-edit-security-policies"></a>セキュリティ ポリシーを編集できるユーザーは誰ですか。

REST API 経由または Windows PowerShell を使用して、Azure Policy ポータルからセキュリティ ポリシーを編集できます。

Security Center ではロールベースのアクセス制御 (RBAC) が使用されています。RBAC が提供する組み込みのロールは、Azure でユーザー、グループ、サービスに割り当てることができます。 ユーザーが Security Center を開くと、アクセス権のあるリソースに関する情報のみが表示されます。 これは、リソースのサブスクリプションに対して、"*所有者*"、"*共同作成者*"、または "*閲覧者*" のロールがユーザーに割り当てられていることを意味します。 これらのロールに加え、Security Center には、次の 2 つの固有のロールがあります。

- **セキュリティ閲覧者**: Security Center に対する閲覧権限を持ちます。推奨事項、アラート、ポリシー、および正常性を確認できますが、変更を加えることはできません。
- **セキュリティ管理者**:*セキュリティ閲覧者*と同じ閲覧権限を持ちますが、セキュリティ ポリシーの更新と推奨事項とアラートの解除を実行することもできます。


## <a name="disable-security-policies"></a>セキュリティ ポリシーの無効化
既定のセキュリティ ポリシーによって、お使いの環境に関係のない推奨事項が生成される場合は、その推奨事項を送信するポリシー定義を無効にすることで、生成を停止できます。
推奨事項の詳細については、[セキュリティに関する推奨事項の管理](security-center-recommendations.md)についてのページを参照してください。

1. Security Center 内の **[Policy & Compliance]\(ポリシーとコンプライアンス\)** セクションで **[セキュリティ ポリシー]** を選択します。

   ![ポリシー管理](./media/tutorial-security-policy/policy-management.png)

2. 推奨設定を無効にするサブスクリプションまたは管理グループを選択します。

   > [!NOTE]
   > 管理グループでは、そのポリシーがそのサブスクリプションに適用されることに注意してください。 そのため、サブスクリプションのポリシーを無効にしても、そのサブスクリプションが、同じポリシーをまだ使用している管理グループに属していると、引き続きポリシー推奨事項を受け入れることになります。 ポリシーは管理レベルから引き続き適用され、推奨事項は引き続き生成されます。

1. **[有効なポリシーの表示]** を選択します。

   ![ポリシーの無効化](./media/tutorial-security-policy/view-effective-policy.png)

1. 割り当てられたポリシーを選択します。

   ![ポリシーの無効化](./media/tutorial-security-policy/security-policy.png)

1. **[パラメーター]** セクションで、推奨事項を呼び出すポリシーのうち、無効にするものを検索します。次に、ドロップダウン リストから **[無効]** を選択します。

   ![ポリシーの無効化](./media/tutorial-security-policy/disable-policy.png)

1. **[保存]** を選択します。

   > [!NOTE]
   > ポリシー無効化の変更が有効になるまでに、最大 12 時間かかる場合があります。



## <a name="next-steps"></a>次のステップ
この記事では、セキュリティ ポリシーについて説明しました。 関連情報については、次の記事をご覧ください。

* PowerShell を使用してポリシーを設定する手順については、「[クイック スタート: Azure PowerShell モジュールを使用してポリシーの割り当てを作成し、準拠していないリソースを特定する](../governance/policy/assign-policy-powershell.md)」をご覧ください。

* Azure Policy でセキュリティ ポリシーを編集する方法については、「[コンプライアンスを強制するポリシーの作成と管理](../governance/policy/tutorials/create-and-manage.md)」をご覧ください。

* Azure Policy を使用して、サブスクリプションまたは管理グループでポリシーを設定する方法については、[Azure Policy の概要](../governance/policy/overview.md)に関する記事をご覧ください。