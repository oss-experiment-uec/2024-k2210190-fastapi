# Artifact Descliption

## FastAPIとは
* FastAPIとはPython3.6以降でAPIを構築するためのWebフレームワーク.
  + [詳細](https://fastapi.tiangolo.com/ja/)

## 今回解決しようとした問題点
* [現状のfastapiではmultipart/form-dataをうまく扱えていない](https://github.com/fastapi/fastapi/issues/10999).
* multipart/form-dataは複数の種類のデータを一度に扱える形式で、主な利用シーンはHTMLフォーム。特にファイルアップロードでよく利用される。
  + [詳細はここ](https://developer.mozilla.org/ja/docs/Web/HTTP/MIME_types)
* 
