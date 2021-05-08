---
title: インクルード ファイル
description: 機密クライアント シナリオのランディング ページのインクルード ファイル (デーモン、Web アプリ、Web API)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/14/2020
ms.author: jmprieur
ms.openlocfilehash: 42102f38959911388cefcc141d949e59f24a2c31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95996010"
---
## <a name="add-a-client-secret-or-certificate"></a>クライアント シークレットまたは証明書を追加する

すべての機密クライアント アプリケーションと同様に、そのアプリケーションの "*資格情報*" として機能するシークレットまたは証明書を追加する必要があります。これにより、ユーザーによる操作なしに認証可能となります。

クライアント アプリの登録に資格情報を追加するには、[Azure portal](#add-client-credentials-by-using-the-azure-portal) を使用するか [PowerShell](#add-client-credentials-by-using-powershell) などのコマンドライン ツールを使用します。

### <a name="add-client-credentials-by-using-the-azure-portal"></a>Azure portal を使用してクライアント資格情報を追加する

機密クライアント アプリケーションのアプリ登録に資格情報を追加するには、追加する資格情報の種類に応じて、「[クイックスタート: Microsoft ID プラットフォームにアプリケーションを登録する](../articles/active-directory/develop/quickstart-register-app.md)」の手順に従います。

* [クライアント シークレットの追加](../articles/active-directory/develop/quickstart-register-app.md#add-a-client-secret)
* [証明書を追加する](../articles/active-directory/develop/quickstart-register-app.md#add-a-certificate)

### <a name="add-client-credentials-by-using-powershell"></a>PowerShell を使用してクライアント資格情報を追加する

または、PowerShell を使用して Microsoft ID プラットフォームにアプリケーションを登録すると、資格情報を追加することもできます。

GitHub の [active-directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2)コード サンプルは、アプリケーションの登録時にシークレットまたは証明書を登録する方法を示しています。

- PowerShell を使用して **クライアント シークレット** を追加する方法の詳細については、[AppCreationScripts/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190) を参照してください。
- PowerShell を使用して **資格情報** を追加する方法の詳細については、[AppCreationScripts-withCert/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178) を参照してください。
