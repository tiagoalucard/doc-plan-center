
![Logo](https://midiasimples.com.br/wp-content/uploads/2020/03/midia-simples.png)


# API de integração do Plan Center

Api de integração para consultar planos,
e cadastrar leads no plan center

## Índice

  - [Lead](#LEAD)
    - [Criar lead](#criar-lead)
    - [Editar Lead](#editar-lead)
  - [Planos](#Planos)
    - [Consultar Planos](#consultar-planos)
    - [Consultar CEP](#consultar-cep)
  - [Horários de Atendimento](#horários-de-atendimento)
  - [Planos disponíveis para upgrade](#planos-disponíveis-para-upgrade)
  - [Dúvidas](#Dúvidas)
  - [Contato](#contato)
  - [Links Uteis](#links-uteis)

## LEAD
Para a rota de leads, é indicado que as requisições
sejam feitas em dois momentos:

- A primeira vez, quando as informações básicas do lead são coletadas (nome, celular)
- Quando todo o fluxo de captura do lead for completado (com todas as informações possíveis).

#### Criar Lead

```http
  POST /lead
```

| Parameter     | Type     | Description                |
| :--------     | :------- | :------------------------- |
| `name`        | `string` | **Obrigatório** Nome do Lead Capturado |
| `celphone`    | `array` | **Obrigatório**. Celular do lead (com DDD)|
| `lead_origin` | `string` | **Obrigatório**. Origem da requisição [chatbot, carrinho, app]

## Exemplo - HTTP (200)

##### body da requisição

```json
{
  "name":     "Alberto Teste",
  "celphone": [
      "(67) 99999-9999"
  ],
}
```

#### Resposta

```json
{
  "success": true,
  "message": "Lead cadastrado com sucesso!",
  "lead_id": 4
}
```
---

## Exemplo - HTTP (422)

#### Resposta

```json
{
  "message": "The given data was invalid.",
  "errors": {
    "name": ["O campo Nome é obrigatório."],
    "celphone": ["O campo Celular é obrigatório."],
    "lead_origin": ["O campo Origem do Lead é obrigatório."]
  }
}
```

## Exemplo - HTTP (500)

#### Resposta

```json
{
  "success": false,
  "message": "Erro ao cadastrar Lead!"
}
```

#### Editar Lead

```http
  PUT /lead{lead_id}
```
Evie sempre o máximo de dados possíveis, *lembrete: Nem todos os campos são obrigatórios*
- Essa rota deve ser consumida após retirar todos os dados do lead.


| Parâmetro                     | Tipo      | Descrição                |
| :--------                     | :-------  | :------------------------- |
| `name`                        | `string`  | **Obrigatório** Nome do Lead Capturado |
| `celphone`                    | `array`  | **Obrigatório**. Celular do lead (com DDD)|
| `lead_origin`                 | `string`  | **Obrigatório**. Origem da requisição [chatbot, carrinho, app]|
| `registration_document`       | `string`  | RG do lead |
| `document`                    | `string`  | CPF ou CNPJ |
| `phone`                       | `array`  | Número de telefone (com DDD)|
| `email`                       | `string`  | email |
| `zip_code`                    | `string`  | CEP |
| `address`                     | `string`  | Endereço|
| `address_number`              | `string`  | Número da casa |
| `neighborhood`                | `string`  | Bairro |
| `address_type`                | `string`  | Tipo do endereço [casa, condomínio de casas, condomnínio de prédio] |
| `complementary`               | `string`  | complemento |
| `city`                        | `string`  | Cidade |
| `state`                       | `string`  | Estado [SP, MS, MG] |
| `service_type`                | `string`  | Tipo de serviço [internet, combos, movel]
| `notes`                       | `text`    | Notas de observação
| `date_of_birthday`            | `date`    | Data de nascimento [Y-m-d]
| `plan_name`                   | `string`  | Nome do plano selecionado
| `plan_price`                  | `float`   | Valor do plano selecionado
| `installation_fee`            | `float`   | Valor da taxa de instalação
| `installation_installaments`  | `integer` | Quantidade de parcelas da taxa de instalação
| `due_date`                    | `integer` | Dia do vencimento
| `plans_type`                  | `string`  | Tipo de Planos [ PF, PJ]
| `is_upgrade`                  | `boolean` | Caso seja um lead de upgrade |
| `contact_date`                | `string`  | Caso esteja fora do horário de atendimento, adicionar a data e hora para entrar em contato [16/02/22 Tarde]

## Exemplo - HTTP (200)

#### Resposta
```json
{
  "success": true,
  "message": "Lead atualizado com sucesso"
}
```

## Exemplo - HTTP (422)

#### Resposta

```json
{
  "message": "The given data was invalid.",
  "errors": {
    "name": ["O campo Nome é obrigatório."],
    "celphone": ["O campo Celular é obrigatório."],
    "lead_origin": ["O campo Origem do Lead é obrigatório."]
  }
}
```

## Exemplo - HTTP (500)

#### Resposta

```json
{
  "success": false,
  "message": "Erro ao atualizar o lead!"
}
```

----

## Planos
Rota utilizada para consultar planos disponíveis para contratação do usuário

#### Consultar Planos
 Na consulta de planos nenhum dos parametros são obrigatórios, porém cabe utiliza-los para melhor informar o cliente dos planos disponíveis.
```http
  GET /plans
```

| Parâmetro                     | Tipo      | Descrição                |
| :--------                     | :-------  | :------------------------- |
| `city`                        | `string`  | Nome ou slug (_) da cidade do usuário |
| `is_cpf`                      | `boolean` | True para consultar planons de Pessoa Física|
| `order`                       | `string`  | Nome do campo, dentro do array de planos, no qual a consulta será ordenada.
| `direction`                   | `string`  | [DESC, ASC], caso tenha o parametro a cima, também é possível escolher o tipo de ordenação
| `cheaper`                     | `float`   | Consulta planos mais baratos que tal valor |
| `expensive`                   | `float`   | Consulta planos mais caros que tal valor |
| `fields`                      | `string`  | Nome dos campos que deseja separados por vírgula, ex: plan,slug,price |
| `limit`                       | `int`     | Quantidade de planos máximo a ser retornado |
| `only_chip`                   | `boolean` | Passe true para consultar apenas planos móveis |
| `only_combo`                  | `boolean` | Passe true para consultar apenas planos combo |
| `only_internet`               | `boolean` | Passe true para consultar apenas planos dentro internet|
## Exemplo - HTTP (200)

#### Resposta
<details>
    <summary>Exemplo de resposta</summary>

  ```json
  {
    "success": true,
    "data": {
      "plan": [
        {
          "id": 1,
          "plan": "Combo MEI Mobilidade",
          "slug": "internet_fibra_otica_200_mega__mais_1_chip",
          "upload": 100,
          "download": 200,
          "plan_description": "Resumo do seu Pedido\n\nCOMBO MEI MOBILIDADE \nEsse combo acompanha: \n\nINTERNET FIBRA ÓPTICA 200 MEGA \n☑️ 200 Mega de Download\n☑️ 100 Mega de Upload\n☑️ Wifi Gigabit\n☑️ 12 meses de Fidelidade\n\n1 CHIP DE 10 GB FALE À VONTADE \n☑️ Ligações Para Fixo e Móvel Brasil ILIMITADO\n☑️ Velocidade 4G + 10GB Franquia de Dados por chip\n☑️ SMS ilimitado\n☑️ Portabilidade numérica disponível\n\nFIT RESOLVE Assistência Técnica Avançada\nSuporte para ajudar resolver problemas de conexão no seu:\n☑️ SmartPhone\n☑️ Smart TV\n☑️ Computador\n☑️ Câmeras de monitoramento\n☑️ Alarme\n\nAntivírus - Mcafee\nProteção avançada para seus dispositivos para poder navegar com segurança:\n☑️ 1 dispositivo\n*No valor do plano não está incluso o valor do chip que é de R$15,00",
          "internet_description": "Internet de 200 Mega",
          "has_chip": 1,
          "chip_description": "1 chip de 10GB fale à vontade",
          "qty_chip": null,
          "has_fixo": 0,
          "fixo_description": "",
          "qty_fixo": null,
          "is_able_to_upgrade": 1,
          "is_cpf": 0,
          "instalattion_fee_no_debts": "180.00",
          "instalattion_fee_with_debts": "180.00",
          "installment_qty": 12,
          "price": "164.70",
          "is_promotional": 0,
          "created_at": "2022-01-31T18:27:51.000000Z",
          "updated_at": "2022-01-31T18:27:51.000000Z",
          "svas": [
            {
              "id": 1,
              "sva": "mcafee",
              "site": null,
              "logo": null,
              "created_at": "2022-01-31T18:27:51.000000Z",
              "updated_at": "2022-01-31T18:27:51.000000Z",
              "pivot": {
                "plan_id": 1,
                "sva_id": 1,
                "cluster_id": 1
              }
            },
            {
              "id": 2,
              "sva": "wifiturbo",
              "site": null,
              "logo": null,
              "created_at": "2022-01-31T18:27:51.000000Z",
              "updated_at": "2022-01-31T18:27:51.000000Z",
              "pivot": {
                "plan_id": 1,
                "sva_id": 2,
                "cluster_id": 1
              }
            }
          ],
          "disclaimer": [
            {
              "id": 1,
              "disclaimers": "*Contrato de Internet Fixa de 200 Mega de download",
              "created_at": "2022-01-31T18:27:51.000000Z",
              "updated_at": "2022-01-31T18:27:51.000000Z",
              "pivot": {
                "plan_id": 1,
                "disclaimer_id": 1
              }
            },
            {
              "id": 2,
              "disclaimers": "Oferta Válida para contrato de 12 meses;",
              "created_at": "2022-01-31T18:27:51.000000Z",
              "updated_at": "2022-01-31T18:27:51.000000Z",
              "pivot": {
                "plan_id": 1,
                "disclaimer_id": 2
              }
            },
            {
              "id": 3,
              "disclaimers": "Valor da instalação é de R$180,00 parcelado em até 12x;",
              "created_at": "2022-01-31T18:27:51.000000Z",
              "updated_at": "2022-01-31T18:27:51.000000Z",
              "pivot": {
                "plan_id": 1,
                "disclaimer_id": 3
              }
            },
            {
              "id": 4,
              "disclaimers": "O valor do chip é de R$ 15,00 cada;",
              "created_at": "2022-01-31T18:27:51.000000Z",
              "updated_at": "2022-01-31T18:27:51.000000Z",
              "pivot": {
                "plan_id": 1,
                "disclaimer_id": 4
              }
            },
            {
              "id": 5,
              "disclaimers": "Prazo de instalação são de até 30 dias;",
              "created_at": "2022-01-31T18:27:51.000000Z",
              "updated_at": "2022-01-31T18:27:51.000000Z",
              "pivot": {
                "plan_id": 1,
                "disclaimer_id": 5
              }
            },
            {
              "id": 6,
              "disclaimers": "Consulte a cobertura da sua região;",
              "created_at": "2022-01-31T18:27:51.000000Z",
              "updated_at": "2022-01-31T18:27:51.000000Z",
              "pivot": {
                "plan_id": 1,
                "disclaimer_id": 6
              }
            },
            {
              "id": 7,
              "disclaimers": "Valores sujeito à alteração sem aviso prévio;",
              "created_at": "2022-01-31T18:27:51.000000Z",
              "updated_at": "2022-01-31T18:27:51.000000Z",
              "pivot": {
                "plan_id": 1,
                "disclaimer_id": 7
              }
            }
          ],
          "observation": [
            {
              "id": 1,
              "observation": "No valor do plano não está incluso o valor do chip que é de R$15,00",
              "created_at": "2022-01-31T18:27:51.000000Z",
              "updated_at": "2022-01-31T18:27:51.000000Z",
              "pivot": {
                "plan_id": 1,
                "observation_id": 1
              }
            }
          ],
          "slugs_aliases": []
        },
        {
          "id": 2,
          "plan": "Combo MEI Mobilidade+",
          "slug": "internet_fibra_otica_200_mega__mais_2_chip",
          "upload": 100,
          "download": 200,
          "plan_description": "Resumo do seu Pedido\n\nCOMBO MEI MOBILIDADE+ \nEsse combo acompanha: \n\nINTERNET FIBRA ÓPTICA 200 MEGA \n☑️ 200 Mega de Download\n☑️ 100 Mega de Upload\n☑️ Wifi Gigabit\n☑️ 12 meses de Fidelidade\n\n2 CHIPS DE 10 GB CADA FALE À VONTADE \n☑️ Ligações Para Fixo e Móvel Brasil ILIMITADO\n☑️ Velocidade 4G + 10 GB Franquia de Dados por chip\n☑️ SMS ilimitado\n☑️ Portabilidade numérica disponível\n\nFIT RESOLVE Assistência Técnica Avançada\nSuporte para ajudar resolver problemas de conexão no seu:\n☑️ SmartPhone\n☑️ Smart TV\n☑️ Computador\n☑️ Câmeras de monitoramento\n☑️ Alarme\n\nAntivírus - Mcafee\nProteção avançada para seus dispositivos para poder navegar com segurança:\n☑️ 1 dispositivo\n*No valor do plano não está incluso o valor do chip que é de R$15,00",
          "internet_description": "Internet de 200 Mega",
          "has_chip": 1,
          "chip_description": "2 chips de 10 GB cada fale à vontade",
          "qty_chip": null,
          "has_fixo": 0,
          "fixo_description": "",
          "qty_fixo": null,
          "is_able_to_upgrade": 1,
          "is_cpf": 0,
          "instalattion_fee_no_debts": "180.00",
          "instalattion_fee_with_debts": "180.00",
          "installment_qty": 12,
          "price": "194.60",
          "is_promotional": 0,
          "created_at": "2022-01-31T18:27:51.000000Z",
          "updated_at": "2022-01-31T18:27:51.000000Z",
          "svas": [
            {
              "id": 1,
              "sva": "mcafee",
              "site": null,
              "logo": null,
              "created_at": "2022-01-31T18:27:51.000000Z",
              "updated_at": "2022-01-31T18:27:51.000000Z",
              "pivot": {
                "plan_id": 2,
                "sva_id": 1,
                "cluster_id": 1
              }
            },
            {
              "id": 2,
              "sva": "wifiturbo",
              "site": null,
              "logo": null,
              "created_at": "2022-01-31T18:27:51.000000Z",
              "updated_at": "2022-01-31T18:27:51.000000Z",
              "pivot": {
                "plan_id": 2,
                "sva_id": 2,
                "cluster_id": 1
              }
            }
          ],
          "disclaimer": [
            {
              "id": 1,
              "disclaimers": "*Contrato de Internet Fixa de 200 Mega de download",
              "created_at": "2022-01-31T18:27:51.000000Z",
              "updated_at": "2022-01-31T18:27:51.000000Z",
              "pivot": {
                "plan_id": 2,
                "disclaimer_id": 1
              }
            },
            {
              "id": 2,
              "disclaimers": "Oferta Válida para contrato de 12 meses;",
              "created_at": "2022-01-31T18:27:51.000000Z",
              "updated_at": "2022-01-31T18:27:51.000000Z",
              "pivot": {
                "plan_id": 2,
                "disclaimer_id": 2
              }
            },
            {
              "id": 3,
              "disclaimers": "Valor da instalação é de R$180,00 parcelado em até 12x;",
              "created_at": "2022-01-31T18:27:51.000000Z",
              "updated_at": "2022-01-31T18:27:51.000000Z",
              "pivot": {
                "plan_id": 2,
                "disclaimer_id": 3
              }
            },
            {
              "id": 4,
              "disclaimers": "O valor do chip é de R$ 15,00 cada;",
              "created_at": "2022-01-31T18:27:51.000000Z",
              "updated_at": "2022-01-31T18:27:51.000000Z",
              "pivot": {
                "plan_id": 2,
                "disclaimer_id": 4
              }
            },
            {
              "id": 5,
              "disclaimers": "Prazo de instalação são de até 30 dias;",
              "created_at": "2022-01-31T18:27:51.000000Z",
              "updated_at": "2022-01-31T18:27:51.000000Z",
              "pivot": {
                "plan_id": 2,
                "disclaimer_id": 5
              }
            },
            {
              "id": 6,
              "disclaimers": "Consulte a cobertura da sua região;",
              "created_at": "2022-01-31T18:27:51.000000Z",
              "updated_at": "2022-01-31T18:27:51.000000Z",
              "pivot": {
                "plan_id": 2,
                "disclaimer_id": 6
              }
            },
            {
              "id": 7,
              "disclaimers": "Valores sujeito à alteração sem aviso prévio;",
              "created_at": "2022-01-31T18:27:51.000000Z",
              "updated_at": "2022-01-31T18:27:51.000000Z",
              "pivot": {
                "plan_id": 2,
                "disclaimer_id": 7
              }
            }
          ],
          "observation": [
            {
              "id": 1,
              "observation": "No valor do plano não está incluso o valor do chip que é de R$15,00",
              "created_at": "2022-01-31T18:27:51.000000Z",
              "updated_at": "2022-01-31T18:27:51.000000Z",
              "pivot": {
                "plan_id": 2,
                "observation_id": 1
              }
            }
          ],
          "slugs_aliases": []
        }
      ],
      "count": 2
    }
  }
  ```
</details>

## Exemplo - HTTP (500)

#### Resposta
```json
{
  "success": false,
  "message": "Houve uma falha ao consultar os planos"
}
```

---
---

## Consultar CEP
```http
GET /CEP
```
| Parâmetro | Tipo      | Descrição                |
| :-------- | :-------  | :------------------------- |
| `cep`     | `string`  | CEP do usuário [16052-805] |

## Exemplo - HTTP (200)

#### Resposta
```json
{
  "success": true,
  "data": {
    "city": "Araçatuba",
    "district": "Aeroporto",
    "state": "SP",
    "street": "Via Aguinaldo Fernando dos Santos",
    "zipCode": "16052805"
  }
}
```

## Exemplo - HTTP (422)

#### Resposta
```json
{
  "message": "The given data was invalid.",
  "errors": {
    "cep": ["O campo cep não possui um formato válido de CEP."]
  }
}
```

## Exemplo - HTTP (500)

##### Resposta
```json
{
  "success": false,
  "message": "Todos os serviços de CEP retornaram erro."
}
```

---

## Horários de Atendimento
Essa rota consulta quais os horários em que os nossos atendentes, estão em serviço.
Caso não estivermos em serviço, retorna as próximas datas para atendimento.

```http
GET /hours
```

## Exemplo - HTTP (200)

#### Resposta em horário de atendimento
```json
{
  "success": true,
  "data": {
    "day": "Segunda-Feira",
    "is_open": true
  }
}
```

#### Resposta fora do horário de atendimento.
```json
{
  "success": true,
  "data": {
    "day": "Domingo",
    "is_open": false,
    "next_days": [
      {
        "date": "14/02/2022",
        "hours": ["morning", "afternoon"]
      },
      {
        "date": "15/02/2022",
        "hours": ["morning", "afternoon"]
      },
      {
        "date": "16/02/2022",
        "hours": ["morning", "afternoon"]
      }
    ]
  }
}
```

---

## Planos disponíveis para upgrade
Nessa rota é possível ver todos os planos ativos do
cliente, e quais os planos possíveis e upgrade pra cada plano ativo.

```http
GET /upgrade
```

| Parâmetro | Tipo      | Descrição                |
| :-------- | :-------  | :------------------------- |
| `document`| `string`  | Documento do usuário, CPF ou CNPJ |
| `limit`   | `int`     | Limite de planos a serem retornados|
| `fields`  | `string`  | Nome dos campos que deseja separados por vírgula, ex: plan,slug,price |

## Exemplo - HTTP (200)

#### Resposta em horário de atendimento
<details>
  <summary>Exemplo de resposta</summary>

```json
{
  "success": true,
  "data": [
    {
      "planName": "FIT | CORTESIA FUNCIONARIO + FIT FONE POS PAGO",
      "planPrice": 89.9,
      "plansAvailable": {
        "plan": [
          {
            "id": 1,
            "plan": "Combo MEI Mobilidade",
            "slug": "internet_fibra_otica_200_mega__mais_1_chip",
            "upload": 100,
            "download": 200,
            "plan_description": "Resumo do seu Pedido\n\nCOMBO MEI MOBILIDADE \nEsse combo acompanha: \n\nINTERNET FIBRA ÓPTICA 200 MEGA \n☑️ 200 Mega de Download\n☑️ 100 Mega de Upload\n☑️ Wifi Gigabit\n☑️ 12 meses de Fidelidade\n\n1 CHIP DE 10 GB FALE À VONTADE \n☑️ Ligações Para Fixo e Móvel Brasil ILIMITADO\n☑️ Velocidade 4G + 10GB Franquia de Dados por chip\n☑️ SMS ilimitado\n☑️ Portabilidade numérica disponível\n\nFIT RESOLVE Assistência Técnica Avançada\nSuporte para ajudar resolver problemas de conexão no seu:\n☑️ SmartPhone\n☑️ Smart TV\n☑️ Computador\n☑️ Câmeras de monitoramento\n☑️ Alarme\n\nAntivírus - Mcafee\nProteção avançada para seus dispositivos para poder navegar com segurança:\n☑️ 1 dispositivo\n*No valor do plano não está incluso o valor do chip que é de R$15,00",
            "internet_description": "Internet de 200 Mega",
            "has_chip": 1,
            "chip_description": "1 chip de 10GB fale à vontade",
            "qty_chip": null,
            "has_fixo": 0,
            "fixo_description": "",
            "qty_fixo": null,
            "is_able_to_upgrade": 1,
            "is_cpf": 0,
            "instalattion_fee_no_debts": "180.00",
            "instalattion_fee_with_debts": "180.00",
            "installment_qty": 12,
            "price": "164.70",
            "is_promotional": 0,
            "created_at": "2022-01-31T18:27:51.000000Z",
            "updated_at": "2022-01-31T18:27:51.000000Z",
            "svas": [
              {
                "id": 1,
                "sva": "mcafee",
                "site": null,
                "logo": null,
                "created_at": "2022-01-31T18:27:51.000000Z",
                "updated_at": "2022-01-31T18:27:51.000000Z",
                "pivot": {
                  "plan_id": 1,
                  "sva_id": 1,
                  "cluster_id": 1
                }
              },
              {
                "id": 2,
                "sva": "wifiturbo",
                "site": null,
                "logo": null,
                "created_at": "2022-01-31T18:27:51.000000Z",
                "updated_at": "2022-01-31T18:27:51.000000Z",
                "pivot": {
                  "plan_id": 1,
                  "sva_id": 2,
                  "cluster_id": 1
                }
              }
            ],
            "disclaimer": [
              {
                "id": 1,
                "disclaimers": "*Contrato de Internet Fixa de 200 Mega de download",
                "created_at": "2022-01-31T18:27:51.000000Z",
                "updated_at": "2022-01-31T18:27:51.000000Z",
                "pivot": {
                  "plan_id": 1,
                  "disclaimer_id": 1
                }
              }
            ],
            "observation": [
              {
                "id": 1,
                "observation": "No valor do plano não está incluso o valor do chip que é de R$15,00",
                "created_at": "2022-01-31T18:27:51.000000Z",
                "updated_at": "2022-01-31T18:27:51.000000Z",
                "pivot": {
                  "plan_id": 1,
                  "observation_id": 1
                }
              }
            ],
            "slugs_aliases": []
          }
        ],
        "count": 3
      }
    },
    {
      "planName": "BANDA LARGA 300MB - PARCEIROS",
      "planPrice": 89.9,
      "plansAvailable": {
        "plan": [
          {
            "id": 1,
            "plan": "Combo MEI Mobilidade",
            "slug": "internet_fibra_otica_200_mega__mais_1_chip",
            "upload": 100,
            "download": 200,
            "plan_description": "Resumo do seu Pedido\n\nCOMBO MEI MOBILIDADE \nEsse combo acompanha: \n\nINTERNET FIBRA ÓPTICA 200 MEGA \n☑️ 200 Mega de Download\n☑️ 100 Mega de Upload\n☑️ Wifi Gigabit\n☑️ 12 meses de Fidelidade\n\n1 CHIP DE 10 GB FALE À VONTADE \n☑️ Ligações Para Fixo e Móvel Brasil ILIMITADO\n☑️ Velocidade 4G + 10GB Franquia de Dados por chip\n☑️ SMS ilimitado\n☑️ Portabilidade numérica disponível\n\nFIT RESOLVE Assistência Técnica Avançada\nSuporte para ajudar resolver problemas de conexão no seu:\n☑️ SmartPhone\n☑️ Smart TV\n☑️ Computador\n☑️ Câmeras de monitoramento\n☑️ Alarme\n\nAntivírus - Mcafee\nProteção avançada para seus dispositivos para poder navegar com segurança:\n☑️ 1 dispositivo\n*No valor do plano não está incluso o valor do chip que é de R$15,00",
            "internet_description": "Internet de 200 Mega",
            "has_chip": 1,
            "chip_description": "1 chip de 10GB fale à vontade",
            "qty_chip": null,
            "has_fixo": 0,
            "fixo_description": "",
            "qty_fixo": null,
            "is_able_to_upgrade": 1,
            "is_cpf": 0,
            "instalattion_fee_no_debts": "180.00",
            "instalattion_fee_with_debts": "180.00",
            "installment_qty": 12,
            "price": "164.70",
            "is_promotional": 0,
            "created_at": "2022-01-31T18:27:51.000000Z",
            "updated_at": "2022-01-31T18:27:51.000000Z",
            "svas": [
              {
                "id": 1,
                "sva": "mcafee",
                "site": null,
                "logo": null,
                "created_at": "2022-01-31T18:27:51.000000Z",
                "updated_at": "2022-01-31T18:27:51.000000Z",
                "pivot": {
                  "plan_id": 1,
                  "sva_id": 1,
                  "cluster_id": 1
                }
              },
              {
                "id": 2,
                "sva": "wifiturbo",
                "site": null,
                "logo": null,
                "created_at": "2022-01-31T18:27:51.000000Z",
                "updated_at": "2022-01-31T18:27:51.000000Z",
                "pivot": {
                  "plan_id": 1,
                  "sva_id": 2,
                  "cluster_id": 1
                }
              }
            ],
            "disclaimer": [
              {
                "id": 1,
                "disclaimers": "*Contrato de Internet Fixa de 200 Mega de download",
                "created_at": "2022-01-31T18:27:51.000000Z",
                "updated_at": "2022-01-31T18:27:51.000000Z",
                "pivot": {
                  "plan_id": 1,
                  "disclaimer_id": 1
                }
              }
            ],
            "observation": [
              {
                "id": 1,
                "observation": "No valor do plano não está incluso o valor do chip que é de R$15,00",
                "created_at": "2022-01-31T18:27:51.000000Z",
                "updated_at": "2022-01-31T18:27:51.000000Z",
                "pivot": {
                  "plan_id": 1,
                  "observation_id": 1
                }
              }
            ],
            "slugs_aliases": []
          }
        ],
        "count": 3
      }
    }
  ]
}
```
</details>


### Contato
Entre em contato por email ```samuel_lujan@hotmail.com```

### Links Uteis

- [Link para a collection no postman](https://www.getpostman.com/collections/d499fe8d7b2fb162f196)
