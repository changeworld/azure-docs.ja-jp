---
title: DICOM サービス用に Azure Active Directory アプリケーションを登録する - Azure Healthcare APIs
description: この記事では、DICOM サービス用に Azure Active Directory アプリケーションを登録する方法について説明します。
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 07/10/2021
ms.author: aersoy
ms.openlocfilehash: 1db5ba11d4a3bd8380561ae493c69b809edf5135
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779491"
---
# <a name="register-azure-active-directory-applications-for-the-dicom-service"></a>DICOM サービス用に Azure Active Directory アプリケーションを登録する

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

DICOM サービスまたは FHIR Server for Azure (OSS) を設定する際は、いくつかのオプションから選択できます。 オープンソースの場合は、独自のリソース アプリケーションの登録を作成する必要があります。 Azure API for FHIR の場合、このリソース アプリケーションは自動的に作成されます。

## <a name="application-registrations"></a>アプリケーションの登録

アプリケーションが Azure AD とやり取りするには、それを登録する必要があります。 DICOM サービスのコンテキストでは、3 種類の説明すべきクライアント アプリケーションの登録があります。

## <a name="client-applications"></a>クライアント アプリケーション

"クライアント アプリケーション" は、トークンを要求するクライアントの登録です。 OAuth 2.0 では、多くの場合、3 種類以上のアプリケーションが区別されます。

### <a name="confidential-clients"></a>機密クライアント

機密クライアントは、Azure AD では Web アプリとも呼ばれます。 機密クライアントとは、[承認コード フロー](../../active-directory/azuread-dev/v1-protocols-oauth-code.md)を使用して、有効な資格情報を提示しているサインイン ユーザーの代わりにトークンを取得するアプリケーションです。 これらが機密クライアントと呼ばれるのは、シークレットを保持でき、認証コードをトークンと交換する際にこのシークレットを Azure AD に提示するためです。 機密クライアントでは、クライアント シークレットを使用して自身を認証できるため、パブリック クライアントよりも信頼性が高く、より有効期間が長いトークンを保持することができます。このクライアントには、更新トークンを付与することもできます。 詳細については、「[Azure Active Directory に機密クライアント アプリケーションを登録する](dicom-register-confidential-client-application.md)」をご覧ください。 クライアントが認可コードを受け取るときに応答 URL を登録することが重要です。

### <a name="public-clients"></a>パブリック クライアント

これらは、シークレットを保持できないクライアントです。 通常、これはモバイル デバイス アプリケーションまたは単一ページの JavaScript アプリケーションであり、クライアント内のシークレットがユーザーによって発見される場合があります。 パブリック クライアントも承認コード フローを使用しますが、トークンの取得時にシークレットを提示することはできません。また、保持できるトークンの有効期間は短くなり、更新トークンを保持できません。 [パブリック クライアント アプリケーションを Azure Active Directory に登録する](dicom-register-public-application.md)方法の詳細をご覧ください。

### <a name="service-clients"></a>サービス クライアント

このクライアントは、[クライアント資格情報フロー](.././../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)を使用して、(ユーザーの代わりではなく) 自身の代わりにトークンを取得します。 これらは通常、非インタラクティブな方法で DICOM サービスにアクセスするアプリケーションを表します。 たとえば、インジェスト プロセスです。 サービス クライアントを使用する場合、/authorize エンドポイントへの呼び出しでトークンを取得するプロセスを開始する必要はありません。 サービス クライアントは、直接 /token エンドポイントにアクセスし、クライアント ID とクライアント シークレットを提示してトークンを取得できます。 詳細については、[Azure Active Directory へのクライアント アプリケーションの登録](dicom-register-service-client-application.md)に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ

この概要記事では、リソースおよびクライアント アプリケーションを DICOM サービスで動作させるためのアプリケーション登録プロセスについて説明します。 アプリケーションを登録したら、DICOM サービスをデプロイできます。

>[!div class="nextstepaction"]
>[Azure Active Directory に Confidential クライアント アプリケーションを登録する](dicom-register-confidential-client-application.md)

>[!div class="nextstepaction"]
>[Azure Active Directory にパブリック クライアント アプリケーションを登録する](dicom-register-public-application.md)

>[!div class="nextstepaction"]
>[Azure Active Directory にサービス クライアント アプリケーションを登録する](dicom-register-service-client-application.md)