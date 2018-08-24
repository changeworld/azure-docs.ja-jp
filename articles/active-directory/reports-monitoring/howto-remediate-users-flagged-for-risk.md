---
title: Azure Active Directory ポータルのリスクのフラグ付きユーザー セキュリティ レポート | Microsoft Docs
description: Azure Active Directory ポータルのリスクのフラグ付きユーザー セキュリティ レポートについて説明します。
services: active-directory
author: priyamohanram
manager: mtillman
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 05/23/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 385106065322cae9b8e29f66bfd6920d4ebb52f8
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2018
ms.locfileid: "42145169"
---
# <a name="remediate-users-flagged-for-risk-in-the-azure-active-directory-portal"></a>Azure Active Directory ポータルでリスクのフラグ付きユーザーを修復する

Azure Active Directory (Azure AD) のセキュリティ レポートでは、環境内でユーザー アカウントが侵害されている確率を調べることができます。 [リスクのフラグ付きユーザー](../identity-protection/overview.md#users-flagged-for-risk)は、セキュリティが侵害された可能性のあるユーザー アカウントを示すものです。

Microsoft は、お客様の環境のセキュリティの保持に努めています。 この取り組みの一環として、Microsoft は、異常なアクティビティや既知の攻撃パターンと一致するアクティビティを継続的に監視しています。 


お客様のユーザーのアカウントに対する未承認のアクセスを示す可能性がある異常なアクティビティが検出された場合は、対処できるように通知します。 通知があっても、Microsoft 自体のシステムがなんらかの方法で侵害されたとは限りません。
 

## <a name="access-the-users-flagged-for-risk-report"></a>リスクのフラグ付きユーザー レポートへのアクセス

リスクのフラグ付きユーザーは、Azure Active Directory (AD) の関連[レポート](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UsersAtRisk)を通じて確認できます。 Azure AD のサブスクライバーでない場合は、[https://aka.ms/AccessAAD](https://aka.ms/AccessAAD) で、1 回限りのサブスクリプション プロセスを無料で行うことができます。 このレポートで、次のようなさまざまなアクションを実行できます。

- 一時パスワードを生成する
- ユーザーに次回のサインインで自分のパスワードを安全にリセットすることを要求する
- 修復アクションを行わずにユーザーのリスクを無視する。

詳しくは、「[Azure Active Directory ポータルのリスクのフラグ付きユーザー セキュリティ レポート](concept-user-at-risk.md)」をご覧ください。

### <a name="azure-ad-subscription-for-office-365-customers"></a>Office 365 のお客様の Azure AD サブスクリプション

完了したら、Office 365 の資格情報を使用して Azure 管理センターにアクセスすることができます。 Azure AD へのアクセスをアクティブにすると、Azure AD ポータルにリダイレクトされます。 Basic サブスクリプション レベルでは、レポートで提供される詳細情報の量が限定されます。 追加のデータと分析を使用できるのは、Azure Premium サブスクライバーのみです。


**Office 365 管理センターでリスクのフラグ付きユーザーのレポートにアクセスするには:**

1.  左側のナビゲーション メニューで、**[管理センター]** をクリックします。 
2.  **[Azure AD]** をクリックします。
3.  **Azure Active Directory 管理センター**にログインします。
4.  ページ上部に **[新しいポータルの確認]** というバナーが表示される場合は、そのリンクをクリックします。
4.  左側のナビゲーション メニューで、**[Azure Active Directory]** をクリックします。 
5.  ナビゲーション ウィンドウで、**[セキュリティ]** の **[リスクのフラグ付きユーザー]** をクリックします。

ここに表示される情報を確認します。 一覧に表示されているアカウントのパスワードは、リセットする必要があります。 

## <a name="remediation-actions"></a>修復アクション

影響を受けたアカウントの問題を解決し、環境を保護するために、以下の操作を実行してください。

1.  多要素認証とセルフサービスのパスワード リセットに関する情報が正しいことを[検証](http://aka.ms/MFAValid)します。 
2.  すべてのユーザーの多要素認証を[有効](http://aka.ms/MFAuth)にします。 
3.  この[修復スクリプト](http://aka.ms/remediate)を使用すると、影響を受けたすべてのアカウントに対して、以下の手順を自動的に実行することができます。 

    a. パスワードをリセットしてアカウントを保護し、アクティブなセッションを強制終了します。

    b. メールボックスの代理人を削除します。

    c. 外部ドメインへのメール転送ルールを無効にします。

    d. メールボックスのグローバル メール転送プロパティを削除します。

    e. ユーザーのアカウントで MFA を有効にします。

    f. アカウントのパスワードの複雑さを高く設定します。

    g. メールボックスの監査を有効にします。

    h. 管理者が確認するための監査ログを生成します。

4. Office 365 のテナントおよびその他の IT インフラストラクチャを調査します。たとえば、すべてのテナント設定、ユーザー アカウント、ユーザーごとの構成設定で、変更の可能性を確認します。 持続的な侵害手段の兆候や、侵入者が VPN 資格情報を取得したり他の組織リソースにアクセスしたりするための最初の足掛かりを利用した兆候を調べます。 

5.  調査の一環として、法執行機関などの官公庁に通知する必要があるかどうかを検討してください。

他にも、以下の作業が必要です。

- 異常なアクティビティへの対処について、この[ガイダンス](http://aka.ms/fixaccount)を読み、実装します。 
- テナントでのアクティビティの分析に役立てるために、[監査パイプラインを有効](http://aka.ms/improvesecurity)にします。 そのようにすると、監査ストアにすべてのアクティビティ ログが読み込まれるようになります。 この時点で、[セキュリティ/コンプライアンス センターの検索と調査](http://aka.ms/sccsearch)を活用することもできます。 
- この[スクリプト](http://aka.ms/mailboxaudit1)を使用して、すべてのアカウントのメールボックスの監査を有効にします。 
- すべてのメールボックスの代理人のアクセス許可とメール転送ルールを確認します。 この [PowerShell スクリプト](http://aka.ms/delegateforwardrules)を使用して、この作業を実行することができます。 



## <a name="next-steps"></a>次の手順

- Azure Active Directory Identity Protection の詳細については、「[Azure Active Directory Identity Protection](../active-directory-identityprotection.md)」を参照してください。

