---
title: "Azure Active Directory のアプリケーション オブジェクトとサービス プリンシパル オブジェクト | Microsoft Docs"
description: "Azure Active Directory におけるアプリケーション オブジェクトとサービス プリンシパル オブジェクトのリレーションシップについての説明"
documentationcenter: dev-center-name
author: bryanla
manager: mbaldwin
services: active-directory
editor: 
ms.assetid: adfc0569-dc91-48fe-92c3-b5b4833703de
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/28/2016
ms.author: bryanla;mbaldwin
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: e155891ff8dc736e2f7de1b95f07ff7b2d5d4e1b
ms.openlocfilehash: 6ee0c0b5e606b1fd9fc9eecc877d2718b7079ecb
ms.contentlocale: ja-jp
ms.lasthandoff: 05/02/2017


---
# <a name="application-and-service-principal-objects-in-azure-active-directory-azure-ad"></a>Azure Active Directory のアプリケーション オブジェクトとサービス プリンシパル オブジェクト (Azure AD)
「アプリケーション」という用語の意味が、Azure AD のコンテキストで使用する場合に誤解されることがあります。 この記事の目的は、Azure AD アプリケーションの統合について概念的な側面と具体的な側面を、[マルチテナント アプリケーション](active-directory-dev-glossary.md#multi-tenant-application)の登録および同意の例を示しながら明らかにして、この用語の意味をよりわかりやすくすることです。

## <a name="overview"></a>概要
Azure AD と統合されているアプリケーションは、ソフトウェアとしての側面以外の意味を持ちます。 「アプリケーション」は、アプリケーション ソフトウェアを指すだけではなく、実行時の認証/承認の「メッセージ交換」における Azure AD の登録やロールを指すなど、概念的な用語として頻繁に使用されます。 定義上、アプリケーションは[クライアント](active-directory-dev-glossary.md#client-application) ロール (リソースの使用)、[リソース サーバー](active-directory-dev-glossary.md#resource-server) ロール (クライアントへの API の公開)、またはその両方のロールで動作できます。 メッセージ交換プロトコルは [OAuth 2.0 承認付与フロー](active-directory-dev-glossary.md#authorization-grant)で定義されています。これにより、クライアントとリソースが、それぞれリソースのデータにアクセスし、保護できるようになります。 次にもう 1 段階掘り下げ、Azure AD アプリケーション モデルにおいて、デザイン時および実行時にアプリケーションが内部的にはどのように表されるのかを見てみましょう。 

## <a name="application-registration"></a>アプリケーションの登録
[Azure Portal][AZURE-Portal] で Azure AD アプリケーションを登録すると、Azure AD テナントに、アプリケーション オブジェクトとサービス プリンシパル オブジェクトという 2 つのオブジェクトが作成されます。

#### <a name="application-object"></a>アプリケーション オブジェクト
Azure AD アプリケーションは、その唯一のアプリケーション オブジェクトによって定義されます。アプリケーション オブジェクトは、アプリケーションの登録先である Azure AD テナント (アプリケーションの "ホーム" テナントと呼ばれます) 内にあります。 アプリケーション オブジェクトのプロパティのスキーマは、Azure AD Graph [Application エンティティ][AAD-Graph-App-Entity]によって定義されています。 

#### <a name="service-principal-object"></a>サービス プリンシパル オブジェクト
サービス プリンシパル オブジェクトは、特定のテナントでアプリケーションを使用する場合のポリシーとアクセス許可を定義しており、実行時にアプリケーションを表す、セキュリティ プリンシパルの基となる情報を提供します。 サービス プリンシパル オブジェクトのプロパティのスキーマは、Azure AD Graph [ServicePrincipal エンティティ][AAD-Graph-Sp-Entity]によって定義されています。 

#### <a name="application-and-service-principal-relationship"></a>アプリケーションとサービス プリンシパルのリレーションシップ
アプリケーション オブジェクトはアプリケーションの*グローバル*な表現 (すべてのテナント用) であり、サービス プリンシパル オブジェクトはアプリケーションの*ローカル*な表現 (特定のテナント用) と考えることができます。 アプリケーション オブジェクトは、対応するサービス プリンシパル オブジェクトの作成に使用するために、一般的な既定のプロパティが*派生*するテンプレートとして機能します。 そのため、アプリケーション オブジェクトには、ソフトウェア アプリケーションとの間に 1 対 1 のリレーションシップがあり、対応するサービス プリンシパル オブジェクトとの間に 1 対多のリレーションシップがあります。

サービス プリンシパルは、テナントによりセキュリティで保護されたリソースにサインインしたり、アクセスするための ID を設定できるように、アプリケーションを使用するテナントごとに作成する必要があります。 シングル テナント アプリケーションには、通常アプリケーション登録時に使用するために作成され、同意されたサービス プリンシパルが (そのホーム テナントに) 1 つだけがあります。 マルチテナント Web アプリケーションまたは API にも、そのテナントからユーザーが使用に同意したテナントごとに作成されたサービス プリンシパルがあります。  

> [!NOTE]
> アプリケーション オブジェクトに加えたすべての変更は、アプリケーションのホーム テナント (アプリケーションが登録されたテナント) にだけ存在するサービス プリンシパル オブジェクトにも反映されます。 マルチテナント アプリケーションの場合は、[アプリケーション アクセス パネル](https://myapps.microsoft.com)を通じてアクセス権を削除し、もう一度アクセス権を付与するまで、そのコンシューマー テナントのサービス プリンシパル オブジェクトにアプリケーション オブジェクトへの変更が反映されることはありません。
><br>  
> 既定でネイティブ アプリケーションがマルチテナントとして登録されていることにも注意してください。
> 
> 

## <a name="example"></a>例
次の図は、 **HR アプリ**という名前のサンプル マルチテナント アプリケーションを基に、アプリケーションのアプリケーション オブジェクトと、対応するサービス プリンシパル オブジェクトの間のリレーションシップを表しています。 このシナリオには、次の 3 つの Azure AD テナントがあります。 

* **Adatum** - **HR アプリ**を開発した会社が使用するテナント
* **Contoso** - **HR アプリ**のコンシューマーである Contoso という組織が使用するテナント
* **Fabrikam** - Contoso と同じく **HR アプリ**のコンシューマーである Fabrikam という組織が使用するテナント

![Relationship between an application object and a service principal object](./media/active-directory-application-objects/application-objects-relationship.png)

前の図でのステップ 1. は、アプリケーションとサービス プリンシパル オブジェクトを、アプリケーションのホーム テナント内に作成するプロセスです。

ステップ 2. では、Contoso と Fabrikam の管理者が同意を終えると、それぞれの会社の Azure AD テナント内にサービス プリンシパル オブジェクトが作成され、それに管理者が付与したアクセス許可が割り当てられます。 HR アプリは、個々のユーザー用として、ユーザーによる同意を許可するように構成/設計することができる点にも注目してください。

ステップ 3 では、HR アプリケーション (Contoso と Fabrikam) のコンシューマー テナントにそれぞれ独自のサービス プリンシパル オブジェクトが作成されます。 それぞれ実行時におけるアプリケーションのインスタンスの使用を表し、それぞれの管理者によって同意されたアクセス許可によって管理されます。

## <a name="next-steps"></a>次のステップ
Azure AD Graph API、[Azure ポータル][AZURE-Portal]のアプリケーション マニフェスト エディター、または [Azure AD PowerShell コマンドレット](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0)を通じて、OData [Application エンティティ][AAD-Graph-App-Entity]によって表されるアプリケーションのアプリケーション オブジェクトにアクセスできます。

Azure AD Graph API または [Azure AD PowerShell コマンドレット](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0)を通じて、OData [ServicePrincipal エンティティ][AAD-Graph-Sp-Entity]によって表されるアプリケーションのサービス プリンシパル オブジェクトにアクセスできます。

[Azure AD Graph Explorer](https://graphexplorer.azurewebsites.net/) は、アプリケーションとサービス プリンシパル オブジェクトの両方を照会するのに便利です。

<!--Image references-->

<!--Reference style links -->
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AZURE-Portal]: https://portal.azure.com

