# Using Plateau Model in Unity - UnityにPlateauモデルを持っていこう


## Plateauモデル？

国土交通省が公開している東京２３区を含む各市町村の３Dデータです。ダウンロードできる形式にはCityGML、FBX、OBJなどがあります。

- [PLATEAU公式サイト](https://www.mlit.go.jp/plateau/)
- [3D都市モデル（Project PLATEAU）ポータルサイト](https://www.geospatial.jp/ckan/dataset/plateau)
- [ライセンス情報](https://www.mlit.go.jp/plateau/site-policy/)


今回はここからデータをダウンロードしてUnity（ゲームエンジン）に持っていって描写できるようにしようという初心者向けのハンズオンです。今回コードは一切書きません。

## 何が学べる？

- 公開されている都市や各市町村のデータの簡単な内容の説明とその入手の方法
- Unityに3Dモデルをインポートする方法
- インポートした重い3Dモデルを最適化して描写パフォーマンスを何十倍も上げる方法
- Unityでフライスルー（飛べる）

## 手順

1. Plateauの3Dモデルのダウンロード
2. Unityに3Dモデルのインポート
3. パフォーマンスのチェック
4. インポートしたモデルの最適化
5. Unityでフライスルー

## 必要なもの

- [Unity](https://unity3d.com/get-unity/download)：今回使うゲームエンジン（動作環境は2020で行いました）
- [Simplygon SDK](https://www.simplygon.com/Downloads)：3Dモデル最適化ツール（Windowsのみ）
- Microsoftのアカウント：Simplygonのインストールに必要。
- [Free Fly Camera](https://assetstore.unity.com/packages/tools/camera/free-fly-camera-140739)：Unityのフライスルー用アセット。
- Unityのアカウント：Unityのアセットをダウンロードするのに必要。無料版でOK。

## 1. Plateauの3Dモデルのダウンロード

[3D都市モデルポータルサイト](https://www.geospatial.jp/ckan/dataset/plateau)に行き、好きな都市のモデルをダウンロードします。

![2021-07-17_01-45-25](https://user-images.githubusercontent.com/2336918/125981997-38590749-800b-45d1-a673-569353388229.png)


色々なフォーマットのデータが公開されていますが、今回はUnityに持っていく都合上、直接インポートすることができるFBX形式を選ぶことにします。東京都23区のページに行くと、FBX形式に３次メッシュと４次メッシュの二種類がありますが、これは1つの３Dモデルデータの切り取り範囲を示しています。３次メッシュnの場合、1つのモデルファイルは1km四方になっています。4次の場合は500m四方です。Plaeauのモデルは非常に重いため、Unityに持っていく場合は可能な限り細切れで持っていった方がいいので、モデルが500m四方に切られている４次メッシュを選びます。


![2021-07-17_01-45-25](https://user-images.githubusercontent.com/2336918/125982680-5a424c57-1877-4578-b986-948ab7472c5d.png)

ちょっとわかりづらいですが、このサイトではデータは地域メッシュコードという緯度軽度ベースのコードによって管理されています。どの地区がどのコードに対応するかを確認したい場合は、ダウンロードページの中にある範囲図を確認してください。


![2021-07-17_02-01-50](https://user-images.githubusercontent.com/2336918/125984704-9ab31b80-85a2-479f-82eb-f41c694df7f2.png)

![2021-07-17_02-02-43](https://user-images.githubusercontent.com/2336918/125984750-1192a82b-152a-4302-965d-a05bc9e4e682.png)

ダウンロードしたい地区が決まったら、FBXデータをダウンロードしてください。

![2021-07-17_02-00-50](https://user-images.githubusercontent.com/2336918/125984759-589623a3-70e3-4ce1-8c48-825057b568d0.png)

こちらのデータ容量はそれぞれギガを超えることがあるくらい重いので、ハンズオンでは事前にこちらでダウンロードしていた品川のLOD2（詳細な形状＋テクスチャ付き）のモデルを利用します。利用規約を見ると公衆送信は可能ということなので、事前にダウンロードしたデータを下記URLからダウンロードしてください(期間限定)。

https://8.gigafile.nu/0724-e1d4c984284d233b23cf0da8ed7bec31e



## 2. Unityに3Dモデルのインポート

Unityで新しいプロジェクトを作り、早速ダウンロードした3Dモデルをインポートしてみます。まずはAssetsフォルダの中にModelsというフォルダを作っておきます。

![2021-07-17_02-21-49](https://user-images.githubusercontent.com/2336918/125985571-67a15789-941e-4d55-9169-5e5899dceec5.png)

作ったら、フォルダの中に入り、ドラッグドロップでダウンロードしたFBXデータを突っ込みます。環境によって読み込みが非常に遅い場合があるので、とりあえず最初は一つずつインポートしましょう。

![2021-07-17_02-26-51](https://user-images.githubusercontent.com/2336918/125986177-5eb22826-fe03-46d9-88fc-f070394267a0.png)

インポートしたモデルをシーンにドラッグしてみると、環境によってはテクスチャがインポートされてない場合があります。その時はメッシュアセットのMaterialsタブにいって、Extract Texturesボタンを押して、好きなフォルダにテクスチャを書き出します。そうすると自動でメッシュにテクスチャが貼り付けられます。

![2021-07-17_02-31-53](https://user-images.githubusercontent.com/2336918/125986763-7c7a9567-1948-4cfc-8612-b9639f0b1e5c.png)

![2021-07-17_02-35-56](https://user-images.githubusercontent.com/2336918/125987236-71f78c69-6f49-472d-9bfd-fa2a88e95b6c.png)

時間が許せば用意したモデルを全部インポートしてしまいましょう。

![2021-07-17_02-38-09](https://user-images.githubusercontent.com/2336918/125987527-938e2b36-7fd0-47a8-bf43-e8344aa1be16.png)



## 3. パフォーマンスのチェック

さてインポートまではできたので、これくらいのモデルでどれくらい重いのか、パフォーマンスを確認してみましょう。

カメラをまずは建物を向くように配置します。エディタ上の見え方をカメラにコピーするためにGameObjectメニューのAlign With Viewを利用します。

![2021-07-17_02-43-19](https://user-images.githubusercontent.com/2336918/125988060-f53f60a5-bdef-4863-9acd-9206ed64bfb6.png)


レンダリングの速度を確認するためにStatsボタンをクリックして描写に関係ある情報のウィンドウを表示して、またMaximize On Playボタンもオンになっていることを確認します。


![2021-07-17_02-45-48](https://user-images.githubusercontent.com/2336918/125988507-abc343dc-28e7-4a92-860d-551795fdf362.png)

準備ができたらプレイボタンを押してプレイしてみましょう。ここでStatsのウィンドウを確認します。

![2021-07-17_02-46-25](https://user-images.githubusercontent.com/2336918/125988516-371dbc22-dbec-4170-9814-ace31ff600f7.png)

特に確認したいのは、FPS（１秒間に何回描写更新できているか）と、Batchesの部分です。FPSは多ければ多い程よく（通常60以上あるのがリアルタイム描写上は望ましい）、Batchesは少なければ少ないほどいいです。

![2021-07-17_02-47-01](https://user-images.githubusercontent.com/2336918/125988538-8c81ebbe-468c-4a5d-8ccb-78c533667dd2.png)

見てわかる通り、とても遅いです。これはBatchesが異様に大きいためです。この値をドローコール数とも呼びます。このドローコール数は一個一個のメッシュやマテリアルを順番に描写する際の全体の数というイメージで持っていればいいかと思います。つまりマテリアルの数が増えれば増えるほど、メッシュの数が増えれば増える程ドローコール数が増えて、１秒間に全体を描写できる回数が少なくなって遅くなるというわけです。

逆を言えば、このドローコール数を減らせば、全体的なパフォーマンスを上げることができるという意味でもあります。


![2021-07-17_02-58-30](https://user-images.githubusercontent.com/2336918/125989815-54716af4-650a-4e45-aada-dbe4debed5e9.png)

![2021-07-17_02-58-53](https://user-images.githubusercontent.com/2336918/125989828-00b447d9-2e93-4855-9674-adc90a9116f0.png)


見てわかる通り、無駄にメッシュ数とテクスチャ数が多いです。重いはずです。



## 4. インポートしたモデルの最適化

ではこの重いモデルを最適化（ドローコール数を減らす）してパフォーマンスを上げてみましょう。やることは二つです。

1. バラバラになっている3Dモデルを結合してドローコール数を減らす。
2. 細かく分割されているテクスチャを一つ、あるいは数個の大きなテクスチャとして結合してドローコール数を減らす。

これらの操作はゲーム作りでは一般的によく行われていることで、メッシュに関してはMesh CombineとかMesh Mergeとかのキーワードで探すと色々な方法が出てきます。テクスチャの結合はTexture PackingやTexture Bakeなどのキーワードでその方法が色々出てきます。

UnityのAsset Storeでこれらのキーワードで検索すると、[Mesh Baker](https://assetstore.unity.com/packages/tools/modeling/mesh-baker-5017)のようなこのドローコール数削減のためのアセットがいくつか出てきます。ただ、使えるアセットはほぼ有料のため、今回はAsset Store上のアセットではなく、Windows専用になってしまいますが無料で使えるUnityのプラグインを使いたいと思います。

今回使うプラグイン（ツール）の名前は[Simplygon](https://www.simplygon.com/)という元々有料であったツールで、AAAゲームにも使われた実績のあるものです。個人利用あるいは組織で１席分までは無料で１日２００個のモデル変換ができるツールになっています。歴史が古いので、信用度は高いです。

![2021-07-17_03-10-12](https://user-images.githubusercontent.com/2336918/125990949-483c01aa-60cd-42dd-907d-6398b79f06f4.png)


![2021-07-17_03-10-40](https://user-images.githubusercontent.com/2336918/125991076-513e01ba-6c47-4601-8adb-a7ecda87b8c6.png)

ダウンロードページへ行って、SDKインストーラーをダウンロードしてインストールしてください。インストール時、Microsoftのアカウントが必要です。

次にUnityでSimplygonを使えるようにするために、必要なパッケージをインストールしておきます。WindowメニューからPackage Managerを選びます。

![2021-07-17_03-16-08](https://user-images.githubusercontent.com/2336918/125991578-68589241-2a5a-46bc-bc5e-af94e99c34e7.png)

歯車アイコンをクリックしてAdvanced Project Settingsを選び、開いたウィンドウのEnable Preview PackagesとShow Dependenciesのチェックをオンにしてプレビューのパッケージを表示できるようにします。

![2021-07-17_03-17-36](https://user-images.githubusercontent.com/2336918/125991719-c6148241-00be-420f-861a-d584b958d688.png)

![2021-07-17_03-17-57](https://user-images.githubusercontent.com/2336918/125991744-74f75e70-58ed-444c-924e-7f922f094923.png)


Package ManagerでUntiy Registryを表示し、USDをインストールします。

![2021-07-17_03-21-04](https://user-images.githubusercontent.com/2336918/125992106-41883fcc-457e-4e99-a06e-2e1fe1f20294.png)


諸々Unity側のセットアップができたら、Program Files > Simplygon > 9 > Unity > bin　のフォルダに行き、中にあるdllファイルをUnityの任意のフォルダにドラッグドロップでコピーします。

![2021-07-17_03-10-40](https://user-images.githubusercontent.com/2336918/125991076-513e01ba-6c47-4601-8adb-a7ecda87b8c6.png)

![2021-07-17_03-14-48](https://user-images.githubusercontent.com/2336918/125991434-26ac0dda-5e97-4cce-a70f-2e50edc87807.png)


うまくインストールできてれば、WindowメニューにSimplygonという項目が増えているはずです。起動しましょう。

![2021-07-17_03-23-47](https://user-images.githubusercontent.com/2336918/125992401-efbf9cc6-e2be-42e6-b077-43be29b39491.png)

![2021-07-17_03-25-25](https://user-images.githubusercontent.com/2336918/125992520-91ecde91-7c30-4677-8d85-2cd822517dd7.png)


このWindowの中のAdd LOD Componentというボタンを押して、Basicテンプレートの中のAggregation with material bakingという項目を選びます。これが今回欲しい、メッシュの結合とテクスチャの結合を同時に行うことができるオプションです。

![2021-07-17_03-27-22](https://user-images.githubusercontent.com/2336918/125992864-922fbfb4-5fe3-4d1e-8e1b-f219a23d2b6d.png)

次に、最適化したいモデルを複数選択します。その選択したモデルが結合されることになりますが、一つ注意点があります。選択した時のモデルのメッシュの三角形の総数が65000を越えてはいけないという点です。越えた状態で結合しようとするとエラーが出ます。選択したモデルの三角形の総数は右下のTrianglesで確認することができます。

また、MappingImageSettings>OutputMaterialSettingsのTexture WidthとTexture Heightを最大の8192にしておきます。最終的に結合された時のテクスチャのサイズになります。大きければ大きいほどテクスチャのディテールを残せるというわけです。テクスチャのサイズを大きくしてもドローコール数は大きくはなりません。

全部設定が終わったら、最後に一番下にある黄色いボタンを押して結合を開始します。重いモデルだとそれなりの時間がかかります。


![2021-07-17_03-29-51](https://user-images.githubusercontent.com/2336918/125993140-dcd52806-cb51-4087-957e-1cef413e772d.png)


結合が終わると、LODというフォルダが作られていて、結合されたメッシュとテクスチャのデータが入ります。そしてシーンには結合されたモデルができているはずです。

![2021-07-17_03-38-27](https://user-images.githubusercontent.com/2336918/125993916-26fa960b-13a2-416a-87d1-09093cb4aade.png)


では、結合前のモデルは隠して、結合後のモデルを表示した上でプレイして、パフォーマンスがどう変化したかみてみましょう。


![2021-07-17_03-43-13](https://user-images.githubusercontent.com/2336918/125994438-78e3a362-0e60-4d43-9865-6b5ee76b05a7.png)

![2021-07-17_03-43-53](https://user-images.githubusercontent.com/2336918/125994532-688c02d7-93d6-43f3-92b1-4d574f81f8f9.png)

ドローコール数18で、約300FPSになりました。おめでとうございます。これでだいぶ速くなりました。


## 5. Unityでフライスルー

最後にその速さを体感するために、プレイ中にフライスルーしてみましょう。

[Free Fly Camera](https://assetstore.unity.com/packages/tools/camera/free-fly-camera-140739)をインストールしてインポートし、フライスルー用のスクリプトをカメラに貼り付けます。それだけです。


![2021-07-17_03-48-31](https://user-images.githubusercontent.com/2336918/125995020-6b3d8149-7fd0-450f-a2e9-79ad329ebe93.png)

![2021-07-17_03-49-58](https://user-images.githubusercontent.com/2336918/125995173-f0c0592f-60dc-44e5-ac2a-c0154822df37.png)

プレイボタンを押して、マウスで回転、AWSDキーで移動できるようになっているはずです。


## まとめ

Plateau自体を利用することは簡単ですし、日本の各都市の３Dモデルを無料で利用できる恩恵は大きいと思います。ただそのままだと描写に最適化されてないモデルなので、そのままゲームエンジンなどリアルタイム性が求められる環境で使うのはなかなか厳しいものがあります。ということで今回はSimplygonというツールを使って描写の最適化を行いました。どちらかというとそちらの方がメインです。このフローは他のどんな重いモデルにも適用できるテクニックなので、覚えておくと便利かと思います。Macでも同じようなことをやりたい場合は、Asset StoreにあるMesh Bakerみたいな有料なアセットを利用するのがいいかと思います。ただ、安定度でいうとSimplygonの方が上です（UE4やMaya用にもあったり、C#やPython用のAPIなども用意されているのでバッチプログラムも簡単に作れてしまいます、すごい）
