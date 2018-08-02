---
title: Azure SDK を使用して Azure VM が有効になっているマネージド サービス ID を構成する方法
description: Azure SDK を使用して、Azure VM でマネージド サービス ID を構成および使用する方法をステップ バイ ステップで説明します。
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/28/2017
ms.author: daveba
ms.openlocfilehash: 2763c78d309f5a90d68429caa46581e50f8b4303
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2018
ms.locfileid: "39257662"
---
# <a name="configure-a-vm-managed-service-identity-using-an-azure-sdk"></a>Azure SDK を使用して、VM マネージド サービス ID を構成する

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

管理対象サービス ID は、Azure Active Directory (AD) で自動管理対象 ID を使用する Azure サービスを提供します。 この ID を使用して、コードに資格情報が含まれていなくても、Azure AD の認証をサポートする任意のサービスに認証することができます。 

この記事では、Azure SDK を使用して Azure VM のマネージド サービス ID を有効化および削除する方法について説明します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="azure-sdks-with-managed-service-identity-support"></a>マネージド サービス ID がサポートされる Azure SDK 

Azure は、一連の [Azure SDK](https://azure.microsoft.com/downloads) によって、複数のプログラミング プラットフォームをサポートしています。 そのいくつかが、マネージド サービス ID をサポートするために更新され、対応するサンプルを提供することで、その使用方法を示しています。 次の一覧は、サポートが追加されると更新されます。

| SDK | サンプル |
| --- | ------ | 
| .NET   | [MSI 対応 VM からリソースを管理する](https://azure.microsoft.com/resources/samples/aad-dotnet-manage-resources-from-vm-with-msi/) |
| Java   | [MSI 対応 VM からストレージを管理する](https://azure.microsoft.com/resources/samples/compute-java-manage-resources-from-vm-with-msi-in-aad-group/)|
| Node.js| [MSI を有効にして VM を作成する](https://azure.microsoft.com/resources/samples/compute-node-msi-vm/) |
| Python | [MSI を有効にして VM を作成する](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [MSI を使用して Azure VM を作成する](https://azure.microsoft.com/resources/samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>次の手順

- Azure Portal、PowerShell、CLI、およびリソース テンプレートを使用する方法については、「Configure Managed Service Identity for an Azure VM」 (Azure VM 用のマネージド サービス ID の構成) の関連記事を参照してください。

Microsoft のコンテンツ改善のため、次のコメント セクションよりご意見をお寄せください。
