---
title: Azure portal を使用して Azure AD のデバイスを管理する
description: この記事では、Azure portal を使用してデバイスの ID を管理し、関連するイベント情報を監視する方法について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 10/14/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: hafowler
ms.collection: M365-identity-device-management
ms.openlocfilehash: c9cdb24ac332530a8294cd6a39b5fe58ab2727ed
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131049940"
---
# <a name="manage-device-identities-by-using-the-azure-portal"></a>Azure portal を使用してデバイス ID を管理する

Azure Active Directory (Azure AD) は、デバイス ID を一元的に管理し、関連するイベント情報を監視するための場所を提供します。

[![デバイスの概要を示している Azure portal のスクリーンショット。](./media/device-management-azure-portal/devices-azure-portal.png)](./media/device-management-azure-portal/devices-azure-portal.png#lightbox)

デバイスの概要にアクセスするには、次の手順を実行します。

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. **[Azure Active Directory]**  >  **[デバイス]** の順に移動します。

デバイスの概要から、すべてのデバイス、古くなったデバイス、非準拠デバイス、およびアンマネージド デバイスの数を確認できます。 また、Intune、条件付きアクセス、BitLocker キー、および基本的な監視へのリンクにアクセスすることもできます。 

概要ページのデバイス数はリアルタイムでは更新されません。 変更は数時間ごとに反映されます。

そこから、 **[すべてのデバイス]** に移動すると、以下を実行することができます。

- 次のようなデバイスを識別する。
   - Azure AD に参加または登録しているデバイス。
   - [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot) でデプロイされたデバイス。
   - [ユニバーサル印刷](/universal-print/fundamentals/universal-print-getting-started)を使用するプリンター。
- 有効化、無効化、削除、管理などのデバイス ID 管理タスクを実行する。
   - [プリンター](/universal-print/fundamentals/)および [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot) の管理オプションは、Azure AD では制限されています。 これらのデバイスは、それぞれの管理インターフェイスから管理する必要があります。
- デバイス ID 設定を構成する。
- Enterprise State Roaming を有効または無効にする。
- デバイス関連の監査ログを確認する。
- デバイスをダウンロードする (プレビュー)。

[![[すべてのデバイス] ビューを示している Azure portal のスクリーンショット。](./media/device-management-azure-portal/all-devices-azure-portal.png)](./media/device-management-azure-portal/all-devices-azure-portal.png#lightbox)

> [!TIP]
> - Hybrid Azure AD 参加済みの Windows 10 デバイスには、所有者はいません。 所有者でデバイスを検索しても見つからなかった場合は、デバイス ID で検索してください。
>
> - **[登録済み]** 列の下の状態が **[保留中]** の **[Hybrid Azure AD 参加済み]** のデバイスが表示されている場合、これは、そのデバイスが Azure AD 接続から同期されており、クライアントからの登録の完了を待機していることを示します。 詳細については、[Hybrid Azure AD 参加の実装を計画する方法](hybrid-azuread-join-plan.md)に関するページを参照してください。 詳細については、[デバイス管理のよく寄せられる質問](faq.yml)に関するページを参照してください。
>
> - 一部の iOS デバイスでは、アポストロフィーを含むデバイス名に、アポストロフィーに似た別の文字が使われていることがあります。 そのため、そのようなデバイスの検索には少し注意が必要になります。 検索結果が正しく表示されない場合は、検索文字列に一致するアポストロフィー文字が含まれていることを確認してください。

## <a name="manage-an-intune-device"></a>Intune デバイスの管理

Intune でデバイスを管理する権限がある場合は、モバイル デバイス管理で **Microsoft Intune** と表示されているデバイスを管理できます。 デバイスが Microsoft Intune に登録されていない場合、 **[管理]** オプションは利用できません。

## <a name="enable-or-disable-an-azure-ad-device"></a>Azure AD デバイスを有効または無効にする

デバイスを有効または無効にするには、次の 2 つの方法があります。

- 1 つまたは複数のデバイスを選択した後の、 **[すべてのデバイス]** ページのツールバー。
- 特定のデバイスをドリルダウンした後のツール バー。

> [!IMPORTANT]
> - デバイスを有効または無効にするには、Azure AD の全体管理者、Intune 管理者、またはクラウド デバイス管理者である必要があります。 
> - デバイスを無効にすると、Azure AD を介して認証することができなくなります。 これにより、デバイスベースの条件付きアクセスによって保護されている Azure AD リソースへのアクセスや、Windows Hello for Business の資格情報の使用ができなくなります。
> - デバイスを無効にすると、デバイス上のプライマリ更新トークン (PRT) とすべての更新トークンが取り消されます。
> - プリンターは、Azure AD で有効または無効にすることはできません。

## <a name="delete-an-azure-ad-device"></a>Azure AD デバイスを削除する

デバイスを削除する方法は 2 つあります。

- 1 つまたは複数のデバイスを選択した後の、 **[すべてのデバイス]** ページのツールバー。
- 特定のデバイスをドリルダウンした後のツール バー。

> [!IMPORTANT]
> - デバイスを削除するには、Azure AD のクラウド デバイス管理者、Intune 管理者、または全体管理者である必要があります。
> - プリンターと Windows Autopilot デバイスは、Azure AD では削除できません。
> - デバイスを削除する場合、以下の点に注意してください。
>    - Azure AD リソースにアクセスできなくなります。
>    - デバイスに関連付けられているすべての詳細が削除されます。 たとえば、Windows デバイスの BitLocker キーなどです。  
>    - これは、回復不可能なアクティビティです。 必要な場合を除き、推奨されていません。

デバイスが他の管理機関 (Microsoft Intune など) によって管理されている場合は、そのデバイスが削除または廃止されていることを確認してから削除するようにします。 デバイスを削除する前に、[古くなったデバイスを管理する](manage-stale-devices.md)方法に関するページを参照してください。

## <a name="view-or-copy-a-device-id"></a>デバイス ID を表示またはコピーする

デバイス ID を使うと、デバイス上で、または PowerShell を使用してトラブルシューティングを行う際に、デバイス ID の詳細を確認することができます。 コピー オプションにアクセスするには、デバイスを選択します。

![デバイス ID とコピー ボタンを示すスクリーンショット。](./media/device-management-azure-portal/35.png)
  
## <a name="view-or-copy-bitlocker-keys"></a>BitLocker キーを表示またはコピーする

BitLocker キーを表示およびコピーして、暗号化されたドライブをユーザーが復旧できるようにすることができます。 これらのキーは、暗号化され、Azure AD にキーが保存されている Windows デバイスでのみ利用できます。 デバイスの詳細を表示するときにこれらのキーを見つけられるようにするには、 **[回復キーの表示]** を選択します。 **[回復キーの表示]** を選択すると、監査ログが生成されます。これは、`KeyManagement` カテゴリにあります。

![BitLocker キーを表示する方法を示すスクリーンショット。](./media/device-management-azure-portal/device-details-show-bitlocker-key.png)

BitLocker キーを表示またはコピーするには、デバイスの所有者であるか、次のロールのいずれかが割り当てられている必要があります。

- クラウド デバイス管理者
- グローバル管理者
- ヘルプデスク管理者
- Intune サービス管理者
- セキュリティ管理者
- セキュリティ閲覧者

## <a name="device-list-filtering-preview"></a>デバイス一覧のフィルター処理 (プレビュー)

これまでは、デバイスの一覧はアクティビティと有効な状態でのみ、フィルター処理することができました。 このプレビューでは、デバイスの一覧を次のデバイス属性でフィルター処理できます。

- 有効な状態
- 準拠状態
- 参加の種類 (Azure AD 参加済み、ハイブリッド Azure AD 参加済み、Azure AD に登録済み)
- アクティビティ タイムスタンプ
- OS
- デバイスの種類 (プリンター、セキュリティで保護された VM、共有デバイス、登録済みデバイス)

**[すべてのデバイス]** ビューのプレビュー フィルター機能を有効にするには、次のようにします。

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. **[Azure Active Directory]**  >  **[デバイス]** の順に移動します。
1. **新しいデバイスのフィルターの改善された機能をお試しください。クリックすると、プレビューが有効になります。** と書かれているバナーを選択します。

   ![プレビュー フィルター機能を有効にする](./media/device-management-azure-portal/device-filter-preview-enable.png)

これで、 **[すべてのデバイス]** ビューにフィルターを追加できるようになりました。

## <a name="download-devices-preview"></a>デバイスをダウンロードする (プレビュー)

クラウド デバイス管理者、Intune 管理者、および全体管理者は、 **[Download devices (preview)]\(デバイスのダウンロード (プレビュー)\)** オプションを使用して、デバイスの一覧の CSV ファイルをエクスポートできます。 フィルターを適用して、一覧表示するデバイスを決定できます。 フィルターを適用しない場合は、すべてのデバイスが一覧表示されます。 エクスポート タスクは、選択内容によっては 1 時間ほど実行されることがあります。

エクスポートされた一覧には、次のデバイス ID 属性が含まれています。

`accountEnabled, approximateLastLogonTimeStamp, deviceOSType, deviceOSVersion, deviceTrustType, dirSyncEnabled, displayName, isCompliant, isManaged, lastDirSyncTime, objectId, profileType, registeredOwners, systemLabels, registrationTime, mdmDisplayName`

## <a name="configure-device-settings"></a>デバイス設定の構成

Azure portal を使用してデバイス ID を管理するには、それらのデバイスが Azure AD に[登録されているか参加している](overview.md)必要があります。 管理者は、次のデバイスの設定を構成することによって、デバイスの登録および参加のプロセスを制御できます。

Azure portal でデバイスの設定を表示または管理するには、次のいずれかのロールが割り当てられている必要があります。

- グローバル管理者
- クラウド デバイス管理者
- グローバル閲覧者
- ディレクトリ閲覧者

![Azure AD に関連するデバイス設定を示すスクリーンショット。](./media/device-management-azure-portal/device-settings-azure-portal.png)

- **[ユーザーはデバイスを Azure AD に参加させることができます]** : この設定を使用すると、Azure AD 参加済みデバイスとしてデバイスを登録できるユーザーを選択できます。 既定値は **[すべて]** です。

   > [!NOTE]
   > **[ユーザーはデバイスを Azure AD に参加させることができます]** という設定は、Windows 10 上の Azure AD 参加にのみ適用されます。 これらの方法はユーザーがいないコンテキストで機能するため、この設定は、ハイブリッド Azure AD 参加済みデバイス、[Azure 内の Azure AD 参加済み VM](./howto-vm-sign-in-azure-ad-windows.md#enabling-azure-ad-login-in-for-windows-vm-in-azure)、または [Windows Autopilot の自己デプロイ モード](/mem/autopilot/self-deploying)を使用する Azure AD 参加済みデバイスには適用されません。

- **[Azure AD 参加済みデバイスの追加のローカル管理者]** : この設定を使用すると、デバイスに対するローカル管理者権限を付与するユーザーを選択できます。 これらのユーザーは、Azure AD の "デバイス管理者" ロールに追加されます。 Azure AD のグローバル管理者とデバイスの所有者には、既定でローカル管理者権限が付与されます。 このオプションは、Azure AD Premium や Enterprise Mobility + Security などの製品を通じて使用できる Premium Edition 機能です。
- **[ユーザーはデバイスを Azure AD に登録できます]** : ユーザーが Windows 10 (個人用)、iOS、Android、および macOS デバイスを Azure AD に登録できるようにするには、この設定を構成する必要があります。 **[なし]** を選択した場合、デバイスは Azure AD に登録できません。 Microsoft Intune または Microsoft 365 のモバイル デバイス管理への登録には、この登録が必要です。 これらのサービスのいずれかを構成した場合は、 **[すべて]** が選択され、 **[なし]** は選択できなくなります。
- **[Azure AD を使用してデバイスを登録または参加させるには Multi-Factor Authentication が必要です]** : この設定を使用すると、デバイスを Azure AD に参加させる、または登録する際に、追加の認証要素を提供するようユーザーに求めるかどうかを選択できます。 既定値は **No** です。 デバイスを登録または参加させる場合は、多要素認証を要求することをお勧めします。 このサービスの多要素認証を有効にする前に、デバイスを登録するユーザーに対して多要素認証が構成されていることを確認する必要があります。 Azure AD Multi-Factor Authentication サービスの詳細については、[Azure AD Multi-Factor Authentication の概要](../authentication/concept-mfa-howitworks.md)に関するページを参照してください。 

   > [!NOTE]
   > **[Azure AD を使用してデバイスを登録または参加させるには Multi-Factor Authentication が必要です]** 設定は、Azure AD 参加 (一部の例外あり) または Azure AD 登録のデバイスに適用されます。 この設定は、Hybrid Azure AD Join を使用したデバイス、[Azure 内の Azure AD 参加済み VM](./howto-vm-sign-in-azure-ad-windows.md#enabling-azure-ad-login-in-for-windows-vm-in-azure)、または [Windows Autopilot の自己デプロイ モード](/mem/autopilot/self-deploying)を使用する Azure AD 参加済みデバイスには適用されません。

   > [!IMPORTANT]
   > - デバイスの参加または登録に多要素認証を強制する目的で、条件付きアクセスで[デバイスの登録または参加のユーザー](../conditional-access/concept-conditional-access-cloud-apps.md#user-actions) アクションを使用することをお勧めします。 
   > - 条件付きアクセス ポリシーを使用して多要素認証を要求する場合、この設定を **[いいえ]** に構成する必要があります。 

- **[デバイスの最大数]** : この設定を使用すると、Azure AD でユーザーが持つことができる、Azure AD に参加しているか Azure AD に登録されているデバイスの最大数を選択できます。 ユーザーがこの制限に達した場合、1 つ以上の既存のデバイスを削除するまでデバイスを追加できなくなります。 既定値は **50** です。 値は、最大 100 まで増やすことができます。 100 より大きな値を入力すると、Azure AD によって 100 に設定されます。 また、 **[無制限]** を使用すると、既存のクォータ制限以外に制限をかけないようにすることができます。

   > [!NOTE]
   > **[デバイスの最大数]** 設定は、Azure AD に参加しているか Azure AD に登録されているデバイスに適用されます。 この設定は、Hybrid Azure AD Join を使用したデバイスには適用されません。

- **Enterprise State Roaming**: この設定の詳細については、[概要に関する記事](enterprise-state-roaming-overview.md)を参照してください。

## <a name="audit-logs"></a>監査ログ

デバイス アクティビティは、アクティビティ ログに表示されます。 これらのログには、デバイス登録サービスおよびユーザーによってトリガーされる、次のようなアクティビティが含まれます。

- デバイスの作成と、デバイスへの所有者/ユーザーの追加
- デバイス設定への変更
- デバイスの削除や更新などのデバイス操作

監査データへのエントリ ポイントは、 **[デバイス]** ページの **[アクティビティ]** セクションの **[監査ログ]** です。

監査ログには、次のものを示す既定のリスト ビューがあります。

- 発生の日付と時刻。
- ターゲット。
- アクティビティのイニシエーターまたはアクター。
- アクティビティ。

:::image type="content" source="./media/device-management-azure-portal/63.png" alt-text="[デバイス] ページの [アクティビティ] セクションにあるテーブルのスクリーンショット。表には、4 つの監査ログの日付、ターゲット、アクター、およびアクティビティが一覧表示されています。" border="false":::

ツール バーの **[列]** を選択することで、リスト ビューをカスタマイズできます。

:::image type="content" source="./media/device-management-azure-portal/64.png" alt-text="[デバイス] ページのツール バーを示しているスクリーンショット。" border="false":::

報告されたデータを自分に合ったレベルに減らすには、次のフィールドを使用してフィルター処理を行います。

- **カテゴリ**
- **アクティビティのリソースの種類**
- **アクティビティ**
- **日付範囲**
- **移行先**
- **開始者 (アクター)**

特定のエントリを検索することもできます。

:::image type="content" source="./media/device-management-azure-portal/65.png" alt-text="監査データのフィルター処理コントロールを示すスクリーンショット。" border="false":::

## <a name="next-steps"></a>次のステップ

- [方法: Azure AD で古いデバイスを管理する](manage-stale-devices.md)
- [保留中のデバイス状態のトラブルシューティング](/troubleshoot/azure/active-directory/pending-devices)
