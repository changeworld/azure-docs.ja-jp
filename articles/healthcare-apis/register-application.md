---
title: Azure の医療 api の Azure Active Directory にクライアントアプリケーションを登録する
description: Azure AD でクライアントアプリケーションを登録する方法と、Azure の医療 api にシークレットと API のアクセス許可を追加する方法
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.topic: tutorial
ms.date: 08/25/2021
ms.author: ginle
ms.openlocfilehash: b1ac54f71c9c49af5bb8656bdbe05d639b8bca25
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129278050"
---
# <a name="register-a-client-application-in-azure-active-directory"></a>Azure Active Directory にクライアントアプリケーションを登録する

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

この記事では、Azure Active Directory (Azure AD) にクライアントアプリケーションを登録して、医療 api にアクセスする方法について説明します。 詳細については[、「アプリケーションを Microsoft ID プラットフォームに登録](../active-directory/develop/quickstart-register-app.md)する」を参照してください。

## <a name="register-a-new-application"></a>新しいアプリケーションの登録

1. [Azure portal](https://portal.azure.com) で、 **[Azure Active Directory]** を選択します。
2. **[アプリの登録]** を選択します。
[![新しいアプリの登録ウィンドウのスクリーンショット。 ](media/register-application-one.png) ](media/register-application-one.png#lightbox)
3. **[新規登録]** を選択します。
4. サポートされているアカウントの種類については、[ **この組織ディレクトリ内のアカウントのみ**] を選択します。 他のオプションはそのままにしておきます。
[![新しい登録アカウントオプションのスクリーンショット。 ](media/register-application-two.png) ](media/register-application-two.png#lightbox)
5. **[登録]** を選択します。

## <a name="application-id-client-id"></a>アプリケーション ID (クライアント ID)

新しいアプリケーションを登録すると、[概要] メニューオプションからアプリケーション (クライアント) ID とディレクトリ (テナント) ID を確認できます。 後で使用するための値をメモしておきます。

[![クライアント ID の概要パネルのスクリーンショット。 ](media/register-application-three.png) ](media/register-application-three.png#lightbox)

[![クライアント ID ](media/register-application-four.png) のスクリーンショット ](media/register-application-four.png#lightbox)

## <a name="authentication-setting-confidential-vs-public"></a>認証設定: 社外秘とパブリック

[ **認証** ] をクリックして設定を確認します。 [ **パブリッククライアントフローを許可する** ] の既定値は "No" です。

この既定値をそのまま使用する場合、アプリケーションの登録は **機密クライアントアプリケーション** であり、証明書またはシークレットが必要です。

[![Confidential クライアントアプリケーションのスクリーンショット。 ](media/register-application-five.png) ](media/register-application-five.png#lightbox)

既定値を "Yes" に変更した場合、アプリケーションの登録は **パブリッククライアントアプリケーション** であり、証明書またはシークレットは必要ありません。 "Yes" の値は、モバイルアプリまたは JavaScript アプリでクライアントアプリケーションを使用して、シークレットを格納したくない場合に便利です。

リダイレクト URL を必要とするツールの場合は、[ **プラットフォームの追加** ] を選択してプラットフォームを構成します。

[![プラットフォームの追加のスクリーンショット。 ](media/register-application-five-alpha.png) ](media/register-application-five-alpha.png#lightbox)

Postman の場合は、[ **モバイルアプリケーションとデスクトップアプリケーション**] を選択します。 [ https://www.getpostman.com/oauth2/callback **カスタムリダイレクト uri** ] セクションに「」と入力します。 設定を保存するには、[ **構成** ] ボタンを選択します。

[![他のサービスの構成のスクリーンショット。 ](media/register-application-five-bravo.png) ](media/register-application-five-bravo.png#lightbox)

## <a name="certificates--secrets"></a>証明書 & シークレット

[ **証明書 & シークレット** ] を選択し、[ **新しいクライアントシークレット**] を選択します。 [**有効期限**] フィールドで [**推奨6か月**] を選択します。 この新しいシークレットは6か月間有効になります。 次のような異なる値を選択することもできます。
 
* 03か月
* 12 か月
* 24 か月
* カスタム開始日と終了日。

>[!NOTE]
>シークレット ID ではなく、シークレット値を保存することが重要です。

[![証明書とシークレットのスクリーンショット。 ](media/register-application-six.png) ](media/register-application-six.png#lightbox)

必要に応じて、証明書 (公開キー) をアップロードし、証明書 ID (証明書に関連付けられている GUID 値) を使用することもできます。 テストの目的で、PowerShell コマンドラインなどのツールを使用して自己署名証明書を作成し、証明書ストアから証明書をエクスポートすることができ `New-SelfSignedCertificate` ます。

## <a name="api-permissions"></a>API のアクセス許可

医療 Api のアクセス許可は、RBAC によって管理されます。 詳細については、「 [FHIR サービス用の AZURE RBAC の構成](./fhir/configure-azure-rbac-for-fhir.md)」を参照してください。

>[!NOTE]
>Postman や Rest クライアントなどのツールを使用して FHIR サービスのアクセストークンを otain するときに、client_credentials の grant_type を使用します。 詳細については、「 [Postman を使用してアクセス](use-postman.md)し、 [REST クライアント拡張機能を使用して Visual Studio Code で医療 api に](using-rest-client.md)アクセスする」を参照してください。

これで、アプリケーションの登録が完了しました。

## <a name="next-steps"></a>次の手順

この記事では、Azure AD にクライアントアプリケーションを登録する方法について説明しました。 また、Azure の医療 Api にシークレットと API のアクセス許可を追加する方法についても説明しました。 Azure の医療 Api の詳細については、「」を参照してください。

>[!div class="nextstepaction"]
>[Azure の医療 Api の概要](healthcare-apis-overview.md)
