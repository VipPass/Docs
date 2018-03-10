# VIP Pass Card - Roadpass integration

## Este documento tem como objetivo descrever o que seria necessário retornar da API Roadpass para o correto funcionamento do nosso sistema.
## As nomenclaturas dos métodos foram sugeridas em inglês, mas fica a critério da Roadpass esta definição. Lembrando também que os campos descritos nesta primeira versão são os necessários e também os desejados, ou seja, a Roadpass precisará nos informar quais serão passíveis de retorna em sua API.

### **Exemplo de resposta com status OK**
```json
{
    "status": "ok",
    "data": {
        "dados a serem retornados, sendo estes particulares de cada método."
    }
}
```

### **Exemplo de resposta com status ERROR**
```json
{
    "status": "error",
    "reason_code": "1",
    "message": "Não foi possível retornar os dados da API Mastercard."
}
```


# Operações necessárias 

## Segue abaixo um resumo sobre as operações necessárias.

| Method | HTTP verb | Description |
|:---------|:-------------|----------|
| GlobalBalanceInfo | POST | Solicita informações do saldo da conta MASTER | 
| GlobalCardInfo | POST | Solicita informações sobre os cartões da conta MASTER |
| GlobalStatement | POST | Consulta o extrato / movimentações da conta MASTER | 
| CardAdd | POST | Solicita um novo cartão |
| CardInfo | POST | Informações sobre o cartão |
| CardActivate | POST | Ativa o cartão |
| CardBlock | POST | Bloqueia o cartão |
| CardCancel | POST | Cancelar o cartão |
| CardPinCodeReset | POST | Troca do PIN do cartão |
| CardPinCodeValidate | POST | Valida cartão + PIN |
| CardBalance | POST | Consulta o saldo do cartão |
| CardStatement | POST | Consulta o extrato / movimentações de um cartão |
| CardAddFund | POST | Transfere fundos da conta MASTER para o cartão |
| CardSubFund | POST | Transfere fundos do cartão para a conta MASTER | 
| CardListByPersonId | POST | Retorna os cartões do CPF do usuário  | 
| CardChargeback | POST | Realiza o estorno de uma transação realizada no cartão|

# Catálogo das operações

## **GlobalBalanceInfo - POST**

### Solicita informações do saldo da conta MASTER

### **Input Parameters**

Não possui


### **Output parameters**
```json
{
    "status": "ok",
    "data":{
        "balance": {
            "available": 20000.33,
            "in_cards": 10000.17,
            "pending": 0,
            "total": 30000.50
        }
    }
}
```

### **Data Dictionary**

| Campo | Descrição| Tipo |
|:---------|:-------------|----------|
| available | Total disponível para uso | number |
| in_cards | Total disponível cartões | number |
| pending | Valor pendente de confirmação (Por exemplo, foi realizada uma TED/DOC, porém o banco ainda não creditou este valor) | number |
| total | Soma de todas as informações anteriores (available, in_cards e pending) | number |

___

## **GlobalCardInfo - POST**

### Solicita informações sobre os cartões da conta MASTER

### **Input Parameters**

Não possui


### **Output parameters**
```json
{
    "status": "ok",
    "data":{
        "cards": {
            "actived": 3000,
            "canceled": 2000,
            "blocked_temporary": 1000,
            "blocked_never_used": 200,
            "total": 6700
        }
    }
}
```

### **Data Dictionary**

| Campo | Descrição| Tipo |
|:---------|:-------------|----------|
| active | Total cartões ativos | number |
| canceled | Total de cartões cancelados | number |
| blocked_temporary | Cartões que foram bloqueados pelo usuário | number |
| blocked_never_used | Cartões que foram solicitados e estão pendentes de desbloqueio para uso | number |
| total | Total de cartões já registrados, independente do status | number |

___

## **GlobalStatement - POST**

### Consulta o extrato / movimentações da conta MASTER

### **Input Parameters**

Não possui


### **Output parameters**
```json
{
    "status": "ok",
    "data":[        
            { "id": 1, "type": "External", "date_time": "10/01/2018 01:02:12.0000 GMT -03:00", "description": "TED 798789/12", "amount": 10000.00 },
            { "id": 2, "type": "External", "date_time": "10/01/2018 01:02:12.0000 GMT -03:00", "description": "TED 798789/12", "amount": 10000.00 },
            { "id": 3, "type": "Internal", "date_time": "10/01/2018 01:02:12.0000 GMT -03:00", "description": "CARGA ADD CARTAO 12313212313", "amount": -10000.00 },
            { "id": 4, "type": "Internal", "date_time": "10/01/2018 01:02:12.0000 GMT -03:00", "description": "CARGA EST CARTAO 12313212313", "amount": 10000.00 }
        ]    
}
```

