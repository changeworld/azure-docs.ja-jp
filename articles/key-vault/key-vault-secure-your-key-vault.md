---
title: Azure Key Vault をセキュリティで保護する | Microsoft Docs
description: Azure Key Vault、キー、シークレットのアクセス許可を管理します。 キー コンテナーの認証と承認モデルおよびキー コンテナーをセキュリティで保護する方法について説明します。
services: key-vault
documentationcenter: ''
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: e5b4e083-4a39-4410-8e3a-2832ad6db405
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: ambapat
ms.openlocfilehash: 67f24bbccdd2dcf5cca09e09557d7ebebd0a5c2d
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2018
ms.locfileid: "52891080"
---
# <a name="secure-your-key-vault"></a>キー コンテナーのセキュリティ保護
Azure Key Vault は、暗号化キーとシークレット (証明書、接続文字列、パスワードなど) を保護するクラウド サービスです。 このデータは機密性が高く、ビジネス上重要であるため、キー コンテナーへのアクセスをセキュリティで保護する必要があり、承認されたアプリケーションとユーザーだけを許可します。 この記事では、キー コンテナーのアクセス モデルの概要について説明します。 認証と承認について、およびアクセスをセキュリティで保護する方法について説明します。

## <a name="overview"></a>概要
キー コンテナーへのアクセスは、2 つの独立したインターフェイス (管理プレーンとデータ プレーン) を使って制御します。 
**管理プレーン**では、コンテナーの作成、更新、削除など、コンテナーの管理を行います。 
**データ プレーン**では、コンテナー内のシークレットを扱います。具体的には、コンテナー内でシークレットの作成、更新、削除、読み取りを行います。 どちらのプレーンでも、呼び出し元 (ユーザーまたはアプリケーション) がキー コンテナーにアクセスするには、適切な認証と承認が必要です。 認証では呼び出し元の ID を確認し、承認では呼び出し元が実行できる操作を決定します。

認証については、管理プレーンとデータ プレーンの両方で Azure Active Directory を使用します。 承認については、管理プレーンではロールベースのアクセス制御 (RBAC) を使用し、データ プレーンでは Key Vault アクセス ポリシーを使用します。

取り上げるトピックの概要を次に示します。

