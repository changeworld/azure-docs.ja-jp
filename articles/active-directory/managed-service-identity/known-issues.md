---
title: Azure Active Directory の管理対象サービス ID (MSI) の FAQ と既知の問題
description: Azure Active Directory の管理対象サービス ID の既知の問題です。
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.component: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 12/12/2017
ms.author: daveba
ms.openlocfilehash: c48d03b6e8a3d850d02d2c36c35915f8214b00e8
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2018
ms.locfileid: "39035814"
---
# <a name="faqs-and-known-issues-with-managed-service-identity-msi-for-azure-active-directory"></a>Azure Active Directory の管理対象サービス ID (MSI) の FAQ と既知の問題

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

## <a name="frequently-asked-questions-faqs"></a>よく寄せられる質問 (FAQ)

### <a name="does-msi-work-with-azure-cloud-services"></a>MSI は Azure Cloud Services で動作しますか。

いいえ、Azure Cloud Services で MSI をサポートする予定はありません。

### <a name="does-msi-work-with-the-active-directory-authentication-library-adal-or-the-microsoft-authentication-library-msal"></a>MSI は Active Directory Authentication Library (ADAL) または Microsoft Authentication Library (MSAL) で動作しますか。

いいえ、MSI はまだ ADAL または MSAL と統合されていません。 MSI REST エンドポイントを使用した MSI トークンの取得の詳細については、「[トークン取得に Azure VM の管理対象サービス ID (MSI) を使用する方法](how-to-use-vm-token.md)」をご覧ください。

### <a name="what-is-the-security-boundary-of-a-managed-service-identity"></a>管理対象サービス ID のセキュリティ境界は何ですか。

ID のセキュリティ境界は、ID のアタッチ先リソースです。 たとえば、仮想マシン MSI のセキュリティ境界は仮想マシンです。 その VM で実行しているすべてのコードは、MSI エンドポイントを呼び出して、トークンを要求できます。 MSI をサポートする他のリソースでも同じように作動します。

### <a name="should-i-use-the-msi-vm-imds-endpoint-or-the-msi-vm-extension-endpoint"></a>MSI VM IMDS エンドポイントまたは MSI VM 拡張機能エンドポイントを使用する必要がありますか?

VM で MSI を使用する場合は、MSI IMDS エンドポイントの使用をお勧めします。 Azure Instance Metadata Service は、Azure Resource Manager を使用して作成されたすべての IaaS VM にアクセスできる REST エンドポイントです。 IMDS 経由で MSI を使用すると、次のような利点があります。

1. Azure IaaS でサポートされているすべてのオペレーティング システムが、IMDS 経由で MSI を使用することができます。 
2. MSI を有効にするために VM に拡張機能をインストールする必要がなくなります。 
3. MSI によって使用される証明書が、VM に存在しなくなります。 
4. IMDS エンドポイントは、既知のルーティング不可能な IP アドレスです。VM 内でのみ使用できます。 

MSI VM 拡張機能は、現在でも使用可能です。ただし、今後は IMDS エンドポイントの使用が既定になります。 MSI VM 拡張機能は、間もなく廃止計画が開始されます。 

Azure Instance Metada Service の詳細については、[IMDS のドキュメント](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)を参照してください。

### <a name="what-are-the-supported-linux-distributions"></a>どのような Linux ディストリビューションがサポートされていますか。

Azure IaaS でサポートされているすべての Linux ディストリビューションを、IMDS エンドポイント経由の MSI で使用することができます。 

メモ: MSI VM 拡張機能では、次の Linux ディストリビューションだけがサポートされています。
- CoreOS Stable
- CentOS 7.1
- Red Hat 7.2
- Ubuntu 15.04
- Ubuntu 16.04

他の Linux ディストリビューションは現在サポートされておらず、サポートされていないディストリビューションでは拡張機能が失敗する可能性があります。

