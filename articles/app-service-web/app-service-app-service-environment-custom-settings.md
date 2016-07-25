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
	ms.date="06/01/2016"
	ms.author="stefsch"/>

# App Service Environment のカスタム構成設定

## 概要 ##
App Service Environment は単一の顧客に分離されるため、App Service Environment にのみ適用できる特定の構成設定があります。この記事では、App Service Environment で使用可能な、固有の各種カスタマイズについて説明します。

App Service Environment のカスタマイズは、新しい **clusterSettings** 属性の配列を使って保存できます。この属性は、Azure Resource Manager の *hostingEnvironments* エンティティの "Properties" ディクショナリにあります。

次の簡略化された Resource Manager テンプレートのスニペットに、その **clusterSettings** 属性が示されています。


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

Resource Manager テンプレートに **clusterSettings** 属性を含めて、App Service Environment を更新することができます。

## Azure リソース エクスプローラーを使った App Service Environment の更新
App Service Environment は、[Azure リソース エクスプローラー](https://resources.azure.com)を使って更新することもできます。

1. リソース エクスプローラーで、App Service Environment のノードに移動します (**subscriptions**、**resourceGroups**、**providers**、**Micrososft.Web**、**hostingEnvironments** の順に移動)。次に、更新する App Service Environment をクリックします。

2. 右側のペインで、上部のツールバーの **[読み取り/書き込み]** をクリックし、リソース エクスプローラーでの対話型の編集を許可します。

3. 青色の **[編集]** ボタンをクリックし、Resource Manager テンプレートを編集可能にします。

4. 右側のペインの一番下までスクロールします。**clusterSettings** 属性は一番下にあります。ここで、値の入力または更新ができます。

5. **clusterSettings** 属性に、必要な構成値の配列を入力します (またはコピーして貼り付けます)。

6. 右側のペインの上部にある緑色の **[PUT]** ボタンをクリックし、App Service Environment に変更をコミットします。

ただし、変更を送信してから反映されるまでに、App Service Environment 内のフロント エンドの数に 30 分をかけた程度の時間がかかります。たとえば、App Service Environment に 4 つのフロント エンドがある場合、構成の更新が完了するまでに約 2 時間かかります。構成の変更がロールアウトされている間は、App Service Environment で他のスケーリング操作や構成の変更操作は実行できません。

## TLS 1.0 の無効化 ##
お客様 (特に PCI 準拠の監査に対応しているお客様) から繰り返したずねられるのは、アプリで TLS 1.0 を明示的に無効にする方法についてです。

TLS 1.0 は、次の **clusterSettings** エントリで無効にすることができます。

        "clusterSettings": [
            {
                "name": "DisableTls1.0",
                "value": "1"
            }
        ],

## TLS 暗号スイートの順序変更 ##
お客様から寄せられるもう 1 つの質問は、サーバーによってネゴシエートされた暗号のリストを変更できるかどうかということです。これは、以下に示したように **clusterSettings** を変更することで実行できます。利用できる暗号スイートのリストは、[こちらの MSDN 記事](https://msdn.microsoft.com/library/windows/desktop/aa374757(v=vs.85).aspx)) で確認できます。

        "clusterSettings": [
            {
                "name": "FrontEndSSLCipherSuiteOrder",
                "value": "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384_P256,TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256"
            }
        ],

> [AZURE.WARNING]  SChannel が認識できない間違った値を暗号スイートに設定すると、ご利用のサーバーに対するすべての TLS 通信が機能しなくなります。この場合は、**clusterSettings** から *FrontEndSSLCipherSuiteOrder* エントリを削除し、更新された ARM テンプレートを送信して、既定の暗号スイート設定に戻す必要があります。この機能は慎重に使用してください。

## 作業開始
Azure クイック スタート Resource Manager テンプレートのサイトには、[App Service Environment を作成](https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/)するための基本定義を含むテンプレートが用意されています。


<!-- LINKS -->

<!-- IMAGES -->

<!---HONumber=AcomDC_0713_2016-->