---
title: Azure Virtual Desktop の最新情報 - Azure
description: Azure Virtual Desktop の新機能と製品の更新情報。
author: Heidilohr
ms.topic: overview
ms.date: 09/27/2021
ms.author: helohr
ms.reviewer: thhickli; darank
manager: femila
ms.custom: references_regions
ms.openlocfilehash: 1781a566f84825971ac3728a360b52fcd1270e0a
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132490233"
---
# <a name="whats-new-in-azure-virtual-desktop"></a>Azure Virtual Desktop の最新情報

Azure Virtual Desktop は定期的に更新されます。 この記事では、以下について説明します。

- 最新の更新プログラム
- 新機能
- 既存の機能の強化
- バグの修正

この記事は毎月更新されます。 こちらを頻繁に確認して、新しい更新プログラムに関する最新情報を入手してください。

## <a name="client-updates"></a>クライアント更新

Azure Virtual Desktop およびリモート デスクトップ サービスに関するクライアント向けの更新プログラムについては、次の記事を参照してください。

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [macOS](/windows-server/remote/remote-desktop-services/clients/mac-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)
- [Android](/windows-server/remote/remote-desktop-services/clients/android-whatsnew)
- [Web](/windows-server/remote/remote-desktop-services/clients/web-client-whatsnew)

## <a name="azure-virtual-desktop-agent-updates"></a>Azure Virtual Desktop エージェントの更新

Azure Virtual Desktop エージェントは、少なくとも 1 か月に 1 回更新されます。

Azure Virtual Desktop エージェントの変更内容は次のとおりです。

- バージョン  1.0.3719.1700: この更新プログラムは 2021 年 11 月にリリースされ、次の変更が加えられました。
    - エージェントのエラー メッセージが更新されました。
    - サイドバイサイド スタックが更新されるたびに、エージェントが再起動される問題を修正しました。
    - 一般的なエージェントの機能強化。
- バージョン 1.0.3583.2600: この更新プログラムは 2021 年 10 月にリリースされ、Windows 10 から Windows 11 にアップグレードするとサイドバイサイド スタックが無効になる問題を修正しました。
- バージョン 1.0.3373.2605: この更新プログラムは 2021 年 9 月にリリースされ、MSIX アプリのアタッチを使用したときにパッケージの登録解除がスタックするという問題を修正しました。
- バージョン 1.0.3373.2600: この更新プログラムは 2021 年 9 月にリリースされ、次の変更が加えられました。
    - 一般的なエージェントの機能強化。
    - Windows 7 VM でエージェントを再起動する際の問題を修正。
    - WVDAgentHealthStatus テーブルのフィールドが正しく表示されない問題を修正。
- バージョン 1.0.3130.2900: この更新プログラムは 2021 年 7 月にリリースされ、次の変更が加えられました。
    - 一般的な機能強化とバグの修正。
    - Intune 登録のホスト プール パスを取得する際の問題を修正します。
    - エージェントの問題をより適切に診断するためにログ記録を追加しました。
    - オーケストレーションのタイムアウトに関する問題を修正します。
- バージョン 1.0.3050.2500: この更新プログラムは 2021 年 7 月にリリースされ、次の変更が加えられました。
    - エージェントの正常性の内部モニターを更新しました。
    - スタックの正常性の再試行ロジックが更新されました。
- バージョン 1.0.2990.1500: この更新プログラムは 2021 年 4 月にリリースされ、次の変更が加えられました。
    - エージェントのエラー メッセージが更新されました。
    - Windows 7 VM に Windows 7 以外のエージェントをインストールできないようにする例外が追加されました。
    - ハートビート サービス ロジックが更新されました。
- バージョン 1.0.2944.1400: この更新プログラムは 2021 年 4 月にリリースされ、次の変更が加えられました。
    - エージェント エラーのイベント ビューアー ログに、Azure Virtual Desktop エージェントのトラブルシューティング ガイドへのリンクが配置されました。
    - エラー処理を改善するための例外が追加されました。
    - アクセスできる必要な URL を顧客自身で確認できるようにする WVDAgentUrlTool.exe が追加されました。
-   バージョン 1.0.2866.1500: この更新プログラムは 2021 年 3 月にリリースされ、スタック正常性チェックに関する問題が修正されました。
-   バージョン 1.0.2800.2802: この更新プログラムは 2021 年 3 月にリリースされ、全般的な機能強化とバグ修正が行われました。
-   バージョン 1.0.2800.2800: この更新プログラムは 2021 年 3 月にリリースされ、逆接続の問題が修正されました。
-   バージョン 1.0.2800.2700: この更新プログラムは 2021 年 2 月にリリースされ、アクセス拒否されるオーケストレーションの問題が修正されました。

## <a name="fslogix-updates"></a>FSLogix の更新

FSLogix に対して行われた最新の更新については、 [FSLogix の最新情報](/fslogix/whats-new)に関するページを参照してください。

## <a name="october-2021"></a>2021 年 10 月

2021 年 10 月に実施する変更

### <a name="azure-virtual-desktop-support-for-windows-11"></a>Azure Virtual Desktop の Windows 11 サポート

Azure Virtual Desktop では、シングルおよびマルチ セッションのデプロイで、Windows 11 のサポートの一般提供を開始しました。 Azure portal でホスト プールを作成するときに、Windows 11 のイメージを使用できます。 詳細については、こちらの[ブログ記事](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/windows-11-is-now-generally-available-on-azure-virtual-desktop/ba-p/2810545)を参照してください。

### <a name="rdp-shortpath-now-generally-available"></a>RDP Shortpath の一般提供開始

マネージド ネットワーク用の Remote Desktop Protocol (RDP) Shortpath の一般提供を開始しました。 RDP Shortpath では、Remote Desktop クライアントとセッション ホストを直接接続します。 この直接接続により、ゲートウェイへの依存が減り、接続の安定性が向上し、各ユーザー セッションで利用できる帯域幅が増えます。 詳細については、こちらの[ブログ記事](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/rdp-shortpath-for-managed-networks-is-generally-available/m-p/2861468)を参照してください。

### <a name="screen-capture-protection-updates"></a>画面キャプチャ保護の更新

