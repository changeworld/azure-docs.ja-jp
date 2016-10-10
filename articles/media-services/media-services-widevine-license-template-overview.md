<properties 
	pageTitle="Widevine ライセンス テンプレートの概要 | Microsoft Azure" 
	description="このトピックでは、Widevine ライセンスの構成に使用する Widevine ライセンス テンプレートの概要を示します。" 
	authors="juliako" 
	manager="erikre" 
	editor="" 
	services="media-services" 
	documentationCenter=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/26/2016"  
	ms.author="juliako"/>

#Widevine ライセンス テンプレートの概要

##Overview

Azure Media Services では、Widevine ライセンスを構成および要求できるようになりました。エンド ユーザーのプレーヤーが Widevine の保護されたコンテンツを再生しようとすると、ライセンスを取得する要求がライセンス配信サービスに送信されます。ライセンス サービスはその要求を承認した後、ライセンスを発行します。このライセンスはクライアントに送信され、指定されたコンテンツの暗号化解除と再生に用いられます。

Widevine ライセンス要求の形式は、JSON メッセージです。

値を指定せずに "{}" のみの空のメッセージを作成すると、すべてに既定値を使用したライセンス テンプレートが作成されます。

	{  
	   “payload”:“<license challenge>”,
	   “content_id”: “<content id>” 
	   “provider”: ”<provider>”
	   “allowed_track_types”:“<types>”,
	   “content_key_specs”:[  
	      {  
	         “track_type”:“<track type 1>”
	      },
	      {  
	         “track_type”:“<track type 2>”
	      },
	      …
	   ],
	   “policy_overrides”:{  
	      “can_play”:<can play>,
	      “can persist”:<can persist>,
	      “can_renew”:<can renew>,
	      “rental_duration_seconds”:<rental duration>,
	      “playback_duration_seconds”:<playback duration>,
	      “license_duration_seconds”:<license duration>,
	      “renewal_recovery_duration_seconds”:<renewal recovery duration>,
	      “renewal_server_url”:”<renewal server url>”,
	      “renewal_delay_seconds”:<renewal delay>,
	      “renewal_retry_interval_seconds”:<renewal retry interval>,
	      “renew_with_usage”:<renew with usage>
	   }
	}

##JSON メッセージ

名前 | 値 | Description
---|---|---
payload |Base64 でエンコードされた文字列 |クライアントから送信されたライセンス要求です。 
content\_id | Base64 でエンコードされた文字列|各 content\_key\_specs.track\_type のキー ID およびコンテンツ キーを取得するために使用される識別子です。
provider |string |コンテンツ キーおよびポリシーを検索するために使用されます。必須。
policy\_name | string |以前に登録されたポリシーの名前です。省略可能
allowed\_track\_types | enum | SD\_ONLY または SD\_HD。どのコンテンツ キーをライセンスに含めるかを制御します
content\_key\_specs | JSON 構造体の配列。次の「**コンテンツ キーの仕様**」を参照してください | どのコンテンツ キーを返すかについて、より細かく制御します。詳細については、次の「コンテンツ キーの仕様」を参照してください。allowed\_track\_types と content\_key\_specs のいずれかのみを指定できます。 
use\_policy\_overrides\_exclusively | ブール値。true または false | policy\_overrides に指定されたポリシー属性を使用し、前に格納したポリシーをすべて除外します。
policy\_overrides | JSON 構造体。次の「**ポリシーのオーバーライド**」を参照してください | このライセンスのポリシー設定です。このアセットに事前定義されたポリシーがある場合、これらの指定された値が使用されます。 
session\_init | JSON 構造体。次の「**セッションの初期化**」を参照してください | ライセンスに渡される省略可能なデータです。
parse\_only | ブール値。true または false | ライセンス要求は解析されますが、ライセンスは発行されません。ただし、応答でライセンス要求を構成する値が返されます。  

##コンテンツ キーの仕様 

既存のポリシーが存在する場合は、"コンテンツ キーの仕様" に値を指定する必要はありません。このコンテンツに関連付けられている既存のポリシーを使用して、HDCP や CGMS などの出力保護が決定されます。既存のポリシーが Widevine ライセンス サーバーに登録されていない場合、コンテンツ プロバイダーはライセンス要求に値を挿入できます。


オプション use\_policy\_overrides\_exclusively に関係なく、各 content\_key\_specs がすべてのトラックに対して指定されている必要があります。


名前 | 値 | Description
---|---|---
content\_key\_specs track\_type | string | トラックの種類の名前です。ライセンス要求で content\_key\_specs が指定されている場合は、すべてのトラックの種類を明示的に指定します。指定しないと、過去 10 秒間を再生できません。 
content\_key\_specs <br/> security\_level | uint32 | 再生に関するクライアントの堅牢性の要件を定義します。<br/> 1 - ソフトウェアベースのホワイトボックス暗号化が必須です。<br/> 2 - ソフトウェア暗号化と難読化デコーダーが必須です。<br/> 3 - キー マテリアルと暗号化の操作を、ハードウェアを基盤にした信頼できる実行環境で実行する必要があります。<br/> 4 - コンテンツの暗号化とデコードを、ハードウェアを基盤にした信頼できる実行環境で実行する必要があります。<br/> 5 - 暗号化、デコード、およびメディア (圧縮済みおよび圧縮解除済み) のすべての処理を、ハードウェアを基盤にした信頼できる実行環境で実行する必要があります。  
content\_key\_specs <br/> required\_output\_protection.hdc | 文字列 - 次のいずれか 1 つ: HDCP\_NONE、HDCP\_V1、HDCP\_V2 | HDCP が必須かどうかを示します
content\_key\_specs <br/>key | Base64 <br/>でエンコードされた文字列|このトラックで使用するコンテンツ キーです。指定した場合、track\_type または key\_id が必要です。このオプションを使用すると、Widevine ライセンス サーバーでキーを生成または検索する代わりに、コンテンツ プロバイダーがこのトラックのコンテンツ キーを挿入できます。
content\_key\_specs.key\_id| Base64 でエンコードされた文字列バイナリ、16 バイト | キーの一意識別子です。 


