---
title: "Azure SDK を使用して MSI 対応 Azure VM を構成する方法"
description: "Azure SDK を使用して、Azure VM で管理対象サービス ID (MSI) を構成および使用する方法をステップ バイ ステップで説明します。"
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/28/2017
ms.author: daveba
ms.openlocfilehash: dee89e4cd6501bb56015a2e4cfc045cc6308400e
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2018
---
# <a name="configure-a-vm-managed-service-identity-msi-using-an-azure-sdk"></a>Azure SDK を使用して、VM 管理対象サービス ID (MSI) を構成する

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

管理対象サービス ID は、Azure Active Directory (AD) で自動管理対象 ID を使用する Azure サービスを提供します。 この ID を使用して、コードに資格情報が含まれていなくても、Azure AD の認証をサポートする任意のサービスに認証することができます。 

この記事では、Azure SDK を使用して Azure VM の MSI を有効化および削除する方法について説明します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="azure-sdks-with-msi-support"></a>Azure SDK (MSI をサポート) 

Azure は、一連の [Azure SDK](https://azure.microsoft.com/downloads) によって、複数のプログラミング プラットフォームをサポートしています。 そのいくつかが、MSI をサポートするために更新され、対応するサンプルを提供することで、その使用方法を示しています。 次の一覧は、サポートが追加されると更新されます。

| SDK | サンプル |
| --- | ------ | 
| .NET   | [MSI 対応 VM からリソースを管理する](https://azure.microsoft.com/resources/samples/aad-dotnet-manage-resources-from-vm-with-msi/) |
| Java   | [MSI 対応 VM からストレージを管理する](https://azure.microsoft.com/resources/samples/compute-java-manage-resources-from-vm-with-msi-in-aad-group/)|
| Node.js| [MSI を有効にして VM を作成する](https://azure.microsoft.com/resources/samples/compute-node-msi-vm/) |
| Python | [MSI を有効にして VM を作成する](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [MSI を使用して Azure VM を作成する](https://azure.microsoft.com/resources/samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>次の手順

- Azure Portal、PowerShell、CLI、およびリソース テンプレートを使用する方法については、「Azure VM 用の MSI の構成」の関連記事を参照してください。

Microsoft のコンテンツ改善のため、次のコメント セクションよりご意見をお寄せください。
