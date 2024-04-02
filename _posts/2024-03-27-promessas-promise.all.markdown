---
layout: post
title:  "Promessas - Promise All"
date:   2024-03-17 18:30:00 +0000
categories: jekyll update
---
Esse é o meu primeiro post, espero que gostem e consigam aprender algo com meu jeito de escrever/expressar &#128516;.

Uns dias atrás a partir da data que eu comecei a escrever isso aqui, eu recebi uma tarefa para adicionar um novo filtro no <em>front-end</em> e 
consequentemente também teria que aplicar um <em>"where"</em> no banco de dados para esse novo filtro. Quando me deparo com o seguinte código no <em>front-end</em> (usamos Vue.js):

{% highlight js %}
await this.getCompanies();
await this.getSavedFilters();
await this.getBanks();
await this.getPaymentMethods();
await this.getCustomers();
await this.getSuppliers();
{% endhighlight %}
<em>(meramente ilustrativo, tem uma coisa chamada confidencialidade que a gente assina nos contratos com as empresas, baboseira isso ai rs)</em>

A princípio inofensivo, não é mesmo? Porém eu estava usando um banco de dados de teste (remoto, e não tão perfomático quanto de produção) e a tela estava demorando uns segundos para carregar.

Para termos uma visualização melhor desse código na prática, vamos olhar o diagrama abaixo:

{% mermaid %}
sequenceDiagram
    front-end->>back-end: getCompanies() 
    back-end-->>front-end: getCompanies() 
    front-end->>back-end: getSavedFilters() 
    back-end-->>front-end: getSavedFilters() 
    front-end->>back-end: getBanks() 
    back-end-->>front-end: getBanks() 
    front-end->>back-end: ... 
    back-end-->>front-end: ... 
{% endmermaid %}

Pensando que cada função pode levar 1 segundo de ida e volta, levaríamos 6 segundos no total para carregar uma tela com filtros, então pensei, "ok, esses <em>"awaits"</em> sequenciais vão esperar executar um de cada vez e por isso está demorando o <em>loading</em>", logo pensei na função do "javascripto"
chamada `Promise.all` para poder paralelizar e otimizar o tempo, então o código ficou isso aqui:

{% highlight js %}
await Promise.all([
  await this.getCompanies();
  await this.getSavedFilters();
  await this.getBanks();
  await this.getPaymentMethods();
  await this.getCustomers();
  await this.getSuppliers();
])
{% endhighlight %}

Show de bola! Coisa de 2min, com uma boa otimização, e de fato, o <em>loading</em> que carregava esses dados abaixou o tempo de segundos para milisegundos, logo pensei "em produção esse <em>loading</em> vai ficar invísivel praticamente". Mas, adivinha? A vida não é um morango &#128544;.

Após carregar a tela com os dados, comecei a notar alguns comportamentos estranhos/errados nos filtros, era para preencher automaticamente alguns campos com valores que foram usados anteriormente.
Porém não carregaram, então fui revisar o que cada função fazia e é aqui que foi o motivo para escrever esse <em>post</em>, apresentar soluções e seus possíveis problemas juntos.

Então, o que aconteceu aqui? Quando usamos qualquer solução que envolve paralelismo, temos que pensar que podem haver problemas de concorrência. O que isso quer dizer? Significa que tinha uma função que deveria executar primeiro, porém o `Promise.all` não está nem ai com suas prioridades, ele vai executar tudo junto e resolver cada um no seu tempo, então o código necessitou fazer a seguinte modificação:

{% highlight js %}
await this.getCompanies();
await this.getSavedFilters();
await Promise.all([
  await this.getBanks();
  await this.getPaymentMethods();
  await this.getCustomers();
  await this.getSuppliers();
])
{% endhighlight %}

Eu preciso esperar que as funções `getCompanies()` e `getSavedFilters()` finalizarem primeiro, porque as outras funções (dentro da `Promise.all`) iriam depender das mudanças 
de alguns estados (famoso <em>side-effects</em> mas fica para outro <em>post</em> quando tiver vontade) que essas funções de fora executam.

Agora nós temos o seguinte diagrama que representa nosso código, em ordem de execução:

{% mermaid %}
graph TD;
    getCompanies(getCompanies)-->|await|getSavedFilters(getSavedFilters)
    getSavedFilters-->|await|promisseAll
    promisseAll-->back-end1(getBanks);
    promisseAll-->back-end2(getPaymentsMethods);
    promisseAll-->back-end3(getCustomers);
    promisseAll-->back-end4(getSuppliers);
{% endmermaid %}

Ou seja, a função `Promise.all` irá invocar as 4 funções ao mesmo tempo para processar, não conseguimos paralelizar todas as funções mas ainda assim conseguimos manter uma boa otimização no tempo de execução do código, entretanto foi necessário validar se havia algum problema de concorrência (eu tinha consciência disso) para evitar novos <em>bugs</em> em produção. 
Além do mais, paralelizar funções dependendo do que estão executando, podem consumir muito processamento (mas isso fica em outro <em>post</em> quando tiver vontade também rs).
