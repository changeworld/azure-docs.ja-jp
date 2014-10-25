<properties linkid="manage-services-how-to-create-and-deploy-a-cloud-service" urlDisplayName="How to create and deploy" pageTitle="How to create and deploy a cloud service - Azure" metaKeywords="Azure creating cloud service, deleting cloud service" description="Learn how to create and deploy a cloud service using the Quick Create method in Azure." metaCanonical="" services="cloud-services" documentationCenter="" title="How to Create and Deploy a Cloud Service" authors="ryanwi" solutions="" manager="timlt" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="ryanwi"></tags>

# クラウド サービスを作成してデプロイする方法

[WACOM.INCLUDE [disclaimer][disclaimer]]

Azure の管理ポータルには、クラウド サービスを作成してデプロイする方法が 2 つ用意されています。**簡易作成**と**カスタム作成**です。

このトピックでは、簡易作成の方法を使って新しいクラウド サービスを作成し、その後、**[アップロード]** を使用して Azure にクラウド サービス パッケージをアップロードして展開する方法について説明します。この方法を使うと、Azure の管理ポータルに、必要な事項をすべて完了するのに便利なリンクが操作の進行につれて表示されます。クラウド サービスの作成時に展開する準備が整っている場合は、**[カスタム作成]** を使用して作成と展開を同時に実行できます。

**注:** Windows Team Foundation Service (TFS) からクラウド サービスを発行する予定の場合は、[簡易作成] を使用した後、**[クイック スタート]** またはダッシュボードから TFS 発行を設定する必要があります。詳細については、[Continuous Delivery to Azure by Using Visual Studio Online (Visual Studio Online を使用した Azure への継続的な配信に関するページ)][Continuous Delivery to Azure by Using Visual Studio Online (Visual Studio Online を使用した Azure への継続的な配信に関するページ)]を参照するか、**[クイック スタート]** ページのヘルプを参照してください。

## 目次

-   [概念][概念]
-   [アプリケーションの準備][アプリケーションの準備]
-   [開始する前に][開始する前に]
-   [方法: 簡易作成によるクラウド サービスの作成][方法: 簡易作成によるクラウド サービスの作成]
-   [方法: クラウド サービスの証明書のアップロード][方法: クラウド サービスの証明書のアップロード]
-   [方法: クラウド サービスのデプロイ][方法: クラウド サービスのデプロイ]

## <span id="concepts"></span></a>概念

Azure のクラウド サービスとしてアプリケーションを展開するには、3 つのコンポーネントが必要です。

> -   **サービス定義ファイル** クラウド サービス定義ファイル (.csdef) は、ロールの数も含めて、サービス モデルを定義します。

> -   **サービス構成ファイル** クラウド サービス構成ファイル (.cscfg) は、ロール インスタンスの数も含めて、クラウド サービスおよび個々のロールの構成設定を指定します。

> -   **サービス パッケージ** サービス パッケージ (.cspkg) は、アプリケーション コードとサービス定義ファイルを含みます。

## <span id="prepare"></span></a>アプリケーションの準備

クラウド サービスを展開する前に、アプリケーション コードとクラウド サービス構成ファイル (.cscfg) からクラウド サービス パッケージ (.cspkg) を作成する必要があります。各クラウド サービス パッケージは、アプリケーション ファイルと構成を含みます。サービス構成ファイルは構成設定を指定します。

Azure SDK には、こういった必須の展開ファイルを準備するためのツールが用意されています。SDK は、[Azure のダウンロード][Azure のダウンロード] ページからアプリケーション コードの開発に使用する言語でインストールできます。

クラウド サービスに慣れていない場合は、[Azure Code Samples][Azure Code Samples] からサンプルのクラウド サービス パッケージ (.cspkg) とクラウド サービス構成ファイル (.cscfg) をダウンロードして利用することができます。

サービス パッケージをエクスポートする前に、以下の 3 つのクラウド サービス機能について特別な構成が必要です。

-   データ暗号化のために Secure Sockets Layer (SSL) を使用するクラウド サービスを展開する場合は、アプリケーションを SSL 用に構成します。詳細については、「[HTTPS エンドポイントでの SSL 証明書の構成][HTTPS エンドポイントでの SSL 証明書の構成]」を参照してください。

