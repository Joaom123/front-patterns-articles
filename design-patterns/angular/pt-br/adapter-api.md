# Integração com APIs

Grande parte das aplicações *frontend* fazem requisições HTTP, consumindo uma API, que retorna uma *estrutura* de dados conhecida (chamada de DTOs). Porém, eles podem mudar, necessitando de adaptações inesperadas no *frontend*. Estas mudanças ocorrem por:

* Mudanças nos requisitos do sistema
* Necessidade de integração com um serviço de terceiro
* Proteção de dados sensíveis

## Problema

Deve ser exibido a seguinte tela com as informações de um item:

| Nome   | Preço Unitário | Quantidade  | Desconto | Preço Total | Preço Final |
|--------|----------------|-------------|----------|-------------|-------------|
| Item A | R$ 20,00       | 2           | 10%      | R$40,00     | R$36,00     |
| Item B | R$ 10,00       | 10          | 5%       | R$100,00    | R$95,00     |

Porém, nem todos esses dados estão presentes no endpoint exposto pelo backend, que é o seguinte:

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

O JSON enviado pelo backend não possui todas as informações da tabela, ele precisa ser tratado/manipulado para que todos os dados da tabela possam ser exibidos.

## Solução sem Adapter

Uma solução sem a utilização do adapter seria a criação de um service que fará a chamada pra API. No contexto do Angular, criamos o seguinte service:

```ts
@Injectable({
  providedIn: 'root'
})
export class DataService {

  constructor() { }

  getItems(): Observable<ItemDTO[]> {
    const mockItems: ItemDTO[] = [
      {
        id: 1,
        name: 'Item A',
        price: 20,
        quantity: 2,
        discount: 0.1
      },
      {
        id: 2,
        name: 'Item B',
        price: 10,
        quantity: 10,
        discount: 0.05
      }
    ]

    return of(mockItems).pipe(
      delay(1000)
    );
  }
}
```

Essa função `getItems` apenas emula uma requisição, então após 1000 milisegundos, será retornado um `Observable<ItemDTO[]>`.

```ts
export class AdapterComponent implements OnInit {
  constructor(private DataService: DataService) { }

  ngOnInit(): void {
    this.DataService.getItems().subscribe((items) => {
      // Implementation using items of type ItemDTO
      // Usage of calculateTotalPrice
      // Usage of calculateFinalPrice
    })
  }

  private calculateTotalPrice(item: ItemDTO): number {
    return item.price * item.quantity;
  }

  private calculateFinalPrice(item: ItemDTO): number {
    return this.calculateTotalPrice(item) * (1 - item.discount);
  }
}
```

As funções `calculateFinalPrice` e `calculateTotalPrice` podem estar isoladas em um utils mas isso não importa para o nosso contexto. Aqui, observamos que o AdapterComponent é responsável por implementar as mudanças no `ItemDTO[]` para que ele obdeça suas regras.

### Vantagens

* Fácil entendimento
* Fácil aplicação
* Poucas interfaces

### Desvantagens

* Não é DRY: Caso seja necessário utilizar esses dados tratados em outro componente, provavelmente vamos ter que reutilizaremos a lógica implementada.
* Mudanças no endpoint:
  * Caso ItemDTO mude, teremos que alterar em cada ponto do nosso código para que obdeça a mudança feita. Ex.: Suponha que a API é alterada e o atributo name é alterado para description. Então teríamos que alterar em cada ponto do nosso código esse atributo.
  * O caso acima não geraria muito impacto, porém algumas mudanças gerariam mais impacto do que essa. Ex.: Mudanças na tipagem do endpoint. Suponha que a tipagem de price é alterada e agora passa a ser uma string (não vamos o julgar o motivo da mudança, é apenas um exemplo). Essa simples mudança poderia gerar alguns bugs pela diferença entre uma string e um number em operações matemáticas
* Por mais que isolemos as chamadas em um serviço, no caso o DataService, há um alto acoplamento entre o componente e o serviço, pois a interface ItemDTO passa a representar tanto o modelo dos dados que devem vir do endpoint quanto o que deve ser utilizado pelo cliente daquele serviço (no caso o componente AdapterComponent)

## Solução com Adapter

O serviço `DataService` permanece o mesmo. Porém, criamos uma camada entre o serviço e o componente, afim de remover o acoplamento entre eles. Essa camada será o adapter e ela se chamará `DataServiceAdapter`.

```ts
export class DataServiceAdapter  {
  constructor(private service: GetDataService) { }

  getItems(): Observable<TableItem[]> {
    return this.service.getItems().pipe(
      map((items: ItemDTO[]) => items.map(item => this.mapToTableItem(item)))
    );
  }

  private mapToTableItem(item: ItemDTO): TableItem {
    return {
      id: item.id,
      name: item.name,
      price: item.price,
      quantity: item.quantity,
      totalPrice: this.calculateTotalPrice(item),
      finalPrice: this.calculateFinalPrice(item)
    }
  }

  private calculateTotalPrice(item: ItemDTO): number {
    return item.price * item.quantity;
  }

  private calculateFinalPrice(item: ItemDTO): number {
    return this.calculateTotalPrice(item) * (1 - item.discount);
  }
}
```

O seu uso no componente ficará assim

```js
export class AdapterComponent implements OnInit {
  constructor(private dataServiceAdapter: dataServiceAdapter) { }

  ngOnInit(): void {
    this.dataServiceAdapter.getItems().subscribe((items) => {
      // Implementation using items of type TableItem
      // The entire logic was moved to the adapter
    })
  }
}
```

A função `mapToTableItem()` é responsável por transformar um `ItemDTO` em `TableItem`, transformando uma representação external para uma interna. Já a função `getItems()` aplica a função `mapToTableItem()` para cada item que recebemos do array.

É importante observar que toda a lógica que estava dentro do componente (ele estava fazendo mais do que uma coisa), foi movida para o adapter (que dentro do MVC corresponderia a camada de modelo/model).

### Vantagens

### Desvantagens

## Outra Solução com Adapter

### Vantagens

### Desvantagens

* Alta complexidade

## Outra Solução co
