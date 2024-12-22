# Artifact Descliption

## 概要:{FastAPIのバグ修正}
* FastAPIとはPython3.6以降でAPIを構築するためのWebフレームワーク.
  + [詳細](https://fastapi.tiangolo.com/ja/)
* [現状のfastapiではmultipart/form-dataをうまく扱えていない](https://github.com/fastapi/fastapi/issues/10999).
* multipart/form-dataは複数の種類のデータを一度に扱える形式で、主な利用シーンはHTMLフォーム。特にファイルアップロードでよく利用される。
  + [詳細はここ](https://developer.mozilla.org/ja/docs/Web/HTTP/MIME_types)

以下、問題点を具体的に説明.

一例として、評価手順の通りに実行すると、[http://127.0.0.1:8000/docs](http://127.0.0.1:8000/docs)で次のような画面が出力される。

![image](https://github.com/user-attachments/assets/4a67f3d6-1fce-4cae-ab81-30537f7e97f3)

なお、Formなしだと正常に動作する模様.

例えば、下記のプログラム(main.pyとする)を評価手順の手法の通りに実行すると、

```
from fastapi import FastAPI

app = FastAPI()


@app.get("/")
async def root():
    return {"message": "Hello World"}
```

次のような画面が出力される。

![image](https://github.com/user-attachments/assets/41df2a3b-cd0d-455d-94d5-0c8922bcda1f)

[こちら](https://fastapi.tiangolo.com/ja/tutorial/first-steps/)
のページを確認すると正常に動作していると考えられる。

## クイックスタート

Dockerイメージは以下のようにしてpullしてrunする.

```
docker pull mitsukikawaguchi/2024-k2210190-fastapi
docker run -it --rm --name console mitsukikawaguchi/2024-k2210190-fastapi
```

## 評価手順
例えば、以下のコードがあるとき、(ファイル名をCheck_Form.pyとする)

```
from typing import Annotated

from fastapi import FastAPI, Form
from pydantic import BaseModel

app = FastAPI()


class FormData(BaseModel):
    username: str
    password: str


@app.post("/login/")
async def login(data: Annotated[FormData, Form()]):
    return data
```
次のコマンドで実行し、

```
python -m uvicorn Check_Form:app
```

[http://127.0.0.1:8000/docs](http://127.0.0.1:8000/docs)にアクセスして下のような画面が表示されれば、成功.

![image](https://github.com/user-attachments/assets/0150b697-4864-4d68-8cf5-6cedaeaba506)

## 制限と展望


