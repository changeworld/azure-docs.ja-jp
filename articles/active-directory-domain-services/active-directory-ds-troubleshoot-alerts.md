---
title: 'Azure Active Directory Domain Services: アラートのトラブルシューティング | Microsoft Docs'
description: Azure AD Domain Services のアラートのトラブルシューティング
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: ''
editor: ''
ms.assetid: 54319292-6aa0-4a08-846b-e3c53ecca483
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/28/2018
ms.author: ergreenl
ms.openlocfilehash: 6be67a92c95ccf1161ffeeb636ee4f998c65fa05
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2018
ms.locfileid: "39503691"
---
# <a name="azure-ad-domain-services---troubleshoot-alerts"></a>Azure AD Domain Services - アラートのトラブルシューティング
この記事では、マネージド ドメインで発生する可能性があるすべてのアラート向けのトラブルシューティング ガイドを提供します。


アラートの ID またはメッセージに対応するトラブルシューティング手順を選択してください。

| **アラート ID** | **アラート メッセージ** | **解決策** |
| --- | --- | :--- |
| AADDS001 | "*マネージド ドメインに対してインターネット経由のセキュリティで Secure LDAP が有効になっています。ただし、ポート 636 へのアクセスはネットワーク セキュリティ グループを使用してロックダウンされていません。これにより、マネージド ドメインのユーザー アカウントがパスワードの総当り攻撃の対象になる可能性があります。*" | [不適切なセキュリティで保護された LDAP 構成](active-directory-ds-troubleshoot-ldaps.md) |
| AADDS100 | "*マネージド ドメインに関連付けられている Azure AD ディレクトリが削除されている可能性があります。マネージド ドメインはもうサポートされる構成に含まれていません。Microsoft は、マネージド ドメインに対する監視、管理、修正プログラムの適用、および同期を実行できません。*" | [不足しているディレクトリ](#aadds100-missing-directory) |
| AADDS101 | "*Azure AD B2C ディレクトリで Azure AD Domain Services を有効にできません。*" | [このディレクトリで Azure AD B2C が実行されている](#aadds101-azure-ad-b2c-is-running-in-this-directory) |
| AADDS102 | "*Azure AD Domain Services が正常に機能するために必要なサービス プリンシパルが Azure AD ディレクトリから削除されています。この構成は、マネージド ドメインに対する監視、管理、修正のプログラム適用、および同期を行うための Microsoft の能力に影響します。*" | [不足しているサービス プリンシパル](active-directory-ds-troubleshoot-service-principals.md) |
| AADDS103 | "*Azure AD Domain Services を有効にしている仮想ネットワークの IP アドレス範囲がパブリック IP 範囲内にあります。Azure AD Domain Services は、プライベート IP アドレス範囲にある仮想ネットワークで有効にする必要があります。この構成は、マネージド ドメインに対する監視、管理、修正のプログラム適用、および同期を行うための Microsoft の能力に影響します。*" | [アドレスがパブリック IP 範囲内である](#aadds103-address-is-in-a-public-ip-range) |
| AADDS104 | "*このマネージド ドメインのドメイン コントローラーに到達できません。これは、仮想ネットワークに構成されているネットワーク セキュリティ グループ (NSG) がマネージド ドメインへのアクセスをブロックしている場合に発生する可能性があります。他の理由として、インターネットからの着信トラフィックをブロックしているユーザー定義ルートが存在する可能性があります。*" | [ネットワーク エラー](active-directory-ds-troubleshoot-nsg.md) |
| AADDS105 | *アプリケーション ID "d87dcbc6-a371-462e-88e3-28ad15ec4e64" のサービス プリンシパルは削除されましたが、その後再作成されました。The recreation leaves behind inconsistent permissions on Azure AD Domain Services resources needed to service your managed domain. (再作成により、マネージド ドメインのサービスに必要な Azure AD Domain Services リソースに整合性のないアクセス許可が残っています。)Synchronization of passwords on your managed domain could be affected. (マネージド ドメインでのパスワードの同期が影響を受ける可能性があります。)* | [パスワード同期アプリケーションが古い](active-directory-ds-troubleshoot-service-principals.md#alert-aadds105-password-synchronization-application-is-out-of-date) |
| AADDS500 | "*マネージド ドメインが Azure AD と最後に同期されたのは [date] です。ユーザーがマネージド ドメインでサインインできない、またはグループ メンバーシップが Azure AD と同期されていない可能性があります。*" | [同期がしばらく行われていない](#aadds500-synchronization-has-not-completed-in-a-while) |
| AADDS501 | "*マネージド ドメインが最後にバックアップされたのは [date] です。*" | [バックアップがしばらく行われていない](#aadds501-a-backup-has-not-been-taken-in-a-while) |
| AADDS502 | *マネージド ドメインのセキュリティで保護された LDAP 証明は[日付] に有効期限が切れます。* | [セキュリティで保護された LDAP 証明書の期限切れ間近](active-directory-ds-troubleshoot-ldaps.md#aadds502-secure-ldap-certificate-expiring) |
| AADDS503 | "*マネージド ドメインは、ドメインに関連付けられている Azure サブスクリプションがアクティブでないため中断されます。*" | [無効にしたサブスクリプションが原因の中断](#aadds503-suspension-due-to-disabled-subscription) |
| AADDS504 | "*マネージド ドメインは、無効な構成により中断されます。サービスは、マネージド ドメインのドメイン コントローラーの管理、パッチ適用、または更新を長い間行うことができませんでした。*" | [無効な構成が原因の中断](#aadds504-suspension-due-to-an-invalid-configuration) |


## <a name="aadds100-missing-directory"></a>AADDS100: 不足しているディレクトリ
**アラート メッセージ:**

"*マネージド ドメインに関連付けられている Azure AD ディレクトリが削除されている可能性があります。マネージド ドメインはもうサポートされる構成に含まれていません。Microsoft は、マネージド ドメインに対する監視、管理、修正プログラムの適用、および同期を実行できません。*"

**解決策:**

通常、このエラーは、Azure サブスクリプションを新しい Azure AD ディレクトリに移動し、まだ Azure AD Domain Services に関連付けられている古い Azure AD ディレクトリを削除したことによって発生します。

このエラーは回復できません。 解決するには、[既存のマネージド ドメインを削除](active-directory-ds-disable-aadds.md)し、新しいディレクトリ内に再作成する必要があります。 削除で問題が発生した場合は、Azure Active Directory Domain Services 製品チームに[お問い合わせ](active-directory-ds-contact-us.md)ください。

## <a name="aadds101-azure-ad-b2c-is-running-in-this-directory"></a>AADDS101: このディレクトリで Azure AD B2C が実行されている
**アラート メッセージ:**

"*Azure AD B2C ディレクトリで Azure AD Domain Services を有効にできません。*"

**解決策:**

>[!NOTE]
>Azure AD Domain Services の使用を続行するには、Azure AD B2C 以外のディレクトリで Azure AD Domain Services インスタンスを再作成する必要があります。

サービスを復元するには、次の手順に従います。

1. 既存の Azure AD ディレクトリから[マネージド ドメインを削除](active-directory-ds-disable-aadds.md)します。
2. Azure AD B2C 以外の新しいディレクトリを作成します。
3. 
  [使用の開始](active-directory-ds-getting-started.md)ガイドに従って、マネージド ドメインを再作成します。

## <a name="aadds103-address-is-in-a-public-ip-range"></a>AADDS103: アドレスがパブリック IP 範囲内である

**アラート メッセージ:**

"*Azure AD Domain Services を有効にしている仮想ネットワークの IP アドレス範囲がパブリック IP 範囲内にあります。Azure AD Domain Services は、プライベート IP アドレス範囲にある仮想ネットワークで有効にする必要があります。この構成は、マネージド ドメインに対する監視、管理、修正のプログラム適用、および同期を行うための Microsoft の能力に影響します。*"

**解決策:**

> [!NOTE]
> この問題に対処するには、既存のマネージド ドメインを削除し、プライベート IP アドレス範囲にある仮想ネットワーク内に再作成します。 このプロセスは中断を伴います。

開始する前に、[こちらの記事](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces)の「**private IP v4 address space**」(プライベート IP v4 アドレス空間) セクションを参照してください。

仮想ネットワーク内では、マシンが、サブネット用に構成されたものと同じ IP アドレス範囲内にある Azure リソースを要求することがあります。 ただし、仮想ネットワークはこの範囲に構成されているため、これらの要求は仮想ネットワーク内でルーティングされ、目的の Web リソースに到達しません。 この構成により、Azure AD Domain Services で予期しないエラーが発生する可能性があります。

**仮想ネットワークに構成されているインターネットの IP アドレス範囲を保有している場合は、このアラートを無視できます。ただし、予期しないエラーが発生する可能性があるため、Azure AD Domain Services は、この構成で [SLA](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/)] にコミットすることはできません。**


1. ディレクトリから[マネージド ドメインを削除](active-directory-ds-disable-aadds.md)します。
2. サブネットの IP アドレス範囲を修正します。
  1. [Azure Portal の [仮想マシン] ページ](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_AAD_DomainServices=preview#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FvirtualNetworks)に移動します。
  2. Azure AD Domain Services で使用する予定の仮想ネットワークを選択します。
  3. [設定] の下の **[アドレス空間]** をクリックします。
  4. 既存のアドレス範囲をクリックし、それを編集するかアドレス範囲を追加することでアドレス範囲を更新します。 新しいアドレス範囲がプライベート IP 範囲にあることを確認します。 変更を保存します。
  5. 左側のナビゲーション メニューで、**[サブネット]** をクリックします。
  6. テーブルの編集するサブネットをクリックします。
  7. アドレス範囲を更新し、変更を保存します。
3. 
  [Azure AD Domain Services の使用開始に関するガイド](active-directory-ds-getting-started.md)に従って、マネージド ドメインを再作成します。 プライベート IP アドレス範囲にある仮想ネットワークを選択していることを確認します。
4. 仮想マシンを新しいドメインに参加させるには、[こちらのガイド](active-directory-ds-admin-guide-join-windows-vm-portal.md)に従います。
8. アラートが解決されたことを確認するために、ドメインの正常性を 2 時間以内に確認します。

## <a name="aadds500-synchronization-has-not-completed-in-a-while"></a>AADDS500: 同期がしばらく行われていない

**アラート メッセージ:**

"*マネージド ドメインが Azure AD と最後に同期されたのは [date] です。ユーザーがマネージド ドメインでサインインできない、またはグループ メンバーシップが Azure AD と同期されていない可能性があります。*"

**解決策:**


  [ご利用のドメインの正常性](active-directory-ds-check-health.md)で、マネージド ドメインの構成の問題を示している可能性があるアラートをすべて確認します。 構成の問題によって、マネージド ドメインの同期を行うための Microsoft の機能がブロックされることがあります。 アラートを解決できる場合は、2 時間待機してから、同期が完了したかどうかを再度確認してください。

管理対象ドメインで同期が停止する一般的な理由を次に示します。
- 管理対象ドメインでネットワーク接続がブロックされる。 ネットワークに問題がないか確認することの詳細については、[ネットワーク セキュリティ グループをトラブルシューティングする方法](active-directory-ds-troubleshoot-nsg.md)および[Azure AD Domain Services に対するネットワーク要件](active-directory-ds-networking.md)を参照してください。
-  パスワード同期が設定されないか、完了しない。 パスワード同期を設定するには、[この記事](active-directory-ds-getting-started-password-sync.md)を読んでください。

## <a name="aadds501-a-backup-has-not-been-taken-in-a-while"></a>AADDS501: バックアップがしばらく行われていない

**アラート メッセージ:**

"*マネージド ドメインが最後にバックアップされたのは [date] です。*"

**解決策:**


  [ご利用のドメインの正常性](active-directory-ds-check-health.md)で、マネージド ドメインの構成の問題を示している可能性があるアラートをすべて確認します。 構成の問題によって、マネージド ドメインの同期を行うための Microsoft の機能がブロックされることがあります。 アラートを解決できる場合は、2 時間待機してから、同期が完了したかどうかを再度確認してください。


## <a name="aadds503-suspension-due-to-disabled-subscription"></a>AADDS503: 無効にしたサブスクリプションが原因の中断

**アラート メッセージ:**

"*マネージド ドメインは、ドメインに関連付けられている Azure サブスクリプションがアクティブでないため中断されます。*"

**解決策:**

> [!WARNING]
> 管理対象ドメインが長時間中断する場合、削除される危険性があります。 中断にできるだけ早く対処することをお勧めします。 詳細については、[この記事](active-directory-ds-suspension.md)を参照してください。

サービスを復元するには、ご利用のマネージド ドメインに関連付けられた [Azure サブスクリプションを更新](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable)します。

## <a name="aadds504-suspension-due-to-an-invalid-configuration"></a>AADDS504: 無効な構成が原因の中断

**アラート メッセージ:**

"*マネージド ドメインは、無効な構成により中断されます。サービスは、マネージド ドメインのドメイン コントローラーの管理、パッチ適用、または更新を長い間行うことができませんでした。*"

**解決策:**

> [!WARNING]
> 管理対象ドメインが長時間中断する場合、削除される危険性があります。 中断にできるだけ早く対処することをお勧めします。 詳細については、[この記事](active-directory-ds-suspension.md)を参照してください。


  [ご利用のドメインの正常性](active-directory-ds-check-health.md)で、マネージド ドメインの構成の問題を示している可能性があるアラートをすべて確認します。 これらのアラートのいずれかを解決できる場合は、それを解決します。 その後、サポートに連絡して、サブスクリプションを再度有効にしてください。


## <a name="contact-us"></a>お問い合わせ
[フィードバックの共有およびサポートについては](active-directory-ds-contact-us.md)、Azure Active Directory Domain Services 製品チームにお問い合わせください。
