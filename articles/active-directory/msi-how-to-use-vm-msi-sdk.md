---
title: "Azure SDK で Azure VM 管理対象サービス ID を使用する方法"
description: "Azure VM MSI で Azure SDK を使用するコード サンプルです。"
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: bryanla
ms.openlocfilehash: 222acd1624ce025985bf1a3eef40eb124ab7678e
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/02/2017
---
# <a name="how-to-use-an-azure-vm-managed-service-identity-msi-with-azure-sdks"></a>Azure SDK で Azure VM 管理対象サービス ID (MSI) を使用する方法 

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]この記事では、各 Azure SDK の MSI サポートの使用例を示す SDK サンプルの一覧を示します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

> [!IMPORTANT]
> - この記事のすべてのサンプル コード/スクリプトでは、クライアントが、MSI 対応の仮想マシンで実行されていることを前提としています。 お使いの VM にリモート接続するには、Azure ポータルの VM への "接続" 機能を使用します。 VM で MSI を有効にする方法の詳細については、「[Azure Portal を使用して、VM 管理対象サービス ID (MSI) を構成する](msi-qs-configure-portal-windows-vm.md)」、または関連する記事 (PowerShell、CLI、テンプレート、または Azure SDK を使用) のいずれかを参照してください。 

## <a name="sdk-code-samples"></a>SDK コード サンプル

| SDK             | サンプル コード |
| --------------- | ----------- |
| .NET            | [管理対象サービス ID を使用して、Windows VM から Azure Resource Manager テンプレートを展開する](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet) |
| .NET Core       | [管理対象サービス ID を使用して、Linux VM から Azure サービスを呼び出す](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/) |
| Node.js         | [管理対象サービス ID を使用してリソースを管理する](https://azure.microsoft.com/resources/samples/resources-node-manage-resources-with-msi/) |
| Python          | [MSI を使用して VM 内から認証する](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) |
| Ruby            | [MSI 対応 VM からリソースを管理する](https://azure.microsoft.com/resources/samples/resources-ruby-manage-resources-with-msi/) |

## <a name="related-content"></a>関連コンテンツ

- ライブラリのダウンロード、ドキュメントなどを含む Azure SDK リソースの完全な一覧については、「[Azure SDK](https://azure.microsoft.com/downloads/)」を参照してください。
- Azure VM で MSI を有効にするには、「[Azure ポータルを使用して、VM 管理対象サービス ID (MSI) を構成する](msi-qs-configure-portal-windows-vm.md)」をご覧ください。

Microsoft のコンテンツ改善のため、次のコメント セクションよりご意見をお寄せください。








