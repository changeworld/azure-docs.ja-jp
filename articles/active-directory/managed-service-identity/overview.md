---
title: Azure リソースのマネージド サービス ID
description: Azure リソースのマネージド サービス ID 機能の概要。
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.component: msi
ms.devlang: ''
ms.topic: overview
ms.custom: mvc
ms.date: 03/28/2018
ms.author: daveba
ms.openlocfilehash: 1edd6f846d539b1f263877a5e0af107148513c6e
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2018
ms.locfileid: "39398387"
---
# <a name="what-is-managed-service-identity-for-azure-resources"></a>Azure リソースのマネージド サービス ID とは

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

クラウド アプリケーションの構築時における一般的な課題は、クラウド サービスへの認証用のコードで資格情報をどのように管理するかです。 資格情報を安全に保つことは重要な課題です。 資格情報は開発者のワークステーションに表示されないこと、またソース管理にチェックインされないことが理想です。 資格情報やシークレットなど、各種キーを安全に保管する手段としては Azure Key Vault がありますが、それらを取得するためには、コードから Key Vault に対して認証を行わなければなりません。 

Azure Active Directory (Azure AD) のマネージド サービス ID 機能は、この問題を解決します。 Azure AD で自動的に管理される ID を Azure サービスに提供する機能となります。 この ID を使用すれば、コードに資格情報を追加しなくても、Azure AD の認証をサポートするさまざまなサービス (Key Vault を含む) に対して認証を行うことができます。

マネージド サービス ID は、Azure サブスクリプションを対象とした Azure AD の無料の機能です。 追加コストはかかりません。

## この機能の働きを教えてください<a name="how-does-it-work"></a>

マネージド ID には、次の 2 種類があります。

- **システム割り当て ID** は、Azure サービス インスタンス上で直接有効にされます。 この ID を有効にすると、そのインスタンスのサブスクリプションによって信頼されている Azure AD テナントに対し、対応するインスタンスの ID が Azure によって作成されます。 ID が作成されると、その資格情報がインスタンスにプロビジョニングされます。 システム割り当て ID のライフ サイクルは、その ID が有効にされた Azure サービス インスタンスに直接関連付けられます。 インスタンスが削除された場合、Azure は Azure AD の資格情報および ID を自動的にクリーンアップします。
- **ユーザー割り当て ID** は、スタンドアロン Azure リソースとして作成されます。 作成プロセスで、使用されているサブスクリプションによって信頼されている Azure AD テナントに、Azure が ID を作成します。 作成された ID は、1 つまたは複数の Azure サービス インスタンスに割り当てることができます。 ユーザー割り当て ID のライフ サイクルは、その ID が割り当てられている Azure サービス インスタンスのライフ サイクルとは個別に管理されます。

Azure AD Authentication をサポートするサービスのアクセス トークンは、コードからマネージド サービス ID を使用して要求できます。 Azure は、サービス インスタンスによって使用される資格情報のローリングを実行します。

次の図は、マネージド サービス ID と Azure 仮想マシン (VM) が連携するようすを示したものです。

![マネージド サービス ID と Azure VM](media/overview/msi-vm-vmextension-imds-example.png)

### <a name="how-a-system-assigned-identity-works-with-an-azure-vm"></a>システム割り当て ID と Azure VM の連携

1. Azure Resource Manager は、VM でシステム割り当て ID を有効にするための要求を受け取ります。
2. Azure Resource Manager は、VM の ID を表すサービス プリンシパルを Azure AD に作成します。 このサブスクリプションによって信頼されている Azure AD テナントに、サービス プリンシパルが作成されます。
3. Azure Resource Manager が、VM 上で ID を構成します。
    1. Azure Instance Metadata Service の ID エンドポイントを、サービス プリンシパルのクライアント ID と証明書で更新します。
    1. VM 拡張機能をプロビジョニングし、サービス プリンシパルのクライアント ID と証明書を追加します。 (この手順は非推奨となる予定です。)
4. VM に ID が設定された後、Azure リソースにアクセスする権利を VM に与えるには、そのサービス プリンシパル情報を使用します。 Azure Resource Manager を呼び出すには、Azure AD のロールベースのアクセス制御 (RBAC) を使用して、VM のサービス プリンシパルに適切なロールを割り当てます。 Key Vault を呼び出すには、Key Vault 内の特定のシークレットまたは特定のキーにアクセスする権利をコードに与えます。
5. VM 上で実行されているコードは、VM 内からのみアクセス可能な次の 2 つのエンドポイントにトークンを要求できます。

    - Azure Instance Metadata Service の ID エンドポイント (推奨): `http://169.254.169.254/metadata/identity/oauth2/token`
        - リソース パラメーターは、トークンの送信先のサービスを指定します。 Azure Resource Manager に対して認証を行うには、`resource=https://management.azure.com/` を使用します。
        - API バージョン パラメーターは、IMDS バージョンを指定します。api-version=2018-02-01 以降を使用してください。
    - VM 拡張機能エンドポイント (非推奨となる予定): `http://localhost:50342/oauth2/token` 
        - リソース パラメーターは、トークンの送信先のサービスを指定します。 Azure Resource Manager に対して認証を行うには、`resource=https://management.azure.com/` を使用します。

6. 手順 3. で構成したクライアント ID と証明書を使用して、手順 5. で指定したアクセス トークンを要求する呼び出しが Azure AD に対して行われます。 Azure AD は、JSON Web トークン (JWT) アクセス トークンを返します。
7. コードは、Azure AD 認証をサポートするサービスへの呼び出しでアクセス トークンを送信します。

### <a name="how-a-user-assigned-identity-works-with-an-azure-vm"></a>ユーザー割り当て ID と Azure VM の連携

