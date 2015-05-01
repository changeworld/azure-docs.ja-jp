<properties 
	pageTitle="ドルビー デジタル プラスを使ったメディアのエンコード" 
	description="このトピックでは、ドルビー デジタル プラスを使用してメディアをエンコードする方法を説明します。" 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/01/2015" 
	ms.author="juliako"/>

#ドルビー デジタル プラスを使ったメディアのエンコード

Azure メディア エンコーダーでは、**ドルビー(r) デジタル プラス** エンコードがサポートされています。ドルビー(r) デジタル プラス (別称 Enhanced AC-3 (E-AC-3)) は、日々進化するメディア専用に設計された先進的なサラウンド サウンド オーディオ コーデックです。ホーム シアターやホーム PC を初め、携帯電話やオンライン ストリーミングまで、ドルビー デジタル プラスは高品質な音声を実現します。あらゆるエンターテイメントにおいて、よく知られたドルビーの映画のような音響表現が可能になります。ドルビー デジタル プラスはドルビー デジタルの主要なテクノロジを基盤としています。ドルビー デジタルは、映画、放送、ホーム シアターのサラウンド サウンドの規格として確立されています。モバイル デバイスの急増に伴い、ドルビー デジタル プラスはモバイル エンターテイメントの規格としても使用されています。オーディオの拡張にこの先進的な新しいテクノロジを取り入れることで、より高品質なサウンドと帯域幅の節減を実現できます。帯域幅が限られている場合でも、ほとんど途切れることなくすばらしいコンテンツを入手できます。


##ドルビー デジタル プラスを使ったエンコード用の Azure メディア エンコーダーのセットアップ

###Azure メディア エンコーダー プロセッサの取得 

Azure メディア エンコーダーでは、ドルビー デジタル プラスがサポートされています。**Azure メディア エンコーダー**の詳細については、「[メディア プロセッサの取得](media-services-get-media-processor.md)」に関するトピックをご覧ください。

###<a id="configure_preset"></a>Azure メディア エンコーダーの設定

Azure メディア エンコーダーで使用するエンコード設定を構成したときに、覚えやすい文字列で表された定義済みプリセットが多数ありました。ドルビー デジタル プラス エンコーダーには豊富なコントロールが用意されています。詳細については、[<DolbyDigitalPlusAudioProfile>](https://msdn.microsoft.com/library/azure/dn296500.aspx) をご覧ください。したがって、このコーデックを使用する既成の文字列プリセットはありません。目的のエンコーダー設定は XML ファイルで指定し、次のコード例にあるタスクを使ってこのデータを送信する必要があります。
	
	string configuration = File.ReadAllText(pathToXMLConfigFile));

    ITask task = job.Tasks.AddNew("My Dolby Digital Plus Encode Task",
        processor,
        configuration,
        _clearConfig);

