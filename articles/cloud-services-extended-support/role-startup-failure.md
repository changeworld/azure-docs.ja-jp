---
title: Azure Cloud Services (延長サポート) でのロール インスタンスのスタートアップ エラー
description: Azure Cloud Services (延長サポート) でのロール インスタンスのスタートアップ エラーをトラブルシューティングします。
ms.topic: article
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachadw
ms.date: 04/01/2021
ms.custom: ''
ms.openlocfilehash: 9c6fdee8dbb28e86e5084c9c0cfca97f5ac5ea05
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/30/2021
ms.locfileid: "108287160"
---
# <a name="troubleshoot-azure-cloud-services-extended-support-roles-that-fail-to-start"></a>起動できない Azure Cloud Services (延長サポート) ロールをトラブルシューティングする

ここでは、Azure Cloud Services (延長サービス) ロールの起動失敗に関連した一般的な問題と解決法を取り上げます。

## <a name="cloud-service-operation-fails-with-roleinstancestartuptimeouterror"></a>RoleInstanceStartupTimeoutError でクラウド サービスの操作に失敗する

Azure Cloud Services (延長サポート) の 1 つ以上のロール インスタンスが起動に時間がかかっている可能性があるかリサイクルされている可能性があり、ロール インスタンスが失敗します。 ロール アプリケーション エラー `RoleInstanceStartupTimeoutError` が表示されます。

ロール アプリケーションには、"*スタートアップ タスク*" と "*ロール コード (RoleEntryPoint の実装)* " という、ロールのリサイクルを引き起こす可能性がある 2 つの部分が含まれています。 

ロールが停止すると、サービスとしてのプラットフォーム (PaaS) エージェントによってロールが再起動されます。

PowerShell または Azure portal を使用すると、ロール インスタンスの現在の状態と詳細を取得してエラーを診断できます。

* **PowerShell**: ロール インスタンスの実行時の状態に関する情報を取得するには、[Get-AzCloudServiceRoleInstanceView](/powershell/module/az.cloudservice/get-azcloudserviceroleinstanceview) コマンドレットを使用します。

    ```powershell
    Get-AzCloudServiceRoleInstanceView -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstanceName "WebRole1_IN_0"
     
    Statuses           PlatformFaultDomain PlatformUpdateDomain
    --------           ------------------- --------------------
    {RoleStateStarting} 0                   0
    ```

* **Azure portal**: ポータルで、クラウド サービス インスタンスに移動します。 状態の詳細を表示するには、 **[ロールとインスタンス]** を選択してから、ロール インスタンスを選択します。

  :::image type="content" source="media/role-startup-failure-portal.png" alt-text="Azure portal でのロールのスタートアップ エラーを示すスクリーンショット。":::

## <a name="missing-dlls-or-dependencies"></a>DLL または依存コンポーネントの欠落

応答しないロールおよび状態の変化を繰り返すロールを引き起こす原因は、DLL またはアセンブリの欠落であると考えられます。

DLL またはアセンブリが欠落している場合の症状を、次に示します。

* ロール インスタンスが **[初期化しています]** 、 **[ビジー]** 、 **[停止しています]** の状態を繰り返す。
* ロール インスタンスが **[準備完了]** 状態に移行したにもかかわらず、Web アプリケーションにアクセスしてもページが表示されない。


Web ロールにデプロイされ、DLL が欠落している Web サイトでは、次のサーバー ランタイム エラーが表示されることがあります。

  :::image type="content" source="media/role-startup-failure-runtime-error.png" alt-text="ロールのスタートアップ エラーの発生後のランタイム エラーを示すスクリーンショット。":::

### <a name="resolve-missing-dlls-and-assemblies"></a>欠落している DLL とアセンブリを解決する

欠落している DLL とアセンブリのエラーを解決するには、次のようにします。

1. Visual Studio でソリューションを開きます。
2. ソリューション エクスプローラーで *[参照]* フォルダーを開きます。
3. エラーで示されているアセンブリを選択します。
4. **[プロパティ]** で、 **[ローカル コピー]** プロパティを **[True]** に設定します。
5. クラウド サービスを再デプロイします。

エラーが表示されなくなったことを確認したら、サービスを再デプロイします。 デプロイを設定するときは、 **[.NET 4 のロールに対して IntelliTrace を有効にします]** チェックボックスをオンにしないでください。

## <a name="diagnose-role-instance-errors"></a>ロール インスタンスのエラーを診断する

ロール インスタンスの問題を診断するには、次のオプションのいずれかを選択します。

### <a name="turn-off-custom-errors"></a>カスタム エラーをオフにする

すべてのエラー情報を表示するには、Web ロールの *web.config* ファイルでカスタム エラー モードを `off` に設定してから、サービスを再デプロイします。

