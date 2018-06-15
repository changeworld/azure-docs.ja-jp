---
title: 'Azure AD Connect: デバイス オプション | Microsoft Docs'
description: このドキュメントでは、Azure AD Connect で使用できるデバイス オプションについて詳しく説明します
services: active-directory
documentationcenter: ''
author: anandy
manager: samueld
editor: billmath
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.component: hybrid
ms.author: anandy
ms.openlocfilehash: 0eb3a33ee030dcda6a811a771578c9e976e36619
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2018
ms.locfileid: "34593284"
---
#<a name="azure-ad-connect-device-options"></a>Azure AD Connect: デバイス オプション

このドキュメントでは、Azure AD Connect で使用できるさまざまなデバイス オプションについて説明します。 Azure AD Connect を使うと、次の 2 つの操作を構成できます。 
* **ハイブリッド Azure AD の参加**: 環境にオンプレミスの AD フットプリントがあるときに、Azure Active Directory が提供する機能も活用したい場合は、ハイブリッド Azure AD 参加済みデバイスを実装できます。 これらのデバイスは、オンプレミスの Active Directory と Azure Active Directory の両方に参加しているデバイスです。
* **デバイス ライトバック**: デバイス ライトバックを使うと、AD FS (2012 R2 以降) で保護されているデバイスへの、デバイスに基づく条件付きアクセスを有効にできます。

## <a name="configure-device-options-in-azure-ad-connect"></a>Azure AD Connect でデバイス オプションを構成する

1.  Azure AD Connect を実行します。 **[追加のタスク]** ページで、**[デバイス オプションの構成]** を選びます。
    ![デバイス オプションの構成](./media/active-directory-aadconnect-device-options/deviceoptions.png) 

    [次へ] をクリックすると、**[概要]** ページが表示され、実行できる操作の詳細が示されます。
    ![概要](./media/active-directory-aadconnect-device-options/deviceoverview.png)

    >[!NOTE]
    > 新しい [デバイス オプションの構成] は、バージョン 1.1.819.0 以降でのみ使うことができます。

2.  Azure AD の資格情報を入力した後は、[デバイスのオプション] ページで実行する操作を選択できます。
    ![デバイスの操作](./media/active-directory-aadconnect-device-options/deviceoptionsselection.png)

## <a name="next-steps"></a>次の手順

* [ハイブリッド Azure AD の参加を構成する](../device-management-hybrid-azuread-joined-devices-setup.md)
* [デバイス ライトバックを構成/無効化する](./active-directory-aadconnect-feature-device-writeback.md)

