---
title: "tandpデータの解説と練習問題など"
layout: page
---

基本的なrelational algebraのサンプルとして、LEAPのソースに含まれている、以下の書籍のサンプルのデータ、

<iframe sandbox="allow-popups allow-scripts allow-modals allow-forms allow-same-origin" style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//rcm-fe.amazon-adsystem.com/e/cm?lt1=_blank&bc1=000000&IS2=1&bg1=FFFFFF&fc1=000000&lc1=0000FF&t=karino203-22&language=ja_JP&o=9&p=8&l=as4&m=amazon&f=ifr&ref=as_ss_li_til&asins=B00OD5CB50&linkId=031b79722ee8b82c89df4ef320cc8118"></iframe>

の例に使われているデータを、source/data/tandp下に作っておきました。（LEAPのソースはGPLなのでこれを再配布したい時はご注意を）

tandpはLEAPのソースコードで使われていた名前ですが、おそらく書籍のタイトル、Theory and Practice of Relational Databasesの最初のTheory and Practiceを略してt and pから来ていると思います。

LEAPのソースコードに書かれているページ数は手元の第二版とはずれてるので自分の調べたページ数を以下に書いておきます。

**図書館**

- book.csv (p57, Example 4.1)
- index.csv (p57, Example 4.1)
- subject.csv (p57, Example 4.1)
- auction.csv (p60, Example 4.2)

図書館のデータベース。
bookは実際の各本のデータが入っている。
例えば「Androidを支える技術 I」が二冊ある場合も、このbook.csvに2つエントリが出来る。

subjectは分類コードとその名前が入っている。

indexは書籍の種類と棚が入っている。こちらは同じ本が二冊入っていたりはしない。

この３つが基本的なテーブルとなる。

最後のauctionは付加的なテーブルで、
図書館は古くなった本をオークションに出す、
という設定で、その取引の内容が記録されている。

purchase_priceが図書館が最初にその書籍を購入した時の価格、sell_priceはオークションで支払った価格。

**divideのサンプル(p65, Example 4.7)**

- lc.csv
- q.csv

**ファッション雑貨店の在庫管理データベース(p67〜p68、4.3)**

- goods.csv
- delivery.csv
- stock.csv

ファッション雑貨店の在庫管理データベースです。
商品はgoods.csvに一覧があります。

たくさんの支社に対して商品を配送していて、その配送記録がdelivery.csvにあります。

各支社の在庫がstock.csvにあります。
