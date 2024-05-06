---
layout: post
title:  "Estrutura de dados - Anywhere"
date:   2024-04-29 18:30:00 +0000
categories: jekyll update
---

Quero começar esse post com a seguinte frase "A estrutura de dados Either, não funciona muito bem nesse framework". Ouvi isso recentemente e não vou mentir, isso me incomodou um pouco, então decidi escrever sobre estrutura de dados, importância e mostrar isso na prática com códigos e pseudo-códigos para entendermos melhor. Não quero dizer que essa frase está errada mas também não concordo, acho discutível por isso decidi escrever sobre isso. Porém vai ser bem específico sobre a estrutura Either, mas já vai ser o suficiente para entender a importância de todas as outras.

<h1>O que é uma estrutura de dados? E qual sua importância?</h1>

Estrutura de dados, são formas em que podemos em certo ponto, encapsular dados em um único contexto e manipulalos no mesmo lugar. Bora para os exemplos mais comuns e dessa vez, vou usar Kotlin como meu modelo (vai ser útil depois):

```kt
class Person(val name: String, val lastName: String, var age: Int)

val me = Person('Joao', 'Felipe', 23);
```

Legal, esse exemplo é simples para entendermos, aqui nós estamos criando uma estrutura de dados chamada `Person`. Podemos crescer essa classe da seguinte forma: 

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
}

val me = Person('Joao', 'Felipe', 23);
me.getName() // Joao
```

Aqui eu criei métodos para buscar pelo meus dados, ou seja, só consegui criar esses métodos para recuperar meus dados porque eles estão estruturados, além de que, qualquer pessoa consegue usar essa estrutura tendo nome, sobrenome e idade. Ok, agora que demos uma olhada superficial, sobre estrutura de dados, vamos para a diversão agora.

<h1>Estrutura de dados Either</h1>
