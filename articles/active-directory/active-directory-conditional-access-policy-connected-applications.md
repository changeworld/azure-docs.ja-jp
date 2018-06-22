---
title: Azure Active Directory のデバイス ベースの条件付きアクセスポリシーを構成する | Microsoft Docs
description: Azure Active Directory のデバイスベースの条件付きアクセスを構成する方法について説明します。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: a27862a6-d513-43ba-97c1-1c0d400bf243
ms.service: active-directory
ms.component: protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 1c21c915bc0a83cdafb221a2cd592890577437ee
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/07/2018
ms.locfileid: "34849527"
---
# <a name="configure-azure-active-directory-device-based-conditional-access-policies"></a>Azure Active Directory のデバイス ベースの条件付きアクセスポリシーを構成する

[Azure Active Directory (Azure AD) の条件付きアクセス](active-directory-conditional-access-azure-portal.md)を使うと、承認されたユーザーがどのようにリソースにアクセスするかを制御できます。 たとえば、特定のリソースへのアクセスをマネージド デバイスに制限できます。 マネージド デバイスを必要とする条件付きアクセス ポリシーは、デバイス ベースの条件付きアクセス ポリシーとも呼ばれます。

このトピックでは、Azure AD に接続されたアプリケーション用のデバイス ベースの条件付きアクセス ポリシーを構成する方法について説明します。 


## <a name="before-you-begin"></a>開始する前に

デバイス ベースの条件付きアクセスは、**Azure AD の条件付きアクセス**と **Azure AD のデバイス管理**を連携させます。 これらの領域を十分に理解してない場合は、次のトピックを先に読んでおく必要があります。

- **[Azure Active Directory の条件付きアクセス](active-directory-conditional-access-azure-portal.md)** - このトピックは、条件付きアクセスと関連する用語の概念的な概要を説明しています。

- **[Azure Active Directory のデバイス管理の概要](device-management-introduction.md)** - このトピックは、デバイスを Azure AD に接続する際に使用できるさまざまなオプションの概要を説明しています。 



## <a name="managed-devices"></a>マネージド デバイス  

モバイル ファースト、クラウド ファーストの世界で、Azure Active Directory を使用してデバイス、アプリ、およびサービスにどこからでもサインオンできます。 環境内の特定のリソースでは、適切なユーザーにアクセスを許可するだけでは十分ではない場合があります。 適切なユーザーだけでなく、マネージド デバイスを使用したアクセス試行のみを許可することが必要な場合もあります。

マネージド デバイスは、セキュリティおよびコンプライアンスの基準を満たすデバイスです。 簡単に言えば、マネージド デバイスとは、*何らかの*組織的な統制下に置かれたデバイスのことです。 Azure AD では、マネージド デバイスの前提条件は、デバイスが Azure AD に登録されていることです。 デバイスを登録すると、デバイスの ID がデバイス オブジェクトの形式で作成されます。 このオブジェクトは、デバイスに関する状態情報を追跡するために Azure によって使用されます。 Azure AD 管理者は既に、このオブジェクトを使用してデバイスの状態を切り替える (有効化/無効化する) ことができます。
  
![デバイス ベースの条件](./media/active-directory-conditional-access-policy-connected-applications/32.png)

Azure AD に登録されているデバイスを取得するには、次の 3 つのオプションがあります:

- **[Azure AD 登録済みデバイス](device-management-introduction.md#azure-ad-registered-devices)** - Azure AD に登録されたパーソナル デバイスを取得します

- **[Azure AD 参加済みデバイス](device-management-introduction.md#azure-ad-joined-devices)** - Azure AD に登録されたオンプレミス AD に参加していない、組織の Windows 10 デバイスを取得します。 

- **[ハイブリッド Azure AD 参加済みデバイス](device-management-introduction.md#hybrid-azure-ad-joined-devices)** - Azure AD に登録されたオンプレミス AD に参加している Windows 10 デバイスを取得します。

マネージド デバイスになることができる登録済みデバイスは、ハイブリッド Azure AD 参加済みデバイスまたは準拠とマークされたデバイスです。  

![デバイス ベースの条件](./media/active-directory-conditional-access-policy-connected-applications/47.png)


 
## <a name="require-hybrid-azure-ad-joined-devices"></a>ハイブリッド Azure AD 参加済みデバイスが必要

条件付きアクセス ポリシーで、選択されたクラウド アプリにはマネージド デバイスを使用しなければアクセスできないことを宣言するために、**[ハイブリッド Azure AD 参加済みデバイスが必要]** を選択できます。 

![デバイス ベースの条件](./media/active-directory-conditional-access-policy-connected-applications/10.png)

この設定は、オンプレミスの AD に参加している Windows 10 デバイスにのみ適用されます。 ハイブリッド Azure AD 参加を使用する方法でのみ、これらのデバイスを Azure AD に登録することができ、これは Windows 10 デバイスを登録するための[自動プロセス](device-management-hybrid-azuread-joined-devices-setup.md)です。 

![デバイス ベースの条件](./media/active-directory-conditional-access-policy-connected-applications/45.png)

ハイブリッド Azure AD 参加済みデバイスをマネージド デバイスにする条件は何でしょうか?  オンプレミス AD に参加しているデバイスの場合、これらのデバイスに対する統制は、**System Center Configuration Manager (SCCM)** などの管理ソリューションや、それらを管理するための**グループ ポリシー (GP)** を使用して適用されると想定されます。 これらの方法のいずれかがデバイスに適用されているかどうか Azure AD で判断する方法がないため、ハイブリッド Azure AD 参加済みデバイスを要求することは、マネージド デバイスを要求するための比較的弱いメカニズムです。 当該デバイスがハイブリッド Azure AD 参加済みデバイスでもある場合、オンプレミスのドメイン参加済みデバイスに適用されている方法がマネージド デバイスを構成するために十分に強いかどうかを判断することは管理者の責任です。


## <a name="require-device-to-be-marked-as-compliant"></a>デバイスは準拠としてマーク済みである必要がある

*デバイスは準拠としてマーク済みである必要がある*というオプションは、マネージド デバイスを要求するための最も厳密な形式です。

![デバイス ベースの条件](./media/active-directory-conditional-access-policy-connected-applications/11.png)

このオプションでは、デバイスが Azure AD に登録されている必要があり、次のものによって準拠とマークされている必要もあります。
         
- Intune 
- Azure AD 統合によって Windows 10 デバイスを管理するサードパーティ製のモバイル デバイス管理システム 
 
![デバイス ベースの条件](./media/active-directory-conditional-access-policy-connected-applications/46.png)



準拠としてマークされているデバイスの場合、次のことを想定できます。 

- 会社のデータにアクセスするために従業員が使用するモバイル デバイスが管理されている
- 従業員が使用するモバイル アプリが管理されている
- 従業員が情報にアクセスして共有する方法の統制を補助することによって、会社の情報が保護されている
- デバイスとそのアプリが会社のセキュリティ要件に準拠している




## <a name="next-steps"></a>次の手順

環境内でデバイス ベースの条件付きアクセス ポリシーを構成する前に、「[Azure Active Directory の条件付きアクセスのベスト プラクティス](active-directory-conditional-access-best-practices.md)」を参照してください。

