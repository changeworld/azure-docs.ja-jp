---
title: Azure Virtual Desktop をデプロイする作業の開始機能 - Azure
description: Azure portal の作業の開始機能を使用して Azure Virtual Desktop をすばやく設定する方法についてのクイックスタート ガイド。
author: Heidilohr
ms.topic: quickstart
ms.date: 07/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 62ba9a8fa578ab12408160d9a6d19243474921fc
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132277222"
---
# <a name="deploy-azure-virtual-desktop-with-the-getting-started-feature"></a>作業の開始機能を使用して Azure Virtual Desktop をデプロイする

Azure portal の新しい作業の開始機能を使用すると、Azure Virtual Desktop をご自分のデプロイにすばやく簡単にインストールして構成できます。

## <a name="requirements"></a>必要条件

作業の開始を使用できるようにするには、次の要件を満たす必要があります。

- Azure Active Directory (AD) テナント。
- Azure AD の全体管理者アクセス許可が付与されているアカウント。

   >[!NOTE]
   >現在、作業の開始機能では、MSA、B2B、またはゲスト アカウントはサポートされていません。

- 有効な Azure サブスクリプション

   >[!NOTE]
   >現在、作業の開始機能では、多要素認証を使用するアカウントはサポートされていません。

- サブスクリプションの **所有者アクセス許可** が付与されているアカウント。

Active Directory Domain Services (AD DS) が使用されている環境で作業の開始機能を使用する場合は、これらの要件も満たす必要があります。

- AD DS ドメイン管理者の資格情報。
- 対象のサブスクリプションで Azure AD Connect を構成したうえで、"USERS" コンテナーが Azure AD と同期していることを確認する必要があります。
- 対象の仮想マシン (VM) のドメイン コントローラーには、種類が **Microsoft.Powershell.DSC** の DSC 拡張機能が含まれていてはなりません。

ID プロバイダーのない環境で作業の開始機能を使用する場合は、これらの追加要件に従う必要があります。

