---
title: Azure AD 条件付きアクセスのカスタム コントロール
description: Azure Active Directory の条件付きアクセスのカスタム コントロールのしくみについて説明します。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 02/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f1df037b66c72177a96f77231cee70782d04992
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/26/2020
ms.locfileid: "77620709"
---
# <a name="custom-controls-preview"></a>カスタム コントロール (プレビュー)

カスタム コントロールは、Azure Active Directory Premium P1 エディションの機能です。 カスタム コントロールを使用すると、Azure Active Directory の外部でさらなる要件を満たすために、ユーザーが互換性のあるサービスにリダイレクトされます。 このコントロールを満たすために、ユーザーのブラウザーは外部サービスにリダイレクトされ、すべての必要な認証または検証アクティビティを実行してから、元の Azure Active Directory にリダイレクトされます。 Azure Active Directory は応答を検証し、ユーザーが正常に認証または検証された場合、そのユーザーは条件付きアクセス フロー内にとどまります。

これらのコントロールは、特定の外部サービスまたはカスタム サービスを条件付きアクセス コントロールとして使用できるようにし、一般には条件付きアクセスの機能を拡張します。

現在、互換性のあるサービスを提供しているプロバイダーには次のものがあります。

- [Duo Security](https://duo.com/docs/azure-ca)
- [Entrust Datacard](https://www.entrustdatacard.com/products/authentication/intellitrust)
- [GSMA](https://mobileconnect.io/azure/)
- [Ping Identity](https://documentation.pingidentity.com/pingid/pingidAdminGuide/index.shtml#pid_c_AzureADIntegration.html)
- [RSA](https://community.rsa.com/docs/DOC-81278)
- [SecureAuth](https://docs.secureauth.com/pages/viewpage.action?pageId=47238992#)
- [Silverfort](https://www.silverfort.io/company/using-silverfort-mfa-with-azure-active-directory/)
- [Symantec VIP](https://help.symantec.com/home/VIP_Integrate_with_Azure_AD)
- [Thales (Gemalto)](https://resources.eu.safenetid.com/help/AzureMFA/Azure_Help/Index.htm)
- [Trusona](https://www.trusona.com/docs/azure-ad-integration-guide)

これらのサービスの詳細については、プロバイダーに直接問い合せてください。

## <a name="creating-custom-controls"></a>カスタム コントロールの作成

カスタム コントロールを作成するには、まず利用するプロバイダーに連絡する必要があります。 Microsoft 以外の各プロバイダーには、サインアップ、サブスクライブ、またはサービスの一部になることや、条件付きアクセスとの統合の意思表示について、独自のプロセスと要件があります。 その時点で、プロバイダーからは JSON 形式のデータ ブロックが提供されます。 このデータにより、プロバイダーと条件付きアクセスがユーザーのテナント用に連携して動作できるようになり、新しいコントロールが作成され、ユーザーがプロバイダーの検証を正常に実行した場合は条件付きアクセスからどのように伝えられるかが定義されます。

カスタム コントロールは、多要素認証を必要とする Identity Protection の自動化に使用できず、 Privileged Identity Manager (PIM) でのロールの昇格に使用できません。

その JSON データをコピーし、それを関連するテキスト ボックスに貼り付けます。 行おうとしている変更を明示的に理解していない限り、JSON を変更しないでください。 少しでも変更すると、プロバイダーと Microsoft の間の接続が中断され、アカウントからロック アウトされる可能性があります。

カスタム コントロールを作成するオプションは、 **[条件付きアクセス]** ページの **[管理]** セクションにあります。

![コントロール](./media/controls/82.png)

**[New custom control] \(新しいカスタム コントロール)** をクリックし、コントロールの JSON データ用のテキスト ボックスを含むブレードを開きます。  

![コントロール](./media/controls/81.png)

## <a name="deleting-custom-controls"></a>カスタム コントロールの削除

カスタム コントロールを削除するには、まずそれがどの条件付きアクセス ポリシーでも使用されていないことを確認する必要があります。 完了したら、次のことを行います。

1. [Custom controls] \(カスタム コントロール) 一覧に移動します。
1. […] をクリックします。  
1. **[削除]** を選択します。

## <a name="editing-custom-controls"></a>カスタム コントロールの編集

カスタム コントロールを編集するには、現在のコントロールを削除し、更新された情報で新しいコントロールを作成する必要があります。

## <a name="session-controls"></a>セッション コントロール

セッション コントロールでは、クラウド アプリ内のエクスペリエンスを制限できます。 セッション コントロールは、クラウド アプリによって適用され、Azure AD がアプリに提供するセッションに関する追加情報に依存します。

![コントロール](./media/controls/31.png)

### <a name="use-app-enforced-restrictions"></a>アプリによって適用される制限を使用する

このコントロールを使用して、選択されたクラウド アプリにデバイス情報を渡すように Azure AD に要求できます。 クラウド アプリは、デバイス情報によって、接続が準拠またはドメイン参加済みデバイスから開始されているかどうかを認識できます。 このコントロールでは、選択されたクラウド アプリとして SharePoint Online と Exchange Online のみがサポートされます。 選択されたクラウド アプリは、デバイス情報を使用して、デバイスの状態に応じて制限付きまたは完全なエクスペリエンスをユーザーに提供します。

詳細については、次を参照してください。

- [SharePoint Online での制限付きアクセスの有効化](https://aka.ms/spolimitedaccessdocs)
- [Exchange Online での制限付きアクセスの有効化](https://aka.ms/owalimitedaccess)

## <a name="next-steps"></a>次のステップ

- 条件付きアクセス ポリシーの構成方法を把握するには、「[Azure Active Directory の条件付きアクセスを使用して特定のアプリケーションに対して MFA を必要にする](app-based-mfa.md)」を参照してください。
- 環境に適用する条件付きアクセス ポリシーを構成する準備ができたら、「[Azure Active Directory の条件付きアクセスのベスト プラクティス](best-practices.md)」を参照してください。
