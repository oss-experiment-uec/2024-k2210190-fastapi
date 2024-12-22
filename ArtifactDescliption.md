# Artifact Descliption

## 概要 FastAPIのmultipart/form-dataが表示されない
* FastAPIとは
Python3.6以降でAPIを構築するためのWebフレームワーク
 +[詳細](https://fastapi.tiangolo.com/ja/)
* 現状のfastapiではmultipart/form-dataをうまく扱えていない.
* multipart/form-dataは複数の種類のデータを一度に扱える形式で、主な利用シーンはHTMLフォーム。特にファイルアップロードでよく利用される。
  + [詳細はここ](https://developer.mozilla.org/ja/docs/Web/HTTP/MIME_types)
