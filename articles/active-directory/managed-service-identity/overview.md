---
title: Azure Active Directory の管理対象サービス ID (MSI)
description: Azure リソースの管理対象サービス ID の概要。
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 12/19/2017
ms.author: skwan
ms.openlocfilehash: 6b62baf1fdad6e08535b13f2ca461b00156a7f14
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2018
---
#  <a name="managed-service-identity-msi-for-azure-resources"></a>Azure リソースの管理対象サービス ID (MSI)

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

クラウド アプリケーションの構築時における一般的な課題は、クラウド サービスへの認証用のコードに必要な資格情報を管理する方法です。 これらの資格情報を安全に保つことは重要なタスクです。 こうした資格情報が開発者ワークステーションに表示されず、ソース管理で検査されないことが理想的です。 Azure Key Vault は、資格情報およびその他のキーやシークレットを安全に保管する方法を提供しますが、コードは Key Vault に認証してそれらを取得する必要があります。 管理対象サービス ID (MSI) は、Azure Active Directory (Azure AD) で自動的に管理されている ID を Azure サービスに付与することで、この問題を簡単に解決します。 この ID を使用して、コードに資格情報が含まれていなくても、Key Vault を含む Azure AD の認証をサポートする任意のサービスに認証することができます。

## <a name="how-does-it-work"></a>それはどのように機能しますか?

Azure サービスで管理対象サービス ID を有効にすると、Azure は、Azure サブスクリプションで使用される Azure AD テナントのサービス インスタンスの ID を自動的に作成します。  Azure は背後で、サービス インスタンスに ID の資格情報をプロビジョニングします。  コードは、Azure AD 認証をサポートするサービスのアクセス トークンを取得するためのローカル要求を行うことができるようになります。  Azure は、サービス インスタンスによって使用される資格情報のローリングを実行します。  サービス インスタンスが削除された場合、Azure は Azure AD の資格情報および ID を自動的にクリーンアップします。

管理対象サービス ID が Azure Virtual Machines で動作する方法の例を次に示します。

![仮想マシン MSI の例](../media/msi-vm-imds-example.png)

1. Azure Resource Manager は、VM で管理対象サービス ID (MSI) を有効にするためのメッセージを受け取ります。
2. Azure Resource Manager は、Azure AD で VM の ID を表すサービス プリンシパルを作成します。 このサブスクリプションによって信頼されている Azure AD テナントで、サービス プリンシパルが作成されます。
3. Azure Resource Manager は、VM の Azure Instance Metadata Service で VM のサービス プリンシパルの詳細を構成します。 この手順には、Azure AD からアクセス トークンを取得するために使用されるクライアント ID と証明書の構成が含まれます。 *注: MSI IMDS エンドポイントで、現在の MSI VM 拡張機能エンドポイントが置き換えられます。この変更の詳細については、FAQ と既知の問題のページを参照してください。*
4. これで、VM のサービス プリンシパル ID が認識され、Azure リソースへのアクセスを付与することができます。 たとえば、コードが Azure Resource Manager を呼び出す必要がある場合、Azure AD でロールベースのアクセス制御 (RBAC) を使用して、VM のサービス プリンシパルに適切な役割を割り当てることができます。  コードが Key Vault を呼び出す必要がある場合、Key Vault 内の特定のシークレットまたはキーへのアクセスをコードに付与します。
5. VM で実行されているコードでは、http://169.254.169.254/metadata/identity/oauth2/token のように、VM 内からのみアクセス可能な、Azure Instance Metadata Service (IMDS) MSI エンドポイントからトークンを要求します。 リソース パラメーターは、トークンの送信先のサービスを指定します。 たとえば、Azure Resource Manager に認証するようにコードを設定する場合は、resource=https://management.azure.com/ を使用します。
6. Azure Instance Metadata は、VM のクライアント ID と証明書を使用して、Azure AD からアクセス トークンを要求します。 Azure AD は、JSON Web トークン (JWT) アクセス トークンを返します。
7. コードは、Azure AD 認証をサポートするサービスへの呼び出しでアクセス トークンを送信します。

管理対象サービス ID をサポートする各 Azure サービスには、アクセス トークンを取得するためのコードの独自のメソッドがあります。 トークンを取得する特定のメソッドを検索するには、各サービスのチュートリアルをご覧ください。

## <a name="try-managed-service-identity"></a>管理対象のサービス ID を試行する

