---
title: Azure Cloud Services (延長サポート) 移行後の変更
description: Cloud Services (延長サポート) に移行した後の移行後の変更の概要
ms.topic: how-to
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
author: tanmaygore
ms.author: tagore
ms.reviewer: mimckitt
ms.date: 2/08/2021
ms.custom: ''
ms.openlocfilehash: df9a41b6832c9ca93aa7078b0d06082f1441a5e5
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2021
ms.locfileid: "113439270"
---
# <a name="post-migration-changes"></a>移行後の変更
クラウド サービス (クラシック) のデプロイは、クラウド サービス (延長サポート) のデプロイに変換されます。 詳細については、[Cloud Services (延長サポート) のドキュメント](deploy-prerequisite.md)をご覧ください。  

## <a name="changes-to-deployment-files"></a>デプロイ ファイルの変更 

デプロイ ファイルを Azure Resource Manager と Cloud Services (延長サポート) の要件に準拠させるため、お客様の .csdef および .cscfg ファイルが少し変更されます。 移行後に、新しいデプロイ ファイルが取得されるか、既存のファイルが更新されます。 これは、更新または削除操作で必要になります。  

- Virtual Network では、.cscfg ファイルの NetworkConfiguration セクションのリソース名だけでなく、Azure Resource Manager の完全なリソース ID が使用されます。 たとえば、「 `/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.Network/virtualNetworks/vnet-name` 」のように入力します。 クラウド サービスと同じリソース グループに属している仮想ネットワークの場合は、仮想ネットワーク名のみを使用するように .cscfg ファイルを更新して戻すことができます。  

- Small、Large、ExtraLarge などの従来のサイズは、新しいサイズの名前 (Standard_A*) に置き換えられます。 サイズの名前を、.csdef ファイルで使用されている新しい名前に変更する必要があります。 詳細については、[Cloud Services (延長サポート) のデプロイの前提条件](deploy-prerequisite.md#required-service-definition-file-csdef-updates)に関する記事を参照してください

- デプロイ ファイルの最新のコピーを取得するには、Get API を使用します。 
    - [ポータル](../azure-resource-manager/templates/export-template-portal.md)、[PowerShell](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)、[CLI](../azure-resource-manager/management/manage-resource-groups-cli.md#export-resource-groups-to-templates)、[Rest API](/rest/api/resources/resourcegroups/exporttemplate) を使用して、テンプレートを取得します 
    - [PowerShell](/powershell/module/az.cloudservice/?preserve-view=true&view=azps-5.4.0#cloudservice) または [Rest API](/rest/api/compute/cloudservices/rest-get-package) を使用して、.csdef ファイルを取得します。 
    - [PowerShell](/powershell/module/az.cloudservice/?preserve-view=true&view=azps-5.4.0#cloudservice) または [Rest API](/rest/api/compute/cloudservices/rest-get-package) を使用して、.cscfg ファイルを取得します。 
    
 

## <a name="changes-to-customers-automation-cicd-pipeline-custom-scripts-custom-dashboards-custom-tooling-etc"></a>お客様のオートメーション、CI/CD パイプライン、カスタム スクリプト、カスタム ダッシュボード、カスタム ツールなどに対する変更  

お客様は、新しい API とコマンドを使用してデプロイを管理するには、ツールとオートメーションを更新する必要があります。 お客様は、この変更の一環として、Azure Resource Manager や Cloud Services (延長サポート) の新機能を簡単に導入できます。 

- 移行後のリソースとリソース グループの名前の変更
    - 移行の一部として、クラウド サービスやパブリック IP アドレスなど、いくつかのリソースの名前が変更されます。 クラウド サービスを更新する前に、これらの変更をデプロイ ファイルに反映することが必要な場合があります。 [リソースの名前の変更の詳細については、こちらを参照してください](in-place-migration-technical-details.md#translation-of-resources-and-naming-convention-post-migration)。  

- クラウド サービスの管理とスケーリングに必要なルールとポリシーを作成し直します 
    - [自動スケーリング ルール](configure-scaling.md)は移行されません。 移行後に、自動スケーリング ルールを作成し直します。  
    - [アラート](enable-alerts.md)は移行されません。 移行後に、アラートを作成し直します。
    - キー コンテナーは、アクセス ポリシーなしで作成されます。 キー コンテナーで、証明書を表示または管理するための[適切なポリシーを作成](../key-vault/general/assign-access-policy-portal.md)します。 証明書は、[設定] の [シークレット] タブに表示されます。


## <a name="changes-to-certificate-management-post-migration"></a>移行後の証明書管理の変更 

証明書管理の標準的な手法として、すべての有効な .pfx 証明書ファイルが、Key Vault 内の証明書ストアに追加され、ポータル、PowerShell、Rest API など、あらゆるクライアントを使用して更新が完全に行われます。

現在、Azure portal によって、必要な証明書がすべて Key Vault の証明書ストアにアップロードされているかどうかが検証され、証明書がないときは警告が出されます。 ただし、証明書をシークレットとして使用する場合、その証明書はサムプリントに対して検証できず、シークレットの追加を伴う、ポータルを介した更新操作はすべて失敗します。 お客様には、シークレットを伴う更新は、PowerShell または RestAPI を使用して続行することをお勧めします。


## <a name="changes-for-update-via-visual-studio"></a>Visual Studio を使用した更新の変更
Visual Studio を使用して更新を直接発行した場合は、移行後、最初にご自身のデプロイから最新の CSCFG ファイルをダウンロードする必要があります。 このファイルを参照として使用し、Visual Studio プロジェクト内で現在お使いの CSCFG ファイルにネットワーク構成の詳細を追加します。 次に、ソリューションをビルドして、発行します。 この更新に対して、Key Vault と リソース グループを選択する必要があります。


## <a name="next-steps"></a>次の手順
- [クラシックから Azure Resource Manager への IaaS リソースのプラットフォームでサポートされる移行の概要](../virtual-machines/migration-classic-resource-manager-overview.md)
- [Azure portal](in-place-migration-portal.md) を使用して Cloud Services (延長サポート) に移行する
- [PowerShell](in-place-migration-powershell.md) を使用して Cloud Services (延長サポート) に移行する
