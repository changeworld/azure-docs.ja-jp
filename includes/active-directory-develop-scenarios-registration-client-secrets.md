---
title: インクルード ファイル
description: 機密クライアント シナリオのランディング ページのインクルード ファイル (デーモン、Web アプリ、Web API)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/18/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: cd37880be6d518105e880b93a0bd748f7c729d88
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68286275"
---
## <a name="registration-of-secrets-or-certificates"></a>シークレットまたは証明書の登録

機密クライアント アプリケーションの場合と同様に、シークレットまたは証明書を登録する必要があります。 [Azure portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) の対話型エクスペリエンスまたはコマンドライン ツール (PowerShell など) を使用して、アプリケーション シークレットを登録できます

### <a name="registering-client-secrets-using-the-application-registration-portal"></a>アプリケーション登録ポータルを使用したクライアント シークレットの登録

クライアント資格情報の管理は、アプリケーションの **[証明書とシークレット]** ページで行います。

![image](../articles/active-directory/develop/media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets-expanded.png)

- アプリケーション シークレット (クライアント シークレットとも呼ばれる) は、機密クライアント アプリケーションの登録時に Azure AD によって生成されます。 この生成は、 **[新しいクライアント シークレット]** を選択すると実行されます。 **[保存]** を選択する前に、アプリで使用するために、この時点でシークレットの文字列をクリップボードにコピーする必要があります。 この文字列は、その後は表示されなくなります。
- **[証明書のアップロード]** ボタンを使用すると、証明書がアプリケーション登録にアップロードされます。 Azure AD は、アプリケーションで直接登録されている証明書のみをサポートし、証明書チェーンには従いません。

詳細については、「[クイック スタート:Web API にアクセスするようにクライアント アプリケーションを構成する」で、資格情報のアプリケーションへの追加](../articles/active-directory/develop/quickstart-configure-app-access-web-apis.md#add-credentials-to-your-web-application)に関するセクションを参照してください



### <a name="registering-client-secrets-using-powershell"></a>PowerShell を使用したクライアント シークレットの登録

または、コマンド ライン ツールを使用して、Azure AD でアプリケーションを登録できます。 [active-directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) のサンプルでは、Azure AD アプリケーションでアプリケーション シークレットまたは証明書を登録する方法を示しています。

- アプリケーション シークレットの登録方法の詳細については、[AppCreationScripts/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190) に関するページを参照してください
- アプリケーションでの証明書の登録方法の詳細については、[AppCreationScripts-withCert/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178) に関するページを参照してください
