---
layout: post
title:  "CloudWatch e observabilidade"
date:   2024-04-08 18:47:00 +0000
categories: jekyll update
---

Oii &#128518;! Quero falar sobre a importância de uma observabilidade e mencionar uma ferramenta que eu venho usando bastante (spoiler no título).
Em projetos onde nós temos dados sensíveis e processos que precisam de um bom monitoramento (porque pode nos custar caro se não tivermos), muitas vezes precisamos pensar em observabilidade, mas o que é observabilidade no mundo de <i>software</i>?

Bom, vamos ao que todo mundo já fez na vida ao menos uma vez:
{% highlight js %}
console.log('Hello World');
{% endhighlight %}

Isso aí! Não temos exemplo melhor do que isso de uma observabilidade. Quantas vezes não fizemos cenários nesse sentido, de uma forma mais "realista":

{% highlight js %}
console.log('Antes da minha função executar, com os parâmetros', meusParametros);
minhaFuncao(meusParametros);
console.log('Depois da minha função executar!');
{% endhighlight %}

Porém Mr. João, como isso pode ser realmente útil? 

A princípio não é! A função `console.log` como diz a grosso modo "log no console", vai ser mostrado em tempo de execução e se não tiver nenhum lugar para salvar essa mensagem, nós perdemos ela e nunca mais saberemos o que foi escrito &#128128;.

Ok, essa é fácil! Vamos armazenar as mensagens em um arquivo físico ao rodarmos nossa aplicação, assim não iremos perder nada:
{% highlight js %}
function log(message) {
   fs.writeFileSync(caminhoDoMeuArquivo, 'minha mensagem na minha funcao log ' + message);
}
{% endhighlight %}

Pesquisando para escrever esse post notei que, ou usam bibliotecas ou se você não for preguiçoso (brincadeira) pode implementar isso na mão.

Agora resolvemos o problema de observabilidade, consigo registrar minhas mensagens e depois se eu tiver algum problema posso dar uma espiadinha no arquivo para consultar algum erro ou algum registro que queira monitorar. Mas agora que vem o "final boss", vamos supor que nossa aplicação (onde colocamos esse "logger") executa diariamente 1 milhão de vezes &#128561;. Espero que você tenha muito espaço no HD ou no SSD, se não, tais encrecado!

Com a solução anterior nós temos alguns grandes problemas:

* Tamanho do arquivo:

    Se armazenarmos localmente esse arquivo nesse fluxo mencionado, tranquilamente chegaremos a gigabytes de dados e isso não irá parar até consumir todo espaço disponível no servidor (a não ser que limpemos manualmente mas vamos apagar todo o arquivo???), até não termos mais espaço para atualizar o código, código nada mais é que arquivos de texto ocupando espaço no armazenamento, não é mesmo?!;

* Instabilidade no servidor:
  
    Vamos pensar, minha aplicação está rodando diversos processos, sejam síncrono ou assíncrono, agora com essa escrita de mensagens em arquivos para o monitoramento/observabilidade, nós vamos ter uma concorrência com o processador, até porque um processo está instruindo escrever uma mensagem de texto no arquivo e o outro processo está tentando ler os dados do usuário no banco de dados MySQL (específico!). Sim esse cenário existe e ele piora quanto maior for o arquivo de log, porém é possível contornar com os métodos corretos, mas não vou abordar (não é o intuito do post);

* E pra mim o maior problema de todos, filtros:

    Como vamos filtrar mensagens em milhares de milhões de textos salvos em um arquivo contendo centenas de gigabytes? Isso não seria nada fácil e extremamente lento, até encontrar a real mensagem que está procurando, a empresa já foi de "comes e bebes".


<h4>É ai que vem uma excelente solução, o CloudWatch!</h4>

CloudWatch, é um dos serviços da Amazon Web Services para observabilidade, alertas dentre outros vários benefícios, por exemplo:

* Visualizar/Monitorar

    Visualizar seus logs em <i>dashboards</i> ou apenas em uma listagem simples;

* Filtrar

    Oferece uma sintaxe própria para filtros, vamos supor que eu tenha salvo os seguintes dados de um PIX (eu faço bastante integrações com instituições financeiras &#128518;) no meu log e eu quero filtrar pelo ID do usuário, a sintaxe do filtro ficaria assim `{ $.userId = 1 }` (no serviço web do CloudWatch, tem um campo para aplicar esses filtros, caso não tenha entendido) ou poderia ser por qualquer outro campo também:
    {% highlight json %}
    {
        "userId": 1,
        "transactionId": "01010101010101",
        "value": 1000.01,
        "payer": "Você!"
    }
    {
        "userId": 2,
        "transactionId": "01010101010111",
        "value": 1000.01,
        "payer": "Você também!"
    }
    {% endhighlight %}

* Armazenar

    Você pode definir períodos para realizar limpezas dos logs mas também pode manter para sempre. Limpando regularmente os filtros para logs específicos naturalmente as consultas serão mais rápido. E o mais importante, eles não estarão no servidor da sua aplicação ocupando espaço desnecessário;

* Baixar

    É possível fazer <i>download</i> em CSV, caso precise extrair algum dado, é extremamente útil;

* Custos

    O inimigo é forte mas é possível vencer, CloudWatch oferece mensalmente gratuitamente (até o momento da data desta postagem) 1 MILHÃO de logs. Para serviços/aplicações pequenas e até médio porte é uma excelente solução usar caso não tenha muito recurso financeiramente. E se a aplicação é grande com milhares de milhões de clientes, é claro que precisará gastar com isso mas convenhamos, não é o CloudWatch que vai "arrombar" as finanças da empresa mas talvez a falta de observabilidade pode fazer isso.

E agora, para finalizarmos o intuito desse post é, logs são importantes para as aplicações, independente se é, pequeno, médio ou grande. E independente se for usar CloudWatch, arquivo físico ou alguma outra ferramenta, apenas faça! Foquem em fazer bons logs respondendo perguntas, por exemplo:

* E se eu precisar filtrar algum dado, como posso filtrar de forma rápida e por qual dado? ID da transação? Valor? Nome do usuário?
* Se algum fluxo quebrar, o que preciso de dados para poder corrigir? O que preciso salvar de importante?
* Se eu quiser um alarme pra me avisar sobre um erro, o que é mais relevante eu salvar para poder me alertar? 
* Se eu quiser montar um <i>dashboard</i> para termos ideias do que os clientes estão usando da nossa aplicação, o que posso salvar para ter essas informações?

É isso! Espero que tenham gostado &#128522;. Dúvidas ou correções sobre algo, fiquem à vontade para me chamar!