<properties 
	pageTitle="Azure Multi-Factor Authentication とは" 
	description="Azure Multi-Factor Authentication は、本人性の確認において、ユーザー名とパスワードに加えて、その他の要素を取り入れる手法です。ユーザーのサインインとトランザクションに多層構造のセキュリティを確保することができます。" 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="bryanla"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/02/2015" 
	ms.author="billmath"/>

# 多要素認証とは

多要素認証 (MFA) は、複数の確認方法の使用を要求することで、ユーザーのサインインとトランザクションにさらなる重要なセキュリティ レイヤーを追加する認証方法です。これらは、次の確認方法のうち 2 つ以上を要求することで機能します。

- ユーザーが知っているもの (通常はパスワード)
- ユーザーが持っているもの (携帯電話など、簡単には複製できない信頼できるデバイス)
- ユーザー自身 (生体認証)

<center>![Username and Password](./media/multi-factor-authentication/pword.png) &#160;&#160;&#160;&#160;&#160;![Certificates](./media/multi-factor-authentication/phone.png) &#160;&#160;&#160;&#160;&#160;![Smart Phone](./media/multi-factor-authentication/hware.png) &#160;&#160;&#160;&#160;&#160;![Smart Card](./media/multi-factor-authentication/smart.png) &#160;&#160;&#160;&#160;&#160;![Virtual Smart Card](./media/multi-factor-authentication/vsmart.png) &#160;&#160;&#160;&#160;&#160;![Username and Password](./media/multi-factor-authentication/cert.png)</center>

## Azure Multi-Factor Authentication とは

Azure Multi-Factor Authentication は、本人性の確認において、ユーザー名とパスワードに加えて、その他の要素を取り入れる手法です。ユーザーのサインインとトランザクションに 2 層構造のセキュリティを確保することができます。

Azure Multi-Factor Authentication を使えば、シンプルなサインイン プロセスを好むユーザーのニーズに応えながら、データやアプリケーションへのアクセスを効果的に保護することが可能です。電話やテキスト メッセージ、モバイル アプリによる通知のほか、確認コードやサード パーティの OATH トークンなど、一連の簡単な照合方法を通じて確実な認証を行うことができます。

Azure Multi-Factor Authentication の概要については、次のビデオをご覧ください。


<center>[AZURE.VIDEO multi-factor-authentication-overview]</center>

##Azure Multi-Factor Authentication を使う理由

インターネットの接続人口は今やかつてない水準にまで増加しています。スマート フォン、タブレット、ノート PC、デスクトップ PC など、インターネットへの接続手段は多様化し、常時接続していることが当たり前の時代となりました。人々は、どこにいても、自分のアカウントやアプリケーションにアクセスすることができます。そのことが生産性とサービスの質の向上につながっているといえるでしょう。

Azure Multi-Factor Authentication は、認証手段の多層化によってユーザーを絶えず保護することができる、使いやすくスケーラブルで信頼性の高いソリューションです。

![使いやすい](./media/multi-factor-authentication/simple.png)| ![拡張性](./media/multi-factor-authentication/scalable.png)| ![常時保護](./media/multi-factor-authentication/protected.png)|![信頼性](./media/multi-factor-authentication/reliable.png)
:-------------: | :-------------: | :-------------: | :-------------: |
**使いやすい**|**拡張性**|**常時保護**|**信頼性**

- **使いやすい** -Azure Multi-Factor Authenticaton は、簡単にセットアップして使用することができます。Azure Multi-Factor Authentication によって保護が多層化され、ユーザーは、自分の所有デバイスを使用、活用することができます。セットアップは、多くの場合、数回のクリックで簡単に行うことができます。
- **拡張性** - Azure Multi-Factor Authenticaton はクラウドの強みを活かし、オンプレミスの AD やカスタム アプリと連携します。その保護の範囲は、ハイボリュームのミッション クリティカルなシナリオにまで広げることができます。
- **常時保護** - Azure Multi-Factor Authentication は、きわめて高い業界標準に基づく強力な認証手段です。
- **信頼性** - Azure Multi-Factor Authentication には、99.9% の可用性が確保されています。認証要求を受信することも処理することもできなくなったときに、多要素認証のサービスは利用不可と見なされます。  

Azure Multi-Factor Authentication を使う理由について、さらに詳しい情報については、次のビデオをご覧ください。

<center>[AZURE.VIDEO windows-azure-multi-factor-authentication]</center>

**その他のリソース**

* [ユーザー向け](multi-factor-authentication-end-user.md)
* [Azure Multi-Factor Authentication (MSDN)](https://msdn.microsoft.com/library/azure/dn249471.aspx) 

<!---HONumber=July15_HO1-->