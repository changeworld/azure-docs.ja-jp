---
title: Azure Virtual Desktop の Windows 11 VM に言語パックをインストールする - Azure
description: Azure Virtual Desktop の Windows 11 VM に言語パックをインストールする方法を説明します。
author: Heidilohr
ms.topic: how-to
ms.date: 10/04/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 365d2821ce0fec8fdf901ad4dc663e8325267803
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2021
ms.locfileid: "129536619"
---
# <a name="add-languages-to-a-windows-11-enterprise-image"></a>Windows 11 Enterprise イメージに言語を追加する

組織に属する世界中のユーザーが Azure Virtual Desktop デプロイを使用できるようにする必要があります。 仮想マシン (VM) に使用する Windows 11 Enterprise イメージをカスタマイズして、異なる言語パックを使用できるのは、そのためです。 Windows 11 以降では、管理者以外のユーザー アカウントで、表示言語とそれに対応する言語機能の両方を追加できるようになっています。 この機能は、個人用ホスト プール内のユーザー用の言語パックを事前にインストールする必要がないことを意味します。 プールされたホスト プールの場合でも、カスタム イメージに追加する予定の言語を追加することをお勧めします。 この記事の手順は、単一セッションとマルチセッションの両バージョンの Windows 11 Enterprise に使用できます。

異なる複数の言語のユーザーが組織に存在する場合は、次の 2 つの選択肢があります。

- 言語ごとにカスタマイズされたイメージを使用して、1 つの専用ホスト プールを作成します。
- 同じホスト プール内に、言語の異なる複数のユーザーを含めます。

2 番目の選択肢の方が、リソースとコストに関しては効率が高いのですが、いくつかの追加手順が必要です。 この記事を読むと、すべての言語とローカライズのニーズを持つユーザーに対応できるイメージを構築する方法を学習することができます。

## <a name="requirements"></a>必要条件

Windows 11 Enterprise VM を追加する前に、次のものを用意する必要があります。

