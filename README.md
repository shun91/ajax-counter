# ajax-counter
Ajax によるシンプルなアクセスカウンタです．  
Javascript(jQuery) + PHP で実装されています．

## 機能
- Webページのアクセス数をカウントできます．
- 「PV数」をカウントします．「同一IPはカウントしない」等の機能はありません．
- 累計，今日，昨日のアクセス数を表示することができます．
- 過去の日計アクセス数の保存機能があります．
- Ajax を利用しているので，ページがキャッシュされていてもちゃんとカウントしてくれます．
- カウントしたいページにHTMLタグを数行埋め込むだけで動作します．

## ファイル構成
```
ajax-counter/
- counter.js     # カウンタ本体のAjaxです(JS)
- counter.php    # カウンタ本体のAPIです(PHP)
- README.md      # 説明書
- dat/           # アクセス数保存ディレクトリ
  - count.dat    # 累計, 今日, 昨日のアクセス数
  - log.dat      # 過去の日計アクセス数
```

## 使い方

### ファイルのDL
このリポジトリをクローン or DL します．

### ファイルの書き換え
`counter.js` の3行目の `url` を適切な絶対パスに変更します．
```javascript
url: '/ajax-counter/counter.php', // counter.php への絶対パス．
```

`counter.php` の7, 8行目のログファイルの保存場所を適切な絶対パスに変更します．
```php
define('COUNT_FILE', DOCUMENT_ROOT . '/ajax-counter/dat/count.dat'); // アクセス数
define('LOG_FILE', DOCUMENT_ROOT . '/ajax-counter/dat/log.dat'); // ログ
```

### ファイルのアップロード
`ajax-counter/` ディレクトリをWebサーバ上の任意の場所にアップロードします．

### パーミッションの変更
`dat/` ディレクトリと，それ以下のファイルに書き込み権限を付与します．
```
- dat/           # 707 or 777
  - count.dat    # 606 or 666
  - log.dat      # 606 or 666
```

### Webページにコードの追加
アクセス数をカウントしたいページの，アクセス数を表示したい部分に以下のコードを追加します．  
`counter.js` のパスは適切なものを適宜指定して下さい．
```html
<script src="/ajax-counter/counter.js"></script>
<div class="counter"></div>
```

### 動作テスト
コードを追加したページにアクセスし，以下のように表示されているか確認して下さい．
```
累計 1 今日 1 昨日 0
```
ページをリロードして，アクセス数がカウントアップしていれば正しく動作しています．

### 注意
もし `counter` というクラス名を既にCSSなど別の場所で使用している場合は，本スクリプトの `counter` クラスとバッティングしてしまいます．  
その場合は以下の部分を書き換えて下さい．

`counter.js` の8行目の `.counter` を別のクラス名に変更する．
```javascript
jQuery(".ajax-counter").append('累計&ensp;'+res.total+'&ensp;今日&ensp;'+res.today+'&ensp;昨日&ensp;'+res.yesterday);
```

Webページに追加するコードの `.counter` も↑で変更したのと同じクラスに変更する．
```html
<script src="/ajax-counter/counter.js"></script>
<div class="ajax-counter"></div>
```


## 本スクリプトの使用に関して
本スクリプトは自由にお使いいただけます．改造も自由です．  
ただし，本スクリプトの利用によりいかなる不利益が生じても，作者は一切責任を負わないものとします．

## 技術的なこと

### 仕組み
ざっくり説明すると，以下のように動作しています．

1. ページにアクセスされると， `counter.js` が呼び出されます．
2. `counter.js` は Ajax により `counter.php` を呼び出します．
3. `counter.php` はアクセス数のカウント処理を行い，アクセス数（累計・今日・昨日）を json 形式で出力します．
4. `counter.js` は json を受け取り，アクセス数を `<div class="counter"></div>` 内に表示します．

なお，ブラウザなどで直接 `counter.php` にアクセスしてもエラーとなります．  
`counter.php` は Ajax による呼び出し以外に対してはエラーを吐く仕様となっています．

### アクセス数の表示フォーマット
`counter.js` の8行目の `append()` 内を書き換えることで，自由に変更することができます．
```javascript
jQuery(".ajax-counter").append('累計&ensp;'+res.total+'&ensp;今日&ensp;'+res.today+'&ensp;昨日&ensp;'+res.yesterday);
```

### ログファイルについて

#### count.dat
カンマ区切りで次のデータが格納されています．
```
今日の日付,累計,今日,昨日
```

#### log.dat
1行に1日分のアクセス数が，以下のように保存されています．
```
日付,日計アクセス数
```
下に行くほど新しい日付のアクセス数です．

## 変更履歴

### Ver 1.0.0
初版公開．

### Ver 1.0.1
Ajax の呼び出しをキャッシュしないよう修正．