-   ロール インスタンスに対するリモート デスクトップ接続を構成する場合は、ロールをリモート デスクトップ用に構成します。リモート アクセス用のサービス定義ファイルを準備する方法の詳細については、「[Overview of Setting Up a Remote Desktop Connection for a Role (ロールのリモート デスクトップ接続をセットアップする手順の概要)][Overview of Setting Up a Remote Desktop Connection for a Role (ロールのリモート デスクトップ接続をセットアップする手順の概要)]」を参照してください。

-   クラウド サービスの詳細監視を構成する場合は、クラウド サービスの Azure 診断を有効にします。*最小監視* (既定の監視レベル) では、ロール インスタンス (仮想マシン) のホスト オペレーティング システムから収集したパフォーマンス カウンターが使用されます。詳細監視は、ロール インスタンス内のパフォーマンス データに基づいて追加のメトリックを収集して、アプリケーション処理時に発生する問題を詳しく分析できます。Azure 診断を有効にする方法については、「[Enabling Diagnostics in Azure (Azure における診断の有効化)][Enabling Diagnostics in Azure (Azure における診断の有効化)]」を参照してください。

## <span id="begin"></span></a>開始する前に

-   Azure SDK をまだインストールしていない場合は、**[Azure SDK のインストール]** をクリックして [Azure のダウンロード ページ][Azure のダウンロード]を開き、アプリケーション コードの開発に使用する言語用の SDK をダウンロードします。(これは後でも実行する機会があります)。

-   証明書を必要とするロール インスタンスがある場合は、証明書を作成します。クラウド サービスには、秘密キーのある .pfx ファイルが必要です。証明書は、クラウド サービスを作成して展開するときに、Azure にアップロードできます。証明書の作成方法の詳細については、「[HTTPS エンドポイントでの SSL 証明書の構成][HTTPS エンドポイントでの SSL 証明書の構成]」を参照してください。

-   クラウド サービスをアフィニティ グループに展開することを予定している場合は、アフィニティ グループを作成します。アフィニティ グループを使用すると、自分のクラウド サービスおよびその他の Azure サービスをリージョンの同じ場所に展開することができます。アフィニティ グループは、管理ポータルの **[ネットワーク]** 領域の **[アフィニティ グループ]** ページで作成できます。詳細については、**[アフィニティ グループ]** ページのヘルプを参照してください。

## <span id="quick"></span></a>方法: 簡易作成によるクラウド サービスの作成

1.  [管理ポータル][管理ポータル]で、**[新規]**、**[コンピューティング]**、**[クラウド サービス]**、**[簡易作成]** の順にクリックします。

    ![CloudServices\_QuickCreate][CloudServices\_QuickCreate]

2.  **[URL]** ボックスに、運用展開のクラウド サービスにアクセスするパブリック URL で使用するサブドメイン名を入力します。運用デプロイの URL 形式は、<http://*myURL*.cloudapp.net.> です。

3.  **[リージョン/アフィニティ グループ]** で、クラウド サービスを展開するリージョンまたはアフィニティ グループを選択します。リージョン内の他の Azure サービスと同じ場所にクラウド サービスを展開する場合は、アフィニティ グループを選択します。

    > [WACOM.NOTE]
    > アフィニティ グループを作成するには、管理ポータルの **[ネットワーク]** 領域を開き、**[アフィニティ グループ]** をクリックして、**[新しいアフィニティ グループの作成]** または **[作成]** をクリックします。以前の Azure の管理ポータルで作成したアフィニティ グループも使用できます。また、Azure サービス管理 API を使用してアフィニティ グループを作成し管理することもできます。詳細については、「[アフィニティ グループに対する操作][アフィニティ グループに対する操作]」を参照してください。

4.  **[クラウド サービスを作成する]** をクリックします。

    処理の状態はウィンドウの下部にあるメッセージ領域で監視できます。

    **[クラウド サービス]** 領域が開き、新しいクラウド サービスが表示されます。状態が [作成済み] に変わると、クラウド サービスの作成が正常に完了しています。

    ![CloudServices\_CloudServicesPage][CloudServices\_CloudServicesPage]

    Secure Sockets Layer (SSL) によるデータ暗号化のために証明書を必要とするロールがクラウド サービスにあり、証明書がまだ Azure にアップロードされていない場合は、クラウド サービスを展開する前に証明書をアップロードする必要があります。証明書をアップロードすると、ロール インスタンスで実行されているどの Windows アプリケーションも証明書にアクセスできるようになります。