- AD ドメイン参加 UPN には、[ユーザー名ガイドライン リストで許可されていない](../virtual-machines/windows/faq.yml#what-are-the-username-requirements-when-creating-a-vm-)キーワードを含めないでください。また、ご自分の Azure AD サブスクリプションにまだ存在しない一意のユーザー名を使用する必要があります。
- 作業の開始機能を使用して作成したセッション ホストを追加するために、新しいホスト プールを作成する必要があります。 既存のホスト プールにセッション ホストを作成しようとしても、うまくいきません。

## <a name="for-subscriptions-with-azure-ad-ds-or-ad-ds"></a>Azure AD DS または AD DS が使用されているサブスクリプションの場合

Azure AD DS または AD DS が既に使用されているサブスクリプションで作業の開始機能を使用する方法を次に示します。

1. [Azure portal](https://portal.azure.com) を開きます。

2. Azure にサインインし、 **[Azure Virtual Desktop management]\(Azure Virtual Desktop 管理\)** を開いてから、 **[Getting started]\(作業の開始\)** タブを選択します。これにより、作業の開始機能のランディング ページが開きます。

3. **[作成]** を選択します。

4. **[Basic]\(基本\)** タブで、次の値を選択します。

    - **[サブスクリプション]** で、 **[How is your subscription configured]\(サブスクリプションの構成方法\)** に移動し、 **[Existing setup]\(既存の設定\)** を選択します。

    - **[場所]** で、対象のリソースをデプロイする場所を選択します。

    - **[Azure admin UPN]\(Azure の管理者の UPN\)** で、Azure AD の管理者アクセス許可と、使用する予定のサブスクリプションにおける所有者アクセス許可が付与されているアカウントの完全なユーザー プリンシパル名 (UPN) を入力します。

    - **[AD ドメイン参加 UPN]** で、VM をドメインに参加させるために使用する予定のアクセス許可が付与されているアカウントの完全な UPN を入力します。

    - **[ID]** で、ご自分の環境に応じて **[Azure AD DS]** または **[AD DS]** を選択します。 ここで選択する内容は、VM が必要とする入力に影響します。

5. **[仮想マシン]** タブで、次の値を選択します。

    - **[Do you want the users to share this machine]\(ユーザーがこのマシンを共有できるようにしますか?\)** で、ニーズに応じて次のオプションのいずれかを選択します。
      - シングルセッションまたはパーソナル ホスト プールを作成する場合は、 **[いいえ]** を選択します。
      - マルチセッションまたはプールされたホスト プールを作成する場合は、 **[Yes (multi-session)]\(はい (マルチセッション)\)** を選択します。 これにより、Azure AD DS または AD DS に参加した Azure Files ストレージ アカウントも作成されます。

    - **[Image type]\(イメージの種類\)** で、Azure イメージ ギャラリーのイメージ、カスタム イメージ、またはストレージ BLOB の VHD を選択します。

    - **[VM サイズ]** で、デプロイする VM のサイズと SKU を選択します。

    - **[VM 数]** で、ホスト プールにプロビジョニングする VM の数を選択します。

    - AD DS で既存の設定を使用している場合は、これらのオプションが表示されます。

       - **[サブネット]** で、VNET 内のサブネットを選択します。 選択するサブネットは、ID (AD DS または Azure AD DS) と同じ場所にあるか、それにピアリングされている必要があります。

       - **[Domain controller resource group]\(ドメイン コントローラー リソース グループ\)** で、AD DS VM が配置またはピアリングされているリソース グループを選択します。 ドメイン コントローラーを含むリソース グループは、同じサブスクリプション内に存在する必要があります。 現在、作業の開始機能では、ピアリングされたサブスクリプションはサポートされていません。

       - **[Domain controller virtual machine]\(ドメイン コントローラー仮想マシン\)** で、対象のデプロイの AD DS を実行している VM の名前を入力します。

    - [Select Azure AD users or Users group]\(Azure AD ユーザーまたはユーザー グループの選択\) を開く場合は、 **[Assign existing users]\(既存のユーザーを割り当てる\)** チェック ボックスをオンにします。

    - 対象のデプロイをテストするための検証ユーザー アカウントを作成する場合は、 **[Create validation user]\(検証ユーザーを作成する\)** チェック ボックスをオンにし、表示されるプロンプトにユーザー名とパスワードを入力します。

       >[!NOTE]
       >作業の開始機能により、検証ユーザー グループが "USERS" コンテナーに作成されます。 検証グループが Azure AD に同期されていることを確認する必要があります。 同期が機能しない場合は、Azure AD に同期されている組織単位に AVDValidationUsers グループを作成しておきます。

## <a name="for-subscriptions-without-azure-ad-ds-or-ad-ds"></a>Azure AD DS または AD DS が使用されていないサブスクリプションの場合

このセクションでは、Azure AD DS または AD DS が使用されていないサブスクリプションに対して作業の開始機能を使用する方法を示します。 参考までに、これらのサブスクリプションは "空の" サブスクリプションと呼ばれることもあります。

Azure AD DS または AD DS が使用されていないサブスクリプションに Azure Virtual Desktop をデプロイするには:

1. [Azure portal](https://portal.azure.com) を開きます。

2. Azure にサインインし、 **[Azure Virtual Desktop management]\(Azure Virtual Desktop 管理\)** を開いてから、 **[Getting started]\(作業の開始\)** タブを選択します。これにより、作業の開始機能のランディング ページが開きます。

3. **[Basic]\(基本\)** タブで、次の値を選択します。

    - **[サブスクリプション]** で、Azure Virtual Desktop をデプロイするサブスクリプションを選択します。

    - **[How is your subscription configured]\(サブスクリプションの構成方法\)** で、 **[Empty subscription]\(空のサブスクリプション\)** を選択します。 "空の" サブスクリプションは、Azure AD や AD DS などの ID プロバイダーを必要としないサブスクリプションです。

    - **[Resource group prefix]\(リソース グループのプレフィックス\)** で、作成するリソース グループのプレフィックスを入力します ( *-prerequisite*、 *-deployment*、および *-avd*)。

    - **[場所]** で、対象のデプロイに使用するリソースの場所を入力します。

    - **[Azure admin UPN]\(Azure の管理者の UPN\)** で、Azure AD の管理者アクセス許可とサブスクリプションの所有者アクセス許可が付与されているアカウントの完全な UPN を入力します。

    - **[AD ドメイン参加 UPN]** で、**AAD DC 管理者** グループに追加されるアカウントの完全な UPN を入力します。

    >[!NOTE]
    >AD ドメイン参加 UPN のユーザー名は、Azure AD にまだ存在しない一意の名前である必要があります。 現在、作業の開始機能では、Azure AD または AD DS が使用されていないアカウントでの既存の Azure AD ユーザー名の使用はサポートされていません。

4. **[仮想マシン]** タブで、次の値を選択します。

    - **[Do you want the users to share this machine]\(ユーザーがこのマシンを共有できるようにしますか?\)** で、ニーズに応じて次のオプションのいずれかを選択します。
      - シングルセッションまたはパーソナル ホスト プールを作成する場合は、 **[いいえ]** を選択します。
      - マルチセッションまたはプールされたホスト プールを作成する場合は、 **[Yes (multi-session)]\(はい (マルチセッション)\)** を選択します。 これにより、Azure AD DS または AD DS に参加した Azure Files ストレージ アカウントも作成されます。

    - **[Image type]\(イメージの種類\)** で、Azure イメージ ギャラリーのイメージ、カスタム イメージ、またはストレージ BLOB の VHD を選択します。

    - **[VM サイズ]** で、デプロイする VM のサイズと SKU を選択します。

    - **[VM 数]** で、ホスト プールにプロビジョニングする VM の数を選択します。

5. **[割り当て]** タブで、 **[Create validation user]\(検証ユーザーを作成する\)** を選択し、 **[Validation user username]\(検証ユーザーのユーザー名\)** フィールドと **[Validation user password]\(検証ユーザーのパスワード\)** フィールドにユーザー名とパスワードを入力します。 検証ユーザーは、準備ができたデプロイをテストするユーザーです。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

デプロイ後に気が変わり、追加の課金を発生させることなくお使いの環境から Azure Virtual Desktop リソースを削除する場合は、このセクションの手順に従うことで安全にリソースを削除できます。

Azure AD DS または AD DS が使用されているサブスクリプションでリソースを作成した場合、プレフィックスが " *-deployment*" と " *-avd*" の 2 つのリソース グループが作成されています。 Azure portal で、 **[リソース グループ]** に移動し、これらのプレフィックスが付いたリソース グループをすべて削除して、デプロイを削除します。

Azure AD DS または AD DS が使用されていないサブスクリプションでリソースを作成した場合、プレフィックスが *-prerequisite*、 *-deployment*、および *-avd* の 3 つのリソース グループが作成されています。 Azure portal で、 **[リソース グループ]** に移動し、これらのプレフィックスが付いたリソース グループをすべて削除して、デプロイを削除します。

## <a name="next-steps"></a>次のステップ

Azure Virtual Desktop をデプロイする方法の詳細については、[Azure portal でホスト プールを作成する](create-host-pools-azure-marketplace.md)方法に関するページで始まる、デプロイを手動で設定するためのチュートリアルを確認してください。
