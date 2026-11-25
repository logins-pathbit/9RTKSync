# Keycloak de laboratório para os três RTKSync

O Compose inicia um realm SAML em `http://localhost:19557` e registra os painéis de teste do 9RTKSync, OmniRTKSync e LiteLlmRTKSync. A porta do IdP fica vinculada ao loopback.

## Subir o IdP

Na raiz do 9RTKSync, defina a senha descartável das contas do laboratório e suba o Keycloak.

```sh
export SAML_EXAMPLE_PASSWORD="$(openssl rand -hex 18)"
docker compose -f examples/saml-keycloak/compose.yml up -d
```

No repositório de cada sincronizador, configure as variáveis exigidas pelo seu Compose de teste. Suba o painel com o extra SAML.

```sh
ENABLE_SAML=1 docker compose -f docker-compose.test.yml up -d --build
```

## Configurar o painel

Abra o endereço do painel e entre com a credencial administrativa de teste ou de recuperação.

| Sincronizador | URL base |
| --- | --- |
| 9RTKSync | `http://127.0.0.1:19091` |
| OmniRTKSync | `http://127.0.0.1:19092` |
| LiteLlmRTKSync | `http://127.0.0.1:9093` |

Na configuração de acesso federado, habilite SAML e informe a URL base e o e-mail permitido `operador@example.test`. Use `http://localhost:19557/realms/rtksync-lab` como Entity ID do IdP e `http://localhost:19557/realms/rtksync-lab/protocol/saml` como URL de SSO. Copie o certificado X.509 de assinatura do [descritor do realm](http://localhost:19557/realms/rtksync-lab/protocol/saml/descriptor). Valide a configuração e salve com a senha atual do painel.

Abra uma sessão privada do navegador e entre pela opção SAML. A conta `operador` deve abrir o painel. A conta `visitante` deve autenticar no Keycloak e receber a mensagem genérica de recusa no painel. Ambas usam a senha descartável definida no início.

```sh
docker compose -f examples/saml-keycloak/compose.yml down
unset SAML_EXAMPLE_PASSWORD
```
