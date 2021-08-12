---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 07/16/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 06f337cb524b1ca158baf0627ccdfa88c4ae4e40
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2021
ms.locfileid: "114387866"
---
|名前 |説明 |ポリシー |Version |
|---|---|---|---|
|[セキュリティで保護されていないパスワードのセキュリティ設定があるマシンを監査する](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordSettingsAINE.json) |このイニシアティブはポリシー要件をデプロイし、セキュリティで保護されていないパスワードのセキュリティ設定があるマシンを監査します。 ゲスト構成ポリシーの詳細については、[https://aka.ms/gcpol](../../../../articles/governance/policy/concepts/guest-configuration.md) にアクセスしてください。 |9 |1.0.0 |
|[仮想マシンでゲスト構成ポリシーを有効にするための前提条件をデプロイする](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_Prerequisites.json) |このイニシアチブでは、ゲスト構成ポリシーによる監視の対象になる資格のある仮想マシンに、システム割り当てマネージド ID が追加され、プラットフォームに適したゲスト構成拡張機能がデプロイされます。 これはすべてのゲスト構成ポリシーに対する前提条件であるため、ゲスト構成ポリシーを使用する前にポリシー割り当てスコープに割り当てる必要があります。 ゲスト構成の詳細については、[https://aka.ms/gcpol](../../../../articles/governance/policy/concepts/guest-configuration.md) を参照してください。 |4 |1.0.0 |
|[\[プレビュー\]: Windows マシンは Azure のコンピューティング セキュリティ ベースラインの要件を満たしている必要があります](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_AzureBaseline.json) |このイニシアチブでは、Azure コンピューティング セキュリティ ベースラインを満たしていない設定で Windows マシンを監査します。 詳細については、[https://aka.ms/gcpol](../../../../articles/governance/policy/concepts/guest-configuration.md) を参照してください。 |29 |2.0.1-preview |