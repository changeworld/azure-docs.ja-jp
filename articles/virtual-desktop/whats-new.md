---
title: Windows Virtual Desktop の最新情報 - Azure
description: Windows Virtual Desktop の新機能と製品の更新プログラム。
author: Heidilohr
ms.topic: overview
ms.date: 08/07/2020
ms.author: helohr
ms.reviewer: thhickli; darank
manager: lizross
ms.openlocfilehash: 8663fc3f016fadcd4f4c99acd800cd0ccf8844f8
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2020
ms.locfileid: "88003532"
---
# <a name="whats-new-in-windows-virtual-desktop"></a>Windows Virtual Desktop の最新情報

Windows Virtual Desktop は定期的に更新されます。 この記事では、以下について説明します。

- 最新の更新プログラム
- 新機能
- 既存の機能の強化
- バグの修正

この記事は毎月更新されます。 こちらを頻繁に確認して、新しい更新プログラムに関する最新情報を入手してください。

## <a name="july-2020"></a>2020 年 7 月  

Azure Resource Management 統合を使用した Windows Virtual Desktop の一般提供が 7 月に開始されました。

この新しいリリースでの変更点は次のとおりです。 

- "Fall 2019 リリース" は、"Windows Virtual Desktop (クラシック)" という名称に変わり、今後は "Spring 2020 リリース" が "Windows Virtual Desktop" という名称になります。 詳細については、[こちらのブログ記事](https://azure.microsoft.com/blog/new-windows-virtual-desktop-capabilities-now-generally-available/)を参照してください。 

新機能の詳細については、[こちらのブログ記事](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245)を参照してください。 

### <a name="autoscaling-tool-update"></a>自動スケーリング ツールの更新

プレビュー段階だった自動スケーリング ツールの最新バージョンが一般提供となりました。 このツールは、Azure Automation アカウントと Azure Logic Apps を使用して、ホスト プール内のセッション ホスト仮想マシン (VM) を自動的にシャットダウン、再起動することで、インフラストラクチャ コストを抑えています。 詳細については、「[Azure Automation を使用してセッション ホストをスケーリングする](set-up-scaling-script.md)」を参照してください。

### <a name="azure-portal"></a>Azure portal

Azure portal から Windows Virtual Desktop で次のことを行えるようになりました。 

- 個人用デスクトップ セッション ホストに直接ユーザーを割り当てる  
- ホスト プールの検証環境の設定を変更する 

### <a name="diagnostics"></a>診断

Log Analytics ワークスペース向けに、あらかじめ構築されたいくつかのクエリを新たにリリースしました。 それらのクエリにアクセスするには、 **[ログ]** に移動し、 **[カテゴリ]** の **[Windows Virtual Desktop]** を選択します。 詳細については、「[診断機能に Log Analytics を使用する](diagnostics-log-analytics.md)」を参照してください。

### <a name="update-for-remote-desktop-client-for-android"></a>Android 用リモート デスクトップ クライアントの更新

[Android 用リモート デスクトップ クライアント](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx)で、新たに Windows Virtual Desktop 接続がサポートされます。 バージョン 10.0.7 以降の Android クライアントには、ユーザー エクスペリエンスを改良した新しい UI が備わっています。 また、このクライアントは、Windows Virtual Desktop ワークスペースをサブスクライブする際に、Android デバイス上で Microsoft Authenticator と連携することによって条件付きアクセスが可能となります。  

以前のバージョンのリモート デスクトップ クライアントは、今後 "Remote Desktop 8" と呼ばれます。 以前のバージョンのクライアントにあった既存の接続は、新しいクライアントにシームレスに移行されます。 新しいクライアントは、基になる RDP コア エンジンが、すべてのプラットフォームの中で新機能がいち早くリリースされた iOS や macOS クライアントと同じものに書き換えられています。 

### <a name="teams-update"></a>Teams の更新

Windows Virtual Desktop 用の Microsoft Teams に対して機能強化が行われました。 特筆すべき点として、Windows Virtual Desktop では新たに、Windows デスクトップ クライアント向けのオーディオとビデオの最適化がサポートされました。 ユーザーが通話や会議でオーディオまたはビデオを使用する際にユーザー間に直接パスを作成することで、転送による待ち時間が短縮されます。 距離が短いほどホップ数が少なくなるため、通話時の表示と音が滑らかになります。 [Windows Virtual Desktop で Teams を使用する](teams-on-wvd.md)方法に関するページを参照してください。

## <a name="june-2020"></a>2020 年 6 月

先月、Azure Resource Manager 統合を使用した Windows Virtual Desktop をプレビューとして導入しました。 この更新プログラムには、多くの魅力的な新機能が用意されています。 ここでは、このバージョンの Windows Virtual Desktop について説明します。

### <a name="windows-virtual-desktop-is-now-integrated-with-azure-resource-manager"></a>Windows Virtual Desktop が Azure Resource Manager と統合

Windows Virtual Desktop が Azure Resource Manager と統合されました。 最新の更新プログラムでは、すべての Windows Virtual Desktop オブジェクトが Azure Resource Manager リソースになりました。 また、この更新プログラムは、Azure ロールベースのアクセス制御 (Azure RBAC) とも統合されています。 詳細については、「[Azure Resource Manager とは](../azure-resource-manager/management/overview.md)」を参照してください。

この変更による効果は次のとおりです。

- Windows Virtual Desktop が Azure portal と統合されました。 つまり、ポータルで直接すべてを管理できるため、PowerShell、Web アプリ、サードパーティ製のツールは必要ありません。 開始するには、[Azure portal でのホスト プールの作成](create-host-pools-azure-marketplace.md)に関するチュートリアルを参照してください。

- この更新プログラムより前は、RemoteApp とデスクトップを個々のユーザーにしか発行できませんでした。 Azure Resource Manager を使用すると、Azure Active Directory グループにリソースを発行できるようになりました。

- 以前のバージョンの Windows Virtual Desktop には、テナントまたはホスト プールに割り当てることができる組み込み管理者ロールが 4 つありました。 これらのロールは、[Azure ロールベースのアクセス制御 (Azure RBAC)](../role-based-access-control/overview.md) に組み込まれました。 これらのロールは、すべての Windows Virtual Desktop Azure Resource Manager オブジェクトに適用することができます。これにより、完全な多機能な委任モデルを作成できます。

- この更新プログラムでは、ホスト プールを拡張するために Azure Marketplace または GitHub テンプレートを繰り返し実行する必要がなくなりました。 ホスト プールを拡張するために必要なのは、Azure portal で対象のホスト プールに移動し、 **[+ 追加]** を選択して追加のセッション ホストをデプロイすることだけです。

- ホスト プールのデプロイは、[Azure Shared Image Gallery](../virtual-machines/windows/shared-image-galleries.md) と完全に統合されました。 Shared Image Gallery は、イメージのバージョン管理を含む仮想マシン (VM) イメージ定義を格納する、別の Azure サービスです。 また、グローバル レプリケーションを使用して、イメージをコピーし、ローカル デプロイ用に他の Azure リージョンに送信することもできます。

- 従来 PowerShell または診断サービス Web アプリを通じて実行されていた監視機能は、Azure portal の Log Analytics に移動されました。 また、レポートを視覚化するための 2 つのオプションも用意されました。 Kusto クエリを実行し、Workbooks を使用すると、ビジュアル レポートを作成できます。

- Windows Virtual Desktop を使用するために Azure Active Directory (Azure AD) の同意を完了する必要がなくなりました。 この更新プログラムでは、Azure サブスクリプションの Azure AD テナントによってユーザーが認証され、管理者に Azure RBAC 制御が提供されます。


### <a name="powershell-support"></a>PowerShell のサポート

この更新プログラムでは、Azure PowerShell Az モジュールに新しい AzWvd コマンドレットが追加されました。 この新しいモジュールは、.NET Core で実行される PowerShell Core でサポートされます。

このモジュールをインストールするには、「[Windows Virtual Desktop 用 PowerShell モジュールを設定する](powershell-module.md)」の手順に従ってください。

また、使用可能なコマンドの一覧は、[AzWvd PowerShell リファレンス](/powershell/module/az.desktopvirtualization/?view=azps-4.2.0#desktopvirtualization)で確認できます。

新機能の詳細については、[こちらのブログ記事](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245)を確認してください。

### <a name="additional-gateways"></a>追加のゲートウェイ

接続の待ち時間を短縮するために、南アフリカに新しいゲートウェイ クラスターが追加されました。

### <a name="microsoft-teams-on-windows-virtual-desktop-preview"></a>Windows Virtual Desktop での Microsoft Teams (プレビュー)

Windows Virtual Desktop 用の Microsoft Teams に対していくつかの機能強化が行われました。 最も重要なこととして、Windows Virtual Desktop では、オーディオやビジュアルによる通話の転送がサポートされるようになりました。 ユーザーがオーディオまたはビデオを使用して通話する際にユーザー間に直接パスを作成することで、転送による待ち時間が短縮されます。 距離が短いほどホップ数が少なくなるため、通話時の表示と音が滑らかになります。

詳細については、[こちらのブログ記事](https://azure.microsoft.com/updates/windows-virtual-desktop-media-optimization-for-microsoft-teams-is-now-available-in-public-preview/)を参照してください。

## <a name="next-steps"></a>次のステップ

今後の計画については、[Microsoft 365 Windows 仮想デスクトップ ロードマップ](https://www.microsoft.com/microsoft-365/roadmap?filters=Windows%20Virtual%20Desktop)に関するページを参照してください。

Windows Virtual Desktop およびリモート デスクトップ サービスに関するクライアント向けの更新プログラムについては、次の記事を参照してください。

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [macOS](/windows-server/remote/remote-desktop-services/clients/mac-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)
- [Android](/windows-server/remote/remote-desktop-services/clients/android-whatsnew)
- [Web](/windows-server/remote/remote-desktop-services/clients/web-client-whatsnew)
