---
title: "Azure SDK を使用して Azure VM のユーザーによって割り当てられた MSI を構成する方法"
description: "Azure SDK を使用して、Azure VM のユーザーによって割り当てられた管理対象サービス ID (MSI) を構成する方法をステップ バイ ステップで説明します。"
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
ms.date: 12/22/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 097304162b85599acd1f4591091f986a646ebc2a
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/03/2018
---
# <a name="configure-a-user-assigned-managed-service-identity-msi-for-a-vm-using-an-azure-sdk"></a>Azure SDK を使用して、VM のユーザーによって割り当てられた管理対象サービス ID (MSI) を構成する

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

管理対象サービス ID は、Azure Active Directory の管理対象 ID を Azure サービスに提供します。 この ID を使用すると、コード内に資格情報を埋め込む必要なく、Azure AD の認証をサポートするサービスに認証することができます。 

この記事では、Azure SDK を使用して、Azure VM のユーザー割り当て MSI を有効化する方法と削除する方法について説明します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

## <a name="azure-sdks-with-msi-support"></a>Azure SDK (MSI をサポート) 

Azure は、一連の [Azure SDK](https://azure.microsoft.com/downloads) によって、複数のプログラミング プラットフォームをサポートしています。 そのいくつかが、MSI をサポートするために更新され、対応するサンプルを提供することで、その使用方法を示しています。 次の一覧は、サポートが追加されると更新されます。

| SDK | サンプル |
| --- | ------ | 
| Python | [MSI を有効にして VM を作成する](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [MSI を使用して Azure VM を作成する](https://azure.microsoft.com/resources/samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>次の手順

- [Configure MSI for an Azure VM]\(Azure VM の MSI を構成する\) 下にある関連記事に目を通して、Azure VM 上にユーザー割り当て MSI を構成する方法を確認してください。

Microsoft のコンテンツ改善のため、次のコメント セクションよりご意見をお寄せください。