-   Windows 11 Enterprise がインストールされている Azure VM
-   言語およびオプション機能 (LoF) ISO。 ISO は、[Windows 11 言語とオプション機能 LoF ISO](https://software-download.microsoft.com/download/sg/22000.1.210604-1628.co_release_amd64fre_CLIENT_LOF_PACKAGES_OEM.iso) でダウンロードできます。
-   Azure ファイル共有または Windows ファイル サーバー VM 上のファイル共有

>[!NOTE]
>ファイル共有リポジトリは、カスタム イメージの作成に使用する Azure VM からアクセス可能である必要があります。

## <a name="create-a-content-repository-for-language-packages-and-features-on-demand"></a>言語パッケージとオンデマンド機能のコンテンツ リポジトリを作成する

言語と機能を VM に追加するために使用するコンテンツ リポジトリを作成するには:

1. Azure で言語を追加する VM を開きます。

2. VM の [[要件]](#requirements) でダウンロードした ISO ファイルを開き、マウントします。

3. ファイル共有にフォルダーを作成します。

4. 作成したフォルダーに、ISO の **[LanguagesAndOptionalFeatures]** フォルダーから、すべてのコンテンツをコピーします。

     >[!NOTE]
     > 使用できるストレージが限られている場合は、マウントした "言語とオプション機能" ISO をリポジトリとして使用できます。 リポジトリを作成する方法を調べるには、[カスタム FOD と言語パック リポジトリの構築](/windows-hardware/manufacture/desktop/languages-overview#build-a-custom-fod-and-language-pack-repository)に関するページを参照してください。

     >[!IMPORTANT]
     > 言語によっては、異なる名前付け規則に従うサテライト パッケージに追加のフォントが必要になる場合があります。 たとえば、日本語のフォント ファイル名には "Jpan" が含まれます。
     >
     > [!div class="mx-imgBorder"]
     > ![ファイル名に言語タグ "Jpan" を含む日本語の言語パックの例。](media/language-pack-example.png)

5. 言語コンテンツ リポジトリ共有に対するアクセス許可を設定して、カスタム イメージの作成に使用する VM から読み取りアクセスができるようにします。

## <a name="create-a-custom-windows-11-enterprise-image-manually"></a>カスタム Windows 11 Enterprise イメージを手動で作成する

カスタム イメージを作成するには、次のステップを実行します。

1. Azure VM をデプロイし、Azure ギャラリーにアクセスして、使用している Windows 11 Enterprise の現在のバージョンを選択します。

2. VM をデプロイしたら、ローカル管理者として RDP を使用して VM に接続します。

3. 「[言語パッケージとオンデマンド機能のコンテンツ リポジトリを作成する](#create-a-content-repository-for-language-packages-and-features-on-demand)」で作成したファイル共有リポジトリに接続し、それにドライブ文字 (ドライブ E など) を割り当てます。

4. 管理者特権での PowerShell セッションから、次の PowerShell スクリプトを実行して、言語パックとサテライト パッケージを Windows 11 Enterprise にインストールします。
   
   ```powershell
   ########################################################
   ## Add Languages to running Windows Image for Capture##
   ########################################################
   ##Disable Language Pack Cleanup##
   Disable-ScheduledTask -TaskPath "\Microsoft\Windows\AppxDeploymentClient\" -TaskName "Pre-staged app cleanup"
   Disable-ScheduledTask -TaskPath "\Microsoft\Windows\MUI\" -TaskName "LPRemove"
   Disable-ScheduledTask -TaskPath "\Microsoft\Windows\LanguageComponentsInstaller" -TaskName "Uninstallation"
   reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Control Panel\International" /v "BlockCleanupOfUnusedPreinstalledLangPacks" /t REG_DWORD /d 1 /f

   ##Set Language Pack Content Stores##
   $LIPContent = "E:"

   ##Set Path of CSV File##
   $CSVFile = "Windows-10-1809-FOD-to-LP-Mapping-Table.csv"
   $filePath = (Get-Location).Path + "/$CSVFile"

   ##Import Necesarry CSV File##
   $FODList = Import-Csv -Path $filePath -Delimiter ";"

   ##Set Language (Target)##
   $targetLanguage = "es-es"

   $sourceLanguage = (($FODList | Where-Object {$_.'Target Lang' -eq $targetLanguage}) | Where-Object {$_.'Source Lang' -ne $targetLanguage} | Select-Object -Property 'Source Lang' -Unique).'Source Lang'
   if(!($sourceLanguage)){
       $sourceLanguage = $targetLanguage
   }

   $langGroup = (($FODList | Where-Object {$_.'Target Lang' -eq $targetLanguage}) | Where-Object {$_.'Lang Group:' -ne ""} | Select-Object -Property 'Lang Group:' -Unique).'Lang Group:'

   ##List of additional features to be installed##
   $additionalFODList = @(
       "$LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~~.cab", 
       "$LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~$sourceLanguage~.cab",
       "$LIPContent\Microsoft-Windows-SnippingTool-FoD-Package~31bf3856ad364e35~amd64~$sourceLanguage~.cab",
       "$LIPContent\Microsoft-Windows-Lip-Language_x64_$sourceLanguage.cab" ##only if applicable##
   )
   
   $additionalCapabilityList = @(
    "Language.Basic~~~$sourceLanguage~0.0.1.0",
    "Language.Handwriting~~~$sourceLanguage~0.0.1.0",
    "Language.OCR~~~$sourceLanguage~0.0.1.0",
    "Language.Speech~~~$sourceLanguage~0.0.1.0",
    "Language.TextToSpeech~~~$sourceLanguage~0.0.1.0"
    )

    ##Install all FODs or fonts from the CSV file###
    Dism /Online /Add-Package /PackagePath:$LIPContent\Microsoft-Windows-Client-Language-Pack_x64_$sourceLanguage.cab
    Dism /Online /Add-Package /PackagePath:$LIPContent\Microsoft-Windows-Lip-Language-Pack_x64_$sourceLanguage.cab
    foreach($capability in $additionalCapabilityList){
       Dism /Online /Add-Capability /CapabilityName:$capability /Source:$LIPContent
    }

    foreach($feature in $additionalFODList){
    Dism /Online /Add-Package /PackagePath:$feature
    }

    if($langGroup){
    Dism /Online /Add-Capability /CapabilityName:Language.Fonts.$langGroup~~~und-$langGroup~0.0.1.0 
    }

    ##Add installed language to language list##
    $LanguageList = Get-WinUserLanguageList
    $LanguageList.Add("$targetlanguage")
    Set-WinUserLanguageList $LanguageList -force
    ```

    >[!NOTE]
    >このサンプル スクリプトでは、スペイン語 (es-es) 言語コードを使用しています。 別の言語の適切なファイルを自動的にインストールするには、 *$targetLanguage* パラメーターを、正しい言語コードに変更します。 言語コードの一覧については、「[使用可能な Windows 用言語パック](/windows-hardware/manufacture/desktop/available-language-packs-for-windows)」を参照してください。

    インストールする必要がある言語の数によっては、スクリプトの実行終了までに時間がかかることがあります。 また、スクリプトを別の *$targetLanguage* パラメーターで再度実行して、初期セットアップ後に追加の言語をインストールすることもできます。

5. 適切なインストール ファイルを自動的に選択するには、[使用可能な Windows 10 1809 言語およびオンデマンド機能の表](https://download.microsoft.com/download/7/6/0/7600F9DC-C296-4CF8-B92A-2D85BAFBD5D2/Windows-10-1809-FOD-to-LP-Mapping-Table.xlsx )を CSV ファイルとしてダウンロードして保存し、PowerShell スクリプトと同じフォルダーに保存します。

6. スクリプトの実行が完了したら、言語パックが正しくインストールされていることを確認します。そのためには、 **[スタート]**  >  **[設定]**  >  **[時刻と言語]**  >  **[言語]** に移動します。 そこに言語ファイルがある場合は、完了しています。

7. 最後に、言語のインストール中に VM がインターネットに接続されている場合は、クリーンアップ プロセスを実行して、不要な言語エクスペリエンス パックを削除する必要があります。 ファイルをクリーンアップするには、次のコマンドを実行します。

    ```powershell
    ##Cleanup to prepare sysprep##
    Remove-AppxPackage -Package Microsoft.LanguageExperiencePackes-ES_22000.8.13.0_neutral__8wekyb3d8bbwe

    Remove-AppxPackage -Package Microsoft.OneDriveSync_22000.8.13.0_neutral__8wekyb3d8bbwe
    ```

    別の言語パックをクリーンアップするには、"es-ES" を、別の言語コードに置き換えます。

8. クリーンアップが完了したら、共有の接続を切断します。 

## <a name="finish-customizing-your-image"></a>イメージのカスタマイズを完了する

言語パックをインストールしたら、カスタマイズしたイメージに追加する他のソフトウェアをインストールできます。

イメージのカスタマイズが完了したら、システム準備ツール (sysprep) を実行する必要があります。

Sysprep を実行するには、次のようにします。

1. 管理者特権でコマンド プロンプトを開き、次のコマンドを実行してイメージを生成します。  
   
     ```cmd
     C:\Windows\System32\Sysprep\sysprep.exe /oobe /generalize /shutdown
     ```

2. 問題が発生した場合は、C ドライブの **Windows** > **System32** > **Sysprep** > **Panther** にある **SetupErr.log** ファイルをチェックします。 その後、[Microsoft Store アプリでの Sysprep の障害](/troubleshoot/windows-client/deployment/sysprep-fails-remove-or-update-store-apps)に関するページの手順に従って、セットアップのトラブルシューティングを行います。

3. セットアップが正常に終了した場合は、VM を停止してから、「[Azure で一般化された VM の管理対象イメージを作成する](../virtual-machines/windows/capture-image-resource.md)」の手順に従って、VM をマネージド イメージにキャプチャします。

4. これで、カスタマイズしたイメージを使用して Azure Virtual Desktop ホスト プールをデプロイできるようになりました。 ホスト プールのデプロイ方法の詳細については、「[チュートリアル: Azure portal を使用してホスト プールを作成する](create-host-pools-azure-marketplace.md)」を参照してください。

>[!NOTE]
>ユーザーが表示言語を変更するときは、Azure Virtual Desktop セッションからサインアウトした後に、再度サインインする必要があります。 サインアウトは、[スタート] メニューから行う必要があります。

## <a name="next-steps"></a>次のステップ

Windows 10 マルチセッション VM 用の言語パッケージのインストール方法については、「[Windows 10 マルチセッションへの言語パックの追加](language-packs.md)」を参照してください。

既知の問題の一覧については、[Windows 10 での言語の追加の既知の問題](/windows-hardware/manufacture/desktop/language-packs-known-issue)に関するページを参照してください。
