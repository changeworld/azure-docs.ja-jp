---
title: Azure Key Vault Managed HSM の論理的な削除 | Microsoft Docs
description: Managed HSM の論理的な削除を行うと、削除した HSM のインスタンスおよびキーを復旧できます。 この記事では、この機能の概要について説明します。
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
author: mbaldwin
ms.author: mbaldwin
ms.date: 06/01/2021
ms.openlocfilehash: b832aa066e1d31bfc064f988c722d2503ff96507
ms.sourcegitcommit: 86ca8301fdd00ff300e87f04126b636bae62ca8a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2021
ms.locfileid: "122195049"
---
# <a name="managed-hsm-soft-delete-overview"></a>Managed HSM の論理的な削除の概要

> [!IMPORTANT]
> Managed HSM のリソースの論理的な削除は無効にできません。

> [!IMPORTANT]
> Managed HSM のリソースに対して論理的な削除を行っても、消去されるまでは引き続き時間あたり料金全額が課金されます。

Managed HSM の論理的な削除機能では、削除した HSM およびキーの復旧が可能です。 具体的には、この機能によって次のような保護が提供されます。

- HSM またはキーは、削除した後、設定可能な期間である 7 ～ 90 日間 (暦日)、復旧可能な状態が保たれます。 HSM を作成するときに保持期間を設定できます。 値を指定しないと、既定の保持期間である 90 日になります。 この期間があることによって、ユーザーは、誤ってキーまたは HSM の削除したことに気付き、対処できます。
- キーを完全に削除するには、ユーザーは 2 つのアクションを実行する必要があります。 まず、ユーザーはキーを削除する必要があります。これによってキーは論理的に削除された状態になります。 次に、ユーザーは論理的に削除された状態のキーを消去する必要があります。 消去操作には、Managed HSM 暗号化担当者ロールが必要です。 これらの追加の保護により、ユーザーが誤って、または意図的にキーまたは HSM を削除するリスクが軽減されます。


## <a name="soft-delete-behavior"></a>論理的な削除の動作

Managed HSM のリソースの論理的な削除は無効にできません。

削除済みとマークされたリソースは、指定された期間保持されます。 削除された HSM またはキーを復旧するメカニズムもあるため、削除を元に戻すことができます。

既定の保持期間は 90 日です。 HSM リソースを作成するときに、保持ポリシー期間を 7 ～ 90 日の値に設定できます。 保護の削除のアイテム保持ポリシーでは、同じ間隔が使用されます。 保持ポリシーを設定した後に変更することはできません。

保持期間が終了し、HSM リソースが消去されるまで (完全に削除)、論理的な削除を行った HSM リソースの名前を再利用することはできません。

## <a name="purge-protection"></a>消去保護

消去保護はオプションの動作です。 これは、既定では有効になっていません。 [Azure CLI](./recovery.md?tabs=azure-cli) または [PowerShell](./recovery.md?tabs=azure-powershell) を使用して有効にできます。

消去保護が有効な場合、削除状態の HSM またはキーは、保持期間が終了するまで消去できません｡ 論理的に削除された HSM とキーは復旧可能な状態が維持され、保持ポリシーの有効性が確保されます。

既定の保持期間は 90 日です。 HSM を作成するときに、保持ポリシー期間を 7 ～ 90 日の値に設定できます。 保持ポリシー期間を設定できるのは HSM 作成時のみです。 後で変更することはできません。

「[CLI で Managed HSM の論理的な削除を使用する方法](./recovery.md?tabs=azure-cli#managed-hsms-cli)」または「[PowerShell で Managed HSM の論理的な削除を使用する方法](./recovery.md?tabs=azure-powershell#managed-hsms-powershell)」を参照してください。

## <a name="managed-hsm-recovery"></a>Managed HSM の復旧

HSM を削除すると、サービスによってサブスクリプションにプロキシ リソースが作成され、復旧を有効にするために十分なメタデータが追加されます。 プロキシ リソースは格納済みオブジェクトです。 これは削除された HSM と同じ場所にあります。 

## <a name="key-recovery"></a>キーの復旧

キーは、削除するとサービスによって削除済み状態になり、どの操作からもアクセスできなくなります。 キーはこの状態の間、一覧表示、復旧、または消去できます。 オブジェクトを表示するには、Azure CLI の `az keyvault key list-deleted` コマンド (「[CLI を使用した Managed HSM の論理的な削除と消去保護](./recovery.md?tabs=azure-cli#keys-cli)」で説明) または Azure PowerShell の `-InRemovedState` パラメーター (「[PowerShell を使用したManaged HSM の論理的な削除と消去保護](./recovery.md?tabs=azure-powershell#keys-powershell)」で説明) を使用します。  

キーを削除すると、Managed HSM は、事前に定義された保持期間の後に、削除された HSM またはキーに対応する基になるデータの削除がスケジュールされます。 保持期間中には HSM に対応する DNS レコードも保持されます。

## <a name="soft-delete-retention-period"></a>論理的な削除のリテンション期間

論理的に削除されたリソースは一定期間 (90 日間) 保持されます。 論理的な削除のリテンション期間には、以下の条件が適用されます。

- サブスクリプションの論理的な削除状態にあるすべての HSM とキーを一覧表示できます。 また、これらの削除と復旧に関する情報にアクセスすることもできます。
- Managed HSM 共同作成者ロールを持つユーザーのみが、削除された HSM を一覧表示できます。 削除されたコンテナーを扱うためにこれらのアクセス許可が指定されたカスタム ロールを作成することをお勧めします。
- Managed HSM 名は、指定された場所で一意である必要があります。 キーを作成するとき、削除された状態の HSM 名を持つキーが含まれている場合、その名前を使用することはできません。
- Managed HSM 共同作成者ロールを持つユーザーのみが、Managed HSM を一覧表示、表示、復旧、消去できます。
- キーを一覧表示、表示、復旧、消去できるのは、Managed HSM 暗号化担当者ロールを持つユーザーのみです。
  
Managed HSM またはキーを復旧しない限り、リテンション期間終了時に、サービスによって論理的に削除された HSM またはキーが消去されます。 リソースの削除を再スケジュールすることはできません。

### <a name="billing-implications"></a>課金への影響

Managed HSM はシングルテナント サービスです。 Managed HSM を作成すると、そのサービスによって、HSM に割り当てられている基になるリソースが予約されます。 これらのリソースは、HSM が削除された状態の場合でも割り当てられたままになります。 HSM は削除された状態のときに課金されます。

## <a name="next-steps"></a>次のステップ

次の記事では、論理的な削除を使用する主なシナリオについて説明します。

- [PowerShell で Managed HSM の論理的な削除を使用する方法](./recovery.md?tabs=azure-powershell) 
- [Azure CLI で Managed HSM の論理的な削除を使用する方法](./recovery.md?tabs=azure-cli)
