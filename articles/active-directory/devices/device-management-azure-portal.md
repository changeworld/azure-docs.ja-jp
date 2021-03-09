---
title: Azure Portal によるデバイスの管理 | Microsoft Docs
description: Azure Portal を使用してデバイスを管理する方法を説明します。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 09/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: hafowler
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0aea468c64f70bd7f35dd25206faa9ea33459999
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101688911"
---
# <a name="manage-device-identities-using-the-azure-portal"></a>Azure portal を使用してデバイス ID を管理する

Azure AD は、デバイス ID を一元的に管理する場所を提供します。

**[すべてのデバイス]** ページを使用すると、次のことを実行できます。

- 次のようなデバイスを識別する。
   - Azure AD に参加または登録しているデバイス。
   - [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot) を使用してデプロイされたデバイス。
   - [ユニバーサル印刷](/universal-print/fundamentals/universal-print-getting-started)を使用するプリンター
- 有効化、無効化、削除、管理などのデバイス ID 管理タスクを実行する。
   - [プリンター](/universal-print/fundamentals/)および [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot) デバイスでは、Azure AD での管理オプションが制限されています。 これらは、それぞれの管理インターフェイスから管理する必要があります。
- デバイス ID 設定を構成する。
- Enterprise State Roaming を有効または無効にする。
- デバイス関連の監査ログを確認する

