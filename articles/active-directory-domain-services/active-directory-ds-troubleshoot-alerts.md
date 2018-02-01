---
title: "Azure Active Directory Domain Services: アラートのトラブルシューティング | Microsoft Docs"
description: "Azure AD Domain Services のアラートのトラブルシューティング"
services: active-directory-ds
documentationcenter: 
author: eringreenlee
manager: 
editor: 
ms.assetid: 54319292-6aa0-4a08-846b-e3c53ecca483
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: ergreenl
ms.openlocfilehash: b2e0edf3588f3b1db5f4b6641019be1ded9cb50e
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/18/2018
---
# <a name="azure-ad-domain-services---troubleshoot-alerts"></a>Azure AD Domain Services - アラートのトラブルシューティング
この記事では、管理対象ドメインで発生する可能性があるすべてのアラート向けのトラブルシューティング ガイドを提供します。


表示されたアラート ID またはメッセージに対応するトラブルシューティング手順を選択してください。

| **アラート ID** | **アラート メッセージ** | **解決策** |
| --- | --- | :--- |
| AADDS001 | "*管理対象ドメインに対してインターネット経由のセキュリティで Secure LDAP が有効になっています。ただし、ポート 636 へのアクセスがネットワーク セキュリティ グループを使用してロックダウンされていません。これにより、管理対象ドメインのユーザー アカウントがパスワードの総当り攻撃の対象になる可能性があります。*" | [不適切なセキュリティで保護された LDAP 構成](active-directory-ds-troubleshoot-ldaps.md) |
| AADDS100 | "*管理対象ドメインに関連付けられている Azure AD ディレクトリが削除されている可能性があります。管理対象ドメインはもうサポートされる構成に含まれていません。Microsoft は、管理対象ドメインに対する監視、管理、修正プログラムの適用、および同期を実行できません。*" | [不足しているディレクトリ](#aadds100-missing-directory) |
| AADDS101 | "*Azure AD B2C ディレクトリで Azure AD Domain Services を有効にできません。*" | [このディレクトリで Azure AD B2C が実行されている](#aadds101-azure-ad-b2c-is-running-in-this-directory) |
| AADDS102 | "*Azure AD Domain Services が正常に機能するために必要なサービス プリンシパルが Azure AD ディレクトリから削除されています。この構成は、管理対象ドメインに対する監視、管理、修正のプログラム適用、および同期を行うための Microsoft の能力に影響します。*" | [不足しているサービス プリンシパル](active-directory-ds-troubleshoot-service-principals.md) |
| AADDS103 | "*Azure AD Domain Services を有効にしている仮想ネットワークの IP アドレス範囲がパブリック IP 範囲内にあります。Azure AD Domain Services は、プライベート IP アドレス範囲にある仮想ネットワークで有効にする必要があります。この構成は、管理対象ドメインに対する監視、管理、修正のプログラム適用、および同期を行うための Microsoft の能力に影響します。*" | [アドレスがパブリック IP 範囲内である](#aadds103-address-is-in-a-public-ip-range) |
| AADDS104 | "*この管理対象ドメインのドメイン コントローラーに到達できません。これは、仮想ネットワークに構成されているネットワーク セキュリティ グループ (NSG) が管理対象ドメインへのアクセスをブロックしている場合に発生する可能性があります。他の理由として、インターネットからの着信トラフィックをブロックしているユーザー定義ルートが存在する可能性があります。*" | [ネットワーク エラー](active-directory-ds-troubleshoot-nsg.md) |

## <a name="aadds100-missing-directory"></a>AADDS100: 不足しているディレクトリ
**アラート メッセージ:**

"*管理対象ドメインに関連付けられている Azure AD ディレクトリが削除されている可能性があります。管理対象ドメインはもうサポートされる構成に含まれていません。Microsoft は、管理対象ドメインに対する監視、管理、修正プログラムの適用、および同期を実行できません。*"

**解決策:**

通常、このエラーは、Azure サブスクリプションを新しい Azure AD ディレクトリに移動し、まだ Azure AD Domain Services に関連付けられている古い Azure AD ディレクトリを削除したことによって発生します。

このエラーは回復できません。 解決するには、[既存の管理対象ドメインを削除](active-directory-ds-disable-aadds.md)し、新しいディレクトリ内に再作成する必要があります。 削除で問題が発生した場合は、Azure Active Directory Domain Services 製品チームに[お問い合わせ](active-directory-ds-contact-us.md)ください。

## <a name="aadds101-azure-ad-b2c-is-running-in-this-directory"></a>AADDS101: このディレクトリで Azure AD B2C が実行されている
**アラート メッセージ:**

"*Azure AD B2C ディレクトリで Azure AD Domain Services を有効にできません。*"

**解決策:**

>[!NOTE]
>Azure AD Domain Services の使用を続行するには、Azure AD B2C 以外のディレクトリで Azure AD Domain Services インスタンスを再作成する必要があります。

サービスを復元するには、次の手順に従います。

1. 既存の Azure AD ディレクトリから[管理対象ドメインを削除](active-directory-ds-disable-aadds.md)します。
2. Azure AD B2C 以外の新しいディレクトリを作成します。
3. [使用の開始](active-directory-ds-getting-started.md)ガイドに従って、管理対象ドメインを再作成します。

## <a name="aadds103-address-is-in-a-public-ip-range"></a>AADDS103: アドレスがパブリック IP 範囲内である

**アラート メッセージ:**

"*Azure AD Domain Services を有効にしている仮想ネットワークの IP アドレス範囲がパブリック IP 範囲内にあります。Azure AD Domain Services は、プライベート IP アドレス範囲にある仮想ネットワークで有効にする必要があります。この構成は、管理対象ドメインに対する監視、管理、修正のプログラム適用、および同期を行うための Microsoft の能力に影響します。*"

**解決策:**

> [!NOTE]
> この問題に対処するには、既存の管理対象ドメインを削除し、プライベート IP アドレス範囲にある仮想ネットワーク内に再作成します。 このプロセスは中断を伴います。

開始する前に、[こちらの記事](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces)の「**private IP v4 address space**」(プライベート IP v4 アドレス空間) セクションを参照してください。

1. ディレクトリから[管理対象ドメインを削除](active-directory-ds-disable-aadds.md)します。
2. サブネットの IP アドレス範囲を修正します。
  1. [Azure Portal の [仮想マシン] ページ](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_AAD_DomainServices=preview#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FvirtualNetworks)に移動します。
  2. Azure AD Domain Services で使用する予定の仮想ネットワークを選択します。
  3. [設定] の下の **[アドレス空間]** をクリックします。
  4. 既存のアドレス範囲をクリックし、それを編集するかアドレス範囲を追加することでアドレス範囲を更新します。 新しいアドレス範囲がプライベート IP 範囲にあることを確認します。 変更を保存します。
  5. 左側のナビゲーション メニューで、**[サブネット]** をクリックします。
  6. テーブルの編集するサブネットをクリックします。
  7. アドレス範囲を更新し、変更を保存します。
3. [Azure AD Domain Services の使用開始に関するガイド](active-directory-ds-getting-started.md)に従って、管理対象ドメインを再作成します。 プライベート IP アドレス範囲にある仮想ネットワークを選択していることを確認します。
4. 仮想マシンを新しいドメインに参加させるには、[こちらのガイド](active-directory-ds-admin-guide-join-windows-vm-portal.md)に従います。
8. 手順を正しく完了したことを確認するには、2 時間後にドメインの正常性をチェックします。


## <a name="contact-us"></a>お問い合わせ
[フィードバックの共有およびサポートについては](active-directory-ds-contact-us.md)、Azure Active Directory Domain Services 製品チームにお問い合わせください。
