---
title: Azure Virtual Desktop の MSIX アプリ アタッチに関する FAQ - Azure
description: Azure Virtual Desktop の MSIX アプリ アタッチについてよくあるご質問。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 487d1516e82f856f6c42188248b698b432117906
ms.sourcegitcommit: e1874bb73cb669ce1e5203ec0a3777024c23a486
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/16/2021
ms.locfileid: "112201462"
---
# <a name="msix-app-attach-faq"></a>MSIX アプリ アタッチに関するごよくある質問

この記事では、Azure Virtual Desktop の MSIX アプリ アタッチについてよくあるご質問に回答します。

## <a name="whats-the-difference-between-msix-and-msix-app-attach"></a>MSIX アプリと MSIX アプリ アタッチの違いは何ですか。

MSIX はアプリのパッケージ形式であり、MSIX アプリ アタッチは、デプロイに MSIX パッケージを配信する機能です。

## <a name="does-msix-app-attach-use-fslogix"></a>MSIX アプリ アタッチでは FSLogix は使用されますか?

MSIX アプリ アタッチでは、FSLogix は使用されません。 ただし、MSIX アプリ アタッチと FSLogix は、ユーザーがシームレスなエクスペリエンスを得られるよう連携して動作するように設計されています。

## <a name="can-i-use-the-msix-app-attach-outside-of-azure-virtual-desktop"></a>MSIX アプリ アタッチは Azure Virtual Desktop の外部で使用できますか?

MSIX アプリ アタッチを強化する API が Windows 10 Enterprise 向けに用意されています。 これらの API は、Azure Virtual Desktop の外部で使用できます。 ただし、Azure Virtual Desktop の外部には MSIX アプリ アタッチの管理プレーンはありません。

## <a name="how-do-i-get-an-msix-package"></a>MSIX パッケージはどのように入手できますか?

