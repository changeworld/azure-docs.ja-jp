---
title: "Azure Active Directory の管理対象サービス ID (MSI)"
description: "Azure リソースの管理対象サービス ID の概要。"
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: identity
ms.date: 12/15/2017
ms.author: daveba
ms.reviewer: skwan
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 95980c082b09ad959ab8bbaae0250b40ac08d2c8
ms.sourcegitcommit: 09a2485ce249c3ec8204615ab759e3b58c81d8cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2018
---
#  <a name="managed-service-identity-msi-for-azure-resources"></a>Azure リソースの管理対象サービス ID (MSI)

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

クラウド アプリケーションの構築時における一般的な課題は、クラウド サービスへの認証用のコードに必要な資格情報を管理する方法です。 これらの資格情報を安全に保つことは重要なタスクです。 こうした資格情報が開発者ワークステーションに表示されず、ソース管理で検査されないことが理想的です。 Azure Key Vault は、資格情報およびその他のキーやシークレットを安全に保管する方法を提供しますが、コードは Key Vault に認証してそれらを取得する必要があります。 管理対象サービス ID (MSI) は、Azure Active Directory (Azure AD) で自動的に管理されている ID を Azure サービスに付与することで、この問題を簡単に解決します。 この ID を使用して、コードに資格情報が含まれていなくても、Key Vault を含む Azure AD の認証をサポートする任意のサービスに認証することができます。

## <a name="how-does-it-work"></a>それはどのように機能しますか?

Azure サービス インスタンスで管理対象サービス ID を使用すると、Azure は、Azure サブスクリプションで使用される Azure AD テナントの ID を作成します。 さらに、Azure はサービス インスタンス上に ID の資格情報をプロビジョニングします。 結果として、コードは、Azure AD 認証をサポートするサービスのアクセス トークンを取得するためのローカル要求を行うことができるようになります。 その間、Azure は、サービス インスタンスによって使用される資格情報のローリングを実行します。

## <a name="how-do-i-enable-my-resources-to-use-a-managed-service-identity"></a>自分のリソースで管理対象サービス ID を使用できるようにするには、どうしたらいいですか。

使用可能な管理対象サービス ID として、*システム割り当て*および*ユーザー割り当て*の 2 種類があります。

- **システム割り当て** MSI は、Azure サービス インスタンス上で直接有効化されます。 有効なプロセス経由で、Azure は Azure AD テナントにサービス インスタンスの ID を作成し、サービス インスタンス上に ID の資格情報をプロビジョニングします。 システム割り当て MSI のライフ サイクルは、その MSI が有効化された Azure サービス インスタンスに直接関連付けられます。 サービス インスタンスが削除された場合、Azure は Azure AD の資格情報および ID を自動的にクリーンアップします。

- **ユーザー割り当て** MSI *(プライベート プレビュー)* は、スタンドアロン Azure リソースとして作成されます。 作成プロセス経由で、Azure は、Azure AD テネントに ID を作成します。 ID が作成された後、1 つまたは複数の Azure サービス インスタンスに割り当てできます。 ユーザー割り当て MSI は複数の Azure サービス インスタンスで使用できるので、ユーザー割り当て MSI のライフ サイクルは個別に管理されます。

Azure Virtual Machines でのシステム割り当て MSI の操作方法の例を次に示します。

![仮想マシン MSI の例](~/articles/active-directory/media/msi-vm-example.png)

1. Azure Resource Manager は、VM でシステム割り当て MSI を有効にするためのメッセージを受け取ります。
2. Azure Resource Manager は、Azure AD で VM の ID を表すサービス プリンシパルを作成します。 このサブスクリプションによって信頼されている Azure AD テナントで、サービス プリンシパルが作成されます。
3. Azure Resource Manager は、VM の MSI VM 拡張機能でサービス プリンシパルの詳細を構成します。 この手順には、拡張機能で使用されるクライアント ID と証明書を構成して、Azure AD からアクセス トークンを取得することが含まれます。
4. これで、VM のサービス プリンシパル ID が認識され、Azure リソースへのアクセスを付与することができます。 たとえば、コードが Azure Resource Manager を呼び出す必要がある場合、Azure AD でロールベースのアクセス制御 (RBAC) を使用して、VM のサービス プリンシパルに適切な役割を割り当てることができます。  コードが Key Vault を呼び出す必要がある場合、Key Vault 内の特定のシークレットまたはキーへのアクセスをコードに付与します。
5. VM で実行されているコードは、次のように MSI VM 拡張機能によってホストされているローカル エンドポイントからトークンを要求します。http://localhost:50342/oauth2/token リソース パラメーターは、トークンの送信先のサービスを指定します。 たとえば、Azure Resource Manager に認証するようにコードを設定する場合は、resource=https://management.azure.com/ を使用します。
6. MSI VM 拡張機能は、構成したクライアント ID と証明書を使用して Azure AD からアクセス トークンを要求します。  Azure AD は、JSON Web トークン (JWT) アクセス トークンを返します。
7. コードは、Azure AD 認証をサポートするサービスへの呼び出しでアクセス トークンを送信します。

同じダイアグラムを使用して、Azure Virtual Machines でのユーザー割り当て MSI の操作方法の例を次に示します。

![仮想マシン MSI の例](~/articles/active-directory/media/msi-vm-example.png)

