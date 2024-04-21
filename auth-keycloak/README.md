# 実践Keycloak
「実践Keycloak」を読みながらOpen ID Connect, OAuth2.0について学習する

# Keycloakのローカル起動
```bash
docker run -p 8081:8080 -e KEYCLOAK_ADMIN=admin -e KEYCLOAK_ADMIN_PASSWORD=admin quay.io/keycloak/keycloak:24.0.3 start-dev
```
https://www.keycloak.org/getting-started/getting-started-docker

# Realm
- テナントみたいなもの
- Realmの原義は「王国（の領地）、領内」
- Keycloakにadminユーザーでログインしたら、まずRealmを作って、その中でユーザーやロールを作成する