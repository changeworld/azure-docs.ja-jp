---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/03/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 8c559cd62092d3235d27a94c40cc39457b18a82e
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2021
ms.locfileid: "123474816"
---
|名前 |説明 |ポリシー |Version |
|---|---|---|---|
|[セキュリティで保護されていないパスワードのセキュリティ設定があるマシンを監査する](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordSettingsAINE.json) |このイニシアティブはポリシー要件をデプロイし、セキュリティで保護されていないパスワードのセキュリティ設定があるマシンを監査します。 ゲスト構成ポリシーの詳細については、[https://aka.ms/gcpol](../../../../articles/governance/policy/concepts/guest-configuration.md) にアクセスしてください。 |9 |1.0.0 |
|[仮想マシンでゲスト構成ポリシーを有効にするための前提条件をデプロイする](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_Prerequisites.json) |このイニシアチブでは、ゲスト構成ポリシーによる監視の対象になる資格のある仮想マシンに、システム割り当てマネージド ID が追加され、プラットフォームに適したゲスト構成拡張機能がデプロイされます。 これはすべてのゲスト構成ポリシーに対する前提条件であるため、ゲスト構成ポリシーを使用する前にポリシー割り当てスコープに割り当てる必要があります。 ゲスト構成の詳細については、[https://aka.ms/gcpol](../../../../articles/governance/policy/concepts/guest-configuration.md) を参照してください。 |4 |1.0.0 |
|[\[プレビュー\]: Windows マシンは Azure のコンピューティング セキュリティ ベースラインの要件を満たしている必要があります](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_AzureBaseline.json) |このイニシアチブでは、Azure コンピューティング セキュリティ ベースラインを満たしていない設定で Windows マシンを監査します。 詳細については、[https://aka.ms/gcpol](../../../../articles/governance/policy/concepts/guest-configuration.md) を参照してください。 |29 |2.0.1-preview |