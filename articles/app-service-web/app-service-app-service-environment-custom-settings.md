<properties 
	pageTitle="App Service Environment のカスタム設定" 
	description="App Service Environment のカスタム構成設定" 
	services="app-service" 
	documentationCenter="" 
	authors="stefsch" 
	manager="nirma" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/08/2016" 
	ms.author="stefsch"/>

# App Service Environment のカスタム構成設定

## 概要 ##
App Service Environment は単一の顧客に分離されるため、App Service Environment にのみ適用できる特定の構成設定があります。この記事では、使用可能な App Service Environment 固有のさまざまなカスタマイズについて説明します。

App Service Environment のカスタマイズ内容は、*hostingEnvironments* ARM エンティティの "Properties" ディクショナリにある新しい "clusterSettings" 属性で、配列を使用して保存されます。

省略形の ARM テンプレート スニペット (下記参照) は "clusterSettings"属性を示しています。


    "resources": [
    {
       "apiVersion": "2015-08-01",
       "type": "Microsoft.Web/hostingEnvironments",
       "name": ...,
       "location": ...,
       "properties": {
          "clusterSettings": [
             {
                 "name": "nameOfCustomSetting",
                 "value": "valueOfCustomSetting"
             }
          ],
          "workerPools": [ ...],
          etc...
       }
    }

ARM テンプレートに "clusterSettings"属性を含めて、App Service Environment を更新することができます。

[Azure リソース エクスプローラー](https://resources.azure.com)を使用して属性値を更新することもできます。Azure リソース エクスプローラーで、App Service Environment のノードに移動 (サブスクリプション --> resourceGroups --> プロバイダー --> hostingEnvironments) し、更新する特定の App Service Environment をクリックします。

右側のブラウザー ウィンドウで、上部のツールバーの [読み取り/書き込み] をクリックし、リソース エクスプローラーでの対話型の編集を許可します。青色の [編集] ボタンをクリックし、ARM テンプレートを編集可能にします。右側のブラウザー ウィンドウの下部までスクロールします。"clusterSettings"属性が一番下に表示されます。ここで、値の入力または更新ができます。

"clusterSettings"属性で、必要な構成値の配列を入力します (またはコピーして貼り付けます)。右側のブラウザー ウィンドウの上部にある緑色の [PUT] ボタンをクリックし、App Service Environment に変更をコミットします。

App Service Environment を更新するために選択した方法に関係なく、変更を送信すると、変更が有効になるまで大まかに言って、30 分に App Service Environment のフロント エンドの数を掛けた時間がかかります。たとえば、App Service Environment に 4 つのフロント エンドがある場合、構成の更新が完了するまで約 2 時間がかかります。構成の変更がロールアウトされている間は、App Service Environment で他のスケーリング操作や構成の変更操作はできません。

## TLS 1.0 の無効化 ##
お客様の中でも特に PCI 準拠の監査に対処するお客様から定期的に聞かれる質問は、アプリで TLS 1.0 を明示的に無効にする機能についてです。

TLS 1.0 は、次の *clusterSettings* エントリで無効にすることができます。

        "clusterSettings": [
            {
                "name": "DisableTls1.0",
                "value": "1"
            }
        ],



## 使用の開始
Azure クイック スタート ARM テンプレートのサイトには、[App Service Environment の作成](https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/)の基本定義を含むテンプレートが含まれています。


<!-- LINKS -->

<!-- IMAGES -->
 

<!---HONumber=AcomDC_0413_2016-->