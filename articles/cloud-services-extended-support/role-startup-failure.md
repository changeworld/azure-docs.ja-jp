---
title: Azure Cloud Services (延長サポート) のロール インスタンスのスタートアップ エラー
description: Azure Cloud Services (延長サポート) のロール インスタンスのスタートアップ エラーをトラブルシューティングする
ms.topic: article
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachadw
ms.date: 04/01/2021
ms.custom: ''
ms.openlocfilehash: ced7675350c0e0deadf77837cf0f13ba54fffab9
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/05/2021
ms.locfileid: "106382357"
---
# <a name="troubleshoot-azure-cloud-service-extended-support-roles-that-fail-to-start"></a>起動できない Azure クラウド サービス (延長サポート) ロールをトラブルシューティングする
ここでは、Cloud Services (延長サービス) ロールの起動失敗に関連した一般的な問題と解決法を取り上げます。

## <a name="cloud-service-operation-failed-with-roleinstancestartuptimeouterror"></a>RoleInstanceStartupTimeoutError でクラウド サービスの操作に失敗しました
クラウド サービス (延長サポート) の 1 つ以上のロール インスタンスが、規定された時間内に開始しない場合があります。 これらのロール インスタンスは、開始に時間がかかっているか、リサイクルされている可能性があり、RoleInstanceStartupTimeoutError (ロール アプリケーション エラー) で失敗する場合があります。 ロール アプリケーションには、"スタートアップ タスク" と "ロール コード (RoleEntryPoint の実装)" という 2 つの主要な部分があり、どちらもロールのリサイクルを発生させる可能性があります。 ロールがクラッシュした場合、PaaS エージェントは常にそれを再起動します。 

エラーが発生した場合にロール インスタンスの現在の状態と詳細情報を取得するには、次のものを使用します。

