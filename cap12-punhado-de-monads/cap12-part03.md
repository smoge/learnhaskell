O tipo de classe Monad
======================

Assim como functors tem o tipo de classe [code]Functor[/code] e applicative functors tem o tipo de classe [code]Applicative[/code], monads tem seu próprio tipo de classe: [code]Monad[/code]! Wow, quem teria adivinhado? Assim é como esse tipo de classe se parece:

Vamos começar com a primeira linha. Ela diz: [code]class Monad m where[/code]. Mas espere, nós não dizemos que monads são apenas reforços de applicative functors? Não deveria haver uma restrição de classe ao longo das linhas de [code]class (Applicative m) = &gt; Monad m where[/code] devendo assim o tipo ser primeiro um applicative functor antes de produzir um monad? Bem, não deveria, mas quando Haskell foi criado, não tinha ocorrido ser uma boa opção para Haskell. Mas com certeza, cada monad é um applicative functor, mesmo se a declaração não dizer isso.

A primeira função que o tipo de classe [code]Monad[/code] define é [code]return[/code]. É o mesmo que [code]pure[/code], somente com um nome diferente. Esse tipo é [code](Monad m) =&gt; a -&gt; m a[/code]. É preciso um valor para colocá-lo em um contexto padrão mínimo que ainda mantém esse valor. Em outras palavras, é preciso alguma coisa para envolver ele em um monad. Ele sempre faz a mesma coisa com a função [code]pure[/code] para o tipo de classe [code]Applicative[/code], o que significa que já estamos familiarizados com [code]return[/code]. Nós já usamos [code]return[/code] quando fizemos I/O. Usamos ele para pegar um valor e fazer uma ação fictícia de I/O que não faz nada mas que produz um valor. Para [code]Maybe[/code] ele cria um valor e envolve ele em um [code]Just[/code].

Apenas um lembrete: [code]return[/code] não tem nada a ver com [code]return[/code] que é usado na maioria das outras linguagens. Ele não termina a execução da função ou qualquer coisa assim, ele apenas tem um valor normal e coloca ele em um contexto.

A próxima função é [code]&gt;&gt;=[/code], ou bind. É como um function application, só que em vez de ter um valor normal e alimentar ele para uma função normal, ele tem um valor monadico (ou seja, um valor com um contexto) e alimenta ele para uma função que recebe um valor normal, mas retorna um valor monadico.

Em seguida, temos [code]&gt;&gt;[/code]. Nós não vamos prestar muita atenção nisso agora porque ele vem com uma implementação padrão e nós praticamente nunca implementamos isso ao criar instâncias de monads.

A última função do tipo de classe [code]Monad[/code] é [code]fail[/code]. Nós nunca usamos ela explicitamente em nosso código. Em vez disso, ela é usada pelo Haskell para permitir falhas em um construtor semântico especial para monads que iremos conhecer depois. Nós não precisamos nos preocupar com [code]fail[/code] por hora.


Agora que nós sabemos como que o tipo de classe [code]Monad[/code] se parece, vamos dar uma olhada em como [code]Maybe[/code] é uma instância de [code]Monad[/code]! 

[code]return[/code] é o mesmo que [code]pure[/code], assim não nos exige esforços. Nós fazemos o mesmo que fizemos no tipo de classe [code]Applicative[/code] e envolvemos ele em um [code]Just[/code].

A função [code]&gt;&gt;=[/code] é o mesmo que o nosso [code]applyMaybe[/code]. Quando alimentamos o [code]Maybe a[/code] para nossa função, nós temos em mente o contexto e retornamos um [code]Nothing[/code] se o valor da esquerda é [code]Nothing[/code] porque se não há nenhum valor, então não há nenhuma maneira de aplicar a nossa função nele. Se é um [code]Just[/code] pegamos o que está dentro da função e aplicamos [code]f[/code].

Nós podemos brincar por ai com [code]Maybe[/code] como sendo um monad:

Nada de novo ou emocionante na primeira linha uma vez que já usamos [code]pure[/code] com [code]Maybe[/code] e nós sabemos que [code]return[/code] é apenas [code]pure[/code] com um nome diferente. As próximas duas linhas [code]&gt;&gt;=[/code] mostram um pouco mais.

Note que quando nós oferecemos [code]Just 9[/code] para a função [code]\x -&gt; return (x*10)[/code], o [code]x[/code] assumiu o valor [code]9[/code] dentro da função. Parece que fomos capazes de extrair o valor de [code]Maybe[/code] sem pattern-matching. E nós ainda não perdemos o contexto do nosso valor [code]Maybe[/code], porque quando ele é [code]Nothing[/code], o resultado do uso [code]&gt;&gt;=[/code] vai ser [code]Nothing[/code] também.