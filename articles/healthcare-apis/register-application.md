---
title: Azure Healthcare API の Azure Active Directory にクライアント アプリケーションを登録する
description: Azure Healthcare API にシークレットと API Azure ADを追加する方法について、クライアント アプリケーションを登録する方法
services: healthcare-apis
author: SteveWohl
ms.service: healthcare-apis
ms.topic: tutorial
ms.date: 11/17/2021
ms.author: zxue
ms.openlocfilehash: dcb88484144674122f0a108b92f8986084b80b9f
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/19/2021
ms.locfileid: "132812600"
---
# <a name="register-a-client-application-in-azure-active-directory"></a>クライアント アプリケーションをクライアント アプリケーションに登録Azure Active Directory

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

この記事では、医療 API にアクセスするためにクライアント アプリケーションを Azure Active Directory (Azure AD) に登録する方法について説明します。 詳細については、「アプリケーションを アプリケーション[に登録する」を参照Microsoft ID プラットフォーム。](../active-directory/develop/quickstart-register-app.md)

## <a name="register-a-new-application"></a>新しいアプリケーションの登録

1. [Azure portal](https://portal.azure.com) で、 **[Azure Active Directory]** を選択します。
2. **[アプリの登録]** を選択します。
[![新しいアプリ登録ウィンドウのスクリーン ショット。 ](media/register-application-one.png) ](media/register-application-one.png#lightbox)
3. **[新規登録]** を選択します。
4. [サポートされているアカウントの種類] で、[ **この組織のディレクトリ内のアカウントのみ] を選択します**。 他のオプションはそのままにします。
[![新しい登録アカウント オプションのスクリーンショット。 ](media/register-application-two.png) ](media/register-application-two.png#lightbox)
5. **[登録]** を選択します。

## <a name="application-id-client-id"></a>アプリケーション ID (クライアント ID)

新しいアプリケーションを登録した後、概要メニュー オプションからアプリケーション (クライアント) ID とディレクトリ (テナント) ID を確認できます。 後で使用する値をメモします。

[![クライアント ID の概要パネルのスクリーンショット。 ](media/register-application-three.png) ](media/register-application-three.png#lightbox)

[![クライアント ID のスクリーンショット ](media/register-application-four.png) ](media/register-application-four.png#lightbox)

## <a name="authentication-setting-confidential-vs-public"></a>認証設定: 機密とパブリック

[認証] **をクリック** して設定を確認します。 [パブリック クライアント フローを **許可する] の既定値は** "いいえ" です。

この既定値を保持する場合、アプリケーションの登録は機密クライアント アプリケーション **であり、証明書** またはシークレットが必要です。

[![機密クライアント アプリケーションのスクリーンショット。 ](media/register-application-five.png) ](media/register-application-five.png#lightbox)

詳細設定の [パブリック クライアント フローを許可する] オプションの既定値を [はい] に変更した場合、アプリケーションの登録はパブリック クライアント アプリケーションであり、証明書またはシークレットは必要ありません。 "はい" の値は、モバイル アプリまたはシークレットを格納しない JavaScript アプリでクライアント アプリケーションを使用する場合に便利です。

リダイレクト URL を必要とするツールの場合は、 [ **プラットフォームの追加] を選択して** プラットフォームを構成します。

[![プラットフォームの追加のスクリーンショット。 ](media/register-application-five-alpha.png) ](media/register-application-five-alpha.png#lightbox)

Postman の場合は、[モバイル **アプリケーションとデスクトップ アプリケーション] を選択します**。 [カスタム リダイレクト https://www.getpostman.com/oauth2/callback URI] セクション **に「」と入力** します。 [構成] **ボタンを** 選択して設定を保存します。

[![他のサービスの構成のスクリーンショット。 ](media/register-application-five-bravo.png) ](media/register-application-five-bravo.png#lightbox)

## <a name="certificates--secrets"></a>証明書&シークレット

[ **証明書とシークレット&選択し、** [新しいクライアント シークレット **] を選択します**。 [有効期限 **] フィールドで [推奨される 6** **か月] を選択** します。 この新しいシークレットは 6 か月間有効です。 次のようなさまざまな値を選択することもできます。
 
* 03 か月
* 12 か月
* 24 か月
* カスタム開始日と終了日。

>[!NOTE]
>シークレット ID ではなく、シークレット値を保存することが重要です。

[![証明書とシークレットのスクリーンショット。 ](media/register-application-six.png) ](media/register-application-six.png#lightbox)

必要に応じて、証明書 (公開キー) をアップロードし、証明書 ID (証明書に関連付けられている GUID 値) を使用できます。 テスト目的で、PowerShell コマンド ラインなどのツールを使用して自己署名証明書を作成し、証明書ストアから証明書 `New-SelfSignedCertificate` をエクスポートできます。

## <a name="api-permissions"></a>API のアクセス許可

DICOM サービスには次の手順が必要ですが、FHIR サービスの場合は省略可能です。 さらに、Healthcare API のユーザー アクセス許可またはロールの割り当ては、RBAC を介して管理されます。 詳細については、「医療 API [用に Azure RBAC を構成する」を参照してください](configure-azure-rbac.md)。

1. **[API のアクセス許可]** ブレードを選択します。

   [ ![API アクセス許可を追加する](dicom/media/dicom-add-api-permissions.png) ](dicom/media/dicom-add-api-permissions.png#lightbox)

2. **[アクセス許可の追加]** を選択します。

   Azure Healthcare APIs を使用する場合は、 **[所属する組織で使用している API]** で **DICOM 用の Azure API** を検索し、DICOM サービスへのアクセス許可を追加します。 

   [ ![API アクセス許可を検索する](dicom/media/dicom-search-apis-permissions.png) ](dicom/media/dicom-search-apis-permissions.png#lightbox)

   DICOM 用の Azure API の検索結果が返されるのは、既に DICOM サービスをワークスペースにデプロイしている場合のみです。

   別のリソース アプリケーションを参照する場合は、 **[所属する組織で使用している API]** で作成済みの DICOM API リソース アプリケーションの登録を選択します。

3. ユーザーに代わって機密クライアント アプリケーションが要求するスコープ (アクセス許可) を選択します。 **user_impersonation** を選択し、 **[アクセス許可の追加]** を選択します。

   [ ![アクセス許可のスコープの選択。](dicom/media/dicom-select-scopes.png) ](dicom/media/dicom-select-scopes.png#lightbox)

>[!NOTE]
>Postman grant_type Rest Client client_credentialsツールを使用して FHIR サービスのアクセス トークンを作成する場合は、次の方法を使用します。 詳細については[、Postman](use-postman.md)を使用したアクセスに関するページと、 の REST クライアント拡張機能を使用した医療 API へのアクセスに関[するページVisual Studio Code。](using-rest-client.md)
>>DICOM grant_typeアクセス トークンclient_credentialsをauthentication_doeする場合は、データの一部または一部を使用します。 詳細については [、「CURL での DICOM の使用」を参照してください](dicom/dicomweb-standard-apis-curl.md)。

これで、アプリケーションの登録が完了しました。

## <a name="next-steps"></a>次の手順

この記事では、クライアント アプリケーションをクライアント アプリケーションに登録する方法についてAzure AD。 さらに、Azure Healthcare API にシークレットと API のアクセス許可を追加する方法について学習しました。 Azure Healthcare API の詳細については、「」を参照してください。

>[!div class="nextstepaction"]
>[Azure Healthcare API の概要](healthcare-apis-overview.md)
