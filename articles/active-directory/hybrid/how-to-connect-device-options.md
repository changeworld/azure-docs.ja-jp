---
title: 'Azure AD Connect: デバイス オプション | Microsoft Docs'
description: このドキュメントでは、Azure AD Connect で使用できるデバイス オプションについて詳しく説明します
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: billmath
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 6993895b6f2b836308b8ed7c375212c3fae12768
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/19/2018
ms.locfileid: "46305921"
---
#<a name="azure-ad-connect-device-options"></a>Azure AD Connect: デバイス オプション

このドキュメントでは、Azure AD Connect で使用できるさまざまなデバイス オプションについて説明します。 Azure AD Connect を使うと、次の 2 つの操作を構成できます。 
* **Hybrid Azure AD の参加**: 環境にオンプレミスの AD フットプリントがあるときに、Azure AD のメリットを活用したい場合は、Hybrid Azure AD 参加済みデバイスを実装できます。 これらのデバイスは、オンプレミスの Active Directory と Azure Active Directory の両方に参加します。
* **デバイス ライトバック**: デバイス ライトバックを使うと、AD FS (2012 R2 以降) で保護されているデバイスへの、デバイスに基づく条件付きアクセスを有効にできます。

## <a name="configure-device-options-in-azure-ad-connect"></a>Azure AD Connect でデバイス オプションを構成する

1.  Azure AD Connect を実行します。 **[追加のタスク]** ページで、**[デバイス オプションの構成]** を選びます。  **[次へ]** をクリックします。
    ![デバイス オプションの構成](./media/how-to-connect-device-options/deviceoptions.png) 

    **[概要]** ページに詳細が表示されます。
    ![概要](./media/how-to-connect-device-options/deviceoverview.png)

    >[!NOTE]
    > 新しい [デバイス オプションの構成] は、バージョン 1.1.819.0 以降でのみ使うことができます。

2.  Azure AD の資格情報を入力した後は、[デバイスのオプション] ページで実行する操作を選択できます。
    ![デバイスの操作](./media/how-to-connect-device-options/deviceoptionsselection.png)

## <a name="next-steps"></a>次の手順

* [ハイブリッド Azure AD の参加を構成する](../device-management-hybrid-azuread-joined-devices-setup.md)
* [デバイス ライトバックを構成/無効化する](how-to-connect-device-writeback.md)

