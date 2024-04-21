# 実践Keycloak
「実践Keycloak」を読みながらOpen ID Connect, OAuth2.0について学習する

# Chapter 1
### Keycloakのローカル起動
```bash
docker run -p 8081:8080 -e KEYCLOAK_ADMIN=admin -e KEYCLOAK_ADMIN_PASSWORD=admin quay.io/keycloak/keycloak:24.0.3 start-dev
```
https://www.keycloak.org/getting-started/getting-started-docker

### Realm
- テナントみたいなもの
- Realmの原義は「王国（の領地）、領内」
- Keycloakにadminユーザーでログインしたら、まずRealmを作って、その中でユーザーやロールを作成する

# Chapter 2
### サンプルアプリの起動
- [サンプルアプリのソース](https://github.com/Keycloak-IAM-4-Modern-Apps-JP/Keycloak-Identity-and-Access-Management-for-Modern-Applications/tree/main)
- ch2/frontend, ch2/backendともに`npm install` -> `npm start`で起動。frontendはport 8000で起動する
- Loginボタン押下しても認証に失敗する。KeycloakにてClient登録する必要がある。
- IDトークンの中身がこちら
```json
{
  "exp": 1713694647, // トークン有効期限
  "iat": 1713694347,
  "auth_time": 1713694347,
  "jti": "0f469d00-c3a7-4024-bd4b-7a02aa71872e",
  "iss": "http://localhost:8080/realms/myrealm",//トークン発行者。KeycloakのRealmのURL。
  "aud": "myclient",
  "sub": "363989f9-fc61-4504-8655-67e687e8e7e2",//認証されたユーザーの固有の識別子
  "typ": "ID",
  "azp": "myclient",
  "nonce": "cbcd815f-2b81-4e0b-80af-89ab1cc0da14",
  "session_state": "55ef017c-add3-4a71-9fea-b7167d2a5dd4",
  "at_hash": "VtwF8wtB_-XHK3ZffhwMUQ",
  "acr": "1",
  "sid": "55ef017c-add3-4a71-9fea-b7167d2a5dd4",
  "email_verified": false,
  "name": "Taro Yamada",
  "preferred_username": "tol",
  "given_name": "Taro",
  "family_name": "Yamada",
  "email": "tttol.t4k.b1z@gmail.com"
}
```
- アクセストークンの中身がこちら
```json
{
  "exp": 1713694647,
  "iat": 1713694347,
  "auth_time": 1713694347,
  "jti": "f6a5bbed-abe4-4581-8f74-24ab1b44e1c4",
  "iss": "http://localhost:8080/realms/myrealm",
  "aud": "account",
  "sub": "363989f9-fc61-4504-8655-67e687e8e7e2",
  "typ": "Bearer",
  "azp": "myclient",
  "nonce": "cbcd815f-2b81-4e0b-80af-89ab1cc0da14",
  "session_state": "55ef017c-add3-4a71-9fea-b7167d2a5dd4",
  "acr": "1",
  "allowed-origins": [//CORSリクエストに対して許可するOrigin
    "http://localhost:8000"
  ],
  "realm_access": {
    "roles": [
      "default-roles-myrealm",
      "offline_access",
      "uma_authorization"
    ]
  },
  "resource_access": {
    "account": {
      "roles": [
        "manage-account",
        "manage-account-links",
        "view-profile"
      ]
    }
  },
  "scope": "openid profile email",
  "sid": "55ef017c-add3-4a71-9fea-b7167d2a5dd4",
  "email_verified": false,
  "name": "Taro Yamada",
  "preferred_username": "tol",
  "given_name": "Taro",
  "family_name": "Yamada",
  "email": "a@a.a"
}
```

# Chapter 3
### 用語
- Open Id Provider(OP): ID発行者。今回はKeycloak。
- Relying Party(RP): エンドユーザーを認証したいアプリケーションのこと. OPにエンドユーザーのアイデンティティを要求する。
