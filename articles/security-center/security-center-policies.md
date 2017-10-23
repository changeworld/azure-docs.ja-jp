---
title: "Azure Security Center でのセキュリティ ポリシーの設定 | Microsoft Docs"
description: "このドキュメントは、Azure セキュリティ センターでのセキュリティ ポリシーを構成する場合に役立ちます。"
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 3b9e1c15-3cdb-4820-b678-157e455ceeba
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2017
ms.author: yurid
ms.openlocfilehash: 67564e930310433bf4d51f7642bdd7ebf7e8e600
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="set-security-policies-in-azure-security-center"></a>Azure Security Center でのセキュリティ ポリシーの設定
このドキュメントでは、Security Center でセキュリティ ポリシーを構成するうえで必要な手順について詳しく説明します。


## <a name="how-security-policies-work"></a>セキュリティ ポリシーのしくみ
Security Center では、Azure サブスクリプションごとに自動で既定のセキュリティ ポリシーが作成されます。 そのポリシーを Security Center で編集するか、[Azure Policy](http://docs.microsoft.com/azure/azure-policy/azure-policy-introduction) を使って新しい定義を作成したり、追加のポリシーを定義したり、管理グループ (組織全体のこともあれば、その中の一部署などのこともあります) にポリシーを割り当てたりすることによって、複数のスコープにわたってポリシーに対するコンプライアンスを監視することができます。

> [!NOTE]
> Azure Policy は現在、制限付きのプレビュー段階です。 参加するには、[こちら](https://aka.ms/getpolicy)をクリックしてください。 Azure ポリシーの詳細については、「[Create and manage policies to enforce compliance](http://docs.microsoft.com/en-us/azure/azure-policy/create-manage-policy) (コンプライアンス強化のためのポリシーの作成と管理)」を参照してください。

## <a name="how-to-change-security-policies-in-security-center"></a>Security Center でセキュリティ ポリシーを変更する方法
Security Center では、各 Azure サブスクリプションの既定のセキュリティ ポリシーを編集できます。 セキュリティ ポリシーを変更するには、そのサブスクリプションまたはそれが含まれる管理グループの所有者、共同作成者、セキュリティ管理者のいずれかである必要があります。 Azure Portal にサインインし、次の手順に従って Security Center でセキュリティ ポリシーを表示します。

1. **Security Center** ダッシュボードの **[全般]** で、**[セキュリティ ポリシー]** をクリックします。
2. セキュリティ ポリシーを有効にするサブスクリプションを選択します。

    ![ポリシー管理](./media/security-center-policies/security-center-policies-fig10.png)

3. **[ポリシー コンポーネント]** セクションで、**[セキュリティ ポリシー]** をクリックします。

    ![ポリシー コンポーネント](./media/security-center-policies/security-center-policies-fig12.png)

4. これが、Azure Policy によって Security Center に割り当てられる既定のポリシーです。 **[POLICIES AND PARAMETERS]\(ポリシーとパラメーター\)** にある項目を削除したり、**[利用可能なオプション]** にある他のポリシー定義を追加したりできます。 そのためには、定義の名前の横にあるプラス記号をクリックします。

    ![ポリシーの定義](./media/security-center-policies/security-center-policies-fig11.png)

5. ポリシーに関して詳しい説明が必要な場合には、ポリシーをクリックすると別のページが開き、その詳細と[ポリシー定義(https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-policy#policy-definition-structure)の構造を備えた JSON コードが表示されます。

    ![json](./media/security-center-policies/security-center-policies-fig13.png)

6. 編集が終了したら、**[保存]** をクリックします。

## <a name="see-also"></a>関連項目
このドキュメントでは、Azure セキュリティ センターでのセキュリティ ポリシーの構成方法について説明しました。 Azure セキュリティ センターの詳細については、次を参照してください。

* [Azure Security Center 計画および運用ガイド](security-center-planning-and-operations-guide.md)。 Azure Security Center を導入するための設計上の考慮事項を計画し、理解する方法について説明しています。
* [Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md)。 Azure リソースの正常性を監視する方法について説明しています。
* [Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md)。 セキュリティの警告の管理と対応の方法について説明しています。
* [Azure Security Center を使用したパートナー ソリューションの監視](security-center-partner-solutions.md)。 パートナー ソリューションの正常性状態を監視する方法について説明しています。
* [Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md)。 このサービスの使用に関してよく寄せられる質問が記載されています。
* [Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/)。 Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。
