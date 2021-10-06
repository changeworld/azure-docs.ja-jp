---
title: Azure Active Directory のデバイス ID とは
description: デバイス ID とそのユース ケース
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: overview
ms.date: 06/09/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sandeo, ravenn, spunukol, jogro, jploegert
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b689e27e6b65f5ace628d1f7cf12b246e6e516d
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129354810"
---
# <a name="what-is-a-device-identity"></a>デバイス ID とは

[デバイス ID](/graph/api/resources/device?view=graph-rest-1.0&preserve-view=true) は、Azure Active Directory (Azure AD) 内のオブジェクトです。 このデバイス オブジェクトは、ユーザー、グループ、またはアプリケーションに似ています。 デバイス ID は、管理者がアクセスまたは構成の決定を行うときに使用できる情報を提供します。

![[Azure AD デバイス] ブレードに表示されるデバイス](./media/overview/azure-active-directory-devices-all-devices.png)

デバイス ID を取得する方法は 3 つあります。

- Azure AD の登録
- Azure AD 参加
- ハイブリッド Azure AD 参加

デバイス ID は、[デバイスベースの条件付きアクセス ポリシー](../conditional-access/require-managed-devices.md)や [Microsoft エンドポイント マネージャーを使用したモバイル デバイス管理](/mem/endpoint-manager-overview)などのシナリオの前提条件です。

## <a name="modern-device-scenario"></a>最新のデバイス シナリオ

最新のデバイス シナリオでは、これらの方法のうちの 2 つに焦点を当てています。 

- [Azure AD の登録](concept-azure-ad-register.md) 
   - Bring Your Own Device (BYOD)
   - モバイル デバイス (携帯電話とタブレット)
- [Azure AD 参加](concept-azure-ad-join.md)
   - 組織が所有する Windows 10 デバイス
   - [Azure で VM として実行されている組織内の Windows Server 2019 以降のサーバー](howto-vm-sign-in-azure-ad-windows.md)

[Hybrid Azure AD 参加](concept-azure-ad-join-hybrid.md)は、Azure AD 参加に至るまでの暫定的なステップと見なされます。 Hybrid Azure AD 参加では、Windows 7 および Server 2008 までのダウンレベルの Windows バージョンのサポートを組織に提供します。 3 つのシナリオを単一の組織内ですべて共存させることができます。

## <a name="resource-access"></a>リソース アクセス

Azure AD へのデバイスの登録および参加により、ユーザーはクラウドベースのリソースにシームレスにサインオン (SSO) できるようになります。

Azure AD に参加しているデバイスは、[組織のオンプレミス リソースへの SSO](azuread-join-sso.md) の恩恵を受けます。

## <a name="provisioning"></a>プロビジョニング

Azure AD へのデバイスの追加は、セルフサービス方式、または管理者が管理する制御されたプロセスで実行できます。

## <a name="license-requirements"></a>ライセンスの要件

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>次のステップ

- [Azure AD 登録済みデバイス](concept-azure-ad-register.md)について学習する
- [Azure AD 参加済みデバイス](concept-azure-ad-join.md)について学習する
- [ハイブリッド Azure AD 参加済みデバイス](concept-azure-ad-join-hybrid.md)について学習する
- Azure portal でデバイス ID を管理する方法の概要については、[Azure portal によるデバイス ID の管理](device-management-azure-portal.md)に関するページを参照してください。
- デバイス ベースの条件付きアクセスの詳細については、[Azure Active Directory デバイス ベースの条件付きアクセス ポリシーの構成](../conditional-access/require-managed-devices.md)に関するページを参照してください。