[Azure Active Directory を使用した認証](#authentication-using-azure-active-directory) - このセクションでは、管理プレーンとデータ プレーンを介してキー コンテナーにアクセスするために、呼び出し元が Azure Active Directory で認証する方法について説明します。 

認証については、両方のプレーンで Azure Active Directory (Azure AD) が使用されます。 承認については、管理プレーンではロールベースのアクセス制御 (RBAC) を使用し、データ プレーンでは Key Vault アクセス ポリシーを使用します。

## <a name="authenticate-by-using-azure-active-directory"></a>Azure Active Directory を利用して認証する
Azure サブスクリプション内でキー コンテナーを作成すると、作成したキー コンテナーは、そのサブスクリプションの Azure AD テナントに自動的に関連付けられます。 すべての呼び出し元は、このテナントに登録されている必要があり、キー コンテナーにアクセスするには認証を行う必要があります。 この要件は、管理プレーンとデータ プレーンの両方のアクセスに適用されます。 どちらの場合も、アプリケーションは次の 2 つの方法でキー コンテナーにアクセスできます。

* **User+App アクセス**:サインインしたユーザーの代わりにキー コンテナーにアクセスするアプリケーションで使用されます。 この種類のアクセスの例は、Azure PowerShell や Azure portal です。 ユーザーにアクセスを許可するには 2 つの方法があります。 
  - アプリケーションからキー コンテナーにアクセスする。
  - 特定のアプリケーションを使用している場合にのみ、キー コンテナーにアクセスする (複合 ID と呼ばれます)。

* **App-Only アクセス**:デーモン サービスまたはバックグラウンド ジョブとして実行するアプリケーションで使用されます。 アプリケーションの ID にキー コンテナーへのアクセス権が付与されます。

どちらの種類のアプリケーションも、[サポートされている認証方法](../active-directory/develop/authentication-scenarios.md)のいずれかを使用して Azure AD で認証し、トークンを取得します。 使用する認証方法は、アプリケーションの種類によって異なります。 その後、アプリケーションはこのトークンを使用し、REST API 要求をキー コンテナーに送信します。 管理プレーンの要求は、Azure Resource Manager エンドポイントを介してルーティングされます。 データ プレーンにアクセスする際、アプリケーションはキー コンテナー エンドポイントと直接通信します。 詳細については、[全体的な認証フロー](../active-directory/develop/v1-protocols-oauth-code.md)に関するページをご覧ください。 

アプリケーションでトークンを要求するためのリソース名は、アプリケーションでアクセスするプレーンによって異なります。 リソース名は、Azure 環境に基づき、管理プレーン エンドポイントかデータ プレーン エンドポイントになります。 詳細については、この記事で後に出てくる表をご覧ください。

1 つのメカニズムで両方のプレーンを認証することには次のような利点があります。

* 組織内のすべてのキー コンテナーへのアクセスを一元的に管理できます。
* 退職したユーザーは、組織内のすべてのキー コンテナーに即座にアクセスできなくなります。
* Azure AD のオプションを使用して認証をカスタマイズできます (例: セキュリティを強化するために多要素認証を有効にする)。

## <a name="the-management-plane-and-the-data-plane"></a>管理プレーンとデータ プレーン
管理プレーンを使用し、キー コンテナー自体を管理します。 これには、属性の管理や、データ プレーン アクセス ポリシーの設定などの操作が含まれます。 データ プレーンを使用し、キー コンテナーに格納されているキー、シークレット、証明書を追加、削除、変更、使用します。

管理プレーンとデータ プレーンのインターフェイスには、次の表の一覧にある各種エンドポイントを介してアクセスします。 表の 2 列目では、さまざまな Azure 環境におけるこれらのエンドポイントの DNS 名について説明しています。 3 列目では、各アクセス プレーンから実行できる操作について説明しています。 各アクセス プレーンには、独自のアクセス制御メカニズムもあります。 管理プレーンのアクセス制御は、Azure Resource Manager のロールベースのアクセス制御 (RBAC) を使用して設定されます。 データ プレーンのアクセス制御は、キー コンテナーのアクセス ポリシーを使用して設定されます。

| アクセス プレーン | アクセス エンドポイント | [操作] | アクセス制御メカニズム |
| --- | --- | --- | --- |
| 管理プレーン |**グローバル:**<br> management.azure.com:443<br><br> **Azure China 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Azure US Government:**<br> management.usgovcloudapi.net:443<br><br> **Azure Germany:**<br>  management.microsoftazure.de:443 |キー コンテナーの作成/読み取り/更新/削除 <br> キー コンテナーのアクセス ポリシーの設定<br>キー コンテナーのタグの設定 |Azure Resource Manager RBAC |
| データ プレーン |**グローバル:**<br> &lt;vault-name&gt;.vault.azure.net:443<br><br> **Azure China 21Vianet:**<br> &lt;vault-name&gt;.vault.azure.cn:443<br><br> **Azure US Government:**<br> &lt;vault-name&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Germany:**<br> &lt;vault-name&gt;.vault.microsoftazure.de:443 |キーの場合:Decrypt、Encrypt、UnwrapKey、WrapKey、Verify、Sign、Get、List、Update、Create、Import、Delete、Backup、Restore<br><br> シークレットの場合:取得、一覧表示、設定、削除 |Key Vault アクセス ポリシー |

管理プレーンとデータ プレーンのアクセス制御は独立して動作します。 たとえば、キー コンテナー内のキーを使用するためのアクセスをアプリケーションに許可する場合は、データ プレーンのアクセスを許可するだけで済みます。 アクセスは、キー コンテナーのアクセス ポリシー経由で付与します。 逆に、コンテナーのプロパティとタグを読み取る必要はあっても、データ (キー、シークレット、証明書) にアクセスする必要のないユーザーは、管理プレーンのアクセスのみを必要とします。 アクセスは、RBAC を使用してユーザーに読み取りアクセスを割り当てることで付与します。

## <a name="management-plane-access-control"></a>管理プレーンのアクセス制御
管理プレーンは、キー コンテナー自体に影響を与える次のような操作で構成されています。

- キー コンテナーの作成または削除。
- サブスクリプション内のコンテナーの一覧の取得。
- キー コンテナーのプロパティの取得 (SKU やタグなど)。
- キーおよびシークレットに対するユーザーとアプリケーションのアクセスを制御するキー コンテナー アクセス ポリシーの設定。

管理プレーンのアクセス制御では、RBAC を使用します。  

### <a name="role-based-access-control-rbac"></a>ロール ベースのアクセス制御 (RBAC)
各 Azure サブスクリプションには Azure AD インスタンスがあります。 Azure Resource Manager デプロイ モデルを使用する Azure サブスクリプション内にあるリソースを管理するために、このディレクトリのユーザー、グループ、アプリケーションに対してアクセス権を付与します。 この種のアクセス制御は RBAC と呼ばれます。 このアクセスを管理するには、[Azure Portal](https://portal.azure.com/)、[Azure CLI ツール](../cli-install-nodejs.md)、[PowerShell](/powershell/azureps-cmdlets-docs)、または [Azure Resource Manager REST API](https://msdn.microsoft.com/library/azure/dn906885.aspx) を使用できます。

リソース グループにキー コンテナーを作成し、Azure AD を使用して管理プレーンへのアクセスを制御します。 たとえば、ユーザーまたはグループに対して、リソース グループのキー コンテナーを管理する権限を付与できます。

特定のスコープでユーザー、グループ、およびアプリケーションにアクセス権を付与するには、適切な RBAC ロールを割り当てます。 たとえば、キー コンテナーを管理するためのアクセス権をユーザーに付与するには、定義済みのキー コンテナー共同作成者ロールを特定のスコープでこのユーザーに割り当てます。 この場合のスコープは、サブスクリプション、リソース グループ、または特定のキー コンテナーになります。 サブスクリプション レベルで割り当てられたロールは、そのサブスクリプション内のすべてのリソース グループとリソースに適用されます。 リソース グループ レベルで割り当てられたロールは、そのリソース グループ内のすべてのリソースに適用されます。 特定のリソースに対して割り当てられたロールは、そのリソースに適用されます。 複数の定義済みロールがあります ([RBAC の組み込みのロール](../role-based-access-control/built-in-roles.md)に関する記事をご覧ください)。 定義済みのロールがニーズに合わない場合は、独自のロールを定義できます。

> [!IMPORTANT]
> キー コンテナーの管理プレーンに対する共同作成者権限がユーザーに与えられている場合、キー コンテナー アクセス ポリシーを設定することで、データ プレーンへのアクセス権を自分自身に付与できます。 そのため、キー コンテナーの共同作成者アクセス権を与えるユーザーは厳密に管理する必要があります。 承認されたユーザーだけがコンテナー、キー、シークレット、証明書にアクセスできるようにします。
> 
> 

## <a name="data-plane-access-control"></a>データ プレーンのアクセス制御
キー コンテナーのデータ プレーンの操作は、キー、シークレット、証明書などの格納されているオブジェクトに適用されます。 キーの操作には、キーの作成、インポート、更新、一覧取得、バックアップ、復元などがあります。 暗号化操作には、署名、検証、暗号化、暗号化の解除、ラップ、ラップ解除、タグ、キーに対するその他の属性の設定などがあります。 同様に、シークレットに対する操作には、取得、設定、一覧、削除などがあります。

データ プレーンのアクセス権は、キー コンテナーのアクセス ポリシーを設定して付与します。 キー コンテナーのアクセス ポリシーを設定するには、ユーザー、グループ、またはアプリケーションにそのキー コンテナーの管理プレーンに対する共同作成者権限を与える必要があります。 ユーザー、グループ、またはアプリケーションには、キー コンテナー内のキーやシークレットに対して特定の操作を行うためのアクセス権を付与できます。 キー コンテナーでは、1 つのキー コンテナーに対して最大 1,024 個のアクセス ポリシー エントリをサポートしています。 データ プレーンのアクセス権を複数のユーザーに付与するには、Azure AD セキュリティ グループを作成し、そのグループにユーザーを追加します。

### <a name="key-vault-access-policies"></a>キー コンテナー アクセス ポリシー
キー コンテナー アクセス ポリシーでは、キー、シークレット、証明書へのアクセス許可を個別に付与します。 たとえば、ユーザーにキーのみのアクセス権を付与し、シークレットのアクセス許可は付与しないようにすることができます。 キー、シークレット、または証明書へのアクセス許可は、コンテナー レベルで付与されます。 キー コンテナーのアクセス ポリシーでは、特定のキー、シークレット、証明書など、細かいオブジェクト レベルのアクセス許可はサポートされていません。 キー コンテナーのアクセス ポリシーを設定するには、[Azure portal](https://portal.azure.com/)、[Azure CLI ツール](../cli-install-nodejs.md)、[PowerShell](/powershell/azureps-cmdlets-docs)、または [Key Vault 管理 REST API](https://msdn.microsoft.com/library/azure/mt620024.aspx) を使用できます。

> [!IMPORTANT]
> Key Vault アクセス ポリシーはコンテナー レベルで適用されます。 たとえば、キーを作成および削除するためのアクセス許可を付与されたユーザーは、そのキー コンテナー内のすべてのキーに対してそれらの操作を実行できます。

アクセス ポリシーを使用すること以外に、[Azure Key Vault の仮想ネットワーク サービス エンドポイント](key-vault-overview-vnet-service-endpoints.md)を使用する方法でもデータ プレーン アクセスを制限できます。 追加のセキュリティ層に、[ファイアウォールと仮想ネットワークの規則](key-vault-network-security.md)を設定します。

## <a name="example"></a>例
SSL 用の証明書を使用し、データの格納に Azure Storage を使用して、署名操作に RSA 2048 ビット キーを使用するアプリケーションを開発しているものとします。 このアプリケーションは、Azure 仮想マシン (または仮想マシン スケール セット) で実行されていることにします。 キー コンテナーを使用して、すべてのアプリケーション シークレットや、Azure AD で認証するアプリケーションで使用されるブートストラップ証明書を格納することができます。

格納されるキーとシークレットの種類を以下にまとめます。

* **SSL 証明書**:SSL に使用されます。
* **ストレージ キー**:ストレージ アカウントにアクセスするために使用されます。
* **RSA 2048 ビット キー**:署名操作に使用されます。
* **ブートストラップ証明書**:Azure AD による認証に使用されます。 アクセスが許可されると、ストレージ キーをフェッチし、署名に RSA キーを使用できます。

次に、このアプリケーションの管理、デプロイ、監査を行うユーザーを紹介します。 この例では 3 つのロールを使用します。

* **セキュリティ チーム**:通常、CSO (最高セキュリティ責任者) のオフィスまたはそれと同等の IT スタッフです。 このチームは、シークレットの適切な保管を担当します。 たとえば、SSL 証明書、署名に使用される RSA キー、接続文字列、ストレージ アカウント キーなどです。
* **開発者/運用者**:アプリケーションを開発し、Azure にデプロイするユーザーです。 通常は、セキュリティ チームの一部ではなく、SSL 証明書や RSA キーなどの機密データにアクセスできてはなりません。 これらのユーザーがデプロイするアプリケーションのみが、オブジェクトにアクセスできる必要があります。
* **監査者**:通常、開発者や一般的な IT スタッフとは別のユーザーです。 証明書、キー、シークレットなどの使用と管理について検査し、セキュリティ基準に準拠していることを確認します。 

このアプリケーションのスコープ外であるロールがもう 1 つありますが、ここで言及する関連性があります。 そのロールは、サブスクリプション (またはリソース グループ) 管理者です。 サブスクリプション管理者は、セキュリティ チームの初期のアクセス許可を設定します。 サブスクリプション管理者は、このアプリケーションで必要なリソースを含むリソース グループを使用して、セキュリティ チームにアクセスを付与します。

それでは、このアプリケーションのコンテキストで各ロールが実行するアクションを見てみましょう。

* **セキュリティ チーム**
  * キー コンテナーを作成する。
  * Key Vault のログ記録をオンにする。
  * キーやシークレットを追加する。
  * ディザスター リカバリーのためにキーのバックアップを作成する。
  * 特定の操作を実行するためのアクセス許可をユーザーおよびアプリケーションに付与するように Key Vault アクセス ポリシーを設定する。
  * キーやシークレットを定期的に切り替える。
* **開発者/運用者**
  * セキュリティ チームからブートストラップ証明書と SSL 証明書 (拇印) への参照、ストレージ キー (シークレットの URI) と署名キー (キーの URI) を取得する。
  * キーとシークレットにプログラムからアクセスするアプリケーションを開発し、デプロイする。
* **監査者**
  * 使用状況ログで、キーやシークレットが正しく使用されているかどうかや、データ セキュリティ基準に準拠しているかどうかを確認する。

次に、割り当てられたタスクを実行するために各ロールおよびアプリケーションが必要なアクセス許可について見てみましょう。 

| ユーザー ロール | 管理プレーンのアクセス許可 | データ プレーンのアクセス許可 |
| --- | --- | --- |
| セキュリティ チーム |Key Vault Contributor |キー: バックアップ、作成、削除、取得、インポート、一覧表示、復元 <br> シークレット: すべて |
| 開発者/運用者 |キー コンテナーのデプロイ アクセス許可 (デプロイした仮想マシンがキー コンテナーからシークレットを取得できるようにするため)。 |なし |
| 監査者 |なし |キー: 一覧表示<br>シークレット: 一覧表示 |
| アプリケーション |なし |キー: 署名<br>シークレット: 取得 |

> [!NOTE]
> 監査者には、ログに出力されないキーとシークレットの属性を調べるために、キーとシークレットを一覧表示するためのアクセス許可が必要です。 ログに出力されない属性には、タグ、ライセンス認証を行った日付、有効期限などがあります。
> 
> 

3 つすべてのロールには、キー コンテナーのアクセス許可に加え、その他のリソースへのアクセス許可も必要です。 たとえば、VM (あるいは、Azure App Service の Web Apps 機能) をデプロイできるようにするには、開発者と運用者にもそのような種類のリソースに対する共同作成者アクセスが必要になります。 監査者には、Key Vault のログが格納されているストレージ アカウントに対する読み取りアクセス権が必要です。

この記事の焦点はキー コンテナーへのアクセスをセキュリティ保護することなので、このトピックに関連する概念についてのみ示します。 証明書のデプロイ、キーやシークレットへのプログラムでのアクセスに関する詳細については、別のところで説明します。 次に例を示します。

- キー コンテナーに格納されている証明書の VM へのデプロイについては、「[Deploy Certificates to VMs from customer-managed Key Vault](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/)」 (顧客管理のキー コンテナーから VM に証明書をデプロイする) (ブログ投稿) をご覧ください。
- 「[Azure Key Vault client samples](https://www.microsoft.com/download/details.aspx?id=45343)」 (Azure Key Vault クライアント サンプル) のダウンロードでは、ブートストラップ証明書を使用して、キー コンテナーにアクセスするための認証を Azure AD に対して行う方法が示されています。

アクセス許可の多くは Azure portal を使用して付与することができます。 きめ細かいアクセス許可を付与するには、希望する結果を実現するために Azure PowerShell または CLI を使用する必要がある場合がありますります。 

次の PowerShell スニペットでは、以下を想定しています。

* Azure AD 管理者が、3 つのロールを表すセキュリティ グループ (Contoso Security Team、Contoso App Devops、Contoso App Auditors) を作成しています。 また、管理者はユーザーをそれぞれの所属グループに追加しています。
* **ContosoAppRG** は、すべてのリソースが配置されているリソース グループです。 **contosologstorage** には、ログが格納されます。 
* キー コンテナー **ContosoKeyVault** と Key Vault のログに使用されるストレージ アカウント **contosologstorage** は、同じ Azure の場所に配置する必要があります。

まず、サブスクリプション管理者が `key vault Contributor` ロールと `User Access Administrator` ロールをセキュリティ チームに割り当てます。 これらのロールにより、セキュリティ チームは、その他のリソースへのアクセスと、ContosoAppRG リソース グループ内のキー コンテナーを管理できます。

```
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "key vault Contributor" -ResourceGroupName ContosoAppRG
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "User Access Administrator" -ResourceGroupName ContosoAppRG
```

次のスクリプトでは、セキュリティ チームがキー コンテナーを作成する方法、およびログとアクセス許可を設定する方法を示します。 Key Vault のアクセス ポリシーのアクセス許可について詳しくは、[Azure Key Vault のキー、シークレット、証明書](about-keys-secrets-and-certificates.md)に関するページをご覧ください。

```
# Create key vault and enable logging
$sa = Get-AzureRmStorageAccount -ResourceGroup ContosoAppRG -Name contosologstorage
$kv = New-AzureRmKeyVault -Name ContosoKeyVault -ResourceGroup ContosoAppRG -SKU premium -Location 'westus' -EnabledForDeployment
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent

# Data plane permissions for Security team
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -PermissionsToKeys backup,create,delete,get,import,list,restore -PermissionsToSecrets get,list,set,delete,backup,restore,recover,purge

# Management plane permissions for Dev/ops
# Create a new role from an existing role
$devopsrole = Get-AzureRmRoleDefinition -Name "Virtual Machine Contributor"
$devopsrole.Id = $null
$devopsrole.Name = "Contoso App Devops"
$devopsrole.Description = "Can deploy VMs that need secrets from key vault"
$devopsrole.AssignableScopes = @("/subscriptions/<SUBSCRIPTION-GUID>")

# Add permission for dev/ops so they can deploy VMs that have secrets deployed from key vaults
$devopsrole.Actions.Add("Microsoft.KeyVault/vaults/deploy/action")
New-AzureRmRoleDefinition -Role $devopsrole

# Assign this newly defined role to Dev ops security group
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso App Devops')[0].Id -RoleDefinitionName "Contoso App Devops" -ResourceGroupName ContosoAppRG

# Data plane permissions for Auditors
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso App Auditors')[0].Id -PermissionsToKeys list -PermissionsToSecrets list
```

`ContosoAppRG` リソース グループが作成されるサブスクリプションには、定義済みのカスタム ロールのみを割り当てることができます。 同じカスタム ロールを他のサブスクリプションの他のプロジェクトで使用すると、そのスコープにサブスクリプションが追加される可能性があります。

開発者/運用者に対する "deploy/action" アクセス許可のカスタム ロールの割り当ての対象はリソース グループです。 これにより、`ContosoAppRG` リソース グループに作成された VM のみがシークレット (SSL 証明書とブートストラップ証明書) にアクセスできます。 開発者/運用者チーム メンバーによって別のリソース グループに作成された VM は、シークレットの URI が含まれていたとしても、これらのシークレットにアクセスできません。

この例では、簡単なシナリオを示します。 実際のシナリオはもっと複雑になることがあります。ニーズに基づいて、キー コンテナーに対するアクセス許可を調整できます。 この例では、開発者/運用者がアプリケーションで参照するために必要なキーとシークレットの参照 (URI と拇印) をセキュリティ チームが提供します。 開発者/運用者には、すべてのデータ プレーン アクセスは必要ありません。 この例ではキー コンテナーのセキュリティ保護に焦点を当てています。 [VM](https://azure.microsoft.com/services/virtual-machines/security/) や[ストレージ アカウント](../storage/common/storage-security-guide.md)などの Azure リソースをセキュリティで保護する場合も、同じようなことを考慮します。

> [!NOTE]
> この例では、運用環境でキー コンテナーのアクセスがロックダウンされる仕組みを示しています。 開発者は、キー コンテナー、VM、ストレージ アカウントを管理するための完全なアクセス許可を持つ自分のサブスクリプションまたはリソース グループで、アプリケーションを開発する必要があります。

[Key Vault のファイアウォールと仮想ネットワークを構成](key-vault-network-security.md)して、キー コンテナーへのアクセスをさらにセキュリティ保護することを強くお勧めします。

## <a name="resources"></a>リソース
* [Azure Active Directory のロールベースのアクセス制御](../role-based-access-control/role-assignments-portal.md)
  
* [RBAC:組み込みロール](../role-based-access-control/built-in-roles.md)
  
* [リソース マネージャー デプロイと従来のデプロイを理解する](../azure-resource-manager/resource-manager-deployment-model.md)
  
* [Azure PowerShell を使用したロールベースのアクセス制御の管理](../role-based-access-control/role-assignments-powershell.md)
  
* [REST API を使用したロールベースの Access Control の管理](../role-based-access-control/role-assignments-rest.md)
  
* [Role-Based Access Control for Microsoft Azure from Ignite (Ignite での Microsoft Azure 向けロールベースのアクセス制御の説明)](https://channel9.msdn.com/events/Ignite/2015/BRK2707)
  
  この 2015 Microsoft Ignite カンファレンス ビデオでは、Azure でのアクセス管理とレポート機能について説明されています。 また、Azure AD を使用して Azure サブスクリプションへのアクセスをセキュリティで保護するためのベスト プラクティスについても説明しています。
* [OAuth 2.0 と Azure AD を使用した Web アプリケーションへのアクセスの承認](../active-directory/develop/v1-protocols-oauth-code.md)
  
* [Key Vault 管理 REST API](https://msdn.microsoft.com/library/azure/mt620024.aspx)
  
  このドキュメントは、Key Vault アクセス ポリシーの設定など、プログラムでキー コンテナーを管理するための REST API の参照です。
* [Key Vault REST API](https://msdn.microsoft.com/library/azure/dn903609.aspx)
  
* [Key access control (キーのアクセス制御)](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_KeyAccessControl)
  
* [Secret access control (シークレットのアクセス制御)](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_SecretAccessControl)
  
* PowerShell を使用した Key Vault アクセス ポリシーの[設定](https://docs.microsoft.com/powershell/module/azurerm.keyvault/Set-AzureRmKeyVaultAccessPolicy)と[削除](https://docs.microsoft.com/powershell/module/azurerm.keyvault/Remove-AzureRmKeyVaultAccessPolicy)
  
## <a name="next-steps"></a>次の手順
[Key Vault のファイアウォールと仮想ネットワークを構成する](key-vault-network-security.md)

管理者用の概要チュートリアルについては、「 [Azure Key Vault の概要](key-vault-get-started.md)」をご覧ください。

Key Vault の使用状況に関するログ記録について詳しくは、「[Azure Key Vault のログ記録](key-vault-logging.md)」をご覧ください。

Azure Key Vault でキーとシークレットを使用する方法の詳細については、[キーとシークレット](https://msdn.microsoft.com/library/azure/dn903623.aspx)に関する記事をご覧ください。

Key Vault に関する質問がある場合は、[フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)にアクセスしてください。

