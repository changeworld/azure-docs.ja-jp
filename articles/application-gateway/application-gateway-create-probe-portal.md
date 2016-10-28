<properties
   pageTitle="ポータルを使用してアプリケーション ゲートウェイ用カスタム プローブを作成する | Microsoft Azure"
   description="ポータルを使用してアプリケーション ゲートウェイ用カスタム プローブを作成する方法について説明します。"
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/09/2016"
   ms.author="gwallace" />

# ポータルを使用して Application Gateway 用カスタム プローブを作成する

> [AZURE.SELECTOR]
- [Azure ポータル](application-gateway-create-probe-portal.md)
- [Azure Resource Manager の PowerShell](application-gateway-create-probe-ps.md)
- [Azure Classic PowerShell (Azure クラシック PowerShell)](application-gateway-create-probe-classic-ps.md)

<BR>

[AZURE.INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)]

## シナリオ

次のシナリオでは、既存のアプリケーション ゲートウェイに、カスタムの正常性プローブを作成します。このシナリオでは、[アプリケーション ゲートウェイの作成](application-gateway-create-gateway-portal.md)に関する手順を既に実行したことを前提としています。

## <a name="createprobe"></a>プローブの作成

プローブは、ポータルを通じて 2 段階の手順で構成されます。最初の手順ではプローブを作成し、次にプローブをアプリケーション ゲートウェイのバックエンド http 設定に追加します。

### 手順 1

http://portal.azure.com に移動し、既存のアプリケーション ゲートウェイを選択します。

![Application Gateway の概要][1]

### 手順 2.

**[プローブ]** をクリックし、**[追加]** ボタンをクリックして、新しいプローブを追加します。

![Add Probe blade with information filled out][2]

### 手順 3.

プローブに必要な情報を入力し、完了したら **[OK]** をクリックします。

- **[名前]** - これは、ポータルでアクセス可能なプローブのフレンドリ名です。
- **[ホスト]** - これは、プローブに使用されるホスト名です。
- **[パス]** - カスタム プローブの完全な URL の残りの部分です。
- **[間隔 (秒)]** - 正常性を確認するためにプローブを実行する頻度です。
- **[タイムアウト (秒)]** - タイムアウトまでにプローブが待機する時間です。
- **[異常しきい値]** - 異常であると見なされるまでの試行の失敗回数です。

> [AZURE.IMPORTANT] ホスト名はサーバー名ではありません。これは、アプリケーション サーバーで実行されている仮想ホストの名前です。プローブは、http://(host name):(httpsetting のポート)/urlPath に送信されます。

![probe configuration settings][3]

## ゲートウェイへのプローブの追加

プローブが作成されたら、ゲートウェイに追加します。プローブの設定は、アプリケーション ゲートウェイのバックエンド http 設定で行います。

### 手順 1

アプリケーション ゲートウェイの **[HTTP 設定]** をクリックし、ウィンドウで現在のバックエンド http 設定をクリックして、構成ブレードを表示します。

![https settings window][4]

### 手順 2.

**appGatewayBackEndHttp** 設定ブレードで **[カスタム プローブの使用]** をクリックし、「[プローブの作成](#createprobe)」セクションで作成したプローブを選択します。完了したら、**[OK]** をクリックし、設定を適用します。

![appgatewaybackend settings blade][5]

既定のプローブでは、Web アプリケーションへの既定のアクセスがチェックされます。カスタム プローブが作成されたら、アプリケーション ゲートウェイは、定義されているカスタム パスを使用して、選択されているバックエンドの正常性を監視します。定義された条件に基づいて、アプリケーション ゲートウェイは、プローブで指定されているファイルをチェックします。host:Port/path への呼び出しによって HTTP 200 OK という状態の応答が返されない場合は、異常のしきい値に達した後、サーバーがローテーションから除外されます。プローブは、もう一度正常になるタイミングを判断するために、異常なインスタンス上で続行します。インスタンスが正常なサーバー プールに戻されると、トラフィックはもう一度そこに流れ始め、インスタンスへのプローブは、通常どおり、ユーザーが指定した間隔で続行します。


## 次のステップ

Azure Application Gateway で SSL オフロードを構成する方法を学習するには、[SSL オフロードの構成](application-gateway-ssl-portal.md)に関するページを参照してください。

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png
[3]: ./media/application-gateway-create-probe-portal/figure3.png
[4]: ./media/application-gateway-create-probe-portal/figure4.png
[5]: ./media/application-gateway-create-probe-portal/figure5.png

<!---HONumber=AcomDC_0810_2016-->