---
title: Cloud Services (延長サポート) でリモート デスクトップを適用する
description: Cloud Services (延長サポート) に対してリモート デスクトップを有効にする
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 02e5a30da1efe8c3cd669babddff305296077f20
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123424188"
---
# <a name="apply-the-remote-desktop-extension-to-azure-cloud-services-extended-support"></a>リモート デスクトップ拡張機能を Azure Cloud Services (延長サポート) に適用する

Azure portal では、アプリケーションをデプロイした後でもリモート デスクトップ拡張機能を使用することで、リモート デスクトップを有効にすることができます。 ご利用のクラウド サービスのリモート デスクトップ設定を使用すると、リモート デスクトップの有効化、ローカル管理者アカウントの更新、認証に使用する証明書の選択、およびそれらの証明書の有効期限の設定を行うことができます。 

## <a name="apply-remote-desktop--extension"></a>リモート デスクトップ拡張機能を適用する
1. リモート デスクトップを有効にするクラウド サービスに移動し、左側のナビゲーション ウィンドウで **[リモート デスクトップ]** を選択します。

    :::image type="content" source="media/remote-desktop-1.png" alt-text="Azure portal での [リモート デスクトップ] オプションの選択を示す画像":::

2. **[追加]** を選択します。
3. リモート デスクトップを有効にするロールを選択します。
4. ユーザー名、パスワード、有効期限、証明書 (必須ではない) の各必須フィールドに入力します。
> [注] リモート デスクトップのパスワードは、長さが 8 から 123 文字であり、パスワードの複雑さに関する次の要件のうち 3 つ以上を満たしている必要があります。1) 大文字が含まれている 2) 小文字が含まれている 3) 数字が含まれている 4) 特殊文字が含まれている 5) 制御文字は使用できない

   :::image type="content" source="media/remote-desktop-2.png" alt-text="リモート デスクトップへの接続に必要な情報の入力を示す画像。":::

5. 終わったら、 **[保存]** を選択します。 ロール インスタンスが接続を受信する準備が完了するまで数分間かかります。

## <a name="connect-to-role-instances-with-remote-desktop-enabled"></a>リモート デスクトップが有効になっているロール インスタンスに接続する
ロールに対してリモート デスクトップが有効になったら、Azure portal から直接接続を開始できます。

1. **[ロールとインスタンス]** をクリックして、インスタンスの設定を開きます。

    :::image type="content" source="media/remote-desktop-3.png" alt-text="[構成] ブレードでの [ロールとインスタンス] オプションの選択を示す画像。":::

2. リモート デスクトップが構成されたロール インスタンスを選択します。
3. **[接続]** をクリックして、リモート デスクトップ接続ファイルをダウンロードします。

    :::image type="content" source="media/remote-desktop-4.png" alt-text="Azure portal での worker ロール インスタンスの選択を示す画像。":::
    
4. そのファイルを開いてロール インスタンスに接続します。

## <a name="update-remote-desktop-extension-using-powershell"></a>PowerShell を使用してリモート デスクトップ拡張機能を更新する
下の手順に従い、RDP 拡張機能を含む最新モジュールにクラウド サービスを更新します

1.  Az.CloudService モジュールを[最新版](https://www.powershellgallery.com/packages/Az.CloudService/0.5.0)に更新します

```powershell
Update-Module -Name Az.CloudService 
```
 
2.  クラウドサービスの既存の RDP 拡張機能を削除します 

```powershell
$resourceGroupName='<Resource Group Name>'  
$cloudServiceName='<Cloud Service Name>' 
 
# Get existing cloud service  
$cloudService = Get-AzCloudService -ResourceGroup $resourceGroupName -CloudServiceName $cloudServiceName  
 
# Remove existing RDP Extension from cloud service object  
$cloudService.ExtensionProfile.Extension = $cloudService.ExtensionProfile.Extension | Where-Object { $_.Type-ne "RDP" }  
 ```
 
3.  最新モジュールでクラウド サービスに新しい RDP 拡張機能を追加します

```powershell
# Create new RDP extension object  
$credential = Get-Credential  
$expiration='<Expiration Date>'  
$rdpExtension = New-AzCloudServiceRemoteDesktopExtensionObject -Name "RDPExtension" -Credential $credential -Expiration $expiration -TypeHandlerVersion "1.2.1"  
 
# Add RDP extension to existing cloud service extension object  
$cloudService.ExtensionProfile.Extension = $cloudService.ExtensionProfile.Extension + $rdpExtension  
 
# Update cloud service  
$cloudService | Update-AzCloudService  
```

## <a name="next-steps"></a>次のステップ 
- Cloud Services (延長サポート) の[デプロイの前提条件](deploy-prerequisite.md)を確認します。
- Cloud Services (延長サポート) の[よく寄せられる質問](faq.yml)を確認します。
- [Azure portal](deploy-portal.md)、[PowerShell](deploy-powershell.md)、[テンプレート](deploy-template.md)、または [Visual Studio](deploy-visual-studio.md) を使用してクラウド サービス (延長サポート) をデプロイします。