##ポリシーのオーバーライド 

名前 | 値 | Description
---|---|---
policy\_overrides can\_play | ブール値。true または false | コンテンツの再生が許可されていることを示します。既定値は false です。
policy\_overrides can\_persist | ブール値。true または false |オフラインで使用するために、ライセンスを非揮発性ストレージに保持できることを示します。既定値は false です。
policy\_overrides can\_renew | ブール値 true または false |このライセンスの更新が許可されていることを示します。true の場合、ライセンスの期間をハートビートにより延長できます。既定値は false です。 
policy\_overrides license\_duration\_seconds | int64 | この特定のライセンスの期間を示します。値 0 は、期間に制限がないことを示します。既定値は 0 です。 
policy\_overrides rental\_duration\_seconds | int64 | 再生が許可されている期間を示します。値 0 は、期間に制限がないことを示します。既定値は 0 です。 
policy\_overrides playback\_duration\_seconds | int64 | ライセンス期間内の再生開始後の視聴期間です。値 0 は、期間に制限がないことを示します。既定値は 0 です。 
policy\_overrides renewal\_server\_url |string | このライセンスのすべてのハートビート (更新) 要求を、指定した URL に転送します。このフィールドは、can\_renew が true の場合にのみ使用されます。
policy\_overrides renewal\_delay\_seconds |int64 |license\_start\_time から、最初に更新が試行されるまでの秒数です。このフィールドは、can\_renew が true の場合にのみ使用されます。既定値は 0 です 
policy\_overrides renewal\_retry\_interval\_seconds | int64 | ライセンスの更新要求が失敗した場合の後続の要求までの遅延 (秒) を指定します。このフィールドは、can\_renew が true の場合にのみ使用されます。 
policy\_overrides renewal\_recovery\_duration\_seconds | int64 | ライセンス サーバーのバックエンドの問題が原因で、更新が試行されているがまだ成功していないときに、再生の継続が許可される期間です。値 0 は、期間に制限がないことを示します。このフィールドは、can\_renew が true の場合にのみ使用されます。
policy\_overrides renew\_with\_usage | ブール値 true または false |使用が開始されたときに、更新のためにライセンスが送信されることを示します。このフィールドは、can\_renew が true の場合にのみ使用されます。 

##セッションの初期化

名前 | 値 | Description
---|---|---
provider\_session\_token | Base64 でエンコードされた文字列 |このセッション トークンはライセンスに渡され、後続の更新に含まれます。セッション トークンは、セッションの範囲を超えて保持されません。 
provider\_client\_token | Base64 でエンコードされた文字列 | ライセンスの応答で返信されるクライアント トークンです。ライセンス要求にクライアント トークンが含まれる場合、この値は無視されます。クライアント トークンは、ライセンス セッションの範囲を超えて保持されます。
override\_provider\_client\_token | ブール値。true または false |値が false で、ライセンス要求にクライアント トークンが含まれているときは、この構造体でクライアント トークンを指定している場合でも要求からのトークンが使用されます。true の場合は、常に、この構造体で指定したトークンが使用されます。

##.NET 型を使用した Widevine ライセンスの構成

Media Services が提供する .NET API を使用して、Widevine ライセンスを構成できます。

###Media Services .NET SDK で定義されているクラス

これらの型の定義を次に示します。

	public class WidevineMessage
	{
	    public WidevineMessage();
	
	    [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
	    public AllowedTrackTypes? allowed_track_types { get; set; }
	    [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
	    public ContentKeySpecs[] content_key_specs { get; set; }
	    [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
	    public object policy_overrides { get; set; }
	}

    [JsonConverter(typeof(StringEnumConverter))]
    public enum AllowedTrackTypes
    {
        SD_ONLY = 0,
        SD_HD = 1
    }
    public class ContentKeySpecs
    {
        public ContentKeySpecs();

        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public string key_id { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public RequiredOutputProtection required_output_protection { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public int? security_level { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public string track_type { get; set; }
    }

    public class RequiredOutputProtection
    {
        public RequiredOutputProtection();

        public Hdcp hdcp { get; set; }
    }

    [JsonConverter(typeof(StringEnumConverter))]
    public enum Hdcp
    {
        HDCP_NONE = 0,
        HDCP_V1 = 1,
        HDCP_V2 = 2
    }

###例

次の例では、.NET API を使用して単純な Widevine ライセンスを構成する方法を示しています。

    private static string ConfigureWidevineLicenseTemplate()
    {
        var template = new WidevineMessage
        {
            allowed_track_types = AllowedTrackTypes.SD_HD,
            content_key_specs = new[]
            {
                new ContentKeySpecs
                {
                    required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                    security_level = 1,
                    track_type = "SD"
                }
            },
            policy_overrides = new
            {
                can_play = true,
                can_persist = true,
                can_renew = false
            }
        };

        string configuration = JsonConvert.SerializeObject(template);
        return configuration;
    }


##Media Services のラーニング パス

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##フィードバックの提供

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##関連項目

[PlayReady または Widevine の動的共通暗号化を使用する](media-services-protect-with-drm.md)

<!---HONumber=AcomDC_0928_2016-->