このトピックでは、エンコーダー設定を構成する XML プリセットの例をいくつか示します。ドルビー デジタル プラス エンコードの構成に使用する要素は [<DolbyDigitalPlusAudioProfile>](https://msdn.microsoft.com/library/azure/dn296500.aspx) で、これは、Azure メディア エンコーダー XML プリセットの <AudioProfile> の子ノードとして表されます。この XML 要素には、エンコードのさまざまな要素を制御する多数の属性が含まれます。

##ドルビー デジタル プラス 5.1 マルチチャネルへのエンコード

ドルビー デジタル プラス 5.1 マルチチャネルにエンコードするには、Codec および EncoderMode 属性を "DolbyDigitalPlus" に設定します。エンコードするチャネルの数は、<DolbyDigitalPlusAudioProfile> 要素の AudioCodingMode 属性を使用して指定します。5.1 マルチチャネルのエンコード用に AudioCodingMode を "Mode32" に設定します。 

次の XML プリセットには、H264 ブロードバンド 1080p ビデオとドルビー デジタル プラス 5.1 マルチチャネル オーディオを持つ MP4 ファイルを生成する完全な Azure メディア エンコーダー XML プリセットが含まれています。このプリセットは、LFEOn 属性を true に設定して指定される重低音効果 (LFE) チャネルをエンコードする目的でも指定します。指定しない属性については、すべて既定値が使用されます。

この XML プリセットを **Azure メディア エンコーダー**に渡して、[この](media-services-dotnet-encode-asset.md) トピックにあるようにエンコードのジョブを作成します ([ここ]にあるように、事前定義されたプリセット文字列の代わりにのみ、XML プリセット全体を渡します(#configure_preset))。


	<?xml version="1.0" encoding="utf-16"?>
	<!--Created for Azure Media Encoder, May 26 2013 -->
	  <Preset
	    Version="5.0">
	    <Job />
	    <MediaFile
	      DeinterlaceMode="AutoPixelAdaptive"
	      ResizeQuality="Super"
	      AudioGainLevel="1"
	      VideoResizeMode="Stretch">
	      <OutputFormat>
	        <MP4OutputFormat
	          StreamCompatibility="Standard">
	        <AudioProfile Condition="SourceContainsAudio">
	            <DolbyDigitalPlusAudioProfile
	              Codec="DolbyDigitalPlus"
	              EncoderMode="DolbyDigitalPlus"
	              AudioCodingMode="Mode32"
	              LFEOn="True"
	              SamplesPerSecond="48000"
	              BandwidthLimitingLowpassFilter="True"
	              DialogNormalization="-31">
	              <Bitrate>
	                <ConstantBitrate
	                  Bitrate="512"
	                  IsTwoPass="False"
	                  BufferWindow="00:00:00" />
	              </Bitrate>
	            </DolbyDigitalPlusAudioProfile>
	        </AudioProfile>
	          <VideoProfile Condition="SourceContainsVideo">
	            <HighH264VideoProfile
	              BFrameCount="3"
	              EntropyMode="Cabac"
	              RDOptimizationMode="Speed"
	              HadamardTransform="False"
	              SubBlockMotionSearchMode="Speed"
	              MultiReferenceMotionSearchMode="Balanced"
	              ReferenceBFrames="False"
	              AdaptiveBFrames="True"
	              SceneChangeDetector="True"
	              FastIntraDecisions="False"
	              FastInterDecisions="False"
	              SubPixelMode="Quarter"
	              SliceCount="0"
	              KeyFrameDistance="00:00:05"
	              InLoopFilter="True"
	              MEPartitionLevel="EightByEight"
	              ReferenceFrames="4"
	              SearchRange="64"
	              AutoFit="True"
	              Force16Pixels="False"
	              FrameRate="0"
	              SeparateFilesPerStream="True"
	              SmoothStreaming="False"
	              NumberOfEncoderThreads="0">
	              <Streams
	                AutoSize="False"
	                FreezeSort="False">
	                <StreamInfo
	                  Size="1920, 1080">
	                  <Bitrate>
	                    <ConstantBitrate
	                      Bitrate="6000"
	                      IsTwoPass="False"
	                      BufferWindow="00:00:05" />
	                  </Bitrate>
	                </StreamInfo>
	              </Streams>
	            </HighH264VideoProfile>
	          </VideoProfile>
	        </MP4OutputFormat>
	      </OutputFormat>
	    </MediaFile>
	  </Preset>

##ドルビー デジタル プラス ステレオへのエンコード

ドルビー デジタル プラス ステレオにエンコードするには、Codec および EncoderMode 属性を "DolbyDigitalPlus" に設定します。エンコードするチャネルの数は、AudioCodingMode 属性を使用して指定します。ステレオ エンコード用に AudioCodingMode を "Mode20" に設定します。次の XML プリセットの例では、<DolbyDigitalPlusAudioProfile> を使用して、5.1 オーディオにエンコードしています。指定しない属性については、すべて既定値が使用されます。

この XML プリセットを **Azure メディア エンコーダー**に渡して、[この](media-services-dotnet-encode-asset.md) トピックにあるようにエンコードのジョブを作成します ([ここ](#configure_preset)にあるように、事前定義されたプリセット文字列の代わりにのみ、XML プリセット全体を渡します)。

	<?xml version="1.0" encoding="utf-16"?>
	<!--Created for Azure Media Encoder, May 26 2013 -->
	  <Preset
	    Version="5.0">
	    <Job />
	    <MediaFile
	      DeinterlaceMode="AutoPixelAdaptive"
	      ResizeQuality="Super"
	      AudioGainLevel="1"
	      VideoResizeMode="Stretch">
	      <OutputFormat>
	        <MP4OutputFormat
	          StreamCompatibility="Standard">
	        <AudioProfile Condition="SourceContainsAudio">
	            <DolbyDigitalPlusAudioProfile
	              Codec="DolbyDigitalPlus"
	              EncoderMode="DolbyDigitalPlus"
	              AudioCodingMode="Mode20"
	              LFEOn="False"
	              SamplesPerSecond="48000"
	              DialogNormalization="-31">
	              <Bitrate>
	                <ConstantBitrate
	                  Bitrate="128"
	                  IsTwoPass="False"
	                  BufferWindow="00:00:00" />
	              </Bitrate>
	            </DolbyDigitalPlusAudioProfile>
	        </AudioProfile>
	          <VideoProfile Condition="SourceContainsVideo">
	            <HighH264VideoProfile
	              BFrameCount="1"
	              EntropyMode="Cabac"
	              RDOptimizationMode="Speed"
	              HadamardTransform="False"
	              SubBlockMotionSearchMode="Speed"
	              MultiReferenceMotionSearchMode="Speed"
	              ReferenceBFrames="False"
	              AdaptiveBFrames="True"
	              SceneChangeDetector="True"
	              FastIntraDecisions="False"
	              FastInterDecisions="False"
	              SubPixelMode="Quarter"
	              SliceCount="0"
	              KeyFrameDistance="00:00:05"
	              InLoopFilter="True"
	              MEPartitionLevel="EightByEight"
	              ReferenceFrames="4"
	              SearchRange="32"
	              AutoFit="True"
	              Force16Pixels="False"
	              FrameRate="0"
	              SeparateFilesPerStream="True"
	              SmoothStreaming="False"
	              NumberOfEncoderThreads="0">
	              <Streams
	                AutoSize="False"
	                FreezeSort="False">
	              <StreamInfo
	                Size="852, 480">
	                <Bitrate>
	                  <ConstantBitrate
	                    Bitrate="2200"
	                    IsTwoPass="False"
	                    BufferWindow="00:00:05" />
	                </Bitrate>
	              </StreamInfo>
	              </Streams>
	            </HighH264VideoProfile>
	          </VideoProfile>
	        </MP4OutputFormat>
	      </OutputFormat>
	    </MediaFile>
	  </Preset>

##複数の MP4 ファイルへのエンコード 

1 つの XML プリセット内で複数の MP4 にエンコードできます。生成する各 MP4 について、<Preset> 要素を構成に追加します。各 <Preset> 要素には、ビデオ、オーディオ、またはその両方の構成情報を含めることができます。

###構成

次の構成では、以下の出力が生成されます。

- 8 Video-only MP4 files
	- 1080p Video @ 6000 kbps
	- 1080p Video @ 4700 kbps
	- 720p Video @ 3400 kbps
	- 960 x 540 Video @ 2250 kbps
	- 960 x 540 Video @ 1500 kbps
	- 640 x 380 Video @ 1000 kbps
	- 640 x 380 Video @ 650 kbps
	- 320 x 180 Video @ 400 kbps

- 5 Audio-only MP4 files
	- AAC Audio Stereo @ 128 kbp
	- AAC Audio 5.1 @ 512 kbps
	- Dolby Digital Plus Stereo @ 128 kbps
	- Dolby Digital Plus 5.1 Multichannel @ 512 kbps
	- AAC Stereo @ 56 kbps
- A .ism manifest
- An XML file listing the properties of the generated MP4 files.
		
		<?xml version="1.0" encoding="utf-16"?>
		<!--Created for Azure Media Encoder, May 16 2013 -->
		<Presets>
		  <Preset
		    Version="5.0">
		    <Job />
		    <MediaFile
		      DeinterlaceMode="AutoPixelAdaptive"
		      ResizeQuality="Super"   
		      AudioGainLevel="1"
		      VideoResizeMode="Stretch">
		      <OutputFormat>
		        <MP4OutputFormat
		          StreamCompatibility="Standard">
		          <VideoProfile Condition="SourceContainsVideo">
		            <HighH264VideoProfile
		              BFrameCount="3"
		              EntropyMode="Cabac"
		              RDOptimizationMode="Speed"
		              HadamardTransform="False"
		              SubBlockMotionSearchMode="Speed"
		              MultiReferenceMotionSearchMode="Balanced"
		              ReferenceBFrames="False"
		              AdaptiveBFrames="True"
		              SceneChangeDetector="True"
		              FastIntraDecisions="False"
		              FastInterDecisions="False"
		              SubPixelMode="Quarter"
		              SliceCount="0"
		              KeyFrameDistance="00:00:05"
		              InLoopFilter="True"
		              MEPartitionLevel="EightByEight"
		              ReferenceFrames="4"
		              SearchRange="64"
		              AutoFit="True"
		              Force16Pixels="False"
		              FrameRate="0"
		              SeparateFilesPerStream="True"
		              SmoothStreaming="False"
		              NumberOfEncoderThreads="0">
		              <Streams
		                AutoSize="False"
		                FreezeSort="False">
		                <StreamInfo
		                  Size="1920, 1080">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="6000"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="1920, 1080">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="4700"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="1280, 720">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="3400"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="960, 540">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="2250"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="960, 540">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="1500"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="640, 360">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="1000"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="640, 360">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="650"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="320, 180">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="400"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		              </Streams>
		            </HighH264VideoProfile>
		          </VideoProfile>
		        </MP4OutputFormat>
		      </OutputFormat>
		    </MediaFile>
		  </Preset>
		  <Preset
		    Version="5.0">
		    <Job />
		    <MediaFile
		      DeinterlaceMode="AutoPixelAdaptive"
		      ResizeQuality="Super"
		      NormalizeAudio="True"
		      AudioGainLevel="1"
		      VideoResizeMode="Stretch">
		      <OutputFormat>
		        <MP4OutputFormat
		          StreamCompatibility="PropagateSourceTimeStamps">
		          <AudioProfile>
		            <AacAudioProfile
		              Codec="AAC"
		              Channels="2"
		              BitsPerSample="16"
		              SamplesPerSecond="48000">
		              <Bitrate>
		                <ConstantBitrate
		                  Bitrate="128"
		                  IsTwoPass="False"
		                  BufferWindow="00:00:00" />
		              </Bitrate>
		            </AacAudioProfile>
		          </AudioProfile>
		        </MP4OutputFormat>
		      </OutputFormat>
		    </MediaFile>
		  </Preset>
		  <Preset
		    Version="5.0">
		    <Job />
		    <MediaFile
		      DeinterlaceMode="AutoPixelAdaptive"
		      ResizeQuality="Super"
		      NormalizeAudio="True"
		      AudioGainLevel="1"
		      VideoResizeMode="Stretch">
		      <OutputFormat>
		        <MP4OutputFormat
		          StreamCompatibility="Standard">
		          <AudioProfile>
		            <AacAudioProfile
		              Codec="AAC"
		              Channels="6"
		              BitsPerSample="16"
		              SamplesPerSecond="48000">
		              <Bitrate>
		                <ConstantBitrate
		                  Bitrate="512"
		                  IsTwoPass="False"
		                  BufferWindow="00:00:00" />
		              </Bitrate>
		            </AacAudioProfile>
		          </AudioProfile>
		        </MP4OutputFormat>
		      </OutputFormat>
		    </MediaFile>
		  </Preset>
		  <Preset
		    Version="5.0">
		    <Job />
		    <MediaFile
		      DeinterlaceMode="AutoPixelAdaptive"
		      ResizeQuality="Super"
		      NormalizeAudio="True"
		      AudioGainLevel="1"
		      VideoResizeMode="Stretch">
		      <OutputFormat>
		        <MP4OutputFormat
		          StreamCompatibility="Standard">
		          <AudioProfile>
		            <DolbyDigitalPlusAudioProfile
		              Codec="DolbyDigitalPlus"
		              EncoderMode="DolbyDigitalPlus"
		              Channels="2"
		              AudioCodingMode="Mode20"
		              LFEOn="False"
		              NinetyDegreePhaseShiftSurrounds="False"
		              ThreeDBAttenuationSurrounds="False"
		              DolbySurroundMode="NotIndicated"
		              StereoDownmixPreference="NotIndicated"
		              LtRtCenterMixLevel="-3"
		              LoRoCenterMixLevel="-3"
		              LtRtSurroundMixLevel="-3"
		              LoRoSurroundMixLevel="-3"
		              LFELowpassFilter="False"
		              SamplesPerSecond="48000"
		              BandwidthLimitingLowpassFilter="True"
		              DCHighpassFilter="True"
		              LineModeDynamicRangeControl="FilmStandard"
		              RFModeDynamicRangeControl="FilmStandard"
		              DialogNormalization="-31">
		              <Bitrate>
		                <ConstantBitrate
		                  Bitrate="128"
		                  IsTwoPass="False"
		                  BufferWindow="00:00:00" />
		              </Bitrate>
		            </DolbyDigitalPlusAudioProfile>
		          </AudioProfile>
		        </MP4OutputFormat>
		      </OutputFormat>
		    </MediaFile>
		  </Preset>
		  <Preset
		    Version="5.0">
		    <Job />
		    <MediaFile
		      DeinterlaceMode="AutoPixelAdaptive"
		      ResizeQuality="Super"
		      NormalizeAudio="True"
		      AudioGainLevel="1"
		      VideoResizeMode="Stretch">
		      <OutputFormat>
		        <MP4OutputFormat
		          StreamCompatibility="Standard">
		          <AudioProfile Condition="SourceContainsAudio">
		            <DolbyDigitalPlusAudioProfile
		              Codec="DolbyDigitalPlus"
		              EncoderMode="DolbyDigitalPlus"
		              Channels="6"
		              AudioCodingMode="Mode32"
		              LFEOn="True"
		              NinetyDegreePhaseShiftSurrounds="True"
		              ThreeDBAttenuationSurrounds="False"
		              DolbySurroundMode="NotIndicated"
		              StereoDownmixPreference="NotIndicated"
		              LtRtCenterMixLevel="-3"
		              LoRoCenterMixLevel="-3"
		              LtRtSurroundMixLevel="-3"
		              LoRoSurroundMixLevel="-3"
		              LFELowpassFilter="True"
		              SamplesPerSecond="48000"
		              BandwidthLimitingLowpassFilter="True"
		              DCHighpassFilter="True"
		              LineModeDynamicRangeControl="FilmStandard"
		              RFModeDynamicRangeControl="FilmStandard"
		              DialogNormalization="-31">
		              <Bitrate>
		                <ConstantBitrate
		                  Bitrate="512"
		                  IsTwoPass="False"
		                  BufferWindow="00:00:00" />
		              </Bitrate>
		            </DolbyDigitalPlusAudioProfile>
		          </AudioProfile>
		        </MP4OutputFormat>
		      </OutputFormat>
		    </MediaFile>
		  </Preset>
		  <Preset
		    Version="5.0">
		    <Job />
		    <MediaFile
		      DeinterlaceMode="AutoPixelAdaptive"
		      ResizeQuality="Super"
		      NormalizeAudio="True"
		      AudioGainLevel="1"
		      VideoResizeMode="Stretch">
		      <OutputFormat>
		        <MP4OutputFormat
		          StreamCompatibility="Standard">
		          <AudioProfile>
		            <AacAudioProfile
		              Codec="AAC"
		              Channels="2"
		              BitsPerSample="16"
		              SamplesPerSecond="48000">
		              <Bitrate>
		                <ConstantBitrate
		                  Bitrate="56"
		                  IsTwoPass="False"
		                  BufferWindow="00:00:00" />
		              </Bitrate>
		            </AacAudioProfile>
		          </AudioProfile>
		        </MP4OutputFormat>
		      </OutputFormat>
		    </MediaFile>
		  </Preset>
		</Presets>

##商用のエンコード サービスの作成

Azure Media Services を利用して商用のエンコード サービスを構築したい場合があります。こうした "ビルドオン" サービスを作成する場合は、すべてのドルビー デジタル プラス エンコード パラメーターを利用できることが重要です。<DolbyDigitalPlusAudioProfile> タグ内のすべてのパラメーターがエンドユーザーに公開され、構成可能かご確認ください。これらのパラメーターを利用できるようにするためのガイダンスが必要な場合は、prolicensingsupport@dolby.com までお問い合わせください。

##Dolby Professional Loudness Metering (DPLM) サポートの使用

Azure メディア エンコーダーでは DPLM SDK を使用して、入力オーディオのダイアログのラウドネスを測定し、DialogNormalization の正確な値を設定できます。この機能は、オーディオがドルビー デジタル プラスにエンコードされている場合にのみ利用できます。DPLM は、<DolbyDigitalPlusAudioProfile> 要素の子である <LoudnessMetering> 要素を使用して、プリセット構成ファイルで構成されます。次のプリセットの例は、DPLM の構成方法を示しています。
	
	<?xml version="1.0" encoding="utf-16"?>
	<Preset
	  Version="5.0">
	  <Job />
	  <MediaFile>
	    <OutputFormat>
	      <MP4OutputFormat
	        StreamCompatibility="Standard">
	    <AudioProfile>
	             <DolbyDigitalPlusAudioProfile
	               Codec="DolbyDigitalPlus"
	               EncoderMode="DolbyDigitalPlus"
	               DolbySurroundMode="NotIndicated"
	               StereoDownmixPreference="NotIndicated"
	               SamplesPerSecond="48000"
	               AudioCodingMode="Mode20"
	               Channels="2"
	               BitsPerSample="24">
	               <LoudnessMetering
	                 Mode= "ITU_R_BS_1770_2_DI"
	                 SpeechThreshold="20"
	                 TruePeakEmphasis="false"
	                 TruePeakDCBlock="false" />
	              <Bitrate>
	                <ConstantBitrate
	                  Bitrate="320"
	                  IsTwoPass="False"
	                  BufferWindow="00:00:00" />
	              </Bitrate>
	     </DolbyDigitalPlusAudioProfile>
	    </AudioProfile>
	      </MP4OutputFormat>
	    </OutputFormat>
	  </MediaFile>
	</Preset>

<LoudnessMetering> 要素は、<DolbyDigitalPlusAudioProfile> 要素内にのみ指定できます。<LoudnessMetering> 要素を使用した場合は、DialogNormalization 属性は使用できません。<LoudnessMetering>  要素 DialogNormalization 属性の両方を使用すると、エンコーダーはエラーを生成します。LoudnessMetering のすべての属性は省略可能であり、エンコーダーによって Dolby Laboratories, Inc. が推奨する値が既定で使用されます。

以降のセクションで、各属性について説明します。

###Mode 属性

この属性は、ラウドネス測定モードを指定します。使用できる値は、以下のとおりです。

 
**ITU_R_BS_1770_2_DI** (既定) - ITU-R BS.1770-2 と Dialogue Intelligence を指定します

**ITU_R_BS_1770_1_DI** - ITU-R BS.1770-1 と Dialogue Intelligence を指定します

**ITU_R_BS_1770_2** - ITU-R BS.1770-2 を指定します

**LEQA_DI** - Leq(A) と Dialogue Intelligence を指定します

**注:** 

**EBU R128** モードには、**ITU_R_BS_1770_2_DI** で対応できます。

**Leq(A)** は純粋に従来の資産として含まれており、特定の従来型ワークフローでのみ使用してください。

**ITU** は最近、BS.1770-3 という名前の更新版を公開しました。これは、TruePeakDCBlock と TruePeakEmphasis が共に false に設定された BS.1770-2 に相当します。

###SpeechThreshold 属性

DPLM が統合ラウドネスの結果を生成するために使用する音声のしきい値を指定します (たとえば、音声ゲーティング、レベル ゲーティング、ゲーティングなしのいずれかを選択)。音声のしきい値は、0% から 100% の範囲で 1% 単位で設定します。このパラメーターは、DPLM が Dialogue Intelligence を使用するモードで構成されている場合にのみ有効になります。これは、Mode が ITU_R_BS_1770_2_DI または ITU_R_BS_1770_1_DI に設定されている場合にのみ指定できることを意味します。Mode が ITU_R_BS_1770_2_DI または ITU_R_BS_1770_1_DI のいずれかである場合、既定値は 20% です。この属性の値は、0、1 ～ 100 の範囲内に設定する必要があります。

###TruePeakDCBlock 属性

この入力パラメーターでは、トゥルー ピーク測定内の DC ブロックを有効 (true) にするか、無効 (false) にするかを指定します。DC ブロックの詳細については、ITU‐R BS.1770‐2 をご覧ください。既定値は false です。

###TruePeakEmphasis 属性

トゥルー ピーク測定内の強調フィルターを有効 (true) にするか、無効 (false) にするかを指定します。強調フィルターの詳細については、ITU‐R BS.1770‐2 をご覧ください。既定値は false です。


###DPLM の結果

エンコード タスクで DPLM を使用することを指定した場合、ラウドネス計測の結果は出力アセットのメタデータ XML に含まれます。たとえば次のようになります。
	
	<LoudnessMeteringResultParameters 
	     DPLMVersionInformation="Dolby Professional Loudness Metering Development Kit Version 1.0"
	     DialogNormalization="-15" 
	     IntegratedLoudness="-14.8487606" 
	     IntegratedLoudnessGatingMethod="2" 
	     IntegratedLoudnessSpeechPercentage="11.673481" 
	     SamplePeak="-0.7028221" 
	     TruePeak="0.705999851" />

各属性の詳細は次のとおりです。

**DPLMVersionInformation** - 使用される DPLM SDK のバージョンを示す文字列。

**DialogNormalization** - 入力オーディオからの DialNorm の測定値 (デシベル単位)。出力 DD+ ストリームに {-31, -30, ..., -1} dB の範囲で埋め込まれます。

**IntegratedLoudness** - DPLM で計測された -70 ～ +10 LKFS/dBFS の範囲の統合されたラウドネス (ここで、dBFS は Mode が LEQA_DI に設定された場合にのみ使用されます)。

**IntegratedLoudnessGatingMethod** - 有効な値:0 - なし/ゲーティングなし、1 - 音声ゲーティングあり、2 - レベル ゲーティングあり

**IntegratedLoudnessSpeechPercentage** - この結果には、音声が検出された入力メディアのタイムラインのパーセンテージが含まれます。値の範囲は 0% ～ 100% です。

**SamplePeak** - この結果には、測定のリセット以降の、任意のチャンネルにおけるサンプル絶対値の最大値 (-70 ～ +10 dBFS の範囲) が含まれます。

**TruePeak** - この結果には、測定のリセット以降の、任意のチャンネルにおけるトゥルー ピーク絶対値の最大値が含まれます。トゥルー ピークの詳細については、ITU‐R BS.1770‐2 をご覧ください。-70 から 12.04 dBTP の範囲の値が考えられます。


<!--HONumber=52-->