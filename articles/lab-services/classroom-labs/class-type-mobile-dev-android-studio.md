---
title: Android Studio でのモバイル アプリケーション開発を教えるためのラボを設定する
titleSuffix: Azure Lab Services
description: Android Studio を使用したデータ モバイル アプリケーション開発を教えるクラスのためのラボを設定する方法について説明します。  この記事ではさらに、Azure 内の仮想マシンで Android Studio を使用する場合に行う調整についても説明します。
services: lab-services
author: emaher
ms.service: lab-services
ms.topic: article
ms.date: 1/23/2020
ms.author: enewman
ms.openlocfilehash: 0c257589a2e93ac4c15a639e7156d0c0944b033c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "76851338"
---
# <a name="set-up-a-lab-to-teach-data-mobile-application-development-with-android-studio"></a>Android Studio でのデータ モバイル アプリケーション開発を教えるためのラボを設定する

この記事では、モバイル アプリケーション開発の入門クラスを設定する方法について説明します。  このクラスでは、[Google Play ストア](https://play.google.com/store/apps)に発行できる Android モバイル アプリを重点的に取り上げます。  学生は、[Android Studio](https://developer.android.com/studio) を使用してアプリケーションをビルドする方法を学習します。  [Visual Studio Emulator for Android](https://visualstudio.microsoft.com/vs/msft-android-emulator/) は、そのアプリケーションをローカルでテストするために使用されます。

## <a name="lab-configuration"></a>ラボの構成

このラボを設定するには、Azure サブスクリプションとラボ アカウントが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。 Azure サブスクリプションを取得したら、Azure Lab Services で新しいラボ アカウントを作成できます。 新しいラボ アカウントを作成する方法の詳細については、[ラボ アカウントを設定するためのチュートリアル](tutorial-setup-lab-account.md)を参照してください。  既存のラボ アカウントを使用することもできます。

[クラスルーム ラボの設定チュートリアル](tutorial-setup-classroom-lab.md)に従って、新しいラボを作成し、次の設定を適用します。

| 仮想マシンのサイズ | Image |
| -------------------- | ----- |
| 中 (入れ子になった仮想化) | Windows Server 2019 Datacenter |

## <a name="template-machine-configuration"></a>テンプレート マシンの構成

テンプレート マシンの作成が完了したら、[マシンを起動して接続](how-to-create-manage-template.md#update-a-template-vm)し、次のタスクを実行します。

1. Hyper-V ロールを追加する
2. Java をダウンロードしてインストールする  
3. Visual Studio Emulator for Android をダウンロードしてインストールする
4. Android Studio をダウンロードしてインストールする
5. Visual Studio Emulator for Android Studio を構成する

## <a name="add-hyper-v-role"></a>Hyper-V ロールを追加する

Visual Studio Emulator for Android のインストールを正常に終了するには、Hyper-V を有効にする必要があります。  記事「[Azure Lab Services のテンプレート仮想マシンで入れ子になった仮想化を有効にする](how-to-enable-nested-virtualization-template-vm.md)」で説明する手順に従います。

## <a name="install-java"></a>Java のインストール

Android Studio には、Java が必要です。  次の手順に従って、最新バージョンの Java をダウンロードします。

1. [Java のダウンロード ページ](https://www.java.com/download/)に移動します。 **[無料 Java のダウンロード]** ボタンをクリックします。
2. Windows 版 64 ビット Java の Web ページで、"**同意して無料ダウンロードを開始**" というラベルの付いたボタンをクリックします。
3. **Java セットアップ** インストーラーが表示されたら、 **[インストール]** をクリックします。
4. インストーラーのタイトルが "**Java セットアップ - 完了**" に変わるまで待ちます。  **[閉じる]** ボタンをクリックします。

## <a name="install-visual-studio-emulator-for-android"></a>Visual Studio Emulator for Android のインストール

Android アプリケーションをローカルでテストするには、Android デバイスの仮想化バージョンを使用する必要があります。  開発者が自分のマシンからアプリケーションをテストするために使用できる Android エミュレーターがいくつかあります。  Visual Studio Emulator for Android は入れ子になった仮想化をサポートするエミュレーターであるため、これを使用します。  ラボ サービスの VM は既に仮想マシンであるため、入れ子になった仮想化をサポートするエミュレーターが必要です。  Android Studio 用の組み込みエミュレーターは、入れ子になった仮想化をサポートしません。  入れ子になった仮想化をサポートするエミュレーターを確認するには、「[エミュレーターのパフォーマンスのためのハードウェア高速化 (Hyper-V と HAXM)](https://docs.microsoft.com/xamarin/android/get-started/installation/android-emulator/hardware-acceleration)」を参照してください。

次の手順に従って、Visual Studio Emulator for Android をダウンロードし、インストールします。

1. [Visual Studio Emulator for Android](https://visualstudio.microsoft.com/vs/msft-android-emulator/) のホーム ページに移動します。
2. **エミュレーターのダウンロード** ボタンをクリックします。
3. vs_emulatorsetup.exe をダウンロードしたら、実行可能ファイルを実行します。
4. Visual Studio セットアップ ダイアログが表示されたら、 **[インストール]** ボタンをクリックします。
5. インストーラーが完了するまで待ちます。  **[今すぐ再起動]** ボタンをクリックして、コンピューターを再起動し、インストールを完了します。

Android Studio を使用してアプリケーションをデプロイする前に、まず、エミュレーターを起動します。  Visual Studio Emulator for Android の詳細については、[Visual Studio Emulator for Android に関するドキュメント](https://docs.microsoft.com/visualstudio/cross-platform/visual-studio-emulator-for-android)を参照してください。

## <a name="install-android-studio"></a>Android Studio のインストール

以下の手順に従って、[Android Studio](https://developer.android.com/studio) をダウンロードし、インストールします。

1. [Android Studio ダウンロード ページ](https://developer.android.com/studio#downloads)に移動します。  
    > [!NOTE]
    > このサイトでは、Internet Explorer はサポートされていません。
2. Windows (64 ビット) 版 Android Studio の実行可能パッケージをクリックします。
3. ポップアップに表示される法律条項を読みます。  続行する準備ができたら、 **[上記の利用規約を読んだうえで利用規約に同意します]** チェックボックスをオンにして、 **[ダウンロードする: Android Studio (Windows 用)]** ボタンをクリックします。
4. Android Studio Setup 実行可能ファイルをダウンロードしたら、それを実行します。
5. **Android Studio Setup** インストーラーの **[Welcome to Android Studio Setup]\(Android Studio セットアップへようこそ\)** ページで、 **[Next]\(次へ\)** をクリックします。
6. **[Configuration Settings]\(構成設定\)** ページで、 **[Next]\(次へ\)** をクリックします。
7. **[Choose Start Menu Folder]\(スタート メニュー フォルダーを選択\)** ページで、 **[Install]\(インストール\)** をクリックします。
8. セットアップが完了するまで待ちます。
9. **[Installation Complete]\(インストールの完了\)** ページで、 **[Next]\(次へ\)** をクリックします。
10. **[Completing Android Studio Setup]\(Android Studio セットアップの完了\)** ページで、  **[完了]** をクリックします。
11. セットアップが完了すると、Android Studio が自動的に起動します。
12. **[Import Android Settings From...]\(Android 設定を次の場所からインポート...\)** ダイアログで、 **[Do not import settings]\(設定をインポートしない\)** をオンにします。 **[OK]** をクリックします。
13. **Android Studio 設定ウィザード**の**ようこそ**ページで、 **[Next]\(次へ\)** をクリックします。
14. **[Install Type]\(インストールの種類\)** ページで、 **[Standard]\(標準\)** をオンにします。 **[次へ]** をクリックします。
15. **[Select UI Theme]\(UI テーマの選択\)** ページで、目的のテーマを選択します。 **[次へ]** をクリックします。
16. **[Verify Settings]\(設定の検証\)** ページで、 **[Next]\(次へ\)** をクリックします。
17. **[Downloading Components]\(コンポーネントのダウンロード\)** ページで、すべてのコンポーネントがダウンロードされるまで待ちます。  **[完了]** をクリックします。

    > [!IMPORTANT]
    > HAXM のインストールが失敗することが予想されます。  HAXM は入れ子になった仮想化をサポートしません。この記事で、Visual Studio Emulator for Android を先にインストールしたのは、このためです。

18. セットアップ ウィザードが完了すると、**Welcome to Android Studio** ダイアログが表示されます。

## <a name="configure-android-studio-and-visual-studio-emulator-for-android"></a>Android Studio と Visual Studio Emulator for Android の構成

Android Studio はほとんどすぐに使用できます。  ただし、Android SDK のインストール先を Visual Studio Emulator for Android に指示する必要があります。  これにより、Visual Studio for Android で実行されているすべてのエミュレーターが、Android Studio デバッグのデプロイ先として表示されます。

Android SDK が配置されている場所を Visual Studio Emulator for Android に指示するために、特定のレジストリ キーを設定する必要があります。  必要なレジストリ キーを設定するには、次のスクリプトを実行します。  次の PowerShell スクリプトは、Android SDK の既定のインストール場所を前提としています。  Android SDK を別の場所にインストールした場合は、スクリプトを実行する前に、`$androidSdkPath` の値を変更してください。

```powershell
$androidSdkPath = Resolve-Path $(Join-Path "$($env:APPDATA)" "../Local/Android/Sdk")

$registryKeyPath = "HKLM:Software\WOW6432NODE\Android Sdk Tools"
New-Item -Path $registryKeyPath
New-ItemProperty -Path $registryKeyPath -Name Path -PropertyType String -Value $androidSdkPath
```

> [!IMPORTANT]
> Visual Studio Emulator for Android と Android Studio を再起動して、新しい設定が使用されるようにします。

Visual Studio Emulator で必要なバージョンを起動します。  これは、Android Studio で、Android アプリのデプロイ先として表示されます。  Android Studio プロジェクトの最小バージョンは、Visual Studio Emulator for Android で実行されているバージョンをサポートする必要があります。  以上で、Android Studio と Visual Studio Emulator for Android を使用して、プロジェクトの作成とデバッグを行うことができるようになりました。  問題がある場合は、「Android Emulator のトラブルシューティング」を参照してください。

## <a name="cost"></a>コスト

このラボのコストを見積もるには、次の例を参照してください。
学生数が 25 人、予定されている授業時間が 20 時間のクラスで、10 時間分の宿題または課題を割り当てる場合、ラボの価格は次のようになります。  

25 人の学生 \* (予定されている 20 時間 + 割り当てられる 10 時間) * 55 ラボ ユニット * 0.01 USD/時間 = 412.5 USD

価格について詳しくは、「[Azure Lab Services の価格](https://azure.microsoft.com/pricing/details/lab-services/)」をご覧ください。

## <a name="next-steps"></a>次のステップ

次の手順は、すべてのラボの設定で共通です。

- [テンプレートの作成と管理](how-to-create-manage-template.md)
- [ユーザーの追加](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [クォータを設定する](how-to-configure-student-usage.md#set-quotas-for-users)
- [スケジュールを設定する](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [登録リンクを学生に電子メールで送る](how-to-configure-student-usage.md#send-invitations-to-users)
