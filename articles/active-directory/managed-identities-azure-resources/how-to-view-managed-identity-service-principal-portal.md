---
title: Azure portal でマネージド ID のサービス プリンシパルを表示する方法
description: Azure portal でマネージド ID のサービス プリンシパルを表示するための段階的手順。
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2018
ms.author: priyamo
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6d2896b43d9eb5e126d07353a4bf6fedfd11542
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56183872"
---
# <a name="view-the-service-principal-of-a-managed-identity-in-the-azure-portal"></a>Azure portal でマネージド ID のサービス プリンシパルを表示する

Azure リソースのマネージド ID は、Azure Active Directory で自動的に管理される ID を Azure サービスに提供します。 この ID を使用して、コードに資格情報が含まれていなくても、Azure AD の認証をサポートする任意のサービスに認証することができます。 

この記事では、Azure portal でマネージド ID のサービス プリンシパルを表示する方法について説明します。

## <a name="prerequisites"></a>前提条件

- Azure リソースのマネージド ID の基本点な事柄については、[概要](overview.md)に関するセクションを参照してください。
- まだ Azure アカウントを持っていない場合は、[無料アカウントを新規登録](https://azure.microsoft.com/free/)してください。
- [仮想マシン](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity)または[アプリケーション](/azure/app-service/overview-managed-identity#adding-a-system-assigned-identity)でシステム割り当ての ID を有効にします。

## <a name="view-the-service-principal"></a>サービス プリンシパルを表示する

この手順を行うと、VM のサービス プリンシパルが表示されます。また、システム割り当ての ID が有効になっていることを確認できます。アプリケーションの場合も同じ手順が適用されます。

1. **[Azure Active Directory]** をクリックしてから、**[エンタープライズ アプリケーション]** をクリックします。
2. **[アプリケーションの種類]** で **[すべてのアプリケーション]** を選択します。
3. 検索フィルター ボックスに、マネージド ID が有効になっている VM またはアプリケーションの名前を入力するか、表示された一覧から選択します。

   ![ポータルでマネージド ID サービス プリンシパルを表示する](./media/how-to-view-managed-identity-service-principal-portal/view-managed-identity-service-principal-portal.png)

## <a name="next-steps"></a>次の手順

[Azure リソースのマネージド ID](/azure/active-directory/managed-identities-azure-resources/overview)