1. Visual Studio でソリューションを開きます。
2. ソリューション エクスプローラーで、*web.config* ファイルを開きます。
3. `system.web` セクションで、次のコードを追加します。

   ```xml
   <customErrors mode="Off" />
   ```

4. ファイルを保存します。
5. サービスをパッケージし直して再度デプロイします。

サービスが再度デプロイされると、欠落しているアセンブリまたは DLL の名前がエラー メッセージに含まれます。

### <a name="use-remote-desktop"></a>リモート デスクトップを使用する

リモート デスクトップを使用してロールにアクセスし、詳細なエラー情報を表示します。

1. [Azure Cloud Services (延長サポート) に対してリモート デスクトップ拡張機能を追加します](enable-rdp.md)。
2. Azure portal で、クラウド サービス インスタンスに **[準備完了]** の状態が表示されたら、リモート デスクトップを使用してクラウド サービスにサインインします。 詳細については、[リモート デスクトップを使用したロール インスタンスへの接続](enable-rdp.md#connect-to-role-instances-with-remote-desktop-enabled)に関する記事を参照してください。
3. リモート デスクトップの設定に使用した資格情報を使用して、仮想マシンにサインインします。
4. コマンド プロンプト ウィンドウを開きます。
5. コマンド プロンプトに、「**ipconfig**」と入力します。 IPv4 アドレスとして返される値を確認します。
6. Microsoft Internet Explorer を開きます。
7. アドレス バーに、IPv4 アドレス、スラッシュ、Web アプリケーションの既定ファイルの名前の順に入力します。 たとえば、「 `http://<IPv4 address>/default.aspx` 」のように入力します。

この時点で Web サイトにアクセスすると、詳細情報を含むエラー メッセージが表示されます。 次に例を示します。

:::image type="content" source="media/role-startup-failure-error-message.png" alt-text="エラー メッセージの例を示すスクリーンショット。":::
  
### <a name="use-the-compute-emulator"></a>コンピューティング エミュレーターを使用する

依存コンポーネントの不足や *web.config* エラーに関する問題の診断とトラブルシューティングは、Azure コンピューティング エミュレーターを使用して行うことができます。 この方法で問題を診断する場合、効果を最大限に高めるには、Windows がクリーン インストールされたコンピューターまたは仮想マシンを使用します。

Azure コンピューティング エミュレーターを使用して問題を診断するには、次のようにします。

1. [Azure SDK](https://azure.microsoft.com/downloads/) をインストールします。
2. 開発コンピューターで、クラウド サービスのプロジェクトをビルドします。
3. ファイル エクスプローラーで、クラウド サービス プロジェクトの *bin\debug* フォルダーに移動します。
4. 問題のデバッグに使用するコンピューターに *.csx* フォルダーと *.cscfg* ファイルをコピーします。
5. クリーン インストールされたコンピューターで Azure SDK コマンド プロンプト ウィンドウを開きます。
6. コマンド プロンプトに、「**csrun.exe /devstore:start**」と入力します。
7. 次に、「**run csrun \<path to .csx folder\> \<path to .cscfg file\> /launchBrowser**」と入力します。
8. ロールが起動すると、Internet Explorer に詳細なエラー情報が表示されます。

追加の診断が必要な場合は、標準の Windows トラブルシューティング ツールを使用できます。

### <a name="use-intellitrace"></a>IntelliTrace を使用する

.NET Framework 4 を使用する worker および Web ロールの場合は、[IntelliTrace](/visualstudio/debugger/intellitrace) を使用できます。 IntelliTrace は Visual Studio Enterprise で使用できます。

IntelliTrace を有効にしてクラウド サービスをデプロイするには、次のようにします。

1. Azure SDK 1.3 以上がインストールされていることを確認します。
2. Visual Studio で、ソリューションをデプロイします。 デプロイを設定するときは、 **[.NET 4 のロールに対して IntelliTrace を有効にします]** チェックボックスをオンにします。
3. ロール インスタンスが起動したら、サーバー エクスプローラーを開きます。
4. **[Azure\Cloud Services]** ノードを展開します。
5. デプロイを展開してロール インスタンスを一覧表示します。 ロール インスタンスを右クリックします。
6. **[IntelliTrace ログの表示]** を選択します。
7. **[IntelliTrace の概要]** で、 **[例外データ]** にアクセスします。
8. **[例外データ]** を展開し、`System.IO.FileNotFoundException` エラーを探します。

   :::image type="content" source="media/role-startup-failure-exception-data.png" alt-text="ロールのスタートアップ エラーの例外データのスクリーンショット。" lightbox="media/role-startup-failure-exception-data.png":::

## <a name="next-steps"></a>次のステップ

- [Azure PaaS コンピューターの診断データを使用してクラウド サービス ロールの問題をトラブルシューティングする](/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data)方法について確認します。