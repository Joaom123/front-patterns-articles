# Mapper: Criando um front-end mais robusto a mudanças na API

Boa parte das aplicações *front-end* fazem requisições para uma API e com a resposta obtida, exibem os dados nas telas ou alguma forma de interação com usuário é feita. Porém, as APIs podem mudar, necessitando de adaptações inesperadas no *front-end*. E, por vezes, não o deixamos robusto a tais mudanças.

Sempre que adicionamos uma nova técnica, seja um padrão de projeto, seja uma mudança na arquitetura, devemos fazer não para o código mais "legal" ou coisa do tipo. Devemos fazer para torná-lo mais robusto a mudanças sobre as quais não temos controle (a API, do ponto de vista do *front-end*, é uma delas).

Alguns momentos em que o *front-end* fica exposto à mudanças são:

* Requisitos mudam e torna-se necessário reestruturar parte de um endpoint
* A API está inacabada. O desenvolvimento no *front-end* está adiantado, fazendo com o que o *dev front-end* tenha que criar um *mock* da API, gerando ruído, pois a API real poderá ser diferente, gerando retrabalho.

Primeiramente, vamos ver os pontos negativos de não utilizar um mapper para segregar os dados que veem da API (DTO) dos dados utilizados pela view (Model):

* Uma mesma interface passa a representar tanto aquilo que vem da API quanto aquilo que é utilizado pela camada de apresentação. Passa-se a utilizar propriedades opcionais (?). E essa interface passa a ficar bastante confusa, pois ela está representando mais de uma coisa.
* Mudanças na API, que podem ocorrer devido a mudanças no requisito ou o *dev back-end* julgou ser necessário uma reestruturação nela (ex.: 10% passa a ser 0.1, uma *string* passar a ser um *number*).

## O que é o Mapper?

## Problema

Deve-se criar uma tabela com as seguintes informações:

| Nome   | Preço Unitário | Quantidade  | Desconto | Preço Final |
|--------|----------------|-------------|----------|-------------|
| Item A | R$ 20,00       | 2           | 10%      | R$36,00     |
| Item B | R$ 10,00       | 10          | 5%       | R$95,00     |

Porém, nem todos esses dados estão presentes no seguinte endpoint:

```json
[
  {
    id: 1,
    name: "Item A"
    price: 20,
    quantity: 2,
    discount: 0.1,
  },
  {
    id: 1,
    name: "Item B"
    price: 10,
    quantity: 10,
    discount: 0.05,
  }
]

```

O JSON enviado pelo backend não possui todas as informações da tabela, ele precisa ser tratado/manipulado para que todos os dados da tabela possam ser exibidos. Além disso, a propriedade price se torna uma string (R$ 20,00).

## Pensando em uma interface

## Exemplo em Angular

O projeto está hospedado no [*StackBlitz*](<https://stackblitz-starters-nkzrsv.stackblitz.io>).

## Exemplo em React

O projeto está hospedado no [*StackBlitz*](<>).


## Referências

[1] (<https://florimond.dev/en/posts/2018/09/consuming-apis-in-angular-the-model-adapter-pattern/>)

[2] <https://medium.com/@tijuhasz/data-mapping-in-frontend-applications-67bc6a97c24d>

[3] <https://trailheadtechnology.com/improving-stability-and-security-in-javascript-apps-using-data-mapping/>

[4] <https://frontendjournal.com/data-mapping-tools-techniques-and-process/>

[5] <https://blog.codeminer42.com/using-mappers-to-organize-your-data-and-your-project-46b45d963e77/>
