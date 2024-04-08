---
layout: post
title:  "AWS, CloudWatch e observabilidade, Jeff Bezos realmente merece meu dinheiro?"
date:   2024-04-08 18:47:00 +0000
categories: jekyll update
---

Oii &#128518;! Hoje quero falar sobre observabilidade e como isso salvou, salva e salvará minha vida!
Em projetos onde nós temos dados sensíveis e processos que precisam de um bom monitoramento (porque pode nos custar caro se não tivermos), muitas vezes precisamos pensar em observalidade, mas o que é observalidade no mundo de software?

Bom, vamos ao que todo mundo já fez na vida ao menos uma vez:
{% highlight js %}
console.log('Hello World');
{% endhighlight %}

Isso aí! Não temos exemplo melhor do que isso de uma observabilidade. Quantas vezes não fizemos cenários nesse sentido, de uma forma mais prática:

{% highlight js %}
console.log('Antes da minha função executar! Com os parâmetros', meusParametros);
minhaFunção(meusParametros);
console.log('Depois da minha função executar!');
{% endhighlight %}

Porém Mr. João, como isso pode ser realmente útil? 

A princípio não é! A função `console.log` como diz a grosso modo "log no console", vai ser mostrado em tempo de execução e se não tiver nenhum lugar para salvar essa mensagem, nós perdemos ela e nunca mais saberemos o que foi escrito &#128128;.

Ok, essa é fácil! Vamos armazenar as mensagens em um arquivo fisíco ao rodarmos nossa aplicação, assim não iremos perder nada:
{% highlight js %}
function log(message) {
   console.log(message);
   fs.writeFileSync(caminhoDoMeuArquivo, 'minha mensagem na minha funcao log ' + message);
}
{% endhighlight %}

Pesquisando para escrever esse post notei que, ou usam bibliotecas ou se você não for preguiçoso (brincadeira) pode implementar isso na mão.

Agora resolvemos o problema de observalidade, consigo registrar minhas mensagens e depois se eu tiver algum problema posso dar uma espiadinha no arquivo para consultar algum erro ou algum registro que queira monitorar. Mas agora que vêm o "final boss", vamos supor que nossa aplicação (aonde colocamos esse "logger") executa diariamente 1 milhão de vezes &#128561;. Espero que você tenha muito espaço no HD ou no SSD, se não, tais encrecado!

Com a solução anterior nós temos alguns grandes problemas:

* Tamanho do arquivo:

    Se armazenarmos localmente esse arquivo nesse fluxo mencionado, tranquilamente chegaremos a gigabytes de dados e isso não irá parar até consumir todo espaço disponível no servidor, até não conseguirmos atualizar mais o código, código nada mais é que arquivos de texto, não é mesmo?!;

* Instabilidade no servidor:
  
    Vamos pensar, minha aplicação está rodando diversos processos, seja síncrono ou assíncrono, agora com essa escrita de mensagens em arquivos para o monitoramento/observabilidade, nós vamos ter uma concorrência com o processador, até porque um processo está instruindo escrever uma mensagem de texto no arquivo e o outro processo está tentando ler os dados do usuário no banco de dados MySQL (específico!). Sim esse cenário existe, porém é possível contornar com os métodos corretos, mas não vou abordar, não é o intuito do post;

* E pra mim o maior problema de todos, filtros:

    Como vamos filtrar mensagens em milhares de milhões de textos salvos em um arquivo contendo centenas de gigabytes? Isso não seria nada fácil e extremamente lento, até encontrar a real mensagem que está procurando, a empresa já foi de "comes e bebes".


<h4>É ai que vêm uma excelente solução, o CloudWatch!</h4>