# Artifact Descliption

## 概要:{FastAPIのバグ修正}
* FastAPIとはPython3.6以降でAPIを構築するためのWebフレームワーク.
  + [詳細](https://fastapi.tiangolo.com/ja/)
* [現状のfastapiではmultipart/form-dataをうまく扱えていない](https://github.com/fastapi/fastapi/issues/10999).
* multipart/form-dataは複数の種類のデータを一度に扱える形式で、主な利用シーンはHTMLフォーム。特にファイルアップロードでよく利用される。
  + [詳細はここ](https://developer.mozilla.org/ja/docs/Web/HTTP/MIME_types)
  
## クイックスタート

Dockerイメージは以下のようにしてpullしてrunする.

```
docker pull mitsukikawaguchi/2024-k2210190-fastapi
docker run -it --rm --name console mitsukikawaguchi/2024-k2210190-fastapi
```

## 評価手順
例えば、以下のコードがあるとき、

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

![image](https://github.com/user-attachments/assets/0150b697-4864-4d68-8cf5-6cedaeaba506)

## 制限と展望