### **Data Dictionary**

| Campo | Descrição| Tipo |
|:---------|:-------------|----------|
| id | Id da transação | string |
| type | Tipo da transação: <ul><li>External</li><li>Internal</li></ul> | string |
| date_time | Data da transação | string |
| description | Descrição da Transação | string |
| amount | Valor da transação | number |

___


## **CardAdd - POST**

### Método responsável por solicitar um novo cartão.

### **Input Parameters**

```json
{
    "name": "Fabio Alexandre Garcia",
    "holder_name": "FABIO A GARCIA",
    "birthday": "1986-07-01",
    "card_type": "P",
    "personId": "XXXXXXXX",
    "address":{
        "name": "Rua Lorem ipsum dolor sit amet.",
        "number": "1010",
        "supplement": "apartamento 1111",
        "neighborhood": "Lorem ipsum dolor",
        "postal_code": "88888888",
        "city": "São Paulo",
        "state": "UF"
    }
}
```
### **Data Dictionary**

| Campo || Descrição| Tipo |
|:-|:---------|:-------------|----------|
| name || Nome do completo | string |
| holder_name || Nome impresso no cartão | string |
| birthday || Data de nascimento do solicitante, no formato YYYY-MM-DD | string |
| card_type || Tipo do cartão, sendo: <ul><li>P: cartão físico (P de phisical)</li><li>V: virtual</li></ul>| string |
| personId || Para usuário do Brasil, este campo representa o CPF | string, enviando somente os números ||
| address || Campos referente ao endereço | object |
| | name | Endereço | string |
| |number | Número do imóvel | string |
| | supplement | Complemento | string |
| | neighborhood | Bairro | string |
| | postal_code | CEP | string |
| | state | Estado | string |


### **Exemplo de recebimento - sucesso**
```json
{
    "status": "ok",
    "data": {
        "card_id": "40689030-2ba3-4215-b81c-1ecd7d09859b",
        "card_number_suffix": "123456"
    }
}
```
___

## **CardInfo - POST**
### Método responsável por realizar a consulta dos dados do cartão.

### **Input Parameters (usando card_id, de acordo com normas PCI)**

```json
{
    "card_id": "40689030-2ba3-4215-b81c-1ecd7d09859b",
}
```

### **Input Parameters (de forma alternativa, usando o número do cartão, não está de acordo com as normas PCI)**

```json
{
    "card_number": "1234567891234567",
}
```

### **Data Dictionary**

| Campo | Descrição| Tipo |
|:---------|:-------------|----------|
| card_id | Identificador único do cartão na base da Roadpass| string |
| card_number | Número do cartão | string |



### **Output parameters**

```json
{
    "status": "ok",
    "data":{
       "card_id": "40689030-2ba3-4215-b81c-1ecd7d09859b",
       "card_number": "1234567891234567",
       "holder_name": "FABIO A GARCIA",
       "balance": 1000,
       "cvc": 123,
       "expiration_month": 1,
       "expiration_year": 2050,
       "status": "P",
       "type": "V", 
       "person_id": "9999999999",
       "request_date_time": "2018-01-20 14:10:00",
       "delivery_date_time": "2017-12-28 14:10:00"
    }
}
```

### **Data Dictionary**

| Campo | Descrição| Tipo |
|:---------|:-------------|----------|
| card_id | Identificador único do cartão na base da Roadpass | number |
| card_number | Número do cartão | string, somente números |
| holder_name | Nome no cartão | string |
| balance | Saldo atual do cartão | number |
| cvc | Código de verificação do cartão | number |
| expiration_month | Mês de expiração do cartão | number |
| expiration_year | Ano de expiração do cartão | number |
| status | Status atual do cartão, sendo: <ul><li>P: Pending</li><li>C: Canceled</li><li>B: Blocked</li><li>I: Inactive</li><li>A: Active</li></ul> | string |
| type | Tipo do cartão, sendo: <ul><li>V: Virtual</li><li>P: Phisical</li></ul> | string |
| person_id | Número do documento do proprietário do cartão. Para usuário do Brasil, enviar o CPF | string, somente numeros |
| request\_date\_time | Data da solicitação do cartão pelo usuário| string |
| delivery\_date\_time | Data de envio do cartão | string |


