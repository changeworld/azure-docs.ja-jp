---
title: Azure AD 登録済みデバイスとは
description: Azure AD 登録済みデバイスからは、Bring Your Own Device (BYOD) またはモバイル デバイス シナリオのサポートがユーザーに提供されます。そのしくみについて説明します。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/09/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 18bd3f6993c56187a64ba9db3397a659f9dea098
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131049921"
---
# <a name="azure-ad-registered-devices"></a>Azure AD 登録済みデバイス

Azure AD 登録済みデバイスの目標は、Bring Your Own Device (BYOD) またはモバイル デバイス シナリオのサポートをユーザーに提供することです。 これらのシナリオでは、ユーザーは個人所有のデバイスを使用して、組織のリソースにアクセスできます。

| Azure AD 登録済み | 説明 |
| --- | --- |
| **定義** | デバイスにサインインするための組織アカウントを必要としない Azure AD に登録します |
| **主な対象** | 次の条件に該当するすべてのユーザーに適用できます。 |
|   | Bring Your Own Device |
|   | モバイル デバイス |
| **デバイスの所有権** | ユーザーまたは組織 |
| **オペレーティング システム** | Windows 10 以上、iOS、Android、macOS |
| **プロビジョニング** | Windows 10 以上–設定 |
|   | iOS/Android – ポータル サイトまたは Microsoft Authenticator アプリ |
|   | macOS – ポータル サイト |
| **デバイスのサインイン オプション** | エンドユーザーのローカル資格情報 |
|   | Password |
|   | Windows Hello |
|   | PIN |
|   | 他のデバイスの生体認証またはパターン |
| **デバイス管理** | モバイル デバイス管理 (例:Microsoft Intune) |
|   | モバイル アプリケーション管理 |
| **主な機能** | クラウド リソースへの SSO |
|   | Intune への登録時の条件付きアクセス |
|   | アプリ保護ポリシーを使用する条件付きアクセス |
|   | Microsoft Authenticator アプリでの電話によるサインインを有効にします |

![Azure AD 登録済みデバイス](./media/concept-azure-ad-register/azure-ad-registered-device.png)

Azure AD 登録済みデバイスには、Windows 10 デバイスでの Microsoft アカウントのようなローカル アカウントを使用してサインインできます。 これらのデバイスには、組織のリソースにアクセスするための Azure AD アカウントがあります。 組織内のリソースへのアクセスは、その Azure AD アカウントと、デバイス ID に適用される条件付きアクセス ポリシーに基づいて制限できます。

管理者は、Microsoft Intune などの Mobile Device Management (MDM) ツールを使用して、これらの Azure AD 登録済みデバイスをセキュリティで保護し、さらに制御することができます。 MDM では、ストレージの暗号化、パスワードの複雑さ、セキュリティ ソフトウェアを常に最新の状態に保つことを求めるなど、組織に必要な構成を適用する手段が提供されます。 

Azure ADの登録は、作業アプリケーションに初めてアクセスするとき、またはWindows10またはWindows11の[設定]メニューを使用して手動で行うことができます。 

## <a name="scenarios"></a>シナリオ

組織内のユーザーが、自宅の PC から福利厚生の登録ツールにアクセスしたいと考えています。 組織によって、すべてのユーザーは Intune に準拠したデバイスからこのツールにアクセスすることが要求されています。 ユーザーが Azure AD に自宅の PC を登録すると、必要な Intune ポリシーが適用され、ユーザーにリソースへのアクセス権が付与されます。

別のユーザーは、ルート化されている個人用の Android フォンで組織のメールにアクセスしたいと考えています。 会社では準拠デバイスが必要であり、ルート化されたすべてのデバイスをブロックするために Intune コンプライアンス ポリシーが作成されています。 このデバイス上の組織リソースへの従業員からのアクセスは阻止されます。

## <a name="next-steps"></a>次のステップ

- [Azure portal を使用してデバイス ID を管理する](device-management-azure-portal.md)
- [Azure AD で古いデバイスを管理する](manage-stale-devices.md)
