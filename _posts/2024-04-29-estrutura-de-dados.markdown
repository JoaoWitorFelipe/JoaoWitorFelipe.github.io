---
layout: post
title:  "Estrutura de dados - Anywhere"
date:   2024-04-29 18:30:00 +0000
categories: jekyll update
---

Quero começar esse post com a seguinte frase "A estrutura de dados Either, não funciona no framework NestJS". Eu ouvi isso recentemente e não vou mentir, isso me incomodou um pouco, então decidi escrever sobre a estrutura de dados Either e mostrar isso na prática com códigos e pseudo-códigos para entendermos melhor.

<h1>O que é uma estrutura de dados?</h1>

Estrutura de dados, são formas que podemos encapsular dados manipulando em um contexto. Para os exemplos mais comuns e dessa vez, vou usar a linguagem de programação Kotlin como meu modelo:

```kt
class Person(val name: String, val lastName: String, var age: Int)

val me = Person('Joao', 'Felipe', 23);
```

Esse exemplo é simples para entendermos, aqui nós estamos criando uma estrutura de dados chamada `Person`. Com esses dados, podemos adicionar os seguintes métodos/contextos: 

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

Agora vamos analisar as mudanças, com os dados encapsulados em único lugar, eu consigo criar contextos, por exemplo, recuperar os dados da pessoa e consigo fazer verificações se a pessoa é maior de idade ou validações se possuí nome composto, dentre várias outras.

<h1>Agora que entendemos o minímo para conversarmos, vamos falar sobre Either</h1>
Vou utilizar um pseudo-código da estrutura de dados `Either` para ser meu exemplo:

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

Vamos entender um pouco sobre essa estrutura, podemos passar dois valores no construtor mas não os dois ao mesmo tempo, por que eu iria usar esse tipo de estrutura de dados? Vamos pensar no seguinte pseudo-código:

```txt
requestPersonDataFromThirdAPI() {
    try {
        response = _request.get(myUrl)
        return response
    } catch {
        return 'Error!'
    }
}

response = requestPersonDataFromThirdAPI()
```

Temos uma função que irá requisitar os dados de uma pessoa de uma API de terceiro, porém essa consulta pode ocorrer um erro, nós podemos retornar a resposta (que vai ser minha classe `Person` mencionado antes) ou podemos retornar uma `string`, como podemos contornar isso? Uma forma, seria usar `union type` que algumas linguagens de programação fornecem, por exemplo em TypeScript: 

```ts
type Person = { data: { name: 'Joao', lastName: 'Felipe', age: 23 } }

requestPersonDataFromThirdAPI() Person | Error {
    try {
        response = _request.get(myUrl)
        return response
    } catch {
        return new Error()
    }
}

response = requestPersonDataFromThirdAPI()

if (response instanceof Person) {
    // to do something with person
} else {
    // to do something with error
}
```

Isso resolve nosso problema mas em linguagens de programação que não possuem união de tipos ou que são fracamente tipados, seja JavaScript, PHP, Python ou Ruby, como podemos resolver isso?

Agora vamos reescrever esse código utilizando a estrutura de dados `Either`:

```txt
requestPersonDataFromThirdAPI() Either {
    try {
        data = _request.get(myUrl)
        return Either(undefined, data)
    } catch {
        return Either('Error!', undefined)
    }
}

response = requestPersonDataFromThirdAPI()

if (response.isValueA) then // to do something with string
else then // to do something with data
```

Aqui nós já conseguimos estruturar os nossos dados em um único contexto e ter um único tipo de dados para retornar ao invés de 2, trazendo um exemplo mais real em Kotlin:

```kt
fun main() {
    val result: Either<Person, RequestError> = execute();

    if (result.left()) {
        // to do something with person data
    }

    if (result.right()) {
        // to do something with error
    }
}

fun requestPersonDataFromThirdAPI() = Person('Joao', 'Felipe', 23); 

fun execute(): Either<Person, RequestError> {
    try {
        val response = requestPersonDataFromThirdAPI()
        return Either.Left(response)
    } catch (e: SomeException) {
        return Either.Right(RequestError('Error when call request person data.'))
    }
}
```

Olha que bacana que ficou! A linguagem de programação Kotlin já é fortemente tipado porém não possuí união de tipos mas já tem a implementação do `Either` pronta nativamente.

Mas essa solução poderia ser usada em linguagens de programação sendo PHP, Python ou Ruby, que não possuem soluções de `union type` ou nativamente uma implementação `Either`. E voltando a frase "A estrutura de dados Either, não funciona no framework NestJS", eu discordo, estrutura de dados funcionam independente da linguagem de programação ou de framework. Tudo depende das característica que são fornecida e o intuito que será utilizado.