# Artifact Descliption

## 概要:FastAPIのバグ修正
* FastAPIとはPython3.6以降でAPIを構築するためのWebフレームワーク.
  + [詳細](https://fastapi.tiangolo.com/ja/)
  + [元リポジトリはこちら](https://github.com/fastapi/fastapi)

今回修正しようとしたバグは以下の内容になる.
* [現状のfastapiではmultipart/form-dataをうまく扱えていない](https://github.com/fastapi/fastapi/issues/10999).
  + multipart/form-dataは複数の種類のデータを一度に扱える形式で、主な利用シーンはHTMLフォーム。特にファイルアップロードでよく利用される。
  + [詳細はここ](https://developer.mozilla.org/ja/docs/Web/HTTP/MIME_types)

以下、問題点を具体的に説明.

一例として、評価手順の通りに実行すると、[http://127.0.0.1:8000/docs](http://127.0.0.1:8000/docs)で次のような画面が出力される。

![image](https://github.com/user-attachments/assets/4a67f3d6-1fce-4cae-ab81-30537f7e97f3)
![image](https://github.com/user-attachments/assets/f2c4c7fb-9b15-4488-aa3a-86853863c6f1)

422 Unprocessable Entity 応答状態コードは、サーバーが要求本文のコンテンツ型を理解でき、要求本文の構文が正しいものの、中に含まれている指示が処理できなかったことを表す。

また、File()を含む場合でも同様のエラーを出力した.

なお、Form()やFile()なしだと正常に動作する模様.

例えば、下記のプログラム(main.pyとする)を評価手順の手法の通りに実行すると、

```
from fastapi import FastAPI

app = FastAPI()


@app.get("/")
async def root():
    return {"message": "Hello World"}
```

次のような画面が出力される。

![image](https://github.com/user-attachments/assets/7d84bb28-ab41-4997-b5d9-3b7981d3a020)

上記の画面には422エラーが表示されていないため,正常に動作していると考えられる。

以上から、Form()やFile()に関する部分を修正する必要がある。

改変対象は以下の部分になると考えられる。

[改変元はこちら](https://github.com/fastapi/fastapi/blob/master/fastapi/params.py)

```
class Form(Body):
    def __init__(
        self,
        default: Any = Undefined,
        *,
        default_factory: Union[Callable[[], Any], None] = _Unset,
        annotation: Optional[Any] = None,
        media_type: str = "application/x-www-form-urlencoded",
        alias: Optional[str] = None,
        alias_priority: Union[int, None] = _Unset,
        # TODO: update when deprecating Pydantic v1, import these types
        # validation_alias: str | AliasPath | AliasChoices | None
        validation_alias: Union[str, None] = None,
        serialization_alias: Union[str, None] = None,
        title: Optional[str] = None,
        description: Optional[str] = None,
        gt: Optional[float] = None,
        ge: Optional[float] = None,
        lt: Optional[float] = None,
        le: Optional[float] = None,
        min_length: Optional[int] = None,
        max_length: Optional[int] = None,
        pattern: Optional[str] = None,
        regex: Annotated[
            Optional[str],
            deprecated(
                "Deprecated in FastAPI 0.100.0 and Pydantic v2, use `pattern` instead."
            ),
        ] = None,
        discriminator: Union[str, None] = None,
        strict: Union[bool, None] = _Unset,
        multiple_of: Union[float, None] = _Unset,
        allow_inf_nan: Union[bool, None] = _Unset,
        max_digits: Union[int, None] = _Unset,
        decimal_places: Union[int, None] = _Unset,
        examples: Optional[List[Any]] = None,
        example: Annotated[
            Optional[Any],
            deprecated(
                "Deprecated in OpenAPI 3.1.0 that now uses JSON Schema 2020-12, "
                "although still supported. Use examples instead."
            ),
        ] = _Unset,
        openapi_examples: Optional[Dict[str, Example]] = None,
        deprecated: Union[deprecated, str, bool, None] = None,
        include_in_schema: bool = True,
        json_schema_extra: Union[Dict[str, Any], None] = None,
        **extra: Any,
    ):
        super().__init__(
            default=default,
            default_factory=default_factory,
            annotation=annotation,
            media_type=media_type,
            alias=alias,
            alias_priority=alias_priority,
            validation_alias=validation_alias,
            serialization_alias=serialization_alias,
            title=title,
            description=description,
            gt=gt,
            ge=ge,
            lt=lt,
            le=le,
            min_length=min_length,
            max_length=max_length,
            pattern=pattern,
            regex=regex,
            discriminator=discriminator,
            strict=strict,
            multiple_of=multiple_of,
            allow_inf_nan=allow_inf_nan,
            max_digits=max_digits,
            decimal_places=decimal_places,
            deprecated=deprecated,
            example=example,
            examples=examples,
            openapi_examples=openapi_examples,
            include_in_schema=include_in_schema,
            json_schema_extra=json_schema_extra,
            **extra,
        )


class File(Form):
    def __init__(
        self,
        default: Any = Undefined,
        *,
        default_factory: Union[Callable[[], Any], None] = _Unset,
        annotation: Optional[Any] = None,
        media_type: str = "multipart/form-data",
        alias: Optional[str] = None,
        alias_priority: Union[int, None] = _Unset,
        # TODO: update when deprecating Pydantic v1, import these types
        # validation_alias: str | AliasPath | AliasChoices | None
        validation_alias: Union[str, None] = None,
        serialization_alias: Union[str, None] = None,
        title: Optional[str] = None,
        description: Optional[str] = None,
        gt: Optional[float] = None,
        ge: Optional[float] = None,
        lt: Optional[float] = None,
        le: Optional[float] = None,
        min_length: Optional[int] = None,
        max_length: Optional[int] = None,
        pattern: Optional[str] = None,
        regex: Annotated[
            Optional[str],
            deprecated(
                "Deprecated in FastAPI 0.100.0 and Pydantic v2, use `pattern` instead."
            ),
        ] = None,
        discriminator: Union[str, None] = None,
        strict: Union[bool, None] = _Unset,
        multiple_of: Union[float, None] = _Unset,
        allow_inf_nan: Union[bool, None] = _Unset,
        max_digits: Union[int, None] = _Unset,
        decimal_places: Union[int, None] = _Unset,
        examples: Optional[List[Any]] = None,
        example: Annotated[
            Optional[Any],
            deprecated(
                "Deprecated in OpenAPI 3.1.0 that now uses JSON Schema 2020-12, "
                "although still supported. Use examples instead."
            ),
        ] = _Unset,
        openapi_examples: Optional[Dict[str, Example]] = None,
        deprecated: Union[deprecated, str, bool, None] = None,
        include_in_schema: bool = True,
        json_schema_extra: Union[Dict[str, Any], None] = None,
        **extra: Any,
    ):
        super().__init__(
            default=default,
            default_factory=default_factory,
            annotation=annotation,
            media_type=media_type,
            alias=alias,
            alias_priority=alias_priority,
            validation_alias=validation_alias,
            serialization_alias=serialization_alias,
            title=title,
            description=description,
            gt=gt,
            ge=ge,
            lt=lt,
            le=le,
            min_length=min_length,
            max_length=max_length,
            pattern=pattern,
            regex=regex,
            discriminator=discriminator,
            strict=strict,
            multiple_of=multiple_of,
            allow_inf_nan=allow_inf_nan,
            max_digits=max_digits,
            decimal_places=decimal_places,
            deprecated=deprecated,
            example=example,
            examples=examples,
            openapi_examples=openapi_examples,
            include_in_schema=include_in_schema,
            json_schema_extra=json_schema_extra,
            **extra,
        )
```
改変後の内容はコミットにて示されている.

## クイックスタート

Dockerイメージは以下のようにしてダウンロードする.

```
docker pull mitsukikawaguchi/2024-k2210190-fastapi
```

ダウンロードしたのちに、次のコマンドでfastapiをimportできるようにすること.

```
pip install "fastapi[all]"
```

後はローカル環境で評価手順に沿って実行する。
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
現状では以下が原因でバグの修正には至っていない
* 改変する必要がある(と思われる)部分がよくわかっていない
* プログラミングだけではなくAPIなど様々なことを理解していることが必要

時間に余裕があれば、今回取り上げたバグ以外のバグの修正を行いよりよいFastAPIを作成したい

