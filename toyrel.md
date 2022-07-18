---
title: "第二回 Relational Algebraの処理系、ToyRelを作ろう"
layout: page
---
<style>
   .column{
        padding: 0.5em 1em;
        margin: 2em 0;
        color: #5d627b;
        background: white;
        border-top: solid 5px #5d627b;
        box-shadow: 0 3px 5px rgba(0, 0, 0, 0.22);
    }
    .post-content h1 {
       font-size: 34px;
    }
</style>

第二回では [LEAP RDBMS : Home](http://leap.sourceforge.net/) のようなrelational algebraの処理系を作ってみます。
いわゆるリレーショナルデータベースのおもちゃを作る感じです。
名前をtoyのrelational algebraという事でToyRelと呼ぶ事にします。

なおLEAPを題材にRelational Algebraやデータベースの基礎を教える本として、以下があります。

<iframe sandbox="allow-popups allow-scripts allow-modals allow-forms allow-same-origin" style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//rcm-fe.amazon-adsystem.com/e/cm?lt1=_blank&bc1=000000&IS2=1&bg1=FFFFFF&fc1=000000&lc1=0000FF&t=karino203-22&language=ja_JP&o=9&p=8&l=as4&m=amazon&f=ifr&ref=as_ss_li_til&asins=B00OD5CB50&linkId=031b79722ee8b82c89df4ef320cc8118"></iframe>

ちょっと高くて古い本ですが、ちゃんと基本的なデータベース関連の理論がまとまっていながら不要に難しいと思う所はちゃんとカットしていたりと悪くない本なので興味があったらどうぞ。
自分は第二回の内容を書く為に買いました。

なお、第二回をやるのに特にこの本の内容を前提にはしません。
上のリンクから買ってくれるとちょっとアフィリエイトが入って嬉しいくらい。


## 実装するものの概要

- project
- select
- union
- difference
- intersect
- product
- join

relationは全てcsvで保存される。
toyrel上では拡張子を取り除いたものがrelationの名前として使われる。

```
> シラバスcls = project (シラバス) (class)
> インデックスcls = project (インデックス) (class)
> result = difference (シラバスcls) (インデックスcls)
```

```
> シラバスと建物 = join (シラバス) (建物) (シラバス.専門 = 建物.所属)
```

relationは全てその場で適当に作られたランダムな名前のファイルに保存される。
左辺がある場合はその名前のcsvに保存される。

```
> project (シラバス) (専門, 学年)
Relation zzybac returned.
> print zzybac
専門   学年
----  ----
数学    1
物理    1
数学    2
```

printでリレーションを表示する。
同じ行は削除される事に注意。