1. Azure Resource Manager が、ユーザー割り当て ID を作成するための要求を受け取ります。
2. Azure Resource Manager が、ユーザー割り当て ID を表すサービス プリンシパルを Azure AD に作成します。 このサブスクリプションによって信頼されている Azure AD テナントに、サービス プリンシパルが作成されます。
3. Azure Resource Manager が、VM 上でユーザー割り当て ID を構成するための要求を受け取ります。
    1. Azure Instance Metadata Service の ID エンドポイントを、ユーザー割り当て ID のサービス プリンシパルのクライアント ID と証明書で更新します。
    1. VM 拡張機能をプロビジョニングし、ユーザー割り当て ID のサービス プリンシパルのクライアント ID と証明書を追加します。 (この手順は非推奨となる予定です。)
4. ユーザー割り当て ID が作成された後、Azure リソースにアクセスする権利をその ID に与えるには、そのサービス プリンシパル情報を使用します。 Azure Resource Manager を呼び出すには、Azure AD の RBAC を使用して、ユーザー割り当て ID のサービス プリンシパルに適切なロールを割り当てます。 Key Vault を呼び出すには、Key Vault 内の特定のシークレットまたは特定のキーにアクセスする権利をコードに与えます。

   > [!Note]
   > この手順は、手順 3. の前に行ってもかまいません。

5. VM 上で実行されているコードは、VM 内からのみアクセス可能な次の 2 つのエンドポイントにトークンを要求できます。

    - Azure Instance Metadata Service の ID エンドポイント (推奨): `http://169.254.169.254/metadata/identity/oauth2/token`
        - リソース パラメーターは、トークンの送信先のサービスを指定します。 Azure Resource Manager に対して認証を行うには、`resource=https://management.azure.com/` を使用します。
        - クライアント ID パラメーターは、トークンの要求先の ID を指定します。 この値は、1 つの VM 上に複数のユーザー割り当て ID がある場合に、あいまいさを解消するために必要です。
        - Azure Instance Metadata Service のバージョンは、API バージョン パラメーターで指定します。 `api-version=2018-02-01` 以降を使用してください。

    - VM 拡張機能エンドポイント (非推奨となる予定): `http://localhost:50342/oauth2/token`
        - リソース パラメーターは、トークンの送信先のサービスを指定します。 Azure Resource Manager に対して認証を行うには、`resource=https://management.azure.com/` を使用します。
        - クライアント ID パラメーターは、トークンの要求先の ID を指定します。 この値は、1 つの VM 上に複数のユーザー割り当て ID がある場合に、あいまいさを解消するために必要です。
6. 手順 3. で構成したクライアント ID と証明書を使用して、手順 5. で指定したアクセス トークンを要求する呼び出しが Azure AD に対して行われます。 Azure AD は、JSON Web トークン (JWT) アクセス トークンを返します。
7. コードは、Azure AD 認証をサポートするサービスへの呼び出しでアクセス トークンを送信します。

## <a name="how-can-i-use-managed-service-identities"></a>マネージド サービス ID の使い方を教えてください

マネージド サービス ID を使って各種 Azure リソースにアクセスする方法については、以下のチュートリアルを参考にしてください。

Windows VM でマネージド サービス ID を使用する方法については、以下のページをご覧ください。

* [Azure Data Lake Store にアクセスする](tutorial-windows-vm-access-datalake.md)
* [Azure Resource Manager にアクセスする](tutorial-windows-vm-access-arm.md)
* [Azure SQL にアクセスする](tutorial-windows-vm-access-sql.md)
* [アクセス キーを使用して Azure Storage にアクセスする](tutorial-windows-vm-access-storage.md)
* [Shared Access Signature を使用して Azure Storage にアクセスする](tutorial-windows-vm-access-storage-sas.md)
* [Azure Key Vault で Azure AD 以外のリソースにアクセスする](tutorial-windows-vm-access-nonaad.md)

Linux VM でマネージド サービス ID を使用する方法については、以下のページをご覧ください。

* [Azure Data Lake Store にアクセスする](tutorial-linux-vm-access-datalake.md)
* [Azure Resource Manager にアクセスする](tutorial-linux-vm-access-arm.md)
* [アクセス キーを使用して Azure Storage にアクセスする](tutorial-linux-vm-access-storage.md)
* [Shared Access Signature を使用して Azure Storage にアクセスする](tutorial-linux-vm-access-storage-sas.md)
* [Azure Key Vault で Azure AD 以外のリソースにアクセスする](tutorial-linux-vm-access-nonaad.md)

その他の Azure サービスでマネージド サービス ID を使用する方法については、以下のページをご覧ください。

* [Mobile Apps](/azure/app-service/app-service-managed-service-identity)
* [Azure Functions](/azure/app-service/app-service-managed-service-identity)
* [Azure Service Bus](../../service-bus-messaging/service-bus-managed-service-identity.md)
* [Azure Event Hubs](../../event-hubs/event-hubs-managed-service-identity.md)
* [Azure API Management](../../api-management/api-management-howto-use-managed-service-identity.md)

## この機能をサポートする Azure サービスは?<a name="which-azure-services-support-managed-service-identity"></a>

マネージド サービス ID を使った認証は、Azure AD Authentication をサポートするサービスに対して使用できます。 マネージド サービス ID 機能をサポートする Azure サービスの一覧については、「[マネージド サービス ID をサポートするサービス](services-support-msi.md)」をご覧ください。

## <a name="next-steps"></a>次の手順

以下のクイック スタートでマネージド サービス ID 機能を実際に使ってみましょう。

* [Windows VM のマネージド サービス ID を使用してリソース マネージャーにアクセスする](tutorial-windows-vm-access-arm.md)
* [Linux VM マネージド サービス ID を使用してリソース マネージャーにアクセスする](tutorial-linux-vm-access-arm.md)