macOS クライアント、Azure Government および Azure China クラウドで、画面キャプチャ保護をサポートしました。 詳細については、こちらの[ブログ記事](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/screen-capture-protection-for-macos-client-and-support-for/m-p/2840089#M7940)を参照してください。

### <a name="azure-active-directory-domain-join"></a>Azure Active Directory ドメイン参加 

Azure Government および Azure China クラウドにおいて、Azure Active Directory で Azure Virtual Desktop VM のドメイン参加ができるようになりました。 Microsoft エンドポイント マネージャー (Intune) は現在、Azure Public クラウドでのみサポートしています。 詳しくは「[Azure Virtual Desktop で Azure AD 参加済み仮想マシンをデプロイする](deploy-azure-ad-joined-vm.md)」をご覧ください。

### <a name="breaking-change-in-azure-virtual-desktop-azure-resource-manager-template"></a>Azure Virtual Desktop Azure Resource Manager テンプレートの破壊的変更

Azure Virtual Desktop の Azure Resource Manager テンプレートに対し破壊的変更を行いました。 この変更に依存するコードを使用している場合は、この[ブログ記事](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/azure-virtual-desktop-arm-template-change-removal-of-script/m-p/2851538#M7971)の説明に従って問題に対処する必要があります。

### <a name="autoscale-preview-public-preview"></a>自動スケーリング (プレビュー) のパブリック プレビュー

Azure Virtual Desktop の自動スケーリングのパブリック プレビューをリリースしました。 これは、可用性のニーズに応じて、ホスト プールにある仮想マシン (VM) をネイティブにオン/オフする機能です。 VM をオン/オフするタイミングをスケジュールすれば、デプロイのコストを最適化できます。この機能では、ニーズに対応する柔軟なスケジュール方法も用意しています。 必要なカスタム RBAK ロール (ロールベースのアクセス制御ロール) を設定したら、スケーリングの計画を設定できるようになります。 詳しくは「[Azure Virtual Desktop ホスト プールの自動スケーリング (プレビュー)](autoscale-scaling-plan.md)」をご覧ください。

## <a name="september-2021"></a>2021 年 9 月

2021 年 9 月における変更点は次のとおりです。

### <a name="azure-portal-updates"></a>Azure portal の更新

デプロイ後にセッション ホストに適用するすべての更新プログラムに対して、Azure Resource Manager テンプレートを使用できるようになりました。 この機能にアクセスするには、ホスト プールの作成中に **[仮想マシン]** タブを選択します。

また、ホスト プール、アプリ グループ、およびワークスペースの診断設定を、後からではなくホスト プールの作成時に設定できます。 ホスト プールの作成プロセス中にこれらの設定を構成すると、Azure Virtual Desktop 分析情報のレポート データも自動的に設定されます。

### <a name="azure-active-directory-domain-join"></a>Azure Active Directory ドメイン参加

Azure Active Directory ドメイン参加が一般提供されました。 このサービスを使用すると、セッション ホストを Azure Active Directory に参加させることができます。 ドメイン参加により、Microsoft エンドポイント マネージャーの一部として Intune に自動登録することもできます。 この機能は Azure パブリック クラウドで利用できますが、Government Cloud または Azure China ではアクセスできません。 詳細については、こちらの[ブログ記事](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/announcing-general-availability-of-azure-ad-joined-vms-support/ba-p/2751083)を参照してください。

### <a name="azure-china"></a>Azure 中国

Azure Virtual Desktop が Azure China クラウドで一般提供されました。 詳細については、こちらの[ブログ記事](https://azure.microsoft.com/updates/general-availability-azure-virtual-desktop-is-now-available-in-the-azure-china-cloud/)を参照してください。

### <a name="automatic-migration-module-tool"></a>自動移行モジュール ツール

自動移行ツールを使用すると、いくつかの PowerShell コマンドを使うだけで、組織を Azure Virtual Desktop (クラシック) から Azure Virtual Desktop に移行できます。 この機能は現在パブリック プレビュー段階にあります。自動移行の詳細については、[こちら](automatic-migration.md)を参照してください。

## <a name="august-2021"></a>2021 年 8 月

2021 年 8 月における変更点は次のとおりです。

### <a name="windows-11-preview-for-azure-virtual-desktop"></a>Azure Virtual Desktop 用の Windows 11 (プレビュー)

Windows 11 (プレビュー) イメージを Azure Marketplace で入手できるようになりました。お客様は、これを Azure Virtual Desktop でテストおよび検証することができます。 詳しくは、[こちらのお知らせ](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/windows-11-preview-is-now-available-on-azure-virtual-desktop/ba-p/2666468)をご覧ください。

### <a name="multimedia-redirection-is-now-in-public-preview"></a>マルチメディア リダイレクトがパブリック プレビューになりました

マルチメディア リダイレクトを使用すると、Azure Virtual Desktop の Web ブラウザーでビデオを視聴する際のビデオ再生がスムーズになります。これは、Microsoft Edge と Google Chrome で動作します。 詳細については、[ブログ記事](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/public-preview-announcing-public-preview-of-multimedia/m-p/2663244#M7692)をご覧ください。

### <a name="windows-defender-application-control-and-azure-disk-encryption-support"></a>Windows Defender Application Control と Azure Disk Encryption のサポート

Azure Virtual Desktop で、Windows 仮想マシン (VM) で実行できるドライバーとアプリケーションを制御するための Windows Defender Application Control と、VM の OS とデータ ディスクのボリューム暗号化を提供するために Windows BitLocker を使用する Azure Disk Encryption がサポートされるようになりました。 詳しくは、[こちらのお知らせ](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/support-for-windows-defender-application-control-and-azure-disk/m-p/2658633#M7685)をご覧ください。
 
### <a name="signing-into-azure-ad-using-smart-cards-are-now-supported-in-azure-virtual-desktop"></a>スマート カードを使用した Azure AD へのサインインが Azure Virtual Desktop でサポートされるようになりました。

これは Azure AD の新機能ではありませんが、Azure Virtual Desktop で、スマート カードを使用してサインインするように Active Directory フェデレーション サービス (AD FS) を構成することがサポートされるようになりました。 詳しくは、[こちらのお知らせ](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/signing-in-to-azure-ad-using-smart-cards-now-supported-in-azure/m-p/2654209#M7671)をご覧ください。

### <a name="screen-capture-protection-is-now-generally-available"></a>画面キャプチャ保護の一般提供が開始されました

Azure Virtual Desktop の画面キャプチャ保護を使用して、クライアント エンドポイントで実行されているソフトウェアによって、機密情報の画面がキャプチャされないようにします。 詳細については、こちらの[ブログ記事](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/announcing-general-availability-of-screen-capture-protection-for/m-p/2699684)を参照してください。

## <a name="july-2021"></a>2021 年 7 月

2021 年 7 月における変更点は次のとおりです。

### <a name="azure-virtual-desktop-images-now-include-optimized-teams"></a>Azure Virtual Desktop のイメージに最適化された Teams が含まれるようになりました

Microsoft 365 Apps for Enterprise を含む、Azure Virtual Desktop イメージ ギャラリーで使用可能なすべてのイメージに、メディア最適化バージョンの Teams for Azure Virtual Desktop が事前にインストールされるようになりました。 詳しくは、[こちらのお知らせ](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/media-optimization-for-microsoft-teams-now-part-of-win10/m-p/2550054#M7442)をご覧ください。

### <a name="azure-active-directory-domain-join-for-session-hosts-is-in-public-preview"></a>セッション ホスト 用の Azure Active Directory ドメイン参加がパブリック プレビューになりました

Azure Virtual Desktop 仮想マシン (VM) を Azure Active Directory (Azure AD) に直接参加させることができるようになりました。 この機能を使用すると、任意のデバイスから基本的な資格情報を使用して VM に接続できます。 また、Microsoft エンドポイント マネージャーを使用して VM を自動的に登録することもできます。 特定のシナリオでは、これによりドメインコントローラーの必要性をなくし、コストを削減し、展開を効率化することができます。 詳細については、「[Azure Virtual Desktop で Azure AD 参加済み仮想マシンをデプロイする](deploy-azure-ad-joined-vm.md)」をご覧ください。

### <a name="fslogix-version-2105-is-now-available"></a>FSLogix バージョン 2105 が利用可能になりました

FSLogix バージョン 2105 が一般公開されました。 このバージョンには、パブリック プレビュー バージョン (バージョン 2105) では利用できなかった、改善されたサインイン時間とバグ修正が含まれています。 詳細については、[FSLogix のリリース ノート](/fslogix/whats-new)と[こちらのブログ記事](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/announcing-general-availability-of-fslogix-2105-2-9-7838-44263/m-p/2539491#M7412)を参照してください。

### <a name="azure-virtual-desktop-in-china-has-entered-public-preview"></a>中国の Azure Virtual Desktop がパブリック プレビューに移行されました

中国で Azure Virtual Desktop が利用可能になったため、グローバルのカバレッジがさらに広がりました。これにより、組織はこのリージョンの顧客のパフォーマンスと待機時間を改善できます。 詳細については、[こちらのお知らせページ](https://azure.microsoft.com/updates/azure-virtual-desktop-is-now-available-in-the-azure-china-cloud-in-preview/)を参照してください。
 
### <a name="the-getting-started-feature-for-azure-virtual-desktop"></a>Azure Virtual Desktop で作業を開始するための機能

この機能により、Azure Virtual Desktop を設定するための Azure portal でのオンボード エクスペリエンスが合理化されます。 この機能を使用すると、自動化された Azure Active Directory Domain Services のシステム要件を満たすデプロイをシンプルかつ簡単な方法で作成できます。 詳細については、こちらの[ブログ記事](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/getting-started-wizard-in-azure-virtual-desktop/m-p/2451385)を参照してください。

### <a name="start-vm-on-connect-is-now-generally-available"></a>接続時の VM の起動が一般公開されました

接続時に VM を起動する機能が一般公開されました。 この機能は、割り当てを解除または停止した VM をオフにして、ユーザーの要求に合わせてデプロイを柔軟にすることで、コストを最適化するのに役立ちます。 詳しくは、「[接続時に仮想マシンを起動](start-virtual-machine-connect.md)」をご覧ください。

### <a name="remote-app-streaming-documentation"></a>リモート アプリ ストリーミングのドキュメント

Microsoft では、Azure Virtual Desktop を使用して、顧客およびビジネス パートナーにアプリをサービスとして配信するためのリモート アプリ ストリーミング用の新しい価格オプションを発表しました。 たとえば、ソフトウェア ベンダーは、リモート アプリ ストリーミングを使用して、顧客がアクセスできるサービスとしてのソフトウェア (SaaS) ソリューションとしてアプリを配信できます。 リモート アプリ ストリーミングについて詳しくは、[Microsoft のドキュメント](./remote-app-streaming/overview.md)をご覧ください。

2021 年 7 月 14 日から 2021 年 12 月 31 日まで、リモート アプリ ストリーミングを使用するお客様は、ビジネス パートナーや顧客が Azure Virtual Desktop に無料でアクセスできるようにするキャンペーン プランをご利用いただけます。 このプランは、外部ユーザーのアクセス権にのみ適用されます。 通常の課金は、2022 年 1 月 1 日に再開されます。 それまでの間は、Microsoft 365 E3 または Windows E3 などのライセンスに含まれている既存の Windows ライセンス エンタイトルメントを引き続き使用することができます。 このプランの詳細については、[Azure Virtual Desktop の価格に関するページ](https://azure.microsoft.com/pricing/details/virtual-desktop/)を参照してください。

### <a name="new-azure-virtual-desktop-handbooks"></a>新しい Azure Virtual Desktop ハンドブック

さまざまなシナリオで Azure Virtual Desktop を設計およびデプロイする際に役立つ 4 つの新しいハンドブックを発表しました。 

- [アプリケーションの管理](https://azure.microsoft.com/resources/azure-virtual-desktop-handbook-application-management/)では、アプリケーションの配布を最新化し、IT 管理を簡素化する方法について説明します。  
- [ディザスター リカバリー](https://azure.microsoft.com/resources/azure-virtual-desktop-handbook-disaster-recovery/)では、ディザスター リカバリー戦略を策定してビジネスの復元力を高める方法について説明します。  
- [Azure Virtual Desktop を使用する Citrix Cloud](https://azure.microsoft.com/resources/migration-guide-citrix-cloud-with-azure-virtual-desktop/) への移行ガイドを使用すると、Citrix への投資からより多くの価値を得ることができます。
- [Azure Virtual Desktop を使用する VMware Horizon](https://azure.microsoft.com/resources/migration-guide-vmware-horizon-cloud-and-azure-virtual-desktop/) への移行ガイドを使用すると、VMware への既存の投資からより多くの価値を得ることができます。

## <a name="june-2021"></a>2021 年 6 月

2021 年 6 月における変更点は次のとおりです。

### <a name="windows-virtual-desktop-is-now-azure-virtual-desktop"></a>Windows Virtual Desktop が Azure Virtual Desktop になりました

柔軟なクラウド デスクトップとリモート アプリケーション プラットフォームの構想との連携を強化するために、Windows Virtual Desktop を Azure Virtual Desktop に変更しました。 詳細については、[ブログのお知らせの投稿をご覧ください](https://azure.microsoft.com/blog/azure-virtual-desktop-the-desktop-and-app-virtualization-platform-for-the-hybrid-workplace/)。

### <a name="eu-uk-and-canada-geographies-are-now-generally-available"></a>ヨーロッパ、英国、およびカナダの地域が一般公開されました

欧州連合、英国、およびカナダのメタデータ サービスが一般公開されました。 これらの新しい場所は、米国外のデータ主権にとって非常に重要です。 詳細については、こちらの[ブログ記事](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/announcing-public-preview-of-azure-virtual-desktop-service/m-p/2478401#M7314)を参照してください。

### <a name="the-getting-started-tool-is-now-in-public-preview"></a>Getting Started ツールがパブリック プレビュー中になりました

初めてのユーザーがデプロイ プロセスを簡単にできるように Azure Virtual Desktop Getting Started ツールを作成しました。 デプロイ プロセスを簡略化および自動化することで、このツールによって Azure Virtual Desktop をより迅速に導入できるようになり、さまざまなユーザーがより簡単にアクセスできるようになることを願っています。 詳細については、こちらの[ブログ記事](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/getting-started-wizard-in-azure-virtual-desktop/m-p/2451385)を参照してください。

### <a name="azure-virtual-desktop-pricing-calculator-updates"></a>Azure Virtual Desktop の価格計算ツールの更新

Azure Virtual Desktop の価格エクスペリエンスを向上させるために、Azure 価格計算ツールに次のような重要な更新を行いました。  
  
- サービス名が Azure Virtual Desktop に更新されました  
- また、次の新しい項目を使用してレイアウトを更新しました。  
   - マネージド ディスクとファイル ストレージの両方の帯域幅を含むストレージ セクション  
   - ユーザーごとのコストを示すカスタム セクション

[このページ](https://azure.microsoft.com/pricing/calculator/)で価格計算ツールにアクセスできます。

### <a name="single-sign-on-sso-using-active-directory-federation-services-ad-fs"></a>Active Directory フェデレーション サービス (AD FS) を使用したシングル サインオン (SSO)

AD FS のシングル サインオン機能が一般公開されました。 この機能により、お客様は AD FS を使用して、Windows と Web クライアントのシングル サインオン エクスペリエンスをユーザーに提供できます。 詳細については、「[Azure Virtual Desktop 用に AD FS シングル サインオンを構成する](configure-adfs-sso.md)」を参照してください。

## <a name="may-2021"></a>2021 年 5 月

2021 年 5 月の新機能は次のとおりです。

### <a name="smart-card-authentication"></a>スマート カード認証

キー配布センター (KDC) プロキシのリモート デスクトップ プロトコル (RDP) プロパティが正式にリリースされました。 これらのプロパティにより、Azure Virtual Desktop セッションの RDP 部分で Kerberos 認証が可能になります。これには、パスワードなしでのネットワーク レベル認証の許可も含まれます。 詳細については、こちらの[ブログ記事](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/new-feature-smart-card-authentication-for-windows-virtual/m-p/2323226)を参照してください。

### <a name="the-web-client-now-supports-file-transfer"></a>Web クライアントでファイル転送がサポート

Web クライアントのパブリック プレビュー バージョンであるバージョン 1.0.24.7 (プレビュー) 以降では、ユーザーはリモート セッションとローカル コンピューターの間でファイルを転送できるようになりました。 リモート セッションにファイルをアップロードするには、Web クライアント ページの上部にあるメニューでアップロード アイコンを選択します。 ファイルをダウンロードするには、リモート セッションの [スタート] メニューで、**リモート デスクトップ仮想ドライブ** を検索します。 仮想ドライブを開いたら、ダウンロード フォルダーにファイルをドラッグ アンド ドロップするだけで、ブラウザーによってローカル コンピューターへのファイルのダウンロードが開始されます。

### <a name="start-vm-on-connect-support-updates"></a>Start VM on Connect のサポートの更新

Start VM on Connect (プレビュー) で、プールされたホスト プールと Azure Government クラウドがサポートされるようになりました。 詳細については、こちらの[ブログ記事](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/leverage-start-vm-on-connect-for-pooled-host-pools-and-azure-gov/m-p/2349866)を参照してください。

### <a name="latency-improvements-for-the-united-arab-emirates-region"></a>アラブ首長国連邦リージョンの待ち時間の改善

Azure コントロール プレーンをアラブ首長国連邦 (UAE) でも使用できるようになったため、このリージョンのお客様の待ち時間が改善されます。 詳細については、[Azure Virtual Desktop のロードマップ](https://www.microsoft.com/microsoft-365/roadmap?filters=Windows%20Virtual%20Desktop&searchterms=64545)に関するページを参照してください。

### <a name="ending-internet-explorer-11-support"></a>Internet Explorer 11 のサポートの終了

2021 年 9 月 30 日に、Azure Virtual Desktop Web クライアントでの Internet Explorer 11 のサポートが終了します。 代わりに、Web クライアントとリモート セッション用に [Microsoft Edge](https://www.microsoft.com/edge?form=MY01R2&OCID=MY01R2&r=1) ブラウザーの使用を開始することをお勧めします。 詳細については、[こちらのブログ記事](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/windows-virtual-desktop-web-client-to-end-support-for-internet/m-p/2369007)のお知らせを参照してください。

### <a name="microsoft-endpoint-manager-public-preview"></a>Microsoft エンドポイント マネージャーのパブリック プレビュー

Windows 10 Enterprise マルチセッションにおける Microsoft エンドポイント マネージャーのサポートのパブリック プレビューが開始されました。 この新機能により、お使いのローカル デバイスと同じツールを使用して Windows 10 VM を管理できるようになります。 詳細については、[Microsoft エンドポイント マネージャーのドキュメント](/mem/intune/fundamentals/windows-virtual-desktop-multi-session)を参照してください。

### <a name="fslogix-agent-public-preview"></a>FSLogix エージェントのパブリック プレビュー

最新バージョンの FSLogix エージェントのパブリック プレビューがリリースされました。 こちらの[ブログ記事](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/public-preview-fslogix-release-2105-is-now-available-in-public/m-p/2380996/thread-id/7105)で詳細を確認し、プレビューにアクセスするために必要なフォームを送信してください。

### <a name="may-2021-updates-for-teams-for-azure-virtual-desktop"></a>Azure Virtual Desktop 用の Teams に対する 2021 年 5 月の更新

この更新では、ビデオの共有時に画面が黒いままになる問題が解決されました。 また、セッション クライアントと Teams サーバー間のビデオ解像度の不一致も修正されました。 Teams on Azure Virtual Desktop では、Teams サーバーからの入力に基づいて解像度とビット レートが変更されるようになっています。

### <a name="azure-portal-deployment-updates"></a>Azure portal のデプロイの更新

Azure portal でのデプロイ プロセスが次のように更新されました。

- 新しい Azure Virtual Desktop セッション ホスト VM を作成する際の "イメージ" のドロップダウン リスト ボックスに新しいイメージ (GEN2 を含む) が追加されました。
- ホスト プールの作成時に、仮想マシンのブート診断を構成できるようになりました。
- ホスト プールの詳細な RDP プロパティ タブで、RDP プロキシにヒントが追加されました。
- MSIX パッケージからアプリケーションを追加する際のアイコン パスに情報バブルが追加されました。
- アンマネージド ディスクでマネージド ブート診断を実行できなくなりました。
- Azure Resource Manager でホスト プールを作成するためのテンプレートが更新され、サードパーティの Marketplace イメージを使用したホスト プールの作成が Azure portal でサポートされるようになりました。

### <a name="single-sign-on-using-active-directory-federation-services-public-preview"></a>Active Directory フェデレーション サービス パブリック プレビューを使用したシングル サインオン

ホスト プールごとのシングル サインオン (SSO) をサポートする Active Directory フェデレーション サービス (AD FS) のパブリック プレビューが開始されました。 詳細については、「[Azure Virtual Desktop の AD FS シングル サインオンの構成](configure-adfs-sso.md)」を参照してください。 

### <a name="enterprise-scale-support"></a>エンタープライズ規模のサポート

Azure Virtual Desktop のエンタープライズ規模のサポートのために、クラウド導入フレームワークの更新されたセクションがリリースされました。 詳細については、[Azure Virtual Desktop 構築セットのエンタープライズ規模のサポート](/azure/cloud-adoption-framework/scenarios/wvd/enterprise-scale-landing-zone)に関する記事を参照してください。

### <a name="customer-adoption-kit"></a>顧客導入キット

顧客向けに Azure Virtual Desktop を設定するお客様やパートナーを支援するために、Azure Virtual Desktop 顧客導入キットが最近リリースされました。 このキットは、[こちら](https://www.microsoft.com/azure/partners/resources/customer-adoption-kit-windows-virtual-desktop)からダウンロードできます。

## <a name="april-2021"></a>2021 年 4 月

4 月の新機能は次のとおりです。

### <a name="use-the-start-vm-on-connect-feature-preview-in-the-azure-portal"></a>Azure portal での Start VM on Connect 機能 (プレビュー) の使用

Start VM on Connect (プレビュー) を Azure portal で構成できるようになりました。 この更新プログラムを使用すると、ユーザーは、その VM に Android クライアントや macOS クライアントからアクセスすることができます。 詳細については、「[Start VM on Connect](start-virtual-machine-connect.md#use-the-azure-portal)」を参照してください。

### <a name="required-url-check-tool"></a>必要な URL チェック ツール 

Azure Virtual Desktop エージェント バージョン 1.0.2944.400 には、URL を検証し、仮想マシンが機能するために必要な URL にアクセスできるかどうかを表示するツールが含まれています。 必要な URL にアクセスできる場合、必要に応じてブロックを解除できるよう、それらの URL が一覧表示されます。 詳細については、[安全な URL の一覧](safe-url-list.md#required-url-check-tool)に関するページを参照してください。

### <a name="updates-to-the-azure-portal-ui-for-azure-virtual-desktop"></a>Azure Virtual Desktop に関係する Azure portal UI の更新

Azure Virtual Desktop に関係する Azure portal UI の最新の更新で、次の点が変更されています。

- ドレイン モードが有効な状態でセッション ホストを取得するときのエラー発生の原因となる問題を修正しました。
- Portal SDK をバージョン 7.161.0 にアップグレードしました。
- [ユーザーセッション] タブに、リソース ID が見つからないというエラーメッセージが表示される原因となっていた問題を修正しました。
- Azure portal に、セッション ホストの詳細なサブステータス メッセージが表示されるようになりました。

### <a name="april-2021-updates-for-teams-on-azure-virtual-desktop"></a>Teams on Azure Virtual Desktop の 2021 年 4 月の更新

Teams on Azure Virtual Desktop に関する最新情報は次のとおりです。

- Windows 10 ベースのクライアントの発信ビデオ ストリームのビデオ処理にハードウェア アクセラレータを追加しました。
- 正面カメラと背面カメラの両方または外付けカメラを使用して会議に参加する場合、既定では正面カメラが選択されます。
- x86 ベースのマシンで Teams がクラッシュする問題を解決しました。
- 画面の共有中に筋が発生する問題を解決しました。
- 会議の出席者が着信ビデオを見たり画面を共有したりするのを妨げていた問題を解決しました。

### <a name="msix-app-attach-is-now-generally-available"></a>MSIX アプリのアタッチの一般提供開始

Azure Virtual Desktop の MSIX アプリ アタッチのパブリック プレビューが終了し、全ユーザーへの提供が開始されました。 MSIX アプリ アタッチについては、[Microsoft の技術者コミュニティの発表](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/msix-app-attach-is-now-generally-available/m-p/2270468)で詳しくご覧いただけます。

### <a name="the-macos-client-now-supports-apple-silicon-and-big-sur"></a>macOS クライアントが Apple Silicon と Big Sur に対応

macOS Azure Virtual Desktop クライアントで、Apple Silicon と Big Sur がサポートされるようになりました。 更新プログラムの全一覧については、「[macOS クライアントの新機能](/windows-server/remote/remote-desktop-services/clients/mac-whatsnew)」を参照してください。

## <a name="march-2021"></a>2021 年 3 月

2021 年 3 月における変更点は次のとおりです。

### <a name="updates-to-the-azure-portal-ui-for-azure-virtual-desktop"></a>Azure Virtual Desktop に関係する Azure portal UI の更新

Azure portal の Azure Virtual Desktop が次のように更新されました。

- ワークフローでホスト プールを作成して VM を追加するための新しい可用性オプション (可用性セットおよびゾーン) を有効にしました。
- "支援が必要" という状態のホストが使用不可と表示される問題を修正しました。 ホストの横に警告アイコンが表示されるようになりました。
- アクティブなセッションの並べ替えを有効にしました。
- ホストの詳細タブで、特定のユーザーにメッセージを送信したり、特定のユーザーをサインアウトさせたりできるようになりました。
- セッションの上限フィールドを変更しました。
- ホスト プールを作成するためにワークフローに OU 検証パスを追加しました。
- 個人用ホスト プールの作成時に Windows 10 イメージの最新バージョンを使用できるようになりました。

### <a name="generation-2-images-and-trusted-launch"></a>第 2 世代のイメージとトラステッド起動

Azure Marketplace に、Windows 10 Enterprise および Windows 10 Enterprise マルチセッションの第 2 世代のイメージが含まれるようになりました。 これらのイメージを使用すると、トラステッド起動 VM を使用できます。 第 2 世代の VM の詳細については、[第 1 または第 2 世代の仮想マシンを作成する必要性](../virtual-machines/generation-2.md)に関するページを参照してください。 Azure Virtual Desktop のトラステッド起動 VM をプロビジョニングする方法については、こちらの [TechCommunity の投稿](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/windows-virtual-desktop-support-for-trusted-launch/m-p/2206170)を参照してください。

### <a name="fslogix-is-now-preinstalled-on-windows-10-enterprise-multi-session-images"></a>FSLogix が Windows 10 Enterprise マルチセッション イメージにプレインストールされるようになりました

お客様からのフィードバックに基づいて、FSLogix の未構成バージョンが既にインストールされている Windows 10 Enterprise マルチセッション イメージの新しいバージョンを設定しました。 これにより、Azure Virtual Desktop のデプロイがこれまでより容易になることを期待しています。

### <a name="azure-monitor-for-azure-virtual-desktop-is-now-in-general-availability"></a>Azure Monitor for Azure Virtual Desktop の一般提供開始

Azure Monitor for Azure Virtual Desktop の一般提供が開始されました。 この機能は、デプロイを監視する自動化されたサービスで、使用すると、イベント、正常性、トラブルシューティングの推奨事項を 1 か所で確認できます。 詳細については、[ドキュメント](azure-monitor.md)を参照するか、[TechCommunity の投稿](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/azure-monitor-for-windows-virtual-desktop-is-generally-available/m-p/2242861)を確認してください。

### <a name="march-2021-updates-for-teams-on-azure-virtual-desktop"></a>Teams on Azure Virtual Desktop の 2021 年 3 月の更新

Teams on Azure Virtual Desktop が次のように更新されました。

- 通話と 2x2 モードでのビデオ品質のパフォーマンスを改善しました。
- ビデオ処理 (XVP) のハードウェア オフロードを使用することにより、CPU 使用率を (CPU の世代に応じて) 5 から 10% 低減しました。
- 古いマシンで XVP とハードウェア デコードを使用して、2x2 モードでより多くの受信ビデオ ストリームをスムーズに表示できるようになりました。
- AV 同期のパフォーマンスを向上させ、一時的な問題を低減するために、WebRTC スタックを M74 から M88 に更新しました。
- ソフトウェア H264 エンコーダーを OpenH264 (Web 上の Teams で使用されている OSS) に置き換えました。これにより、送信側カメラのビデオ品質が向上しました。
- 3 月 30 日に一般向けに Teams サーバーの 2x2 モードを有効にしました。 2x2 モードでは、同時に最大 4 つの受信ビデオ ストリームが表示されます。

### <a name="start-vm-on-connect-public-preview"></a>"接続時に仮想マシンを起動" のパブリック プレビュー

新しいホスト プール設定である "接続時に仮想マシンを起動" が、パブリック プレビューとして利用できるようになりました。 この設定により、必要に応じていつでも VM を有効にすることができます。 コストを節約するには、Azure Compute の設定を構成して VM の割り当てを解除する必要があります。 詳細については、[ブログ記事](https://aka.ms/wvdstartvmonconnect)と[ドキュメント](start-virtual-machine-connect.md)を参照してください。

### <a name="azure-virtual-desktop-specialty-certification"></a>Azure Virtual Desktop Specialty 認定

Azure における Azure Virtual Desktop の専門知識があることを証明するための AZ-140 試験のベータ版がリリースされました。 詳細については、[TechCommunity の投稿](https://techcommunity.microsoft.com/t5/microsoft-learn-blog/beta-exam-prove-your-expertise-in-windows-virtual-desktop-on/ba-p/2147107)を参照してください。

## <a name="february-2021"></a>2021 年 2 月

2021 年 2 月における変更点は次のとおりです。

### <a name="portal-experience"></a>ポータルでの操作

Azure portal のエクスペリエンスを次のように改善しました。

- セッション ホスト グリッド タブにおけるホストの一括ドレイン モード。 
- MSIX アプリ アタッチのパブリック プレビューが利用できるようになりました。
- 固定ホスト プールの概要情報がダーク モードに対応しました。

### <a name="eu-metadata-storage-now-in-public-preview"></a>EU メタデータ ストレージがパブリック プレビューに

Azure Virtual Desktop におけるサービス メタデータのストレージ オプションとして、ヨーロッパ (EU) 地域のパブリック プレビューがホストされるようになりました。 ユーザーは、サービス オブジェクトを作成するときに、西ヨーロッパと北ヨーロッパのどちらかを選択できます。 ホスト プールに使用されるサービス オブジェクトとメタデータは、各リージョンに関連付けられている Azure 地域に格納されます。 詳細については、[パブリック プレビューを告知するブログ記事](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/announcing-public-preview-of-windows-virtual-desktop-service/m-p/2143939)を参照してください。

### <a name="teams-on-azure-virtual-desktop-plugin-updates"></a>Teams on Azure Virtual Desktop プラグインの更新

最も頻繁に報告される問題 (画面が突然暗くなる、ビデオとサウンドが同期されていないなど) に対処することで、Azure Virtual Desktop プラグインのビデオ通話の品質が改善されました。 これらの改良により、アクティブ スピーカーの切り替えによって単一動画ビューのパフォーマンスが向上します。 また、特殊な特性を備えたハードウェア デバイスが Teams で利用できない問題も修正済みです。

## <a name="january-2021"></a>2021 年 1 月

2021 年 1 月における変更点は次のとおりです。

### <a name="new-azure-virtual-desktop-offer"></a>新しい Azure Virtual Desktop プラン

Azure Virtual Desktop の D シリーズと Bs シリーズの仮想マシンでネイティブの Microsoft ソリューションを使用する新規のお客様は、最長 90 日間、コンピューティング コストを 30% 節約できます。 このプランは、2021 年 3 月 31 日まで Azure portal で利用できます。 詳細については、[Azure Virtual Desktop プランに関するページ](https://azure.microsoft.com/services/virtual-desktop/offer/)を参照してください。

### <a name="networksecuritygrouprules-value-change"></a>networkSecurityGroupRules の値を変更 

Azure Resource Manager の入れ子になったテンプレートで、networkSecurityGroupRules の既定値をオブジェクトから配列に変更しました。 今後は、networkSecurityGroupRules の値を指定せずに managedDisks-customimagevm.json を使用してもエラーは発生しません。 これは破壊的変更ではありません。下位互換性があります。

### <a name="fslogix-hotfix-update"></a>FSLogix の修正プログラム

FSLogix バージョン 2009 HF_01 (2.9.7654.46150) をリリースし、以前のリリース (2.9.7621.30127) にあった問題を解決しました。 以前のバージョンの使用は中止し、できるだけ早く FSLogix を更新するようお勧めします。

詳細については、リリース ノートで [FSLogix の最新情報](/fslogix/whats-new#fslogix-apps-2009-hf_01-29765446150)を参照してください。

### <a name="azure-portal-experience-improvements"></a>Azure portal エクスペリエンスの改善

Azure portal のエクスペリエンスについて次の点を改善しました。

- ローカル VM 管理者資格情報を直接追加できるようになりました。Active Directory ドメイン参加アカウントの資格情報でローカル アカウントを追加する必要はありません。
- 個人とグループの両方の割り当てを、個人ユーザー用のタブとグループ用のタブで別々に一覧表示できるようになりました。
- ホスト プールの仮想マシン概要に、Azure Virtual Desktop エージェントのバージョン番号が表示されるようになりました。
- ホスト プールとアプリケーション グループの一括削除機能を追加しました。
- ホスト プール内の複数のセッション ホストを対象にドレイン モードを有効または無効にできるようになりました。
- VM の詳細ページからパブリック IP フィールドを削除しました。

### <a name="azure-virtual-desktop-agent-troubleshooting"></a>Azure Virtual Desktop エージェントのトラブルシューティング

Microsoft はこのほど、一般的な問題が発生したお客様を支援するために、[Azure Virtual Desktop エージェントのトラブルシューティング ガイド](troubleshoot-agent.md)を用意しました。

### <a name="microsoft-defender-for-endpoint-integration"></a>Microsoft Defender for Endpoint 統合

Microsoft Defender for Endpoint の統合が一般提供になりました。 この機能により、ローカル Windows 10 マシンと同じ調査エクスペリエンスを Azure Virtual Desktop VM で利用できるようになります。 Windows 10 Enterprise マルチセッションを使用するユーザーのために、Microsoft Defender for Endpoint では最大 50 のコンカレント ユーザー接続がサポートされます。Windows 10 Enterprise マルチセッションのコストを削減し、高い信頼度を備えた Microsoft Defender for Endpoint を活用することが可能です 詳細については、こちらの[ブログ記事](https://techcommunity.microsoft.com/t5/microsoft-defender-for-endpoint/windows-virtual-desktop-support-is-now-generally-available/ba-p/2103712)を参照してください。

### <a name="azure-security-baseline-for-azure-virtual-desktop"></a>Azure Virtual Desktop 用の Azure セキュリティ ベースライン

Microsoft では、ユーザーの注意を喚起するために、Azure Virtual Desktop 用の [Azure セキュリティ ベースラインに関する記事](security-baseline.md)を最近公開しました。 これらのガイドラインには、Azure セキュリティ ベンチマーク バージョン 2.0 を Azure Virtual Desktop に適用する方法に関する情報が記載されています。 Azure セキュリティ ベンチマークでは、Azure 上のクラウド ソリューションをセキュリティで保護するために Microsoft が推奨する設定と手法が説明されています。

## <a name="december-2020"></a>2020 年 12 月

2020 年 12 月における変更点は次のとおりです。 

### <a name="azure-monitor-for-azure-virtual-desktop"></a>Azure Monitor for Azure Virtual Desktop

Azure Monitor for Azure Virtual Desktop のパブリック プレビューが利用可能になりました。 この新機能には、Azure Monitor ブックを基盤とする堅牢なダッシュボードが含まれており、IT プロフェッショナルが Azure Virtual Desktop 環境を理解するうえで役立ちます。 詳細については、[ブログのお知らせ](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/azure-monitor-for-windows-virtual-desktop-public-preview/m-p/1946587)をご覧ください。 

### <a name="azure-resource-manager-template-change"></a>Azure Resource Manager テンプレートの変更点 

最新の更新プログラムでは、ホスト プールを作成およびプロビジョニングするための Azure Resource Manager テンプレートからすべてのパブリック IP アドレス パラメーターを削除しました。 デプロイを保護するために、Azure Virtual Desktop にはパブリック IP を使用しないことを強くお勧めします。 デプロイがパブリック IP に依存している場合は、代わりにプライベート IP を使用するように再構成する必要があります。そうしないと、デプロイが適切に機能しません。

### <a name="msix-app-attach-public-preview"></a>MSIX アプリのアタッチのパブリック プレビュー 

MSIX アプリのアタッチは、今月にパブリック プレビューが開始されたもう 1 つのサービスです。 MSIX アプリ アタッチは、Azure Virtual Desktop セッション ホスト VM に MSIX アプリケーションを動的に提供するサービスです。 詳細については、[ブログのお知らせ](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/msix-app-attach-azure-portal-integration-public-preview/m-p/1986231)をご覧ください。 

### <a name="screen-capture-protection"></a>画面キャプチャ保護 

今月は、画面キャプチャ保護のパブリック プレビューも開始されました。 この機能を使用することで、機密情報がクライアント エンドポイントでキャプチャされるのを防ぐことができます。 [このページ](https://aka.ms/WVDScreenCaptureProtection)に移動して、画面キャプチャ保護をお試しください。  

### <a name="built-in-roles"></a>組み込みのロール

管理者のアクセス許可を付与するための、Azure Virtual Desktop 用の新しい組み込みロールが追加されました。 詳細については、「[Azure Virtual Desktop の組み込みロール](rbac.md)」を参照してください。 

### <a name="application-group-limit-increase"></a>アプリケーション グループの制限の引き上げ

Azure Active Directory テナントあたりのアプリケーション グループの既定の上限値を 200 グループに増やしました。

### <a name="client-updates-for-december-2020"></a>2020 年 12 月のクライアント更新

次のクライアントの新しいバージョンがリリースされました。 

- Android
- macOS
- Windows

クライアントの更新の詳細については、「[クライアント更新](whats-new.md#client-updates)」をご覧ください。

## <a name="november-2020"></a>2020 年 11 月

### <a name="azure-portal-experience"></a>Azure portal での操作

Azure portal のユーザー エクスペリエンスの 2 つのバグを修正しました。

- "VM の追加" ワークフローで、デスクトップ アプリケーションのフレンドリ名が上書きされなくなりました。
- セッション ホストがスケール セットに含まれている場合、[セッション ホスト] タブが読み込まれるようになりました。

### <a name="fslogix-client-version-2009"></a>FSLogix クライアント バージョン 2009 

多くの修正と機能強化が行われた新しいバージョンの FSLogix クライアントがリリースされました。 詳細については、[ブログ記事](https://social.msdn.microsoft.com/Forums/en-US/defe5828-fba4-4715-a68c-0e4d83eefa6b/release-notes-for-fslogix-apps-release-2009-29762130127?forum=FSLogix)をご覧ください。

### <a name="rdp-shortpath-public-preview"></a>RDP Shortpath パブリック プレビュー

RDP Shortpath では、ポイント対サイト VPN、サイト間 VPN、ExpressRoute を使用した、Azure Virtual Desktop セッション ホストへの直接接続が導入されています。 また、URCP トランスポート プロトコルも導入されています。 RDP Shortpath は、ユーザー エクスペリエンスを向上させるために、待ち時間とネットワーク ホップを削減するように設計されています。 詳細については、[Azure Virtual Desktop の RDP Shortpath](shortpath.md) に関する記事を参照してください。

### <a name="azdesktopvirtualization-version-201"></a>Az.DesktopVirtualization、バージョン 2.0.1

Azure Virtual Desktop コマンドレットのバージョン 2.0.1 がリリースされました。 この更新には、MSIX アプリのアタッチを管理できるコマンドレットが含まれています。 新しいバージョンは、[PowerShell ギャラリー](https://www.powershellgallery.com/packages/Az.DesktopVirtualization/2.0.1)でダウンロードできます。

### <a name="azure-advisor-updates"></a>Azure Advisor の更新

Azure Virtual Desktop での近接ガイダンスに関する新しい推奨事項と、深さ優先の負荷分散ホスト プールにおけるパフォーマンスの最適化に関する新しい推奨事項が Azure Advisor に追加されました。 詳細については、[Azure Web サイト](https://azure.microsoft.com/updates/new-recommendations-from-azure-advisor/)を参照してください。

## <a name="october-2020"></a>2020 年 10 月

2020 年 10 月における変更点は次のとおりです。

### <a name="improved-performance"></a>パフォーマンスの向上

- 次の Azure 地域での接続の待ち時間を短縮することで、パフォーマンスを最適化しました。
    - スイス
    - カナダ

[エクスペリエンス予測ツール](https://azure.microsoft.com/services/virtual-desktop/assessment/)を使用して、これらの地域のユーザー エクスペリエンス品質を見積もることができるようになりました。

### <a name="azure-government-cloud-availability"></a>Azure Government クラウドの提供

Azure Government クラウドの一般提供が開始されました。 詳細については、[ブログ記事](https://azure.microsoft.com/updates/windows-virtual-desktop-is-now-generally-available-in-the-azure-government-cloud/)をご覧ください。

### <a name="azure-virtual-desktop-azure-portal-updates"></a>Azure Virtual Desktop Azure portal の更新

Azure Virtual Desktop Azure portal が更新されました。

- ユーザーが [セッション] タブを開くことができなくなる resourceID エラーを修正しました。
- [セッション ホスト] タブの UI を合理化しました。
- RDP のプロパティの [既定値]、[ユーザビリティ]、[既定値に戻す] の各設定を修正しました。
- すべてのタブで "削除" 機能に一貫性を持たせました。
- ポータルで、"アプリの追加" ワークフローのアプリ名が検証されるようになりました。
- セッション ホストのエクスポート データが列に配置されない問題を修正しました。
- ポータルでユーザー セッションを取得できない問題を修正しました。
- 仮想マシンが別のリソース グループに作成されたときに発生するセッション ホストの取得の問題を修正しました。
- アクティブなセッションと切断されたセッションの両方を一覧表示するように [セッション ホスト] タブを更新しました。
- [アプリケーション] タブにページが含まれるようになりました。
- [アプリケーションの一覧] タブに、"コマンド ラインが必要" というテキストが正しく表示されない問題を修正しました。
- ドイツ語版の Shared Image Gallery を使用しているときに、ポータルでホスト プールまたは仮想マシンをデプロイできない問題を修正しました。

### <a name="client-updates-for-october-2020"></a>2020 年 10 月のクライアント更新

クライアントの新しいバージョンがリリースされました。 詳細については、次の記事を参照してください。

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)

他のクライアントの詳細については、「[クライアント更新](#client-updates)」をご覧ください。

## <a name="september-2020"></a>2020 年 9 月

2020 年 9 月における変更点は次のとおりです。

- 次の Azure 地域での接続の待ち時間を短縮することで、パフォーマンスを最適化しました。
    - ドイツ
    - 南アフリカ (検証環境のみ)

[エクスペリエンス予測ツール](https://azure.microsoft.com/services/virtual-desktop/assessment/)を使用して、これらの地域のユーザー エクスペリエンス品質を見積もることができるようになりました。

- Azure Virtual Desktop 用 Windows デスクトップ クライアントのバージョン 1.2.1364 がリリースされました。 この更新では、次の変更を行いました。
    - シングル サインオン (SSO) が Windows 7 で動作しなかった問題を修正しました。
    - Teams のメディア最適化を有効にしているユーザーが、別のアプリでオーディオ ストリームを排他モードで開いている場合に、Teams の会議に通話または参加しようとしたときにクライアントが切断される原因となっていた問題を修正しました。
    - Teams のメディア最適化が有効になっているときに、Teams でオーディオまたはビデオ デバイスを列挙できなかった問題を修正しました。
    - "設定にサポートが必要ですか?" リンクをデスクトップの設定ページに追加しました。
    - ハイコントラストの濃色テーマの使用時に発生する "サブスクライブ" ボタンの問題を修正しました。
    
- ユーザーからの多大な支援によって、Microsoft Store リモート デスクトップ クライアントの 2 つの重大な問題を修正することができました。 クライアントの段階的リリースを世界中の多くのユーザーに拡げるなかで、引き続きフィードバックを確認しながら問題を修正していきます。
    
- Azure portal でお客様のデプロイに VM を追加するワークフローの一環として、VM の場所、イメージ、リソース グループ、プレフィックス名、ネットワーク構成を変更できるようにする新機能が追加されました。

- IT 担当者は、Microsoft エンドポイント マネージャーを使用して、ハイブリッド Azure Active Directory 参加済み Windows 10 Enterprise VM を管理できるようになりました。 詳細については、[こちらのブログ記事](https://techcommunity.microsoft.com/t5/microsoft-endpoint-manager-blog/microsoft-endpoint-manager-announces-support-for-windows-virtual/ba-p/1681048)を参照してください。

## <a name="august-2020"></a>2020 年 8 月

2020 年 8 月における変更点は次のとおりです。

- 次の Azure リージョンで、パフォーマンスの向上により接続の待ち時間が短縮されています。 

    - イギリス
    - フランス
    - ノルウェー
    - 韓国

   [エクスペリエンス予測ツール](https://azure.microsoft.com/services/virtual-desktop/assessment/)を使用すると、これらの変更がユーザーに与える影響を大まかに理解できます。

- Microsoft Store リモート デスクトップ クライアント (v10.2.1522+) の一般提供が開始されました。 このバージョンの Microsoft Store リモート デスクトップ クライアントは、Azure Virtual Desktop と互換性があります。 また、ユーザー エクスペリエンスを向上させるために、更新された UI flows も導入されました。 この更新には、流暢なデザイン、ライト モードとダーク モード、およびその他の多くの興味深い変更が含まれています。 また、クライアントが書き直され、基になるリモート デスクトップ プロトコル (RDP) エンジンに、iOS、macOS、および Android クライアントと同じものが使用されるようになりました。 これにより、すべてのプラットフォームに新しい機能をより迅速に提供できます。 [クライアントをダウンロード](https://www.microsoft.com/p/microsoft-remote-desktop/9wzdncrfj3ps?rtc=1&activetab=pivot:overviewtab)して、お試しください。

- Teams デスクトップ クライアント (バージョン 1.3.00.21759) でチャット、チャネル、予定表に UTC タイム ゾーンのみが表示されていた問題が修正されました。 更新されたクライアントでは、代わりにリモート セッションのタイム ゾーンが表示されるようになりました。

- Azure Advisor が Azure Virtual Desktop の一部になりました。 Azure portal から Azure Virtual Desktop にアクセスすると、お使いの Azure Virtual Desktop 環境を最適化するための推奨事項を確認できます。 [Azure Advisor](azure-advisor.md) の詳細情報。

- Azure CLI で Azure Virtual Desktop (`az desktopvirtualization`) がサポートされ、Azure Virtual Desktop のデプロイを自動化できるようになりました。 拡張コマンドの一覧については、「[desktopvirtualization](/cli/azure/desktopvirtualization)」をご覧ください。

- Azure Virtual Desktop の Azure Resource Manager インターフェイスとの完全な互換性が確保されるようにデプロイ テンプレートが更新されました。 このテンプレートは [GitHub](https://github.com/Azure/RDS-Templates/tree/master/ARM-wvd-templates) で見つけることができます。

- Azure Virtual Desktop US Gov ポータルがパブリック プレビューになりました。 詳細については、[こちらのお知らせ](https://azure.microsoft.com/updates/windows-virtual-desktop-is-now-available-in-the-azure-government-cloud-in-preview/)を参照してください。

## <a name="july-2020"></a>2020 年 7 月  

Azure Resource Management と統合された Azure Virtual Desktop の一般提供が 7 月に開始されました。

この新しいリリースでの変更点は次のとおりです。 

- "Fall 2019 リリース" は "Azure Virtual Desktop (クラシック)" という名称になり、"Spring 2020 リリース" は "Azure Virtual Desktop" という名称になりました。 詳細については、[こちらのブログ記事](https://azure.microsoft.com/blog/new-windows-virtual-desktop-capabilities-now-generally-available/)を参照してください。 

新機能の詳細については、[こちらのブログ記事](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245)を参照してください。 

### <a name="autoscaling-tool-update"></a>自動スケーリング ツールの更新

プレビュー段階だった自動スケーリング ツールの最新バージョンが一般提供となりました。 このツールは、Azure Automation アカウントと Azure Logic Apps を使用して、ホスト プール内のセッション ホスト VM を自動的にシャットダウン、再起動することで、インフラストラクチャ コストを抑えています。 詳細については、「[Azure Automation を使用してセッション ホストをスケーリングする](set-up-scaling-script.md)」を参照してください。

### <a name="azure-portal"></a>Azure portal

Azure portal から Azure Virtual Desktop で次のことを行えるようになりました。 

- 個人用デスクトップ セッション ホストに直接ユーザーを割り当てる  
- ホスト プールの検証環境の設定を変更する 

### <a name="diagnostics"></a>診断

Log Analytics ワークスペース向けに、あらかじめ構築されたいくつかのクエリを新たにリリースしました。 これらのクエリにアクセスするには、 **[ログ]** に移動し、 **[カテゴリ]** の **[Azure Virtual Desktop]** を選択します。 詳細については、「[診断機能に Log Analytics を使用する](diagnostics-log-analytics.md)」を参照してください。

### <a name="update-for-remote-desktop-client-for-android"></a>Android 用リモート デスクトップ クライアントの更新

[Android 用リモート デスクトップ クライアント](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx)で、Azure Virtual Desktop 接続がサポートされるようになりました。 バージョン 10.0.7 以降の Android クライアントには、ユーザー エクスペリエンスを改良した新しい UI が備わっています。 また、このクライアントでは、Android デバイス上の Microsoft Authenticator との統合により、Azure Virtual Desktop ワークスペースをサブスクライブする際に条件付きアクセスが可能となります。  

以前のバージョンのリモート デスクトップ クライアントは、今後 "Remote Desktop 8" と呼ばれます。 以前のバージョンのクライアントにあった既存の接続は、新しいクライアントにシームレスに移行されます。 新しいクライアントは、基になる RDP コア エンジンが、すべてのプラットフォームの中で新機能がいち早くリリースされた iOS や macOS クライアントと同じものに書き換えられています。 

### <a name="teams-update"></a>Teams の更新

Azure Virtual Desktop 用の Microsoft Teams に対して機能強化が行われました。 最も重要なのは、Azure Virtual Desktop で Windows デスクトップ クライアントのオーディオとビデオの最適化がサポートされるようになったことです。 ユーザーが通話や会議でオーディオまたはビデオを使用する際にユーザー間に直接パスを作成することで、転送による待ち時間が短縮されます。 距離が短いほどホップ数が少なくなるため、通話時の表示と音が滑らかになります。 詳細については、[Azure Virtual Desktop での Teams の使用](./teams-on-avd.md)に関する記事を参照してください。

## <a name="june-2020"></a>2020 年 6 月

先月、Azure Resource Manager と統合された Azure Virtual Desktop がプレビューとして導入されました。 この更新プログラムには、多くの魅力的な新機能が用意されています。 ここでは、このバージョンの Azure Virtual Desktop の新機能について説明します。

### <a name="azure-virtual-desktop-is-now-integrated-with-azure-resource-manager"></a>Azure Resource Manager と統合された Azure Virtual Desktop

Azure Virtual Desktop が Azure Resource Manager と統合されました。 最新の更新で、すべての Azure Virtual Desktop オブジェクトが Azure Resource Manager リソースになりました。 また、この更新プログラムは、Azure ロールベースのアクセス制御 (Azure RBAC) とも統合されています。 詳細については、「[Azure Resource Manager とは](../azure-resource-manager/management/overview.md)」を参照してください。

この変更による効果は次のとおりです。

- Azure Virtual Desktop が Azure portal と統合されました。 つまり、ポータルで直接すべてを管理できるため、PowerShell、Web アプリ、サードパーティ製のツールは必要ありません。 開始するには、[Azure portal でのホスト プールの作成](create-host-pools-azure-marketplace.md)に関するチュートリアルを参照してください。

- この更新プログラムより前は、RemoteApp とデスクトップを個々のユーザーにしか発行できませんでした。 Azure Resource Manager を使用すると、Azure Active Directory グループにリソースを発行できるようになりました。

- 以前のバージョンの Azure Virtual Desktop には、テナントまたはホスト プールに割り当てることができる組み込み管理者ロールが 4 つありました。 これらのロールは、[Azure ロールベースのアクセス制御 (Azure RBAC)](../role-based-access-control/overview.md) に組み込まれました。 これらのロールは、すべての Azure Virtual Desktop Azure Resource Manager オブジェクトに適用できます。これにより、完全な多機能の委任モデルを作成できます。

- この更新プログラムでは、ホスト プールを拡張するために Azure Marketplace または GitHub テンプレートを繰り返し実行する必要がなくなりました。 ホスト プールを拡張するために必要なのは、Azure portal で対象のホスト プールに移動し、 **[+ 追加]** を選択して追加のセッション ホストをデプロイすることだけです。

- ホスト プールのデプロイは、[Azure Shared Image Gallery](../virtual-machines/shared-image-galleries.md) と完全に統合されました。 Shared Image Gallery は、イメージのバージョン管理を含む VM イメージ定義を格納する、別の Azure サービスです。 また、グローバル レプリケーションを使用して、イメージをコピーし、ローカル デプロイ用に他の Azure リージョンに送信することもできます。

- 従来 PowerShell または診断サービス Web アプリを通じて実行されていた監視機能は、Azure portal の Log Analytics に移動されました。 また、レポートを視覚化するための 2 つのオプションも用意されました。 Kusto クエリを実行し、Workbooks を使用すると、ビジュアル レポートを作成できます。

- Azure Virtual Desktop を使用するために、Azure Active Directory (Azure AD) の同意を完了する必要がなくなりました。 この更新プログラムでは、Azure サブスクリプションの Azure AD テナントによってユーザーが認証され、管理者に Azure RBAC 制御が提供されます。

### <a name="powershell-support"></a>PowerShell のサポート

この更新プログラムでは、Azure PowerShell Az モジュールに新しい AzWvd コマンドレットが追加されました。 この新しいモジュールは、.NET Core で実行される PowerShell Core でサポートされます。

このモジュールをインストールするには、「[Azure Virtual Desktop 用 PowerShell モジュールを設定する](powershell-module.md)」の手順に従ってください。

また、使用可能なコマンドの一覧は、[AzWvd PowerShell リファレンス](/powershell/module/az.desktopvirtualization/#desktopvirtualization)で確認できます。

新機能の詳細については、[こちらのブログ記事](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245)を確認してください。

### <a name="additional-gateways"></a>追加のゲートウェイ

接続の待ち時間を短縮するために、南アフリカに新しいゲートウェイ クラスターが追加されました。

### <a name="microsoft-teams-on-azure-virtual-desktop-preview"></a>Microsoft Teams on Azure Virtual Desktop (プレビュー)

Azure Virtual Desktop 用の Microsoft Teams に対していくつかの機能強化が行われました。 最も重要なのは、Azure Virtual Desktop で、通話のオーディオとビジュアルのリダイレクトがサポートされるようになったことです。 ユーザーがオーディオまたはビデオを使用して通話する際にユーザー間に直接パスを作成することで、転送による待ち時間が短縮されます。 距離が短いほどホップ数が少なくなるため、通話時の表示と音が滑らかになります。

詳細については、[こちらのブログ記事](https://azure.microsoft.com/updates/windows-virtual-desktop-media-optimization-for-microsoft-teams-is-now-available-in-public-preview/)を参照してください。

## <a name="next-steps"></a>次のステップ

今後の計画については、[Microsoft 365 Azure Virtual Desktop のロードマップ](https://www.microsoft.com/microsoft-365/roadmap?filters=Windows%20Virtual%20Desktop)に関するページを参照してください。
