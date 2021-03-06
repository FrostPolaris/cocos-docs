# cc.Layerのベーク関数の使い方

### 提案 ###

ゲーム開発では、UIレイヤーやバックグラウンドレイヤーをゲームに組み込むことが多いと思います。これらのレイヤーは頻繁に変更されることはありませんが、レンダリングする為に多くのリソースを使用しています。レイヤーが複雑な構造の場合、例えばボタンなどはレンダリングするとドローコールが「9」増えてしまいます。ゲーム本体よりもUIレイヤーなどの方がドローコールが多くなってしまう場合もあり、リソースが限られているモバイルブラウザでは避けたいところです。

上記の問題を解決する為、`cc.Layer`に`bake`関数を追加しました。`bake`関数を呼び出すと、レイヤーはキャッシュされたキャンバスに子ノードをベークします。次のフレームではキャッシュされたキャンバスが直接レンダリングされます。ベークしたあとは1回のドローコールで済みます。`unbake`関数を呼び出すことでベークを解除する事ができます。

### 使用例 ###

1. 複雑なUIレイヤー。パネル、ボタンなどのUIコントロール含み、頻繁に変更されないもの。
2. バックグラウンドを含むレイヤー。

### 利用する ###

`cc.Layer`を生成し、自身の子ノードとして追加します。その後、`bake`関数を呼び出します。

サンプルコード :

	var bakeLayer = cc.Layer.create();
    this.addChild( bakeLayer );

    for( var i = 0; i < 9; i++ ) {
       var sprite1 = cc.Sprite.create( s_pathGrossini );
       if ( i % 2 === 0 ) {
          sprite1.setPosition( 90 + i * 80, winSize.height / 2 - 50 );
       } else {
          sprite1.setPosition( 90 + i * 80, winSize.height / 2 + 50 );
       }
         sprite1.rotation = 360 * Math.random();
         bakeLayer.addChild( sprite1 );
    }
    
    bakeLayer.bake();				// レイヤーのベークを有効にする

詳細は`js-tests/src/BakeLayerTest/BakeLayerTest.js`をご覧ください。

### 注意 ###

1. 子ノードが頻繁に変更されるレイヤーでベーク機能を有効にすると、計算量が増えてしまう可能性があります。
2. ベーク機能をサポートしているのはCanvasモードのみになります。WebGLモードやJSBで呼び出しても問題は発生しませんが、効果を得ることはできません。