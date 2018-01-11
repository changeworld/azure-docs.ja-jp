---
title: "Azure API Management インスタンスのカスタム ドメイン名を構成する | Microsoft Docs"
description: "このトピックでは、Azure API Management インスタンスのカスタム ドメイン名を構成する方法について説明します。"
services: api-management
documentationcenter: 
author: vladvino
manager: anneta
editor: 
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 12/14/2017
ms.author: apimpm
ms.openlocfilehash: cf8a3eb502a808945e97822e10e44d38137d1161
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/15/2017
---
# <a name="configure-a-custom-domain-name"></a>カスタム ドメイン名の構成 

API Management (APIM) インスタンスを作成すると、Azure によって azure-api.net サブドメイン (例: `apim-service-name.azure-api.net`) に割り当てられます。 ただし、独自のドメイン名 (**contoso.com** など) を使用する APIM エンドポイントを公開できます。このチュートリアルでは、既存のカスタム DNS 名を Azure API Management インスタンスによって公開されるエンドポイントにマップする方法を示します。

## <a name="prerequisites"></a>前提条件

この記事で説明されている手順を実行するには、以下が必要です。

+ 有効な Azure サブスクリプション

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ APIM インスタンス。 詳細については、[Azure API Management インスタンスの作成](get-started-create-service-instance.md)に関する記事を参照してください。
+ 自己所有しているカスタム ドメイン名。 使用するカスタム ドメイン名は別途入手して DNS サーバーでホストしている必要があります。 このトピックでは、カスタム ドメイン名をホストする方法の手順は説明しません。
+ パブリックおよびプライベート キー (.PFX) 付きの有効な証明書。 サブジェクトまたはサブジェクト代替名 (SAN) はドメイン名と一致している必要があります (これにより、APIM は SSL 経由で URL を安全に公開できます)。

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>Azure ポータルを使用してカスタム ドメイン名を設定する

1. [Azure ポータル](https://portal.azure.com/)で、APIM インスタンスに移動します。
2. **[カスタム ドメインと SSL]** をクリックします。
    
    カスタム ドメイン名を割り当てることができるエンドポイントは多数あります。 現時点では、次のエンドポイントを利用できます。 
    + **プロキシ** (既定値: `<apim-service-name>.azure-api.net`) 
    + **ポータル** (既定値: `<apim-service-name>.portal.azure-api.net`)     
    + **Management** (既定値: `<apim-service-name>.management.azure-api.net`) 
    + **SCM** (既定値: `<apim-service-name>.scm.azure-api.net`)

    >[!NOTE]
    > すべてのエンドポイントまたは一部を更新できます。 一般的には、**プロキシ**(この URL は API Management を通じて公開される API を呼び出すために使用されます) と**ポータル**(開発者ポータルの URL) を更新します。 **Management** エンドポイントと **SCM** エンドポイントは、APIM ユーザーによって内部的に使用されるため、カスタム ドメイン名が割り当てられることはほぼありません。
3. 更新するエンドポイントを選択します。 
4. 右側のウィンドウで **[カスタム]** をクリックします。

    + **[カスタム ドメイン名]** に、使用する名前を指定します。 たとえば、「`api.contoso.com`」のように入力します。 <br/>ワイルドカード ドメイン名 (たとえば、*. domain.com) もサポートされています。
    + **[証明書]** に、アップロードする有効な .PFX ファイルを指定します。 
    + 証明書にパスワードがある場合は、**[パスワード]** フィールドに入力します。
1. [適用] をクリックします。

    >[!NOTE]
    >証明書を割り当てる処理は、15 分程度かかります。

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="next-steps"></a>次の手順

[サービスのアップグレードとスケーリングを行う](upgrade-and-scale.md)