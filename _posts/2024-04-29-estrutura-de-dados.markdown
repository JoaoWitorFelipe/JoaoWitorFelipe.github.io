---
layout: post
title:  "Estrutura de dados - Anywhere"
date:   2024-04-29 18:30:00 +0000
categories: jekyll update
---

Quero começar esse post com a seguinte frase "A estrutura de dados Either, não funciona muito bem nesse framework". Ouvi isso recentemente e não vou mentir, isso me incomodou um pouco, então decidi escrever sobre estrutura de dados, importância e mostrar isso na prática com códigos e pseudo-códigos para entendermos melhor. Não quero dizer que essa frase está errada mas também não concordo, acho discutível por isso decidi escrever sobre isso. Porém vai ser bem específico sobre a estrutura Either, mas já vai ser o suficiente para entender a importância de todas as outras.

<h1>O que é uma estrutura de dados? E qual sua importância?</h1>

Estrutura de dados, são formas que podemos encapsular dados manipulando em um dado contexto. Bora para os exemplos mais comuns e dessa vez, vou usar a linguagem de programação Kotlin como meu modelo (vai ser útil depois):

```kt
class Person(val name: String, val lastName: String, var age: Int)

val me = Person('Joao', 'Felipe', 23);
```

Legal, esse exemplo é simples para entendermos, aqui nós estamos criando uma estrutura de dados chamada `Person`. Com esses dados, podemos adicionar os seguintes métodos: 

```kt
class Person(val name: String, val lastName: String, val age: Int) {
    fun getName(): String {
        return name 
    }

    fun getLastName(): String {
        return lastName 
    }

    fun getAge(): Int {
        return age 
    }

    fun ofLegalAge(): Boolean {
        return age > 18;
    }
}

val me = Person('Joao', 'Felipe', 23);
me.getName() // Joao
me.ofLegalAge() // True
```

Agora vamos analisar essa minha nova estrutura, com os dados encapsulados em único lugar, eu consigo criar contextos, por exemplo, recuperar meus dados e principalmente, eu consigo fazer verificações se a pessoa é maior de dados. Eu poderia fazer isso de outra forma? Sim, poderia criar funções avulsas! Porém isso me faz perder contextos do que estou validando ou porquê estou validando.

<h1>Estrutura de dados Either</h1>
Essa estrutura de dados é útil principalmente para linguagens de programação que faltam algumas ferramentas de tipagem, por exemplo, `union type`. Um exemplo, é o uso de TypeScript:

```ts
myUnionTypeFunction(data: string | number): string | number {
    return data;
}
```

Nesse exemplo, eu tenho uma função que tem um parâmetro que pode ser uma `string` ou `number` e o retorno também, pode ser um ou outro. Isso, por baixo dos panos não é uma estrutura de dados que está controlando, apenas uma união de tipos, que para o compilado que ler isso, vai entender que a função pode retornar um desses dois tipos.

Agora vamos para um pseudo-código da estrutura de dados `Either`:

```txt
class Either {
    constructor(valueA?, valueB?) {
        if (isValueA() AND isValueB()) {
            throw "Both values ​​cannot be filled in."
        }
    }

    isValueA() = isset(valueA)
    isValueB() = isset(valueB)
    getValueA() = valueA
    getValueB() = valueB
}
```

Legal, vamos entender um pouco sobre essa estrutura, podemos passar dois valores no construtor mas não ao mesmo tempo, por que eu iria usar esse tipo de estrutura de dados? Vamos pensar no seguinte pseudo-código:

```txt
requestDataFromThirdAPI() {
    try {
        data = _request.get(myUrl)
        return data
    } catch {
        return 'Error!'
    }
}

response = requestDataFromThirdAPI()
```

Temos uma função que irá requisitar algum dado de uma API de terceiro, porém essa consulta pode ocorrer um erro e agora, nós podemos retornar os dados (uma outra estrutura de dados) ou podemos retornar uma `string`, como podemos contornar isso? Uma forma, seria usar `union type` que algumas linguagens de programação fornencem: 

```txt
requestDataFromThirdAPI() Data OR string {
    try {
        data = _request.get(myUrl)
        return data
    } catch {
        return 'Error!'
    }
}

response = requestDataFromThirdAPI()

if (response typeof string) then // to do something with string
else then // to do something with data
```

Qual o problema disso aqui? Bom:

  * Validações fracas:
    
    Estamos checando o tipo do nosso dado para verificar se é string ou se é outro tipo de dado, mas o que isso significa realmente? Nós não temos nenhum contextos, por que não validamos se é um número também? 

  * Legibilidade

    Como já mencionado, sem contextos a nossa legibilidade também ficará baixa, como vamos conseguir escerver algo legível sem ter contextos?

Agora vamos reescrever esse código utilizando a estrutura de dados `Either`:

```txt
requestDataFromThirdAPI() Either {
    try {
        data = _request.get(myUrl)
        return Either(undefined, data)
    } catch {
        return Either('Error!', undefined)
    }
}

response = requestDataFromThirdAPI()

if (response.isValueA) then // to do something with string
else then // to do something with data
```