___
## **CardActivate - POST**
### Ativa o cartão

### **Input Parameters**

```json
{
    "card_number": "1234567891234567",
    "expiration_year": 2010,
    "expiration_month": 3,
    "cvc": "123"
}
```

### **Data Dictionary**

| Campo | Descrição| Tipo |
|:---------|:-------------|----------|
| card_number | Número do cartão | string, somente números |
| expiration_year | Ano de expiração do cartão | number |
| expiration_month | Mês de expiração do cartão | number |
| cvc | Código de verificação do cartão | string |

### **Output parameters**

```json
{
   "status": "ok"
}
```
___
## **CardBlock - POST**
### Bloqueia um cartão

### **Input Parameters**

```json
{
    "card_id": "40689030-2ba3-4215-b81c-1ecd7d09859b"
}
```

### **Data Dictionary**

| Campo | Descrição| Tipo |
|:---------|:-------------|----------|
| card_id | Número do cartão | string |


### **Output parameters**

```json
{
   "status": "ok",
   "data":{
       "card_id": "40689030-2ba3-4215-b81c-1ecd7d09859b"
   }
}
```
___
## **CardCancel - POST**
### Cancela um cartão


### **Input Parameters**

```json
{
   "card_id": "40689030-2ba3-4215-b81c-1ecd7d09859b"
}
```

### **Data Dictionary**

| Campo | Descrição| Tipo |
|:---------|:-------------|----------|
| card_id | Identificador do cartão | string |

### **Output parameters**

```json
{
   "status": "ok",
   "data":{
       "card_id": "40689030-2ba3-4215-b81c-1ecd7d09859b"
   }
}
```
___

## **CardPinCodeReset - POST**
### Método responsável por realizar a troca de senha de um cartão.

### **Input Parameters**

```json
{
    "card_id": "40689030-2ba3-4215-b81c-1ecd7d09859b",
    "pin_code_new": "123456"
}
```

### **Data Dictionary**

| Campo | Descrição| Tipo |
|:---------|:-------------|----------|
| card_id | Identificador do cartão | string |
| pin_code_new | Novo número PIN | string, somente números |


### **Output parameters**

```json
{
   "status": "ok",
   "data":{
       "card_id": "40689030-2ba3-4215-b81c-1ecd7d09859b"
   }
}
```
___

## **CardBalance - POST**
### Retorna o saldo do cartão


### **Input Parameters**

```json
{
    "card_id": "40689030-2ba3-4215-b81c-1ecd7d09859b"
}
```

### **Data Dictionary**

| Campo | Descrição| Tipo |
|:---------|:-------------|----------|
| card_id | Identificador do cartão | string |

### **Output parameters**

```json
{
   "status": "ok",
   "data": {
      "card_id": "40689030-2ba3-4215-b81c-1ecd7d09859b", 
      "balance": 1000.17
   }
}
```

### **Data Dictionary**

| Campo | Descrição| Tipo |
|:---------|:-------------|----------|
| balance | Saldo atual do cartão| number |
___


## **CardStatement - POST**
### Retorna o extrato das movimentações do cartão

### **Input Parameters**

```json
{
    "card_id": "40689030-2ba3-4215-b81c-1ecd7d09859b",
    "date_time_start": "2017-07-01 10:00:00 -03:00",
    "date_time_at": "2018-01-01 18:00:00 -03:00",
    "page_size": 50,
    "page_number": 1
}
```

### **Data Dictionary**

| Campo | Descrição| Tipo |
|:---------|:-------------|----------|
| card_id | Identificador do cartão| string |
| date_time_start | Data inicial da busca | string |
| date_time_at | Data final da busca | string |
| page_size | Quantidade de registros retornados por requisição | string |
| page_number | Número da paginação | string |

### **Output parameters**

```json
{
    "status": "ok",
    "data":[
            { 
               "id": 7126,
               "date_time": "2017-01-02 18:55:52",
               "status": "TRANSACAO INVALIDA",
               "type": "SAQUE EM CAIXA AUTOMÁTICO",
               "location": "Lorem ipsum dolor sit amet.",
               "amount": 325.23,
               "accepted": false,
               "rejected_reason": "Auth. processing error: Credit Line not defined for actual transaction"
            }
    ]
}
```


### **Data Dictionary**

