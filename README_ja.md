# Teachable Machine Boilerplate

この小さなボイラープレート(定型的プロジェクト)は、[Teachable Machine](https://teachablemachine.withgoogle.com/) のように [tensorflow.js](https://github.com/tensorflow/tfjs-models) を利用する方法を示したものです。このプロジェクトのソースコードには、Web カメラの画像を用いて、ブラウザでライブトレーニングを行う kNN ([k-nearest neighbors](https://en.wikipedia.org/wiki/K-nearest_neighbors_algorithm)) 分類器を作成する方法が記述されています。新規プロジェクトの出発点となることを目的として、意図的に単純さを維持するような作りにしています。

裏側について言及すると、Web カメラの映像は、[MobileNet](https://github.com/tensorflow/tfjs-examples/tree/master/mobilenet) を用いてアクティベーションされます。このネットワークは、Imagenet データセットからあらゆるクラスの画像を認識するように訓練されており、ブラウザ用にすごく小さなサイズとなるように最適化されています。オリジナルの MobileNet の出力値(分類の予測結果)をそのまま読み取る代わりに、ネットワークの最終層のひとつ手前の層の出力値を kNN(k近傍法) 分類器への入力とすることで、利用者が定めた独自の分類を学習させることができるようになっています。 

画像の各ピクセルの値を直接 kNN 分類器に入力する代わりに MobileNet モデルを利用することで、Imagenet の分類を認識するためにニューラルネットワークが学習した高レベルの抽象化を再利用することができます。これにより、例えば微笑み(smile)としかめっ面(frown)を見分けたり、身体のモーションの小さな差異を見分けることのできる分類器を、ほんの少しのサンプル画像で訓練することができます。この手法は、転移学習([Transfer Learning](https://en.wikipedia.org/wiki/Transfer_learning))と呼ばれています。

Tensorflow では、このような仕組みの実装に役立つ組み込みモデルが提供されています。このボイラープレートには [KNN Classifier](https://github.com/tensorflow/tfjs-models/tree/master/knn-classifier) という名前の組み込み分類器の簡単な利用方法が記述されています。

## Use code

このプロジェクトのコードを利用するには、まず、稼働に必要な Javascript の依存ライブラリをインストールします。

```
npm install
```

次に、budo ローカル Web サーバを起動します。

```
npm start
```

Web サーバがポート [`9966`](http://localhost:9966) で起動します。ブラウザでアクセスし Web カメラのパーミッションを有効化します。ボタンをクリックし続けることでサンプルを取得します。


## Quick Reference - KNN Classifier

概要として最も重要な `tensorflow.js` 関数は [KNNClassifier](https://github.com/tensorflow/tfjs-models/tree/master/knn-classifier) です。

- `knnClassifier.create()`: KNNImageClassifier オブジェクトを返します。

- `.addExample(example, classIndex)`: 特定のクラスに対応するサンプル画像をトレーニングセットに追加します。

- `.clearClass(classIndex)`: 特定のクラスのトレーニングデータを全件削除します。

- `.predictClass(image)`: 入力画像について予測を実行します。結果として、最上位となったクラスのインデックス番号と、その確信度(confidence score) を返します。

[実装を見る](https://github.com/tensorflow/tfjs-models/blob/master/knn-classifier/src/index.ts)


## Quick Reference - MobileNet

概要として最も重要な `tensorflow.js` モデルは [MobileNet](https://github.com/tensorflow/tfjs-models/tree/master/mobilenet) です。

- `.load()`: モデルをロードし、モデルオブジェクトを返します。

- `.infer(image, endpoint)`: 中間のアクティベーションか、もしくは予測結果(logit) を `Tensorflow.js` の `Tensor` 型オブジェクトとして取得します。予測結果を取得する場合は、引数として画像をとり、オプションのエンドポイントには何も指定しません。


[実装を見る](https://github.com/tensorflow/tfjs-models/blob/master/mobilenet/src/index.ts)