1. Azure Resource Manager は、ユーザー割り当て MSI を作成するためのメッセージを受け取ります。
2. Azure Resource Manager は、Azure AD で MSI の ID を表すサービス プリンシパルを作成します。 このサブスクリプションによって信頼されている Azure AD テナントで、サービス プリンシパルが作成されます。
3. Azure Resource Manager は、VM の MSI VM 拡張機能でサービス プリンシパルの詳細を構成するためのメッセージを受信します。 この手順には、拡張機能で使用されるクライアント ID と証明書を構成して、Azure AD からアクセス トークンを取得することが含まれます。
4. これで、MSI のサービス プリンシパル ID が認識され、Azure リソースへのアクセスを付与することができます。 たとえば、コードが Azure Resource Manager を呼び出す必要がある場合、Azure AD でロールベースのアクセス制御 (RBAC) を使用して、 のサービス プリンシパルに適切な役割を割り当てることができます。 コードが Key Vault を呼び出す必要がある場合、Key Vault 内の特定のシークレットまたはキーへのアクセスをコードに付与します。 注: 手順 4 を完了するにあたって、手順 3. は必須ではありません。 MSI が存在すれば、VM 上に構成されているかに関係なく、リソースへのアクセスを付与することができます。
5. VM で実行されているコードは、次のように MSI VM 拡張機能によってホストされているローカル エンドポイントからトークンを要求します。http://localhost:50342/oauth2/token クライアント ID パラメーターは、使用する MSI ID の名前を指定します。 さらに、リソース パラメーターは、トークンの送信先のサービスを指定します。 たとえば、Azure Resource Manager に認証するようにコードを設定する場合は、resource=https://management.azure.com/ を使用します。
6. MSI VM 拡張機能では、要求されたクライアント ID の証明書が構成されているかどうかをチェックし、Azure AD からのアクセス トークンを要求します。 Azure AD は、JSON Web トークン (JWT) アクセス トークンを返します。
7. コードは、Azure AD 認証をサポートするサービスへの呼び出しでアクセス トークンを送信します。

管理対象サービス ID をサポートする各 Azure サービスには、アクセス トークンを取得するためのコードの独自のメソッドがあります。 トークンを取得する特定のメソッドを検索するには、各サービスのチュートリアルをご覧ください。

## <a name="try-managed-service-identity"></a>管理対象のサービス ID を試行する

さまざまな Azure リソースにアクセスするためのエンド ツー エンドのシナリオについては、管理対象サービス ID のチュートリアルを試してください。
<br><br>
| アクセス元の MSI 対応リソース | 説明内容 |
| ------- | -------- |
| Azure VM (Linux)   | [Linux VM 管理対象サービスID (MSI) を使用して Azure Resource Manager にアクセスする](msi-tutorial-linux-vm-access-arm.md) |
|                    | [Linux VM 管理対象サービス ID を使用してアクセス キーで Azure Storage にアクセスする](msi-tutorial-linux-vm-access-storage.md) |

## <a name="which-azure-services-support-managed-service-identity"></a>Azure サービスは管理対象サービス ID をサポートしますか。

管理対象サービス ID をサポートする Azure サービスでは、MSI を使用して Azure AD 認証をサポートするサービスを認証できます。  Azure 全体で MSI と Azure AD 認証を統合する処理を行っています。  更新プログラムがないかどうか、頻繁に確認してください。

### <a name="azure-services-that-support-managed-service-identity"></a>管理対象サービス ID をサポートする Azure サービス

次の Azure サービスは管理対象サービス ID をサポートします。

| サービス | 状態 | 日付 | 構成 | トークンを取得する |
| ------- | ------ | ---- | --------- | ----------- |
| Azure Virtual Machines | プレビュー | 2017 年 9 月 | [Azure CLI](msi-qs-configure-cli-windows-vm.md)<br>[Azure リソース マネージャーのテンプレート](msi-qs-configure-template-windows-vm.md) | [Bash/Curl](msi-how-to-use-vm-msi-token.md#get-a-token-using-curl)<br>[HTTP/REST](msi-how-to-use-vm-msi-token.md#get-a-token-using-http) |

### <a name="azure-services-that-support-azure-ad-authentication"></a>Azure AD 認証をサポートしている Azure サービス

次のサービスは、Azure AD 認証をサポートしており、管理対象サービス ID を使用するクライアント サービスでテストされています。

| サービス | Resource ID | 状態 | 日付 | アクセス権を割り当てる |
| ------- | ----------- | ------ | ---- | ------------- |
| Azure Resource Manager | https://management.azure.com/ | 使用可能 | 2017 年 9 月 | [Azure CLI](msi-howto-assign-access-CLI.md) |
| Azure Key Vault | https://vault.azure.net/ | 使用可能 | 2017 年 9 月 | |
| Azure Data Lake | https://datalake.azure.net/ | 使用可能 | 2017 年 9 月 | |
| Azure SQL | https://database.windows.net/ | 使用可能 | 2017 年 10 月 | |

## <a name="how-much-does-managed-service-identity-cost"></a>管理対象サービス ID にはどれくらいのコストがかかりますか。

管理対象サービス ID は、Azure サブスクリプションの既定値である Azure Active Directory Free に付属しています。  管理対象サービス ID のための追加コストはありません。

## <a name="support-and-feedback"></a>サポートとフィードバック

お気軽にお問い合わせください。

* タグ [azure-msi](http://stackoverflow.com/questions/tagged/azure-msi) を付けて Stack Overflow にある使用方法に関するご質問からお問い合わせください。
* 機能の要求またはフィードバックの提供は、[開発者向けの Azure AD フィードバック フォーラム](https://feedback.azure.com/forums/169401-azure-active-directory/category/164757-developer-experiences)で行ってください。






