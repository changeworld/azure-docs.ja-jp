---
title: ハイブリッド Azure AD 参加済みデバイスとは
description: デバイス ID 管理を活用して、環境内のリソースにアクセスしているデバイスを管理する方法について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/10/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: ccd53294598be8103b7ce9774e65aef802e79205
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128618079"
---
# <a name="hybrid-azure-ad-joined-devices"></a>ハイブリッド Azure AD 参加済みデバイス

既存の Active Directory 実装がある組織は、Hybrid Azure AD Join を使用したデバイスを実装することで、Azure Active Directory (Azure AD) によって提供される機能の一部を活用できます。 これらのデバイスは、オンプレミスの Active Directory に参加し、Azure Active Directory に登録されます。

Hybrid Azure AD Join を使用したデバイスには、オンプレミスのドメイン コントローラーへのネットワーク接続が定期的に必要になります。 この接続がない場合、デバイスは使用できなくなります。 この要件に不安がある場合は、デバイスへの [Azure AD 参加](concept-azure-ad-join.md)を検討してください。

| Hybrid Azure AD Join | 説明 |
| --- | --- |
| **定義** | デバイスにサインインするために組織アカウントを必要とする、オンプレミスの AD と Azure AD に参加します |
| **主な対象** | 既存のオンプレミス AD インフラストラクチャがあるハイブリッド組織に適しています |
|   | 組織内のすべてのユーザーに適用できます |
| **デバイスの所有権** | Organization |
| **オペレーティング システム** | Windows 10、8.1、7 |
|   | Windows Server 2008/R2、2012/R2、2016、2019 |
| **プロビジョニング** | Windows 10、Windows Server 2016/2019 |
|   | IT 担当者によりドメインに参加し、Azure AD Connect または ADFS の構成を使用して自動参加します |
|   | Windows Autopilot でドメインに参加し、Azure AD Connect または ADFS の構成を使用して自動参加します |
|   | Windows 8.1、Windows 7、Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 - MSI が必要です |
| **デバイスのサインイン オプション** | 以下を使用する組織アカウント: |
|   | Password |
|   | Win10 用の Windows Hello for Business |
| **デバイス管理** | グループ ポリシー |
|   | Configuration Manager のスタンドアロンまたは Microsoft Intune との共同管理 |
| **主な機能** | クラウドとオンプレミスの両方のリソースへの SSO |
|   | ドメイン参加、または共同管理の場合は Intune を使用する条件付きアクセス |
|   | ロック画面でのセルフサービス パスワード リセット および Windows Hello PIN のリセット |
|   | デバイス間での Enterprise State Roaming |

![ハイブリッド Azure AD 参加済みデバイス](./media/concept-azure-ad-join-hybrid/azure-ad-hybrid-joined-device.png)

## <a name="scenarios"></a>シナリオ

以下に該当する場合は、ハイブリッド Azure AD 参加済みデバイスを使用します。

- Windows 7 および 8.1 を実行しているダウンレベルのデバイスをサポートします。
- 引き続き、グループ ポリシーを使用して、デバイス構成を管理したいと思っています。
- 引き続き、既存のイメージング ソリューションを使用して、デバイスをデプロイして構成したいと思っています。
- Active Directory コンピューター認証に依存しているこれらのデバイスに展開される Win32 アプリがあります。

## <a name="next-steps"></a>次のステップ

- [ハイブリッド Azure AD 参加の実装を計画する](hybrid-azuread-join-plan.md)
- [Azure portal を使用してデバイス ID を管理する](device-management-azure-portal.md)
- [Azure AD で古いデバイスを管理する](manage-stale-devices.md)
