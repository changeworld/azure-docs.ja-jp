---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 45fe2421883637bb004ab2245886942ecb9084c8
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106090057"
---
|名前 |説明 |ポリシー |Version |
|---|---|---|---|
|[セキュリティで保護されていないパスワードのセキュリティ設定があるマシンを監査する](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordSettingsAINE.json) |このイニシアティブはポリシー要件をデプロイし、セキュリティで保護されていないパスワードのセキュリティ設定があるマシンを監査します。 ゲスト構成ポリシーの詳細については、[https://aka.ms/gcpol](https://aka.ms/gcpol) にアクセスしてください。 |9 |1.0.0 |
|[\[プレビュー\]: 仮想マシンでゲスト構成ポリシーを有効にするための前提条件をデプロイする](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_Prerequisites.json) |このイニシアチブでは、ゲスト構成ポリシーによる監視の対象になる資格のある仮想マシンに、システム割り当てマネージド ID が追加され、プラットフォームに適したゲスト構成拡張機能がデプロイされます。 これはすべてのゲスト構成ポリシーに対する前提条件であるため、ゲスト構成ポリシーを使用する前にポリシー割り当てスコープに割り当てる必要があります。 ゲスト構成の詳細については、[https://aka.ms/gcpol](https://aka.ms/gcpol) を参照してください。 |4 |1.0.0-preview |
|[\[プレビュー\]:Windows マシンは Azure セキュリティ ベースラインの要件を満たしている必要がある](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_AzureBaseline.json) |このイニシアチブでは、Azure セキュリティ ベースラインを満たしていない設定で Windows マシンを監査します。 詳細については、[https://aka.ms/gcpol](https://aka.ms/gcpol) を参照してください。 |29 |2.0.0-preview |