MSIX パッケージは、ご自分のソフトウェア ベンダーから提供されます。 非 MSIX パッケージを MSIX に変換することもできます。 詳細については、「[既存のお使いのインストーラーを MSIX に移行する方法](/windows/msix/packaging-tool/create-an-msix-overview#how-to-move-your-existing-installers-to-msix)」を参照してください。

## <a name="which-operating-systems-support-msix-app-attach"></a>MSIX アプリ アタッチはどのオペレーティング システムでサポートされていますか?

Windows 10 Enterprise および Windows 10 Enterprise マルチセッション、バージョン 2004 以降でサポートされています。

## <a name="is-msix-app-attach-currently-generally-available"></a>現在、MSIX アプリ アタッチは一般公開されていますか。

MSIX アプリ アタッチは、Windows 10 Enterprise および Windows 10 Enterprise マルチセッション、バージョン 2004 以降に含まれています。 現在、両方のオペレーティング システムが一般公開されています。 

## <a name="can-i-use-msix-app-attach-outside-of-azure-virtual-desktop"></a>MSIX アプリ アタッチは Azure Virtual Desktop の外部で使用できますか?

MSIX と MSIX アプリ アタッチ API は、Windows 10 Enterprise および Windows 10 Enterprise マルチセッション、バージョン 2004 以降に含まれています。 現在、Azure Virtual Desktop の外部の MSIX アプリ アタッチの管理ソフトウェアは提供されていません。

## <a name="can-i-run-two-versions-of-the-same-application-at-the-same-time"></a>同じアプリケーションの 2 つのバージョンを同時に実行できますか。

同じ MSIX アプリケーションの 2 つのバージョンを同時に実行するには、appxmanifest.xml ファイルで定義されている MSIX パッケージ ファミリが、アプリごとに異なっている必要があります。

## <a name="should-i-disable-auto-update-when-using-msix-app-attach"></a>MSIX アプリ アタッチを使用する場合は、自動更新を無効にする必要がありますか。

はい。 MSIX アプリ アタッチでは、MSIX アプリケーションの自動更新はサポートされていません。

## <a name="how-do-permissions-work-with-msix-app-attach"></a>MSIX アプリ アタッチでアクセス許可はどのように機能しますか。

MSIX アプリ アタッチを使用するホスト プール内のすべての仮想マシン (VM) に、MSIX イメージが格納されているファイル共有に対する読み取りアクセス許可が必要です。 Azure Files も使用している場合は、ロールベースのアクセス制御 (RBAC) と New Technology File System (NTFS) の両方のアクセス許可が付与されている必要があります。

## <a name="how-many-users-can-use-an-msix-image-handle"></a>MSIX イメージ ハンドルを使用できるユーザーの数はどれくらいですか?

MSIX アプリ アタッチでは、ユーザーごとではなく、マシンごとに MSIX イメージをマウントします。 MSIX イメージ ハンドルを使用できるユーザーの数は、マシンのファイル システムのサイズとネットワークのスループットに基づいています。 また、Azure Files では、開いているハンドルの数はファイルあたり 2,000 に制限されています。 

## <a name="can-i-use-azure-active-directory-domain-services-azure-ad-ds-with-msix-app-attach"></a>MSIX アプリのアタッチで Azure Active Directory Domain Services (Azure AD DS) を使用できますか。

MSIX アプリのアタッチでは現在のところ、Azure AD DS がサポートされていません。 Azure AD DS コンピューター オブジェクトが Azure Active Directory (Azure AD) と同期されないため、Azure Files に必要なロールベースのアクセス制御 (RBAC) アクセス許可を管理者は提供できません。

## <a name="can-i-use-msix-app-attach-for-http-or-https"></a>HTTP または HTTPs で MSIX アプリ アタッチを使用できますか。

HTTP または HTTPs で MSIX アプリ アタッチを使用することは、現在サポートされていません。

## <a name="can-i-restage-the-same-msix-application"></a>同じ MSIX アプリケーションを再ステージングすることはできますか。

はい。 既に再ステージングされているアプリケーションを再ステージングできます。これによってエラーが発生することはありません。

## <a name="does-msix-app-attach-support-self-signed-certificates"></a>MSIX アプリ アタッチで自己署名証明書はサポートされていますか。

はい。 MSIX アプリのアタッチを使用して自己署名アプリケーションをホストするすべてのセッション ホスト VM に、自己署名証明書をインストールする必要があります。 自己署名証明書を作成する方法については、「[パッケージ署名用の証明書を作成する](/windows/msix/package/create-certificate-package-signing)」を参照してください。

## <a name="what-applications-can-i-repackage-to-msix"></a>どのアプリケーションを MSIX に再パッケージ化できますか。

使用される OS の機能、プログラミング言語、フレームワークはアプリケーションごとに異なります。 アプリケーションを再パッケージ化するには、「[既存のインストーラーを MSIX に移動する方法](/windows/msix/packaging-tool/create-an-msix-overview#how-to-move-your-existing-installers-to-msix)」を参照してください。 アプリケーションの再パッケージ化に必要な項目の一覧は、「[デスクトップ アプリケーションのパッケージ化の準備](/windows/msix/desktop/desktop-to-uwp-prepare)」にあります。 

特定のアプリケーションはアプリケーション層化できません。つまり、MSIX ファイルに再パッケージ化できません。 再パッケージ化できないアプリケーションの一覧を次に示します。

- ドライバー 
- Active-X または Silverlight
- VPN クライアント
- ウイルス対策プログラム

## <a name="how-many-misx-applications-can-i-add-to-each-session-host"></a>各セッション ホストには MISX アプリケーションをいくつ追加できますか。

各セッション ホストには、CPU、メモリ、OS に基づいて異なる制限があります。 これらの制限を超えると、アプリケーションのパフォーマンスと全体的なユーザー エクスペリエンスに影響する場合があります。 ただし、MSIX アプリのアタッチ自体には、使用できるアプリケーションの数の制限はありません。

## <a name="how-many-vhd-or-vhdx-files-can-i-mount-on-a-host-pool"></a>ホスト プールに .VHD ファイルまたは .VHDX ファイルをいくつマウントできますか。

MSIX アプリのアタッチ自体には、マウントできるファイルの数の制限はありません。 ただし、ホスト プール自体は、次の要因によって制限されることがあります。

- マウントされたボリュームを処理する OS の機能。
- ストレージ ソリューションまたはファイル システムで保持できる開いているファイルの最大数。
- ホスト プールのセッション ホストのメモリと CPU の使用率。

言い換えると、ホスト プールの制限は、アプリをローカルにインストールして実行する場合と同じです。

## <a name="next-steps"></a>次のステップ

MSIX アプリ アタッチの詳細については、[概要](what-is-app-attach.md)と[用語集](app-attach-glossary.md)に関するページを参照してください。 それ以外の場合、[アプリのアタッチの設定](app-attach.md)を開始してください。
