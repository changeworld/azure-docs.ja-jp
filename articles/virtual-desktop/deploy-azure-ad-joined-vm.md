---
title: Azure Virtual Desktop で Azure AD 参加済み VM をデプロイする - Azure
description: Azure Virtual Desktop で Azure AD 参加済み VM を構成してデプロイする方法。
services: virtual-desktop
author: Heidilohr
manager: lizross
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 09/15/2021
ms.author: helohr
ms.openlocfilehash: 7b4ec084b39d7efde884f0cee0235f6fd3653538
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "129987102"
---
# <a name="deploy-azure-ad-joined-virtual-machines-in-azure-virtual-desktop"></a>Azure Virtual Desktop で Azure AD 参加済み仮想マシンをデプロイする

この記事では、Azure Virtual Desktop での Azure Active Directory 参加済み仮想マシンのデプロイとアクセスのプロセスについて説明します。 Azure AD 参加済み VM を使用すると、VM からオンプレミスまたは仮想化された Active Directory ドメイン コントローラー (DC) への通信経路を確保することや、Azure AD ドメイン サービス (Azure AD DS) をデプロイすることは必要なくなります。 場合によっては、DC の必要性を完全に排除することも可能であり、環境のデプロイと管理が簡素化されます。 管理を容易にするために、これらの VM を Intune に自動的に登録することもできます。

## <a name="supported-configurations"></a>サポートされている構成

Azure AD 参加済み VM については、現在、次の構成がサポートされています。

- ローカル ユーザー プロファイルを使用する個人用デスクトップ。
- ジャンプ ボックスとして使用されるプールされたデスクトップ。 この構成では、ユーザーはネットワーク上の別の PC に接続する前に、まず Azure Virtual Desktop VM にアクセスします。 ユーザーは VM にデータを保存することはできません。
- ユーザーが VM にデータを保存する必要がない、プールされたデスクトップまたはアプリ。 たとえば、データをオンラインで保存したり、リモート データベースに接続したりするアプリケーションの場合です。

ユーザー アカウントは、同じ Azure AD テナントのクラウド専用またはハイブリッド ユーザーとすることができます。

## <a name="known-limitations"></a>既知の制限事項

次の既知の制限事項は、オンプレミスまたは Active Directory ドメイン参加のリソースへのアクセスに影響を与える可能性があり、Azure AD 参加 VM がご利用の環境に適切かどうかを判断する際に考慮してください。 現在、ユーザーがクラウドベース リソースまたは Azure AD ベース認証へのアクセスのみを必要とするシナリオで Azure AD 参加 VM を推奨しています。

- Azure Virtual Desktop (クラシック) では、Azure AD 参加 VM はサポートされていません。
- Azure AD 参加 VM では現在、外部ユーザーがサポートされていません。
- Azure AD 参加 VM では、現時点でローカル ユーザー プロファイルのみがサポートされています。
- Azure AD 参加 VM では、FSLogix または MSIX アプリ アタッチのために Azure Files ファイル共有にアクセスできません。 これらの機能のいずれにアクセスする場合でも、Kerberos 認証が必要です。
- Windows Store クライアントでは現在、Azure AD 参加 VM がサポートされていません。
- 現在、Azure Virtual Desktop で Azure AD 参加済み VM のシングル サインオンはサポートされていません。

## <a name="deploy-azure-ad-joined-vms"></a>Azure AD 参加済み VM のデプロイ

[新しいホスト プールを作成](create-host-pools-azure-marketplace.md)するとき、または[既存のホスト プールを拡張](expand-existing-host-pool.md)するときに、Azure portal から Azure AD 参加済み VM を直接デプロイできます。 [Virtual Machines] タブで、VM を Active Directory と Azure Active Directory のどちらに参加させるかを選択します。 **Azure Active Directory** を選択すると、オプションで自動的に **VM を Intune に登録する** ことができます。そうすることで、[Windows 10 Enterprise](/mem/intune/fundamentals/windows-virtual-desktop) および [Windows 10 Enterprise マルチセッション](/mem/intune/fundamentals/windows-virtual-desktop-multi-session)の VM を簡単に管理できます。 Azure Active Directory オプションは、現在のサブスクリプションと同じ Azure AD テナントに VM を参加させることに注意してください。

> [!NOTE]
> - ホスト プールには、ドメイン参加の種類が同じ VM のみを含める必要があります。 たとえば、AD 参加済み VM は他の AD VM とのみ一緒にする必要があり、その逆も同様です。
> - ホスト プールの VM は、Windows 10 シングルセッションまたはマルチセッションのバージョン 2004 以降でなければなりません。
> - Microsoft エンドポイント マネージャー (Intune) を使った Azure Virtual Desktop セッション ホストの管理は、現在 Azure パブリック クラウドでのみサポートされています。

### <a name="assign-user-access-to-host-pools"></a>ホスト プールにユーザー アクセスを割り当てる