さまざまな Azure リソースにアクセスするためのエンド ツー エンドのシナリオについては、管理対象サービス ID のチュートリアルを試してください。
<br><br>
| アクセス元の MSI 対応リソース | 説明内容 |
| ------- | -------- |
| Azure VM (Windows) | [Windows VM 管理対象サービス ID (MSI) を使用して Azure Data Lake Store にアクセスする](tutorial-windows-vm-access-datalake.md) |
|                    | [Windows VM 管理対象サービスID (MSI) を使用して Azure Resource Manager にアクセスする](tutorial-windows-vm-access-arm.md) |
|                    | [Windows VM 管理対象サービス ID を使用して Azure SQL にアクセスする](tutorial-windows-vm-access-sql.md) |
|                    | [Windows VM 管理対象サービス ID を使用してアクセス キーで Azure Storage にアクセスする](tutorial-windows-vm-access-storage.md) |
|                    | [Windows VM の管理対象サービス ID を使用して SAS で Azure Storage にアクセスする](tutorial-windows-vm-access-storage-sas.md) |
|                    | [Windows VM 管理対象サービス ID (MSI) および Azure Key Vault を使用して Azure AD 以外のリソースにアクセスする](tutorial-windows-vm-access-nonaad.md) |
| Azure VM (Linux)   | [Linux VM 管理対象サービス ID (MSI) を使用して Azure Data Lake Store にアクセスする](tutorial-linux-vm-access-datalake.md) |
|                    | [Linux VM 管理対象サービスID (MSI) を使用して Azure Resource Manager にアクセスする](tutorial-linux-vm-access-arm.md) |
|                    | [Linux VM 管理対象サービス ID を使用してアクセス キーで Azure Storage にアクセスする](tutorial-linux-vm-access-storage.md) |
|                    | [Linux VM 管理対象サービス ID を使用して SAS で Azure Storage にアクセスする](tutorial-linux-vm-access-storage-sas.md) |
|                    | [Linux VM 管理対象サービス ID と Azure Key Vault を使用して Azure AD 以外のリソースにアクセスする](tutorial-linux-vm-access-nonaad.md) |
| Azure App Service  | [Azure App Service または Azure Functions で管理対象サービス ID を使用する](/azure/app-service/app-service-managed-service-identity) |
| Azure Functions    | [Azure App Service または Azure Functions で管理対象サービス ID を使用する](/azure/app-service/app-service-managed-service-identity) |
| Azure Service Bus  | [Azure Service Bus で管理対象サービス ID を使用する](../../service-bus-messaging/service-bus-managed-service-identity.md) |
| Azure Event Hubs   | [Azure Event Hubs で管理対象サービス ID を使用する](../../event-hubs/event-hubs-managed-service-identity.md) |

## <a name="which-azure-services-support-managed-service-identity"></a>Azure サービスは管理対象サービス ID をサポートしますか。

管理対象サービス ID をサポートする Azure サービスでは、MSI を使用して Azure AD 認証をサポートするサービスを認証できます。  Azure 全体で MSI と Azure AD 認証を統合する処理を行っています。  更新プログラムがないかどうか、頻繁に確認してください。

### <a name="azure-services-that-support-managed-service-identity"></a>管理対象サービス ID をサポートする Azure サービス

次の Azure サービスは管理対象サービス ID をサポートします。

| サービス | 状態 | 日付 | 構成 | トークンを取得する |
| ------- | ------ | ---- | --------- | ----------- |
| Azure Virtual Machines | プレビュー | 2017 年 9 月 | [Azure Portal](qs-configure-portal-windows-vm.md)<br>[PowerShell](qs-configure-powershell-windows-vm.md)<br>[Azure CLI](qs-configure-cli-windows-vm.md)<br>[Azure リソース マネージャーのテンプレート](qs-configure-template-windows-vm.md) | [REST](how-to-use-vm-token.md#get-a-token-using-http)<br>[.NET](how-to-use-vm-token.md#get-a-token-using-c)<br>[Bash/Curl](how-to-use-vm-token.md#get-a-token-using-curl)<br>[Go](how-to-use-vm-token.md#get-a-token-using-go)<br>[PowerShell](how-to-use-vm-token.md#get-a-token-using-azure-powershell) |
| Azure App Service | プレビュー | 2017 年 9 月 | [Azure Portal](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Azure Resource Manager テンプレート](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol) |
| Azure Functions<sup>1</sup> | プレビュー | 2017 年 9 月 | [Azure Portal](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Azure Resource Manager テンプレート](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol) |
| Azure Data Factory V2 | プレビュー | 2017 年 11 月 | [Azure Portal](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity)<br>[PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-powershell)<br>[REST](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-rest-api)<br>[SDK](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-sdk) |

<sup>1</sup> Azure Functions がサポートされ、ユーザー コードで ID を使用できるようになりましたが、引き続きトリガーとバインドに接続文字列が必要な場合があります。

### <a name="azure-services-that-support-azure-ad-authentication"></a>Azure AD 認証をサポートしている Azure サービス

次のサービスは、Azure AD 認証をサポートしており、管理対象サービス ID を使用するクライアント サービスでテストされています。

| サービス | Resource ID | 状態 | 日付 | アクセス権を割り当てる |
| ------- | ----------- | ------ | ---- | ------------- |
| Azure Resource Manager | https://management.azure.com | 使用可能 | 2017 年 9 月 | [Azure Portal](howto-assign-access-portal.md) <br>[PowerShell](howto-assign-access-powershell.md) <br>[Azure CLI](howto-assign-access-CLI.md) |
| Azure Key Vault | https://vault.azure.net | 使用可能 | 2017 年 9 月 | |
| Azure Data Lake | https://datalake.azure.net | 使用可能 | 2017 年 9 月 | |
| Azure SQL | https://database.windows.net | 使用可能 | 2017 年 10 月 | |
| Azure Event Hubs | https://eventhubs.azure.net | 使用可能 | 2017 年 12 月 | |
| Azure Service Bus | https://servicebus.azure.net | 使用可能 | 2017 年 12 月 | |

## <a name="how-much-does-managed-service-identity-cost"></a>管理対象サービス ID にはどれくらいのコストがかかりますか。

管理対象サービス ID は、Azure サブスクリプションの既定値である Azure Active Directory Free に付属しています。  管理対象サービス ID のための追加コストはありません。

## <a name="support-and-feedback"></a>サポートとフィードバック

お気軽にお問い合わせください。

* タグ [azure-msi](http://stackoverflow.com/questions/tagged/azure-msi) を付けて Stack Overflow にある使用方法に関するご質問からお問い合わせください。
* 機能の要求またはフィードバックの提供は、[開発者向けの Azure AD フィードバック フォーラム](https://feedback.azure.com/forums/169401-azure-active-directory/category/164757-developer-experiences)で行ってください。