[![Azure portal の [すべてのデバイス] ビュー](./media/device-management-azure-portal/all-devices-azure-portal.png)](./media/device-management-azure-portal/all-devices-azure-portal.png#lightbox)

デバイス ポータルには、次の手順を使用してアクセスできます。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. **[Azure Active Directory]**  >  **[デバイス]** の順に移動します。

## <a name="manage-devices"></a>デバイスを管理する

Azure AD には、デバイスを管理するために次の 2 つの場所があります。

- **Azure portal** >  **[Azure Active Directory]**  >  **[デバイス]**
- **Azure portal** >  **[Azure Active Directory]**  >  **[ユーザー]** > ユーザーの選択 > **[デバイス]**

どちらのオプションでも、管理者は次のことを行うことができます。

- デバイスを検索する。
- 次のようなデバイスの詳細を表示する。
    - [デバイス名]
    - Device ID
    - OS とバージョン
    - 結合の種類
    - 所有者
    - モバイル デバイス管理とコンプライアンス
    - BitLocker 回復キー
- 有効化、無効化、削除、管理などのデバイス ID 管理タスクを実行する。
   - [プリンター](/universal-print/fundamentals/)および [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot) デバイスでは、Azure AD での管理オプションが制限されています。 これらは、それぞれの管理インターフェイスから管理する必要があります。

> [!TIP]
> - ハイブリッド Azure AD 参加済みの Windows 10 デバイスには、所有者がありません。 所有者でデバイスを検索していて、見つからなかった場合は、デバイス ID で検索してください。
>
> - [登録済み] 列の下の状態が "保留中" である "ハイブリッド Azure AD 参加済み" のデバイスが表示されている場合、そのデバイスが Azure AD 接続から同期されており、クライアントからの登録の完了を待機していることを示します。 [Hybrid Azure AD 参加の実装を計画する](hybrid-azuread-join-plan.md)方法について詳細を参照してください。 追加情報については、[デバイスについてよく寄せられる質問](faq.md)に関する記事を参照してください。
>
> - 一部の iOS デバイスの名前で使用されているアポストロフィは、アポストロフィに見える別の文字である可能性があります。 このため、このようなデバイスを検索するときは少し注意が必要です。正しい検索結果が表示されない場合は、検索文字列のアポストロフィが、正しいアポストロフィ文字であることを確認してください。

### <a name="manage-an-intune-device"></a>Intune デバイスの管理

Intune 管理者の場合は、MDM が **Microsoft Intune** とマークされているデバイスを管理することができます。 デバイスが Microsoft Intune に登録されていない場合、[管理] オプションは灰色で表示されます。

### <a name="enable-or-disable-an-azure-ad-device"></a>Azure AD デバイスを有効または無効にする

デバイスを有効または無効にするには、次の 2 つのオプションがあります。

- 1 つ以上のデバイスを選択した後に、 **[すべてのデバイス]** ページのツールバー。
- 特定のデバイスをドリルダウンした後にツールバー。

> [!IMPORTANT]
> - デバイスを有効または無効にするには、Azure AD の全体管理者またはクラウド デバイス管理者である必要があります。 
> - デバイスを無効にすると、デバイスは Azure AD で正常に認証されなくなるため、デバイスは、デバイスベースの条件付きアクセスによって、または Windows Hello for Business の資格情報を使用して保護されている Azure AD リソースにアクセスできなくなります。
> - デバイスを無効にすると、デバイス上のプライマリ更新トークン (PRT) とすべての更新トークン (RT) の両方が取り消されます。
> - プリンターは、Azure AD で有効または無効にすることはできません。

### <a name="delete-an-azure-ad-device"></a>Azure AD デバイスを削除する

デバイスを削除するには、次の 2 つのオプションがあります。

- 1 つ以上のデバイスを選択した後に、 **[すべてのデバイス]** ページのツールバー。
- 特定のデバイスをドリルダウンした後にツールバー。

> [!IMPORTANT]
> - デバイスを削除するには、Azure AD のクラウド デバイス管理者、Intune 管理者、または全体管理者ロールが割り当てられている必要があります。
> - プリンターと Windows Autopilot デバイスは、Azure AD では削除できません。
> - デバイスを削除する場合、以下の点に注意してください。
>    - デバイスは Azure AD リソースにアクセスできなくなります。
>    - Windows デバイスの BitLocker キーなど、デバイスに関連付けられているすべての詳細が削除されます。  
>    - 削除は回復不可能な操作であり、必須の場合以外は推奨されません。

デバイスが別の管理機関 (Microsoft Intune など) によって管理されている場合は、Azure AD でデバイスを削除する前に、デバイスがワイプ/使用中止されているかどうかを確認してください。 デバイスを削除する前に、[古いデバイスを管理する](manage-stale-devices.md)方法を確認してください。

### <a name="view-or-copy-device-id"></a>デバイス ID を表示またはコピーする

デバイス ID を使うと、デバイス上で、またはトラブルシューティング中に PowerShell を使用して、デバイス ID の詳細を確認することができます。 コピー オプションにアクセスするには、デバイスをクリックします。

![デバイス ID の表示](./media/device-management-azure-portal/35.png)
  
### <a name="view-or-copy-bitlocker-keys"></a>BitLocker キーを表示またはコピーする

BitLocker キーを表示およびコピーして、暗号化されたドライブをユーザーが復旧できるようにすることができます。 これらのキーを使用できるのは、暗号化され、キーが Azure AD に格納されている Windows デバイスだけです。 デバイスの詳細にアクセスするときにこれらのキーを見つけられるようにするには、 **[回復キーの表示]** を選択します。 **[回復キーの表示]** を選択すると、監査ログが生成されます。これは、`KeyManagement` カテゴリにあります。

![BitLocker キーの表示](./media/device-management-azure-portal/device-details-show-bitlocker-key.png)

BitLocker キーを表示またはコピーするには、デバイスの所有者であるか、次のロールが 1 つ以上割り当てられているユーザーである必要があります。

- クラウド デバイス管理者
- グローバル管理者
- ヘルプデスク管理者
- Intune サービス管理者
- セキュリティ管理者
- セキュリティ閲覧者

### <a name="device-list-filtering-preview"></a>デバイス一覧のフィルター処理 (プレビュー)

以前は、デバイスの一覧はアクティビティと有効な状態でのみフィルター処理できていました。 このプレビューでは、デバイスの次の属性によってデバイス一覧をフィルター処理できます。

- 有効な状態
- 準拠状態
- 参加の種類 (Azure AD 参加済み、ハイブリッド Azure AD 参加済み、Azure AD に登録済み)
- アクティビティ タイムスタンプ
- OS
- デバイスの種類 (プリンター、セキュリティで保護された VM、共有デバイス、登録済みのデバイス)

**[すべてのデバイス]** ビューのプレビュー フィルター機能を有効にするには、次のようにします。

![プレビュー フィルター機能を有効にする](./media/device-management-azure-portal/device-filter-preview-enable.png)

1. [Azure portal](https://portal.azure.com) にサインインします。
1. **[Azure Active Directory]**  >  **[デバイス]** の順に移動します。
1. **[新しいデバイスのフィルターの改善された機能をお試しください。クリックすると、プレビューが有効になります。]** というバナーを選択します。

**[すべてのデバイス]** ビューに **フィルターを追加** できるようになりました。

## <a name="configure-device-settings"></a>デバイス設定の構成

Azure AD ポータルを使ってデバイス ID を管理するには、それらのデバイスが Azure AD に[登録されているか参加している](overview.md)必要があります。 管理者は、次のデバイスの設定を構成することによって、デバイスの登録および参加のプロセスを制御できます。

Azure portal でデバイスの設定を表示または管理するには、次のいずれかのロールが割り当てられている必要があります。

- 全体管理者
- クラウド デバイス管理者
- グローバル閲覧者
- ディレクトリ閲覧者

![Azure AD に関連するデバイス設定](./media/device-management-azure-portal/device-settings-azure-portal.png)

- **[ユーザーはデバイスを Azure AD に参加させることができます]** - この設定を使用すると、Azure AD 参加済みデバイスとしてデバイスを登録できるユーザーを選択できます。 既定値は **[すべて]** です。

> [!NOTE]
> **[ユーザーはデバイスを Azure AD に参加させることができます]** 設定は、Windows 10 上の Azure AD 参加にのみ適用されます。 これらの方法はユーザーがいないコンテキストで機能するため、この設定は、ハイブリッド Azure AD 参加済みデバイス、[Azure 内の Azure AD 参加済み VM](./howto-vm-sign-in-azure-ad-windows.md#enabling-azure-ad-login-in-for-windows-vm-in-azure)、[Windows Autopilot の自己デプロイ モード](/mem/autopilot/self-deploying)を使用する Azure AD 参加済みデバイスには適用されません。

- **[Azure AD 参加済みデバイスの追加のローカル管理者]** - デバイスに対するローカル管理者権限が付与されるユーザーを選択できます。 これらのユーザーは、Azure AD の "*デバイス管理者*" ロールに追加されます。 Azure AD のグローバル管理者とデバイスの所有者には、既定でローカル管理者権限が付与されます。 このオプションは、Azure AD Premium や Enterprise Mobility Suite (EMS) などの製品を通じて使用できる Premium Edition 機能です。
- **[ユーザーはデバイスを Azure AD に登録できます]** - Windows 10 (個人用)、iOS、Android、および macOS デバイスを Azure AD に登録できるようにするには、この設定を構成する必要があります。 **[なし]** を選択した場合、デバイスは Azure AD に登録できません。 Microsoft Intune または Mobile Device Management (MDM) for Microsoft 365 への登録には、この登録が必要です。 これらのサービスのいずれかを構成した場合は、 **[すべて]** が選択され、 **[なし]** は選択できなくなります。
- **Azure AD 参加または Azure AD 登録となるデバイスには多要素認証が必須** - デバイスを Azure AD に参加させるか、登録するための追加認証要素を提供するよう、ユーザーに求めることを選択できます。 既定値は **No** です。 デバイスの登録または参加時に多要素認証を必要とすることをお勧めします。 このサービスの多要素認証を有効にする前に、デバイスを登録するユーザーに対して多要素認証が構成されていることを確認する必要があります。 他の Azure AD Multi-Factor Authentication サービスの詳細については、[Azure AD Multi-Factor Authentication の概要](../authentication/concept-mfa-howitworks.md)に関するページを参照してください。 

> [!NOTE]
> **Azure AD 参加または Azure AD 登録となるデバイスには多要素認証が必須** になるようにする設定は、Azure AD 参加 (一部の例外あり) または Azure AD 登録のデバイスに適用されます。 この設定は、Hybrid Azure AD Join を使用したデバイス、[Azure 内の Azure AD 参加済み VM](./howto-vm-sign-in-azure-ad-windows.md#enabling-azure-ad-login-in-for-windows-vm-in-azure)、および [Windows Autopilot の自己デプロイ モード](/mem/autopilot/self-deploying)を使用する Azure AD 参加済みデバイスには適用されません。

- **[デバイスの最大数]** - この設定では、Azure AD でユーザーが持つことができる、Azure AD に参加しているか Azure AD に登録されているデバイスの最大数を選択できます。 ユーザーがこのクォータに達した場合、1 つ以上の既存のデバイスを削除するまでデバイスを追加できなくなります。 既定値は **50** です。

> [!NOTE]
> **[デバイスの最大数]** 設定は、Azure AD に参加しているか Azure AD に登録されているデバイスに適用されます。 この設定は、ハイブリッド Azure AD 参加済みデバイスには適用されません。

- [Enterprise State Roaming](enterprise-state-roaming-overview.md)

## <a name="audit-logs"></a>監査ログ

デバイスのアクティビティは、アクティビティ ログを通じて利用できます。 これらのログには、デバイス登録サービスおよびユーザーによってトリガーされる、次のようなアクティビティが含まれます。

- デバイスの作成と、デバイスへの所有者/ユーザーの追加
- デバイス設定への変更
- デバイスの削除や更新などのデバイス操作

監査データへのエントリ ポイントは、 **[デバイス]** ページの **[アクティビティ]** セクションの **[監査ログ]** です。

監査ログには、次のものを示す既定のリスト ビューがあります。

- 発生の日付と時刻
- ターゲット
- アクティビティのイニシエーターまたはアクター ("だれが" を指す)
- アクティビティ ("どうした" を指す)

:::image type="content" source="./media/device-management-azure-portal/63.png" alt-text="[デバイス] ページの [アクティビティ] セクションにあるテーブルのスクリーンショット。4 つの監査ログの日付、ターゲット、アクター、およびアクティビティが一覧表示されている。" border="false":::

リスト ビューをカスタマイズするには、ツール バーの **[列]** をクリックします。

:::image type="content" source="./media/device-management-azure-portal/64.png" alt-text="[デバイス] ページのツールバーを示しているスクリーンショット。[列] 項目が強調表示されている。" border="false":::

報告されるデータを有用なものだけに絞り込むために、次のフィールドを使用して監査データをフィルター処理できます。

- カテゴリ
- アクティビティのリソースの種類
- アクティビティ
- 期間
- 移行先
- 開始者 (アクター)

フィルターだけでなく、特定のエントリを検索することができます。

:::image type="content" source="./media/device-management-azure-portal/65.png" alt-text="監査データのフィルター コントロールのスクリーンショット。[カテゴリ]、[アクティビティのリソースの種類]、[アクティビティ]、[期間]、[移行先]、[アクター] の各フィールドと、検索フィールドが表示されている。" border="false":::

## <a name="next-steps"></a>次のステップ

[方法: Azure AD で古いデバイスを管理する](manage-stale-devices.md)

[Enterprise State Roaming](enterprise-state-roaming-overview.md)