CentOS 6.9 では拡張機能が機能します。 ただし、6.9 はシステム サポートされていないため、クラッシュした場合や停止した場合は拡張機能が自動的に再起動しません。 6.9 は VM の再起動時に再起動します。 拡張機能を手動で再起動するには、「[MSI 拡張機能はどのようにすれば再起動できますか](#how-do-you-restart-the-msi-extension)」を参照してください。

### <a name="how-do-you-restart-the-msi-extension"></a>MSI 拡張機能はどのようにすれば再起動できますか。
Windows と特定のバージョンの Linux で拡張機能が停止した場合は、次のコマンドレットを使用して手動で再起動できます。

```powershell
Set-AzureRmVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

各値の説明: 
- Windows の拡張機能の名前と種類: ManagedIdentityExtensionForWindows
- Linux の拡張機能の名前と種類: ManagedIdentityExtensionForLinux

## <a name="known-issues"></a>既知の問題

### <a name="automation-script-fails-when-attempting-schema-export-for-msi-extension"></a>MSI 拡張機能のスキーマをエクスポートしようとすると、"Automation スクリプト" が失敗する

VM で管理対象サービス ID が有効になっている場合、その VM またはリソース グループに対して "Automation スクリプト" 機能を使用すると、次のエラーが表示されます。

![MSI Automation スクリプト エクスポート エラー](../managed-service-identity/media/msi-known-issues/automation-script-export-error.png)

現時点では、管理対象サービス ID の VM 拡張機能では、そのスキーマをリソース グループ テンプレートにエクスポートする機能はサポートされていません。 その結果、生成されたテンプレートには、リソース上で管理対象サービス ID を有効にする構成パラメーターは示されません。 これらのセクションは、「[テンプレートを使用して、VM 管理対象サービス ID (MSI) を構成する](qs-configure-template-windows-vm.md)」の例に従うことで、手動で追加できます。

MSI VM 拡張機能でスキーマのエクスポート機能が利用可能になると、「[VM 拡張機能を含むリソース グループのエクスポート](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions)」の一覧に表示されます。

### <a name="configuration-blade-does-not-appear-in-the-azure-portal"></a>構成ブレードが Azure ポータルに表示されない

VM 構成ブレードが VM に表示されない場合、MSI はご利用のリージョンのポータルでまだ有効になっていません。  後でもう一度確認してください。  [PowerShell](qs-configure-powershell-windows-vm.md) または [Azure CLI](qs-configure-cli-windows-vm.md) を使用して、VM の MSI を有効にすることもできます。

### <a name="cannot-assign-access-to-virtual-machines-in-the-access-control-iam-blade"></a>Access Control (IAM) ブレードで、仮想マシンにアクセスを割り当てることができない


  **仮想マシン**が Azure ポータルの **[アクセス コントロール (IAM)]** > **[アクセス許可の追加]** で **[アクセスの割り当て先]** の選択肢として表示されない場合、管理対象サービス ID がご利用のリージョンのポータルでまだ有効になっていません。 後でもう一度確認してください。  MSI のサービス プリンシパルを検索することで、ロールの割り当ての管理対象サービス ID を選択することもできます。  **[選択]** フィールドに VM の名前を入力すると、サービス プリンシパルが検索結果に表示されます。

### <a name="vm-fails-to-start-after-being-moved-from-resource-group-or-subscription"></a>リソース グループまたはサブスクリプションから移動した後に VM を開始できない

実行中の状態で VM を移動する場合、VM は移動中も実行し続けます。 ただし、移動後に、VM を停止および再起動すると、VM を開始できなくなります。 この問題は、VM が MSI ID への参照を更新できず、以前のリソース グループで指し続けるために発生します。

**対処法** 
 
VM で更新プログラムをトリガーし、MSI の正しい値を取得できるようにします。 MSI ID への参照を更新するには、VM プロパティの変更を行うことができます。 たとえば、次のコマンドを使用して、VM で新しいタグの値を設定できます。

```azurecli-interactive
 az  vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
このコマンドは、新しいタグ "fixVM" を値 1 で VM に設定します。 
 
このプロパティの設定によって、VM は正しい MSI リソース URI で更新され、VM を開始できます。 
 
VM が開始されると、次のコマンドを使用してタグを削除できます。

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

## <a name="known-issues-with-user-assigned-identities"></a>ユーザー割り当て ID に関する既知の問題

- ユーザー割り当て ID の割り当ては、VM および VMSS に対してのみ使用できます。 重要: ユーザー割り当て ID の割り当ては、今後数か月間に変更されます。
- 同じ VM/VMSS においてユーザー割り当て ID が重複している、VM/VMSS は失敗します。 大文字と小文字の違いだけでは重複と見なされます。 たとえば、MyUserAssignedIdentity と myuserassignedidentity などです。 
- VM に対する VM 拡張機能のプロビジョニングは、DNS 検索エラーが原因で失敗することがあります。 VM を再起動して、もう一度やり直してください。 
- "存在しない" ユーザー割り当て ID を追加すると、VM は失敗します。 
- 名前に特殊文字 (アンダースコアなど) が含まれるユーザー割り当て ID の作成はサポートされていません。
- ユーザー割り当て ID の名前は、エンド ツー エンドのシナリオで 24 文字に制限されています。 ユーザー割り当て ID の名前が 24 文字より長いと、割り当ては失敗します。
- マネージド ID 仮想マシン拡張機能を使用している場合にサポートされるユーザー割り当て済みマネージド ID の制限値は 32 個です。 マネージド ID 仮想マシン拡張機能を使用していない場合にサポートされる制限値は 512 です。  
- 2 番目のユーザー割り当て ID を追加すると、VM 拡張機能のトークンを要求するときに、clientID を使用できなくなる場合があります。 この問題を軽減するには、次の 2 つのバッシュ コマンドを使用して、MSI VM 拡張機能を再起動します。
 - `sudo bash -c "/var/lib/waagent/Microsoft.ManagedIdentity.ManagedIdentityExtensionForLinux-1.0.0.8/msi-extension-handler disable"`
 - `sudo bash -c "/var/lib/waagent/Microsoft.ManagedIdentity.ManagedIdentityExtensionForLinux-1.0.0.8/msi-extension-handler enable"`
- VM にユーザー割り当て ID がありシステム割り当て ID がない場合、ポータルの UI では、MSI は "無効" と表示されます。 システム割り当て ID を有効にするには、Azure Resource Manager テンプレート、Azure CLI、または SDK を使用してください。
