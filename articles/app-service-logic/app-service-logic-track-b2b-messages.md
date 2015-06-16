<properties 
   pageTitle="B2B メッセージを追跡する" 
   description="このトピックでは、B2B 処理の追跡について説明します。" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="harishkragarwal" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="02/27/2015"
   ms.author="hariag"/>


# B2B メッセージを追跡する

## B2B 追跡情報
B2B 通信には取引先間のメッセージ処理が関係します。リレーションシップは、取引先との 2 者間の契約として定義されます。通信が確立されると、期待どおりに通信が行われているかどうかを監視する手段が必要になります。 
Azure App Services の一部として B2B API Apps を公開する一環で、データを追跡できるようになりました。Azure ポータルからも同様に利用できます。 

## AS2
AS2 API アプリのインスタンスを作成したら、このインスタンスを参照して追跡箇所に移動します。ここでは AS2 のすべての追跡情報を確認し、表示されるフィルター ブレードを介してフィルターを適用できます。

![][1]  

## EDIFACT
EDIFACT API アプリのインスタンスを作成したら、そのインスタンスを参照して追跡箇所に移動します。ここでは EDIFACT のすべての追跡情報を確認し、表示されるフィルター ブレードを介してフィルターを適用できます。
さらに、インターチェンジ レベルのデータ、グループ レベルのデータ、トランザクション セット レベルのデータをワン ステップでビューに表示することができます。 

関連付けられている取引先管理 API アプリの EDIFACT アグリーメントの一部としてバッチが作成された場合、バッチ処理の部分にこれらのすべてのバッチが一覧表示されます。バッチを参照して、アクティブなメッセージを構成するメッセージ (存在する場合) や、これまでに完了したバッチの情報も表示することができます。

![][2]      

## X12
X12 API アプリのインスタンスを作成したら、そのインスタンスを参照して追跡箇所に移動します。ここでは X12 のすべての追跡情報を確認し、表示されるフィルター ブレードを介してフィルターを適用できます。
さらに、インターチェンジ レベルのデータ、グループ レベルのデータ、トランザクション セット レベルのデータをワン ステップでビューに表示することができます。 

関連付けられている取引先管理 API アプリの X12 アグリーメントの一部としてバッチが作成された場合、バッチ処理の部分にこれらのすべてのバッチが一覧表示されます。バッチを参照して、アクティブなメッセージを構成するメッセージ (存在する場合) や、これまでに完了したバッチの情報も表示することができます。 

X12 と EDIFACT の追跡ビューは類似しています。 

<!--Image references-->
[1]: ./media/app-service-logic-track-b2b-messages/AS2Tracking.jpg
[2]: ./media/app-service-logic-track-b2b-messages/EDIFACTTracking.jpg

<!--HONumber=49--> 