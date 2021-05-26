---
title: 条件付きアクセス ポリシーの条件としてのデバイスのフィルター - Azure Active Directory
description: 条件付きアクセスでデバイス フィルターを使用してセキュリティ体制を強化する
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/19/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0eb008cdf85e6891f7d6e1159314ceccf46b794
ms.sourcegitcommit: 2ab484c6c8aeef8c6b7a1eb56d28ba13010d912c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2021
ms.locfileid: "110208649"
---
# <a name="conditional-access-filters-for-devices-preview"></a>条件付きアクセス: デバイスのフィルター (プレビュー)

条件付きアクセス ポリシーを作成する際、管理者は、環境内の特定のデバイスを対象または対象外とする機能を求めています。 デバイスのフィルター (プレビュー) 条件が用意されたことで、管理者はこれを実現できます。 [フィルターでサポートされている演算子とデバイスのプロパティ](#supported-operators-and-device-properties-for-filters)と条件付きアクセスポリシーで使用可能なその他の割り当て条件を使用して、特定のデバイスを対象にすることができるようになりました。

:::image type="content" source="media/concept-condition-filters-for-devices/create-filter-for-devices-condition.png" alt-text="条件付きアクセス ポリシーで、デバイスのフィルター条件を作成する":::

> [!IMPORTANT]
> デバイスのフィルター (プレビュー) は現在、パブリック プレビュー段階にあります。 このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="common-scenarios"></a>一般的なシナリオ

組織がデバイスのフィルター条件を使用して有効にできるようになったシナリオは複数あります。 次に、いくつかの主要なシナリオで、この新しい条件をどのように使用するのかについて例を示します。

- Microsoft Azure Management のような特権リソースへのアクセスを、[特権またはセキュリティで保護された管理ワークステーション](/security/compass/privileged-access-devices)からアクセスする特権ユーザーに制限します。 このシナリオでは、組織は次の 2 つの条件付きアクセス ポリシーを作成します。
   - ポリシー 1: 全体管理者のディレクトリ ロールを持つすべてのユーザーが、Microsoft Azure Management クラウド アプリにアクセスし、アクセス制御についてアクセス権を付与しますが、多要素認証を要求し、デバイスは準拠としてマーク済みである必要があります。
   - ポリシー 2: 全体管理者のディレクトリ ロールを持つすべてのユーザーが、Microsoft Azure Management クラウド アプリにアクセスし、device.extensionAttribute1 が SAW に等しいというルール式を使用して対象外デバイスのフィルターを指定し、アクセス制御についてブロックします。
- Windows 7 など、サポートされていないオペレーティング システム バージョンを実行しているデバイスから組織のリソースへのアクセスをブロックします。 このシナリオでは、組織は次の 2 つの条件付きアクセス ポリシーを作成します。
   - ポリシー 1: すべてのユーザーが、すべてのクラウド アプリにアクセスし、アクセス制御についてアクセス権を付与しますが、デバイスは準拠としてマーク済みである必要がある、またはデバイスをハイブリッド Azure AD 参加済みにする必要があります。
   - ポリシー 2: すべてのユーザーが、すべてのクラウド アプリにアクセスし、device.operatingSystem が Windows に等しい、かつ、device.operatingSystemVersion が "6.1" で始まるというルール式を使用して対象デバイスのフィルターを指定し、アクセス制御についてブロックします。
- Teams の電話または Surface Hub デバイスのような特定のデバイスで使用する場合は、サービス アカウントのような特定のアカウントに対する多要素認証を要求しないでください。 このシナリオでは、組織は次の 2 つの条件付きアクセス ポリシーを作成します。
   - ポリシー 1: サービス アカウントを除くすべてのユーザーが、すべてのクラウド アプリにアクセスし、アクセス制御についてアクセス権を付与しますが、多要素認証を要求します。
   - ポリシー 2: ユーザーとグループを選択し、サービス アカウントのみを含むグループを含め、すべてのクラウド アプリにアクセスし、device.extensionAttribute2 が TeamsPhoneDevice に等しくないというルール式を使用して対象外デバイスのフィルターを指定し、アクセス制御についてブロックします。

## <a name="create-a-conditional-access-policy"></a>条件付きアクセス ポリシーを作成する

デバイスのフィルターは、Azure portal で条件付きアクセス ポリシーを作成する場合または Microsoft Graph API を使用する場合のオプションです。

> [!IMPORTANT]
> デバイス状態とデバイスのフィルターは、条件付きアクセス ポリシーで一緒に使用することはできません。 デバイスのフィルターを使用する方が、`trustType` および `isCompliant` プロパティを介して、対象とするデバイス状態情報のサポートも含め、より詳細に対象設定できます。

次の手順を利用して、[一般的なシナリオ](#common-scenarios)の最初のシナリオをサポートする 2 つの条件付きアクセス ポリシーを作成します。 

ポリシー 1: 全体管理者のディレクトリ ロールを持つすべてのユーザーが、Microsoft Azure Management クラウド アプリにアクセスし、アクセス制御についてアクセス権を付与しますが、多要素認証を要求し、デバイスは準拠としてマーク済みである必要があります。

1. **Azure portal** にグローバル管理者、セキュリティ管理者、または条件付きアクセス管理者としてサインインします。
1. **[Azure Active Directory]**  >  **[セキュリティ]**  >  **[条件付きアクセス]** の順に移動します。
1. **[新しいポリシー]** を選択します。
1. ポリシーに名前を付けます。 ポリシーの名前に対する意味のある標準を組織で作成することをお勧めします。
1. **[割り当て]** で、 **[ユーザーとグループ]** を選択します。
   1. **[対象]** で、 **[ディレクトリ ロール]** を選択し、 **[全体管理者]** を選択します。
   
      > [!WARNING]
      > 条件付きアクセス ポリシーでは、組み込みロールがサポートされています。 条件付きアクセス ポリシーは、[管理単位スコープ](../roles/admin-units-assign-roles.md)や[カスタム ロール](../roles/custom-create.md)など、その他の種類のロールには適用されません。

   1. **[除外]** で、 **[ユーザーとグループ]** を選択し、組織の緊急アクセス用または非常用アカウントを選択します。 
   1. **[Done]** を選択します。
1. **[Cloud apps or actions]\(クラウド アプリまたはアクション\)**  >  **[対象]** で、 **[アプリを選択]** を選択し、 **[Microsoft Azure Management]** を選択します。
1. **[アクセス制御]**  >  **[許可]** で **[アクセス権の付与]** 、 **[多要素認証を要求する]** 、 **[デバイスは準拠としてマーク済みである必要があります]** を選択して、 **[選択]** を選択します。
1. 設定を確認し、 **[Enable policy]\(ポリシーの有効化\)** を **[オン]** に設定します。
1. **[作成]** を選択して、ポリシーを作成および有効化します。

ポリシー 2: 全体管理者のディレクトリ ロールを持つすべてのユーザーが、Microsoft Azure Management クラウド アプリにアクセスし、device.extensionAttribute1 が SAW に等しいというルール式を使用して対象外デバイスのフィルターを指定し、アクセス制御についてブロックします。

1. **[新しいポリシー]** を選択します。
1. ポリシーに名前を付けます。 ポリシーの名前に対する意味のある標準を組織で作成することをお勧めします。
1. **[割り当て]** で、 **[ユーザーとグループ]** を選択します。
   1. **[対象]** で、 **[ディレクトリ ロール]** を選択し、 **[全体管理者]** を選択します。
   
      > [!WARNING]
      > 条件付きアクセス ポリシーでは、組み込みロールがサポートされています。 条件付きアクセス ポリシーは、[管理単位スコープ](../roles/admin-units-assign-roles.md)や[カスタム ロール](../roles/custom-create.md)など、その他の種類のロールには適用されません。

   1. **[除外]** で、 **[ユーザーとグループ]** を選択し、組織の緊急アクセス用または非常用アカウントを選択します。 
   1. **[Done]** を選択します。
1. **[Cloud apps or actions]\(クラウド アプリまたはアクション\)**  >  **[対象]** で、 **[アプリを選択]** を選択し、 **[Microsoft Azure Management]** を選択します。
1. **[条件]** で **[Filters for devices (Preview)]\(デバイスのフィルター (プレビュー)\)** を選択します。
   1. **[構成]** を **[はい]** に切り替えます。
   1. **[Devices matching the rule]\(ルールに一致するデバイス\)** を **[Exclude filtered devices from policy]\(フィルター選択されたデバイスをポリシーから除外する\)** に設定します。
   1. プロパティを `ExtensionAttribute1`、演算子を `Equals`、値を `SAW` に設定します。
   1. **[Done]** を選択します。
1. **[アクセス制御]**  >  **[許可]** で、 **[アクセスのブロック]** 、 **[選択]** の順に選択します。
1. 設定を確認し、 **[Enable policy]\(ポリシーの有効化\)** を **[オン]** に設定します。
1. **[作成]** を選択して、ポリシーを作成および有効化します。

### <a name="filters-for-devices-graph-api"></a>デバイスのフィルター Graph API

デバイスのフィルター API は、現在、Microsoft Graph ベータ版のエンドポイントで使用でき、 https://graph.microsoft.com/beta/identity/conditionalaccess/policies/ を使用してアクセスできます。 新しい条件付きアクセス ポリシーを作成するときにデバイスのフィルターを構成するか、既存のポリシーを更新してデバイスのフィルター条件を構成できます。 既存のポリシーを更新するには、既存のポリシーのポリシー ID を追加し、次の要求本文を実行することで、上記の Microsoft Graph ベータ版のエンドポイントでパッチ呼び出しを実行できます。 次の例は、SAW デバイスとしてマークされていないデバイスを対象外とするデバイスのフィルター条件を構成する方法を示しています。 ルール構文は、複数の単一式で構成できます。 構文の詳細については、複数の式を含むルールをご確認ください。 

```json
{
    "conditions": {
        "devices": {
            "deviceFilter": {
                "mode": "exclude",
                "rule": "device.extensionAttribute1 -ne \"SAW\""
            }
        }
    }
}
```

## <a name="supported-operators-and-device-properties-for-filters"></a>フィルターでサポートされている演算子とデバイスのプロパティ

次のデバイス属性は、条件付きアクセスのデバイスのフィルター条件で使用できます。

| サポートされているデバイス属性 | サポートされている演算子 | サポート状況の値 | 例 |
| --- | --- | --- | --- |
| deviceId | Equals、NotEquals、In、NotIn | GUID である有効な deviceId | (device.deviceid -eq “498c4de7-1aee-4ded-8d5d-000000000000”) |
| displayName | Equals、NotEquals、StartsWith、NotStartsWith、EndsWith、NotEndsWith、Contains、NotContains、In、NotIn | 任意の文字列 | (device.displayName -contains “ABC”) |
| manufacturer | Equals、NotEquals、StartsWith、NotStartsWith、EndsWith、NotEndsWith、Contains、NotContains、In、NotIn | 任意の文字列 | (device.manufacturer -startsWith “Microsoft”) |
| mdmAppId | Equals、NotEquals、In、NotIn | 有効な MDM アプリケーション ID | (device.mdmAppId -in [“0000000a-0000-0000-c000-000000000000”]) |
| model | Equals、NotEquals、StartsWith、NotStartsWith、EndsWith、NotEndsWith、Contains、NotContains、In、NotIn | 任意の文字列 | (device.model -notContains “Surface”) |
| operatingSystem | Equals、NotEquals、StartsWith、NotStartsWith、EndsWith、NotEndsWith、Contains、NotContains、In、NotIn | 有効なオペレーティング システム (Windows、iOS、Android など) | (device.operatingSystem -eq “Windows”) |
| operatingSystemVersion | Equals、NotEquals、StartsWith、NotStartsWith、EndsWith、NotEndsWith、Contains、NotContains、In、NotIn | 有効なオペレーティング システムのバージョン (Windows 7 の場合は 6.1、Windows 8 の場合は 6.2、Windows 10 の場合は 10.0 など) | (device.operatingSystemVersion -in [“10.0.18363”, “10.0.19041”, “10.0.19042”]) |
| pyhsicalIds | Contains、NotContains | たとえば、Windows オートパイロット デバイスにはすべて、デバイスの physicalIds プロパティに ZTDId (インポートされたすべての Windows オートパイロット デバイスに割り当てられた一意の値) が格納されています。 | (device.devicePhysicalIDs -contains "[ZTDId]") |
| profileType | Equals、NotEquals | デバイスに設定されている有効なプロファイルの種類。 サポートされている値は、RegisteredDevice (既定値)、SecureVM (Azure AD サインインで有効になっている Azure の Windows VM に使用)、プリンター (プリンターに使用)、共有 (共有デバイスに使用)、IoT (IoT デバイスに使用) | (device.profileType -notIn [“Printer”, “Shared”, “IoT”]) |
| systemLabels | Contains、NotContains | システムによってデバイスに適用されているラベルの一覧。 サポートされている値は、AzureResource (Azure AD サインインで有効になっている Azure の Windows VM に使用)、M365Managed (Microsoft マネージド デスクトップを使用して管理されるデバイスに使用)、MultiUser (共有デバイスに使用) など | (device.systemLabels - "M365Managed" を含む) |
| trustType | Equals、NotEquals | デバイスの有効な登録済み状態。 サポートされている値は、AzureAD (Azure AD 参加デバイスに使用)、ServerAD (Hybrid Azure AD 参加済みデバイスに使用)、Workplace (Azure AD 登録済みデバイスに使用) | (device.trustType -notIn ‘ServerAD, Workplace’) |
| extensionAttribute1-15 | Equals、NotEquals、StartsWith、NotStartsWith、EndsWith、NotEndsWith、Contains、NotContains、In、NotIn | extensionAttributes1-15 は、お客様がデバイス オブジェクトに使用できる属性です。 お客様は、extensionAttributes1 から 15 のいずれかをカスタム値で更新し、条件付きアクセスのデバイスのフィルター条件でそれらを使用できます。 任意の文字列値を使用できます。 | (device.extensionAttribute1 -eq ‘SAW’) |

## <a name="policy-behavior-with-filters-for-devices"></a>デバイスのフィルターを使用したポリシーの動作

条件付きアクセスのデバイスのフィルター (プレビュー) 条件では、Azure AD の登録済みデバイスのデバイス属性に基づいてポリシーが評価されるため、どのような状況でポリシーが適用されるのか、または適用されないのかを理解することが重要です。 次の表は、デバイスのフィルター条件が構成されている場合の動作を示しています。 

| デバイスのフィルター条件 | デバイスの登録状態 | デバイス フィルターの適用 
| --- | --- | --- |
| 対象/対象外モードで正の演算子 (Equals、StartsWith、EndsWith、Contains、In) を指定し、任意の属性を使用する | 未登録のデバイス | No |
| 対象/対象外モードで正の演算子 (Equals、StartsWith、EndsWith、Contains、In) を指定し、extensionAttributes1-15 を対象外とする属性を使用する | 登録済みのデバイス | はい (条件が満たされている場合) |
| 対象/対象外モードで正の演算子 (Equals、StartsWith、EndsWith、Contains、In) を指定し、extensionAttributes1-15 を対象とする属性を使用する | Intune で管理されている登録済みデバイス | はい (条件が満たされている場合) |
| 対象/対象外モードで正の演算子 (Equals、StartsWith、EndsWith、Contains、In) を指定し、extensionAttributes1-15 を対象とする属性を使用する | Intune で管理されていない登録済みデバイス | はい (条件が満たされている場合、かつ、デバイスが準拠している、または Hybrid Azure AD 参加済みの場合) |
| 対象/対象外モードで負の演算子 (NotEquals、NotStartsWith、NotEndsWith、NotContains、NotIn) を指定し、任意の属性を使用する | 未登録のデバイス | Yes |
| 対象/対象外モードで負の演算子 (NotEquals、NotStartsWith、NotEndsWith、NotContains、NotIn) を指定し、extensionAttributes1-15 を対象外とする任意の属性を使用する | 登録済みのデバイス | はい (条件が満たされている場合) |
| 対象/対象外モードで負の演算子 (NotEquals、NotStartsWith、NotEndsWith、NotContains、NotIn) を指定し、extensionAttributes1-15 を対象とする任意の属性を使用する | Intune で管理されている登録済みデバイス | はい (条件が満たされている場合) |
| 対象/対象外モードで負の演算子 (NotEquals、NotStartsWith、NotEndsWith、NotContains、NotIn) を指定し、extensionAttributes1-15 を対象とする任意の属性を使用する | Intune で管理されていない登録済みデバイス | はい (条件が満たされている場合、かつ、デバイスが準拠している、または Hybrid Azure AD 参加済みの場合) |

## <a name="next-steps"></a>次のステップ

- [条件付きアクセス:条件](concept-conditional-access-conditions.md)
- [一般的な条件付きアクセス ポリシー](concept-conditional-access-policy-common.md)
- [特権アクセスの一部としてデバイスをセキュリティで保護する](/security/compass/privileged-access-devices)
