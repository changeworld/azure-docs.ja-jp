---
title: "ILB ASE を Azure Application Gateway と統合する"
description: "ILB ASE のアプリを Azure Application Gateway と統合する方法に関するチュートリアル"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a6a74f17-bb57-40dd-8113-a20b50ba3050
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: ccompy
ms.openlocfilehash: eedad8824add7fe425d34975dab640fbee82c2bc
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2017
---
# <a name="integrating-your-ilb-ase-with-an-application-gateway"></a>ILB ASE を Application Gateway と統合する #

[Azure App Service Environment (ASE)](./intro.md) は、ユーザーの Azure Virtual Network のサブネットに Azure App Service をデプロイしたものです。 これは、アプリにアクセスするためのパブリック エンドポイントまたはプライベート エンドポイントを使用してデプロイできます。 プライベート エンドポイントを使用した ASE のデプロイは、ILB ASE と呼ばれます。  
Azure Application Gateway は、レイヤー 7 負荷分散、SSL オフロード、および WAF 保護を提供する仮想アプライアンスです。 これは、パブリック IP アドレスでリッスンし、トラフィックをアプリケーション エンドポイントにルーティングします。 次の情報は、WAF で構成された Application Gateway を ILB ASE のアプリと統合する方法について説明します。  

Application Gateway と ILB ASE の統合は、アプリ レベルで行われます。  Application Gateway を ILB ASE で構成する場合、ILB ASE の特定のアプリで行うことになります。 これにより、セキュリティで保護されたマルチ テナント アプリケーションを 1 つの ILB ASE でホストできます。  

![ILB ASE 上のアプリを指す Application Gateway][1]

このチュートリアルでは次を行います。

* Application Gateway を作成する
* ILB ASE 上のアプリを指すように Application Gateway を構成する
* カスタム ドメイン名を優先するようにアプリを構成する
* Application Gateway を指すパブリック DNS ホスト名を編集する

Application Gateway を ILB ASE と統合するには、次が必要です。

* ILB ASE
* ILB ASE で実行されているアプリ
* ILB ASE のアプリで使用するインターネット ルーティング可能なドメイン名
* ILB ASE が使用する ILB アドレス (これは、**[設定] -> [IP アドレス]** の ASE ポータルにあります)

    ![ILB ASE が使用する IP アドレス][9]
    
* 後でアプリケーション ゲートウェイを指すために使用されるパブリック DNS 名 

ILB ASE を作成する方法の詳細については、ドキュメント「[ILB ASE の作成と使用][ilbase]」を参照してください

このガイドでは、Application Gateway を ASE がデプロイされているのと同じ Azure Virtual Network で使用することを前提としています。 Application Gateway の作成を開始する前に、Application Gateway をホストするために使用するサブネットを選択するか、または作成します。 GatewaySubnet という名前ではないサブネット、または ILB ASE で使用されるサブネットを使用する必要があります。
Application Gateway を GatewaySubnet に配置した場合、後で 仮想ネットワーク ゲートウェイを作成することはできません。 また、ILB ASE で使用されるサブネットに Application Gateway を配置することもできません。これは、このサブネットには ASE しか含めることができないためです。

## <a name="steps-to-configure"></a>構成する手順 ##

1. Azure ポータル内で、**[新規] > [ネットワーク] > [Application Gateway]** に移動します 
    1. 次を指定します。
        1. Application Gateway の名前
        1. WAF の選択
        1. ASE VNet に使用するのと同じサブスクリプションの選択
        1. リソース グループの作成または選択
        1. ASE VNet を配置する場所の選択

    ![新しいアプリケーション ゲートウェイの作成の基礎][2]   
    1. [設定] 領域で次を設定します。
        1. ASE VNet
        1. Application Gateway をデプロイする必要があるサブネット。 VPN ゲートウェイを作成できなくなるため、GatewaySubnet は使用しないでください。
        1. パブリックの選択
        1. パブリック IP アドレスを選択します。 アドレスが 1 つもない場合は、この時点で 1 つ作成します。
        1. HTTP または HTTPS を構成します。 HTTPS を構成する場合は、PFX 証明書を提供する必要があります。
        1. Web アプリケーション ファイアウォールの設定を選択します。 ここで、ファイアウォールを有効にして、必要に応じて検出または防止に設定することもできます。

    ![新しいアプリケーション ゲートウェイの作成の設定][3]
    
    1. [概要] セクションを確認し、**[OK]** を選択します。 Application Gateway のセットアップの完了には、30 分あまりかかる可能性があります。  

2. Application Gateway のセットアップが完了したら、Application Gateway ポータルに移動します。 **[バックエンド プール]** を選択します。  ILB ASE の ILB アドレスを追加します。

    ![バックエンド プールを構成する][4]

3. バックエンド プールを構成する処理が完了したら、**[正常性プローブ]** を選択します。 アプリに使用するドメイン名の正常性プローブを作成します。 

    ![正常性プローブを構成する][5]
    
4. 正常性プローブを構成する処理が完了したら、**[HTTP 設定]** を選択します。  既存の設定をここで編集し、**[カスタム プローブの使用]** を選択し、構成したプローブを選択します。

    ![HTTP 設定を構成する][6]
    
5. Application Gateway の **[概要]** に移動し、Application Gateway に使用するパブリック IP アドレスをコピーします。  その IP アドレスをアプリ ドメイン名の A レコードとして設定するか、CNAME レコードでそのアドレスの DNS 名を使用します。  パブリック IP アドレスを選択して、それをパブリック IP アドレスの UI にコピーするほうが、Application Gateway の [概要] セクションのリンクからコピーするよりも簡単です。 

    ![Application Gateway ポータル][7]

6. アプリのカスタム ドメイン名を ILB ASE に設定します。  ポータルのアプリに移動し、[設定] で **[カスタム ドメイン]** を選択します。

![アプリでカスタム ドメイン名を設定する][8]

Web アプリのカスタム ドメイン名の設定に関する情報は、「[Web アプリのカスタム ドメイン名を設定する][custom-domain]」にあります。 ILB ASE のアプリとこのドキュメントでのアプリの違いは、ドメイン名の検証が行われないということです。  アプリ エンドポイントを管理する DNS を所有しているため、必要なものをすべて自由に配置できます。 ここで追加するカスタム ドメイン名は、DNS 内に配置する必要はありませんが、アプリで構成する必要はあります。 

セットアップが完了し、DNS 変更を反映するまで少しの時間待機すると、作成したカスタム ドメイン名でアプリにアクセスできるようになります。 


<!--IMAGES-->
[1]: ./media/integrate-with-application-gateway/appgw-highlevel.png
[2]: ./media/integrate-with-application-gateway/appgw-createbasics.png
[3]: ./media/integrate-with-application-gateway/appgw-createsettings.png
[4]: ./media/integrate-with-application-gateway/appgw-backendpool.png
[5]: ./media/integrate-with-application-gateway/appgw-healthprobe.png
[6]: ./media/integrate-with-application-gateway/appgw-httpsettings.png
[7]: ./media/integrate-with-application-gateway/appgw-publicip.png
[8]: ./media/integrate-with-application-gateway/appgw-customdomainname.png
[9]: ./media/integrate-with-application-gateway/appgw-iplist.png

<!--LINKS-->
[appgw]: http://docs.microsoft.com/azure/application-gateway/application-gateway-introduction
[custom-domain]: ../app-service-web-tutorial-custom-domain.md
[ilbase]: ./create-ilb-ase.md
