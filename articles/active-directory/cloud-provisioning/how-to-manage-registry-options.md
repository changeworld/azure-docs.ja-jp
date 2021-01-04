---
title: Azure AD Connect クラウド プロビジョニング エージェント:レジストリ オプションの管理 | Microsoft Docs
description: この記事では、Azure AD Connect クラウド プロビジョニング エージェントのレジストリ オプションの管理方法について説明します。
services: active-directory
documentationcenter: ''
author: cmmdesai
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/11/2020
ms.subservice: hybrid
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad3bd938355d138e660958e34d046d7af03e75c7
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/13/2020
ms.locfileid: "97370922"
---
# <a name="manage-agent-registry-options"></a>エージェントのレジストリ オプションを管理する

このセクションでは、Azure AD Connect プロビジョニング エージェントのランタイム処理動作を制御する場合に設定可能なレジストリ オプションについて説明します。 

## <a name="configure-ldap-connection-timeout"></a>LDAP 接続タイムアウトを構成する
構成された Active Directory ドメイン コントローラー上で LDAP 操作が実行される場合、プロビジョニング エージェントによって既定の接続タイムアウト値として 30 秒が使用されます。 ドメイン コントローラーからの応答に時間がかかる場合は、エージェントのログ ファイルに次のエラー メッセージが表示されることがあります。 

`
System.DirectoryServices.Protocols.LdapException: The operation was aborted because the client side timeout limit was exceeded.
`

検索属性にインデックスが付けられていない場合、LDAP 検索操作に時間がかかることがあります。 最初の手順として、上記のエラーが発生した場合は、まず search/lookup 属性に[インデックスが付けられている](https://docs.microsoft.com/windows/win32/ad/indexed-attributes)かどうかを確認してください。 検索属性にはインデックスが付けられていて、エラーが引き続き発生する場合は、次の手順に従って LDAP 接続のタイムアウトを長くすることができます。 

1. Azure AD Connect プロビジョニング エージェントを実行している Windows Server 上に管理者としてログオンします。
1. *[実行]* メニュー項目を使用して、レジストリ エディター (regedit.exe) を開きます 
1. キー フォルダー **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure AD Connect Agents\Azure AD Connect Provisioning Agent** の場所を特定します
1. 右クリックし、[新規作成] -> [文字列値] の順に選択します。
1. 次の名前を入力します: `LdapConnectionTimeoutInMilliseconds`
1. **[値の名前]** をダブルクリックし、値データを `60000` ミリ秒として入力します。
    > [!div class="mx-imgBorder"]
    > ![LDAP の接続タイムアウト](media/how-to-manage-registry-options/ldap-connection-timeout.png)
1. "*サービス*" コンソールから Azure AD Connect プロビジョニング サービスを再開します。
1. 複数のプロビジョニング エージェントを展開した場合は、このレジストリ変更をすべてのエージェントに適用して整合性を確保します。 

## <a name="configure-referral-chasing"></a>参照の追跡を構成する
既定では、Azure AD Connect プロビジョニング エージェントによって、[参照](https://docs.microsoft.com/windows/win32/ad/referrals)の追跡は行われません。 次のような特定の HR 受信プロビジョニング シナリオをサポートするには、参照の追跡を有効にすることをお勧めします。 
* 複数のドメイン間での UPN の一意性の確認
* ドメイン間マネージャーの参照の解決

参照の追跡をオンにするには、次の手順を使用します。

1. Azure AD Connect プロビジョニング エージェントを実行している Windows Server 上に管理者としてログオンします。
1. *[実行]* メニュー項目を使用して、レジストリ エディター (regedit.exe) を開きます 
1. キー フォルダー **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure AD Connect Agents\Azure AD Connect Provisioning Agent** の場所を特定します
1. 右クリックし、[新規作成] -> [文字列値] の順に選択します。
1. 次の名前を入力します: `ReferralChasingOptions`
1. **[値の名前]** をダブルクリックし、値データを `96` として入力します。 この値は `ReferralChasingOptions.All` の定数値に対応し、サブツリーとベースレベルの両方の参照の後にエージェントが続くように指定するものです。 
    > [!div class="mx-imgBorder"]
    > ![参照の追跡](media/how-to-manage-registry-options/referral-chasing.png)
1. "*サービス*" コンソールから Azure AD Connect プロビジョニング サービスを再開します。
1. 複数のプロビジョニング エージェントを展開した場合は、このレジストリ変更をすべてのエージェントに適用して整合性を確保します。

> [!NOTE]
> [詳細ログ](how-to-troubleshoot.md#log-files)を有効にすることで、レジストリ オプションが設定されていることを確認できます。 エージェントの起動時に生成されるログには、レジストリから選択された構成値が表示されます。 

## <a name="next-steps"></a>次のステップ 

- [プロビジョニングとは](what-is-provisioning.md)
- [Azure AD Connect クラウド プロビジョニングとは](what-is-cloud-provisioning.md)