## <span id="uploadcertificate"></span></a>方法: クラウド サービスの証明書のアップロード

1.  [管理ポータル][管理ポータル]で、**[クラウド サービス]** をクリックします。目的のクラウド サービスの名前をクリックして、ダッシュボードを開きます。

    ![CloudServices\_EmptyDashboard][CloudServices\_EmptyDashboard]

2.  **[証明書]** をクリックします。下図のような **[証明書]** ページが開きます。

    ![CloudServices\_CertificatesPage][CloudServices\_CertificatesPage]

3.  **[証明書をアップロードします]** をクリックします。
    **[証明書のアップロード]** が開きます。

    ![CloudServices\_AddaCertificate][CloudServices\_AddaCertificate]

4.  **[証明書ファイル]** で、**[参照]** を使って、使用する証明書 (.pfx ファイルまたは .cer ファイル) を選択します。

5.  **[パスワード]** ボックスに、証明書の秘密キーを入力します。

6.  [OK] (チェックマーク) をクリックします。

    アップロードの進捗状況は下図のメッセージ領域で監視できます。アップロードが完了すると、証明書がテーブルに追加されます。メッセージ領域で、下向き矢印をクリックしてメッセージを閉じるか、[X] をクリックしてメッセージを削除します。

    ![CloudServices\_CertificateProgress][CloudServices\_CertificateProgress]

    クラウド サービスの展開は、ダッシュボードまたは **[クイック スタート]** から実行できます。

## <span id="deploy"></span></a>方法: クラウド サービスのデプロイ

1.  Azure SDK をまだインストールしていない場合は、**[Azure SDK のインストール]** をクリックして [Azure のダウンロード ページ][Azure のダウンロード]を開き、アプリケーション コードの開発に使用する言語用の SDK をダウンロードします。

    ダウンロード ページでは、Node.js や Java、PHP などの言語で Web アプリケーションを開発するためのクライアント ライブラリやソース コードをインストールして、拡張性の高い Azure のクラウド サービスとして展開することもできます。

    > [WACOM.NOTE]
    > 以前に作成したクラウド サービス (旧称は*ホステッド サービス*) の場合、仮想マシン (ロール インスタンス) のゲスト オペレーティング システムが、インストールする Azure SDK バージョンと互換性があることを確認する必要があります。詳細については、「[Azure SDK .NET リリース ノート][Azure SDK .NET リリース ノート]」を参照してください。

2.  [管理ポータル][管理ポータル]で、**[クラウド サービス]** をクリックします。目的のクラウド サービスの名前をクリックしてダッシュボードを開き、**[ダッシュボード]** をクリックします。

3.  **[運用]** または **[ステージング]** をクリックします。

    運用環境に展開する前に Azure でクラウド サービスをテストする場合は、ステージング環境に展開することができます。ステージング環境の場合、URL ではクラウド サービスのグローバル一意識別子 (GUID) によってクラウド サービスが識別されます (*GUID*.cloudapp.net)。運用環境の場合、もっとわかりやすい割り当てた DNS プレフィックスが使用されます (たとえば、*myservice*.cloudapp.net)。ステージング環境のクラウド サービスを運用環境に昇格する準備ができたら、**[スワップ]** を使用してクライアントのリクエストをその展開にリダイレクトします。

    ![CloudServices\_QuickStartPage][CloudServices\_QuickStartPage]

4.  ダッシュボードの **[アップロード]** をクリックして、クラウド サービスを展開します。

    ![CloudServices\_UploadaPackage][CloudServices\_UploadaPackage]

5.  **[展開ラベル]** ボックスに、新しい展開の名前を入力します (たとえば、MyCloudServicev1)。

6.  **[パッケージ]** で、**[参照]** を使って、使用するサービス パッケージ ファイル (.cspkg) を選択します。