* PowerShell: クラウド サービスのロール インスタンスの実行時の状態に関する情報を取得するには、[Get-AzCloudServiceRoleInstanceView](https://docs.microsoft.com/powershell/module/az.cloudservice/get-azcloudserviceroleinstanceview) コマンドレットを使用します。
```powershell
Get-AzCloudServiceRoleInstanceView -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstanceName "WebRole1_IN_0"
 
Statuses           PlatformFaultDomain PlatformUpdateDomain
--------           ------------------- --------------------
{RoleStateStarting} 0                   0
```

* Azure portal: クラウド サービスにアクセスし、[ロールとインスタンス] タブを選択します。ロール インスタンスをクリックすると、その状態の詳細が表示されます 
 
   :::image type="content" source="media/role-startup-failure-1.png" alt-text="画像は、ポータルでのロールのスタートアップ エラーを示しています。":::
   
ここでは、開始に失敗したり、初期化状態、ビジー状態、および停止状態への移行を繰り返したりする Azure Cloud Services (延長サポート) ロールに関連する一般的な問題と解決策について説明します。

## <a name="missing-dlls-or-dependencies"></a>DLL または依存コンポーネントの欠落
ロールが応答しない、 [初期化しています] 、 [ビジー] 、 [停止しています] の状態を繰り返す、といった症状は、DLL やアセンブリの不足が原因で起こる場合があります。
DLL やアセンブリの不足は、次のような症状を引き起こします。

* ロール インスタンスが **[初期化しています]** 、 **[ビジー]** 、 **[停止しています]** の状態を繰り返す。
* ロール インスタンスが **[準備完了]** 状態に移行したにもかかわらず、Web アプリケーションにアクセスしてもページが表示されない。

これらの問題に関して推奨される調査方法がいくつかあります。

## <a name="diagnose-missing-dll-issues-in-a-web-role"></a>DLL の欠落を Web ロールで診断する
Web ロールにデプロイされている Web サイトにアクセスし、ブラウザーに次のようなサーバー エラーが表示されるとき、それは DLL の欠落を意味している可能性があります。

:::image type="content" source="media/role-startup-failure-2.png" alt-text="画像は、ロールのスタートアップの失敗での実行時エラーを示しています":::

## <a name="diagnose-issues-by-turning-off-custom-errors"></a>カスタム エラーをオフにして問題を診断する
Web ロールの web.config でカスタム エラー モードをオフに設定して再度サービスをデプロイすると、もっと詳しいエラー情報を表示できます。
リモート デスクトップを使用せずに詳細なエラーを表示するには
1.  Microsoft Visual Studio でソリューションを開きます。
2.  ソリューション エクスプローラーで、web.config ファイルを開きます。
3.  web.config ファイルの system.web セクションを探し、次の行を追加します。
 ```xml
<customErrors mode="Off" />
```
4.  ファイルを保存します。
5.  サービスをパッケージし直して再度デプロイします。
サービスを再度デプロイすると、不足しているアセンブリまたは DLL の名前と共にエラー メッセージが表示されます。

## <a name="diagnose-issues-by-viewing-the-error-remotely"></a>遠隔からエラーを参照して問題を診断する
離れた場所からリモート デスクトップを使用してロールにアクセスし、詳細なエラー情報を参照することができます。 リモート デスクトップを使用してエラーを参照するには、次の手順に従います。
1.  クラウド サービスのリモート デスクトップ拡張機能を有効にします (延長サポート)。 詳細については、[Azure portal を使用した Cloud Services (延長サポート) へのリモート デスクトップ拡張機能の適用](enable-rdp.md)に関するページを参照してください
2.  Azure portal で、インスタンスの状態が [準備完了] と表示されたら、インスタンスにリモート接続します。 Cloud Services (延長サポート) でのリモート デスクトップの使用の詳細については、「[リモート デスクトップが有効になっているロール インスタンスに接続する](https://docs.microsoft.com/azure/cloud-services-extended-support/enable-rdp#connect-to-role-instances-with-remote-desktop-enabled)」のセクションを参照してください
3.  リモート デスクトップの構成中に指定した資格情報を使用して仮想マシンにサインインします。
4.  コマンド ウィンドウを開きます。
5.  「IPconfig」と入力します。
6.  IPv4 アドレスの値をメモします。
7.  Internet Explorer を開きます。
8.  Web アプリケーションのアドレスと名前を入力します。 たとえば、「 http://<IPV4 Address>/default.aspx」とします。
Web サイトにアクセスすると、詳しいエラー メッセージが表示されます。
* Server Error in '/' Application.
* 説明: 現在の Web 要求を実行中に、ハンドルされていない例外が発生しました。 エラーに関する詳細および例外の発生場所については、スタック トレースを参照してください。
* 例外の詳細: System.IO.FIleNotFoundException: ファイルまたはアセンブリ ’Microsoft.WindowsAzure.StorageClient, Version=1.1.0.0, Culture=neutral, PublicKeyToken=31bf856ad364e35’、またはその依存関係の 1 つが読み込めませんでした。 指定されたファイルが見つかりません。
次に例を示します。

  :::image type="content" source="media/role-startup-failure-3.png" alt-text="画像は、ロールのスタートアップ エラーでの例外を示しています":::
  
## <a name="diagnose-issues-by-using-the-compute-emulator"></a>コンピューティング エミュレーターを使用した問題の診断
依存コンポーネントの不足や web.config エラーに関する問題の診断とトラブルシューティングは、Azure コンピューティング エミュレーターを使用して行うことができます。
この診断方法の効果を最大限に高めるには、Windows がクリーン インストールされたコンピューターまたは仮想マシンを使用する必要があります。 
1.  [Azure SDK](https://azure.microsoft.com/downloads/) をインストールします 
2.  開発コンピューターで、クラウド サービスのプロジェクトをビルドします。
3.  エクスプローラーで、クラウド サービス プロジェクトの bin\debug フォルダーに移動します。
4.  デバッグ用のコンピューターに .csx フォルダーと .cscfg ファイルをコピーします。
5.  クリーン マシン上で、Azure SDK コマンド プロンプト ウィンドウを開き、「csrun.exe /devstore:start」と入力します。
6.  コマンド プロンプトで、「run csrun <.csx フォルダーへのパス> <.cscfg ファイルへのパス> /launchBrowser」と入力します。
7.  ロールが起動すると、詳しいエラー情報が Internet Explorer に表示されます。 Windows の標準的なトラブルシューティング ツールを使用して、さらに詳しく問題を診断することもできます。

## <a name="diagnose-issues-by-using-intellitrace"></a>IntelliTrace を使用して問題を診断する
.NET Framework 4 を使用する worker ロールと Web ロールに関しては、[IntelliTrace](https://docs.microsoft.com/visualstudio/debugger/intellitrace) (Microsoft Visual Studio Enterprise で利用可能) を使用することができます。
IntelliTrace を有効にしてサービスをデプロイするには、以下の手順に従います。
1.  Azure SDK 1.3 以上がインストールされていることを確認します。
2.  Visual Studio を使用してソリューションをデプロイします。 デプロイメント中は、 [.NET 4 のロールに対して IntelliTrace を有効にします] チェック ボックスをオンにしてください。
3.  インスタンスが起動したら、サーバー エクスプローラーを開きます。
4.  Azure\Cloud Services ノードを展開し、対象のデプロイを特定します。
5.  ロール インスタンスが表示されるまでデプロイメントを展開します。 いずれかのインスタンスを右クリックします。
6.  [IntelliTrace ログの表示] を選択します。 [IntelliTrace の概要] が開きます。
7.  概要の例外セクションを探します。 例外が存在する場合、そのセクションには [例外データ] という見出しが付きます。
8.  [例外データ] を展開し、次のような System.IO.FileNotFoundException エラーを見つけます。

    :::image type="content" source="media/role-startup-failure-4.png" alt-text="画像は、ロールのスタートアップ エラーでの例外データを示しています" lightbox="media/role-startup-failure-4.png":::

## <a name="address-missing-dlls-and-assemblies"></a>不足している DLL とアセンブリの解決
不足している DLL とアセンブリのエラーを解決するには、次の手順を実行します。
1.  Visual Studio でソリューションを開きます。
2.  ソリューション エクスプローラーで [参照] フォルダーを開きます。
3.  エラーに表示されているアセンブリをクリックします。
4.  [プロパティ] ウィンドウで [ローカル コピー] プロパティを探し、その値を [True] に設定します。
5.  クラウド サービスを再デプロイします。
すべてのエラーを修正済みであることが確認できたら、 [.NET 4 のロールに対して IntelliTrace を有効にします] チェック ボックスをオフにしてサービスをデプロイできます。

## <a name="next-steps"></a>次の手順 
- Azure PaaS コンピューターの診断データを使用してクラウド サービス ロールの問題をトラブルシューティングする方法については、 [Kevin Williamson によるブログ シリーズ](https://docs.microsoft.com/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data)をご覧ください。