ホスト プールの作成後、リソースにアクセスできるよう、ユーザーにアクセス権を割り当てる必要があります。 リソースへのアクセスを付与するには、各ユーザーをアプリ グループに追加します。 [アプリ グループの管理](manage-app-groups.md)に関する記事にある手順に従って、アプリとデスクトップへのアクセス権をユーザーに割り当てます。 可能な限り、個々のユーザーではなくユーザー グループを使用することをお勧めします。

Azure AD 参加 VM の場合、Active Directory または Azure Active Directory Domain Services ベースのデプロイの要件に加えて、次の 2 つの追加作業を行う必要があります。  

- ユーザーが VM にサインインできるよう、**仮想マシン ユーザー ログイン** ロールをユーザーに割り当てます。
- ローカル管理特権を必要とする管理者に **仮想マシン管理者ログイン** ロールを割り当てます。

Azure AD 参加済みの VM へのアクセス権をユーザーに付与するには、[VM のロール割り当てを構成する](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md#configure-role-assignments-for-the-vm)必要があります。 **仮想マシン ユーザー ログイン** または **仮想マシン管理者ログイン** のロールは、VM、VM を含むリソース グループ、またはサブスクリプションのいずれかに割り当てることができます。 仮想マシン ユーザー ログイン ロールは、アプリ グループ用に使用したのと同じユーザー グループにリソース グループ レベルで割り当てることをお勧めします。そうすることで、ホスト プール内のすべての VM に適用されます。

## <a name="access-azure-ad-joined-vms"></a>Azure AD 参加済み VM へのアクセス

このセクションでは、さまざまな Azure Virtual Desktop クライアントから Azure AD 参加済み VM にアクセスする方法について説明します。

### <a name="connect-using-the-windows-desktop-client"></a>Windows デスクトップ クライアントを使用した接続

既定の構成では、[Windows デスクトップ クライアント](user-documentation/connect-windows-7-10.md)を使用した Windows 10 からの接続がサポートされます。 資格情報、スマート カード、[Windows Hello for Business 証明書信頼](/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-trust)、または [Windows Hello for Business 証明書によるキー信頼](/windows/security/identity-protection/hello-for-business/hello-deployment-rdp-certs)を使用して、セッション ホストにサインインできます。 ただし、セッション ホストにアクセスするには、ローカル PC が次のいずれかの条件を満たしている必要があります。

- ローカル PC がセッション ホストと同じ Azure AD テナントに Azure AD 参加済みである
- ローカル PC がセッション ホストと同じ Azure AD テナントにハイブリッド Azure AD 参加済みである
- ローカル PC が Windows 10 バージョン 2004 以降を実行しており、セッション ホストと同じ Azure AD テナントに Azure AD 登録済みである

Azure AD に参加していない Windows デバイスからのアクセスを有効にするには、**targetisaadjoined:i:1** を [カスタム RDP プロパティ](customize-rdp-properties.md)としてホスト プールに追加します。 これらの接続は、セッション ホストにサインインするときにユーザー名とパスワードの資格情報を入力するように制限されています。

### <a name="connect-using-the-other-clients"></a>他のクライアントを使用した接続

Web、Android、macOS、および iOS クライアントを使用して Azure AD 参加済み VM にアクセスするには、**targetisaadjoined:i:1** を [カスタム RDP プロパティ](customize-rdp-properties.md)としてホスト プールに追加する必要があります。 これらの接続は、セッション ホストにサインインするときにユーザー名とパスワードの資格情報を入力するように制限されています。

### <a name="enabling-mfa-for-azure-ad-joined-vms"></a>Azure AD 参加済み VM に対する MFA の有効化

Azure Virtual Desktop アプリに条件付きアクセス ポリシーを設定することにより、Azure AD 参加済み VM に対して[多要素認証](set-up-mfa.md)を有効にすることができます。 接続を成功させるには、[従来のユーザー単位の多要素認証を無効](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md#mfa-sign-in-method-required)にする必要があります。 サインインを Windows Hello for Business などの強力な認証方法に制限しない場合は、条件付きアクセス ポリシーから [Azure Windows VM サインイン アプリを除外](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md#mfa-sign-in-method-required)する必要もあります。

## <a name="user-profiles"></a>ユーザー プロファイル

現在、Azure AD 参加済み VM のローカル プロファイルのみが Azure Virtual Desktop でサポートされています。

## <a name="next-steps"></a>次のステップ

Azure AD 参加済み VM をデプロイしたので、サポートされている Azure Virtual Desktop クライアントにサインインし、それをユーザー セッションの一部としてテストできます。 セッションに接続する方法については、こちらの記事を参照してください。

- [Windows デスクトップ クライアントを使用して接続する](user-documentation/connect-windows-7-10.md)
- [Web クライアントに接続する](user-documentation/connect-web.md)
- [Azure AD 参加済み VM への接続のトラブルシューティング](troubleshoot-azure-ad-connections.md)