7.  **[構成]** で、**[参照]** を使って、使用するサービス構成ファイル (.cscfg) を選択します。

8.  クラウド サービスにインスタンスが 1 つしかないロールがある場合は、**[1 つ以上のロールに単一のインスタンスが含まれている場合でも展開する]** チェック ボックスをオンにして、展開を有効にして続行します。

 Azure でクラウド サービスの保守中およびサービス更新中に 99.95% のアクセスを保証できるのは、あらゆるロールに少なくとも 2 つのインスタンスがある場合だけです。必要な場合、クラウド サービスを展開した後に、**[スケール]** ページで、ロール インスタンスを追加できます。詳細については、「[サービス レベル アグリーメント][サービス レベル アグリーメント]」を参照してください。

9.  [OK] (チェックマーク) をクリックして、クラウド サービスの展開を開始します。

    展開の状態は、メッセージ領域で監視できます。下向き矢印をクリックすると、メッセージが表示されなくなります。

    ![CloudServices\_UploadProgress][CloudServices\_UploadProgress]

### 展開が正常に完了したことを確認するには

1.  **[ダッシュボード]** をクリックします。

2.  **[概要]** で、サイト URL をクリックして、Web ブラウザーでクラウド サービスを開きます。

    ![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy/CloudServices_QuickGlance.png)


<!-- -->
  [disclaimer]: ../includes/disclaimer.md
  [Continuous Delivery to Azure by Using Visual Studio Online (Visual Studio Online を使用した Azure への継続的な配信に関するページ)]: http://go.microsoft.com/fwlink/?LinkID=251796&clcid=0x409
  [概念]: #concepts
  [アプリケーションの準備]: #prepare
  [開始する前に]: #begin
  [方法: 簡易作成によるクラウド サービスの作成]: #quick
  [方法: クラウド サービスの証明書のアップロード]: #uploadcertificate
  [方法: クラウド サービスのデプロイ]: #deploy
  [Azure のダウンロード]: http://www.windowsazure.com/ja-jp/develop/downloads/
  [Azure Code Samples]: http://code.msdn.microsoft.com/windowsazure/
  [HTTPS エンドポイントでの SSL 証明書の構成]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ff795779.aspx
  [Overview of Setting Up a Remote Desktop Connection for a Role (ロールのリモート デスクトップ接続をセットアップする手順の概要)]: http://msdn.microsoft.com/ja-jp/library/windowsazure/gg433010.aspx
  [Enabling Diagnostics in Azure (Azure における診断の有効化)]: http://www.windowsazure.com/ja-jp/develop/net/common-tasks/diagnostics/
  [管理ポータル]: http://manage.windowsazure.com/
  [CloudServices\_QuickCreate]: ./media/cloud-services-how-to-create-deploy/CloudServices_QuickCreate.png
  [アフィニティ グループに対する操作]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ee460798.aspx
  [CloudServices\_CloudServicesPage]: ./media/cloud-services-how-to-create-deploy/CloudServices_CloudServicesPage.png
  [CloudServices\_EmptyDashboard]: ./media/cloud-services-how-to-create-deploy/CloudServices_EmptyDashboard.png
  [CloudServices\_CertificatesPage]: ./media/cloud-services-how-to-create-deploy/CloudServices_CertificatesPage.png
  [CloudServices\_AddaCertificate]: ./media/cloud-services-how-to-create-deploy/CloudServices_AddaCertificate.png
  [CloudServices\_CertificateProgress]: ./media/cloud-services-how-to-create-deploy/CloudServices_CertificateProgress.png
  [Azure SDK .NET リリース ノート]: http://msdn.microsoft.com/ja-jp/library/windowsazure/hh552718.aspx
  [CloudServices\_QuickStartPage]: ./media/cloud-services-how-to-create-deploy/CloudServices_QuickStartPage.png
  [CloudServices\_UploadaPackage]: ./media/cloud-services-how-to-create-deploy/CloudServices_UploadaPackage.png
  [サービス レベル アグリーメント]: http://www.windowsazure.com/ja-jp/support/legal/sla/
  [CloudServices\_UploadProgress]: ./media/cloud-services-how-to-create-deploy/CloudServices_UploadProgress.png
