# Conceitos fundamentais do React
Há três conceitos principais que trabalham juntos para definir basicamente todo funcionamento do react que serão melhor explicados a seguir: componente, propriedade e estado.

## Componente
Um componente é um elemento de interface construído com uma função JavaScript que retorna código HTML. Combinados, os componentes podem criar infinitas possibiidades de funcionalidades e layouts para uma aplicação.

A seguir vamos utilizar como exemplo um contador, incrementado com um botão:

```jsx
export function Counter() {
    return (
        <div>
            <h1>0</h1>
            <button type='button'>
                Increment
            </button>
        </div>
    )
}
```

Esse componente pode ser facilmente importado e utilizado dentro de outros componentes react:

```jsx
import {Counter} from './Counter'

export function App() {
    return <Counter />
}
```

## Propriedade
Este conceito sintetiza a possibilidade de passar informações do ambiente externo para que sejam usadas internamente pelo componente desejado. Para isso o react agrupa as propriedades no parâmetro props, sendo possível acessá-las posteriormente através dos atributos desse objeto.

Seguindo com o exemplo do botão, vamos criar a porpriedade color que definirá a cor de fundo do nosso botão.

No componente App:
```jsx
import {Counter} from './Counter'

export function App() {
    return <Counter color='blue'/>
}
```

No componente Counter:

```jsx
export function Counter(props) {
    return (
        <div>
            <h1>0</h1>
            <button 
                type='button' 
                style={{background-color: props.color}}
            >
                Increment
            </button>
        </div>
    )
}
```

## Estado
O estado é o princípio pelo qual os componentes react possuem uma espécie de 'memória' guardando valores a serem exibidos, estados ativos ou inativos, etc.

### Imutabilidade
Para ser mais performático, o react não monitora mudanças nas variáveis usando-as como gatilho para disparar uma nova renderização da página. Ao invés disso, a biblioteca utiliza somente constantes e quando precisa alterar algum valor, cria novamente a variável ao invés de incrementá-la ou decrementá-la.

Exemplo que o react não entende:
```jsx
export function Counter(props) {
    let counter = 0

    function increment() {
        counter ++
    }

    return (
        <div>
            <h1>0</h1>
            <button 
                type='button' 
                style={{background-color: props.color}}
                onclick={increment}
            >
                Increment
            </button>
        </div>
    )
}
```

### useState()
Para sanar o problema existe um hook chamado useState(). Ele retorna um array cuja primeira posição contém o valor da constante e a segunda posição contém uma função que permite setar o valor da constante respeitando a imutabilidade.

A melhor forma de fazer a declaração é via desestruturação conforme a seguir:
```js
const [counter, setCounter] = useState(0)
```

OBS.: É convencionado nomear a função de callback como set + nome da constante.

Aplicando no exemplo do contador temos:

```jsx
export function Counter(props) {
    const [counter, setCounter] = useState(0)

    function increment() {
        setCounter(counter + 1)
    }

    return (
        <div>
            <h1>{counter}</h1>
            <button 
                type='button' 
                style={{background-color: props.color}}
                onclick={increment}
            >
                Increment
            </button>
        </div>
    )
}
```