| Campo | Descrição| Tipo |
|:---------|:-------------|----------|
| id | Identificador único do registro de extrato | number |
| date_time | Data e hora da transação, no formato YYYY-MM-DD HH:mm:ss| string |
| status | Status da transação | string |
| type | Tipo da transação | string |
| location | Estabelecimento onde foi realizada a transação | string |
| amount | Valor da transação | number |
| accepted | Indica se a transação foi ou não aceita | boolean |
| rejected_reason | Motivo pela qual a transação foi rejeitada | string |
___

## **CardAddFund - POST**

### Adiciona fundos a um cartão

## **Input parameters**

```json
{
    "card_id": "40689030-2ba3-4215-b81c-1ecd7d09859b",
    "description": "Descrição da transação",
    "amount": 3230.25
}
```

### **Data Dictionary**

| Campo | Descrição| Tipo |
|:---------|:-------------|----------|
| card_id | Identificador do cartão | string |
| description | Descrição da transação | string |
| amount | Valor a ser creditado no cartão | number |


### **Output parameters**
```json
{
   "status": "ok",
   "data": {
       "card_id": "40689030-2ba3-4215-b81c-1ecd7d09859b"
   }
}
```
___

## **CardSubFund - POST**

### Retira fundos de um cartão

## **Input parameters**

```json
{
    "card_id": "40689030-2ba3-4215-b81c-1ecd7d09859b",
    "description": "Descrição da transação",
    "amount": 3230.25
}
```

### **Data Dictionary**

| Campo | Descrição| Tipo |
|:---------|:-------------|----------|
| card_id | Identificador do cartão | string |
| description | Descrição da transação | string |
| amount | Valor a ser creditado no cartão | number |


### **Output parameters**
```json
{
   "status": "ok",
   "data": {
       "card_id": "40689030-2ba3-4215-b81c-1ecd7d09859b"
   }
}
```
___

## **CardListByPersonId - POST**

### Retorna os cartões de um usuário, baseado em seu CPF

## **Input parameters**

```json
{
    "person_id": "12345678912"
}
```

### **Data Dictionary**

| Campo | Descrição| Tipo |
|:---------|:-------------|----------|
| person_id | CPF do usuário | string, somente números |

### **Output parameters**
```json
{
   "status": "ok",
   "data": {
       "cards": [
           {
              "card_id": "40689030-2ba3-4215-b81c-1ecd7d09859b",
              "card_number": "1234567891234567",
              "holder_name": "FABIO A GARCIA",
              "balance": 1000,
              "cvc": 123,
              "expiration_month": 1,
              "expiration_year": 2050,
              "status": "P",
              "type": "V", 
              "person_id": "9999999999",
              "request_date_time": "2018-01-20 14:10:00",
              "delivery_date_time": "2017-12-28 14:10:00"
           }
       ]
   }
}
```

### **Data Dictionary**

| Campo | Descrição| Tipo |
|:---------|:-------------|----------|
| card_id | Identificador único do cartão na base da Roadpass | number |
| card_number | Número do cartão | string, somente números |
| holder_name | Nome no cartão | string |
| balance | Saldo atual do cartão | number |
| cvc | Código de verificação do cartão | number |
| expiration_month | Mês de expiração do cartão | number |
| expiration_year | Ano de expiração do cartão | number |
| status | Status atual do cartão, sendo: <ul><li>P: Pending</li><li>C: Canceled</li><li>B: Blocked</li><li>I: Inactive</li><li>A: Active</li></ul> | string |
| type | Tipo do cartão, sendo: <ul><li>V: Virtual</li><li>P: Phisical</li></ul> | string |
| person_id | Número do documento do proprietário do cartão. Para usuário do Brasil, enviar o CPF | string, somente numeros |
| request\_date\_time | Data da solicitação do cartão pelo usuário| string |
| delivery\_date\_time | Data de envio do cartão | string |
___

## **CardChargeback - POST**

### Realiza o estorno de uma transação realizada pelo cartão

## **Input parameters**

```json
{
     "card_id": "40689030-2ba3-4215-b81c-1ecd7d09859b",
     "transaction_id": "030-2ba3",
     "chargeback_reason": "Motivo do estorno"
}
```

### **Data Dictionary**

| Campo | Descrição| Tipo |
|:---------|:-------------|----------|
| card_id | Identificador do cartão | string |
| transaction_id | Identificador da transação que deseja-se estornar | string |
| chargeback_reason | Mptivo do estorno | string |

### **Output parameters**
```json
{
   "status": "ok",
   "data": {
      "card_id": "40689030-2ba3-4215-b81c-1ecd7d09859b",
      "transaction_id": "030-2ba3"
   }
}
```
