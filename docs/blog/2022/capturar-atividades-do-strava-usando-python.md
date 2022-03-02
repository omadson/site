---
description: Um tutorial simples de como capturar atividades realizadas no Strava usando a biblioteca stravalib, do python.
tags:
  - tutorial
  - python
  - API
template: blog.html
---

# Atividades do Strava usando Python

!!! abstract "TLDR"

    Um tutorial simples de como capturar atividades realizadas no Strava usando a biblioteca `stravalib`, do python.

Esse ano eu resolvi tentar capturar todas as atividades que faço. Tudo isso, para que no fim do ano eu possa fazer análises mais acuradas sobre onde eu gastei meu tempo. Uma das coisas que eu faço com bastante frequência é pedalar. Sempre que pedalo, uso o [Strava](https://www.strava.com/) pra guardar a minha atividade. Pesquisei e vi que o Strava tem uma [API](https://developers.strava.com/). O usuário do github [hozn](https://github.com/hozn) criou a [stravalib](https://github.com/hozn/stravalib), então eu resolvi postar aqui um breve tutorial de como fazer isso.

## 1. Criar um app no Strava

Vá em [https://www.strava.com/settings/api](https://www.strava.com/settings/api) e crie uma nova aplicação. Coloque `localhost` no campo _Authorization Callback Domain_.

Irá aparecer o seu _Client ID_ e o seu _Client secret_, copie para algum lugar, você irá precisar deles nos próximos passos.

## 2. Instalar o pacote `stravalib`

Abra o terminal e digite o comando:

```
pip3 install stravalib
```

## 3. Encontrar o seu access token e autorizar o app

Com o terminal aberto, entre no python3 e digite o seguinte código:

```Python
from stravalib.client import Client


client = Client()
print(
    client.authorization_url(
        client_id=CLIENT_ID,
        redirect_uri='http://localhost:8000/authorized'
    )
)
```

O `CLIENT_ID` é o mesmo que você conseguiu no passo anterior.


Copie a URL e cole no console. Você deve autorizar a aplicação. Depois disso, copie a variável code da URL.

## 4. Capturar atividades

Depois de autorizado, utilize o seguinte código para capturar as atividades do dia atual:

```Python
from datetime import datetime
from stravalib import Client


client = Client()
access_token = client.exchange_code_for_token(
    client_id=CLIENT_ID,
    client_secret=CLIENT_SECRET,
    code=CODE
)
activities = client.get_activities(after=datetime(2019, 1, 29),  limit=5)
for activity in activities:
    print(
        activity.name,
        activity.moving_time,
        activity.distance
    )
```

E _voilà_.
