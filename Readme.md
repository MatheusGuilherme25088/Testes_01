# Teste de Caixa Branca – Loja SENAI

| | |
|---|---|
| **Instituição** | SENAI |
| **Curso** | Técnico em Desenvolvimento de Sistemas |
| **Unidade curricular** | SESI CE 356 |
| **Atividade** | Teste de Caixa Branca – Sistema de Pedidos |
| **Aluno** | Matheus Guilherme Teixeira Silva |
| **Turma** | 3B |
| **Professores** | Robson e Reenye |
| **Data** | 30/09/2026 |

---

## Contextualização sobre Teste de Caixa Branca

No teste de caixa branca, eu analisei o código por dentro, verificando os `if`, as comparações e os cálculos. Escolhi valores que façam o programa passar por diferentes situações, para conferir se tudo funciona corretamente e encontrar possíveis erros, principalmente nos valores-limite.

O sistema é uma loja com três produtos, na qual o usuário escolhe o produto, a quantidade, o cupom e o frete. O `script.js` faz os cálculos para chegar ao valor final do pedido. O código tem seis erros de lógica de propósito, sendo dois fáceis, dois médios e dois difíceis.

### Comportamento esperado

| Regra | O que se espera |
|---|---|
| Quantidade | Número inteiro maior que zero |
| Estoque | Pode pedir até a quantidade em estoque (notebook 5, mouse 20, teclado 10) |
| Cupom `SENAI10` | 10% sobre o subtotal |
| Cupom `SENAI20` | 20% sobre o subtotal, só se o subtotal for R$ 1.000 ou mais |
| Desconto por quantidade | 5% sobre o subtotal a partir de 5 unidades |
| Frete | Retirada grátis, expresso R$ 60, normal R$ 30 (grátis se subtotal ≥ R$ 500) |
| Pedido de alto valor | Total a partir de R$ 3.000: ganha 5% extra e a mensagem "Pedido de alto valor." |
| Exibição | Subtotal − descontos + frete deve ser igual ao total mostrado |

---

## Análise das Estruturas de Decisão

### Validação da quantidade (D6)

```js
if (qtd < 0) {
    resultado.innerHTML = "<p>Quantidade inválida.</p>";
    return;
}
```
Descrição: verifica se a quantidade é negativa e o caminho percorrido pelo programa: Sim → exibe erro e encerra. Não → vai para o estoque.

### Verificação do estoque (D7)

```js
if (qtd >= estoque[produtoSelecionado]) {
    resultado.innerHTML = "<p>Quantidade indisponível em estoque.</p>";
    return;
}
```
Descrição: compara a quantidade solicitada com o estoque e o caminho percorrido pelo programa: Sim → exibe "indisponível" e encerra. Não → calcula o subtotal.

### Cupom SENAI10 (D1)

```js
if (codigo === "SENAI10") {
    return subtotal * 0.10;
}
```
Descrição: aplica desconto de 10% quando o cupom é `SENAI10` e o caminho percorrido pelo programa: Sim → 10% de desconto. Não → vai para o SENAI20.

### Cupom SENAI20 (D2)

```js
if (codigo === "SENAI20" && subtotal >= 1000) {
    return subtotal * 0.20;
}
```
Descrição: aplica 20% quando o cupom é `SENAI20` e o subtotal é pelo menos R$ 1.000 (única condição composta do código, com `&&`) e o caminho percorrido pelo programa: Sim → 20% de desconto. Não → sem desconto.

### Cálculo do frete (D3, D4 e D5)

```js
if (tipo === "retirada") return 0;
if (tipo === "expresso") return 60;
if (subtotal >= 500) return 0;
return 30;
```
Descrição: define o valor do frete pela modalidade e o subtotal e o caminho percorrido pelo programa: retirada → R$ 0. Expresso → R$ 60. Normal com subtotal ≥ 500 → R$ 0. Normal com subtotal < 500 → R$ 30. 

### Desconto por quantidade (D8)

```js
if (qtd > 5) {
    total = total - subtotal * 0.05;
}
```
Descrição: aplica 5% quando a quantidade é maior que cinco e o caminho percorrido pelo programa: Sim → subtrai 5% do total. Não → o total não muda.

### Desconto para alto valor (D9)

```js
if (total > 3000) {
    total = total * 0.95;
}
```
Descrição: aplica 5% quando o total é superior a R$ 3.000 e o caminho percorrido pelo programa: Sim → total × 0,95. Não → o total não muda.

### Classificação (D10 e D11)

```js
if (total <= 0) {
    mensagem = "Valor do pedido inválido.";
} else if (total >= 3000) {
    mensagem = "Pedido de alto valor.";
}
```
Descrição: classifica pedidos inválidos e de alto valor, usando o `total` já alterado pelas decisões anteriores, e o caminho percorrido pelo programa: `total <= 0` → "inválido". `total >= 3000` → "alto valor". Senão → "sucesso".


## Fluxograma do Exemplo

```
        ( INÍCIO )
            |
            v
 /produto, qtd, cupom, frete/
            |
            v
    < qtd < 0 ? > --Sim--> /Quantidade inválida/ --> ( FIM )
            |Não
            v
 < qtd >= estoque ? > --Sim--> /Indisponível/ --> ( FIM )
            |Não
            v
 [subtotal = preço x qtd]
            |
            v
 [desconto do cupom]
            |
            v
 [calcula o frete]
            |
            v
 [total = subtotal - desconto + frete]
            |
            v
    < qtd > 5 ? > --Sim--> [total -= 5% do subtotal]
            |Não                      |
            +<-----------------------+
            |
            v
  < total > 3000 ? > --Sim--> [total x 0,95]
            |Não                      |
            +<-----------------------+
            |
            v
  < total <= 0 ? > --Sim--> [msg = Valor inválido]
            |Não                      |
            v                          |
 < total >= 3000 ? > --Sim--> [msg = Alto valor]
            |Não                      |
            v                          |
    [msg = Sucesso]                    |
            |                          |
            +<-------------------------+
            |
            v
 /mensagem, subtotal, desconto, frete, total/
            |
            v
        ( FIM )
```

---

## Casos de Teste

| Identificação | Entrada | Condição/Caminho | Resultado Esperado |
|---|---|---|---|
| CT01 | Mouse / quantidade 0 / normal | Validação da quantidade (D6) | "Quantidade inválida." |
| CT02 | Teclado / quantidade 10 / retirada | Limite do estoque (D7) | Pedido aceito, total R$ 1.425,00 |
| CT03 | Mouse / quantidade 5 / retirada | Desconto por quantidade (D8) | Desconto R$ 20,00, total R$ 380,00 |
| CT04 | Mouse / quantidade 10 / SENAI10 / retirada | Dois descontos (D1 e D8) | Desconto exibido R$ 120,00, total R$ 680,00 |
| CT05 | Notebook / quantidade 1 / retirada | Total = R$ 3000 (D9 e D11) | Total R$ 2.850,00, "Pedido de alto valor." |
| CT06 | Notebook / quantidade 1 / expresso | Total 3060, desconto antes da classificação (D9 e D11) | Total R$ 2.907,00, "Pedido de alto valor." |

---

## Resultados dos Testes


### Antes da correção

| Teste | Entrada | Resultado Esperado | Resultado Obtido | Situação |
|---|---|---|---|---|
| CT01 | Mouse / quantidade 0 / normal | "Quantidade inválida." | "Pedido calculado com sucesso.", subtotal R$ 0,00, frete R$ 30,00, total R$ 30,00 | Falhou |
| CT02 | Teclado / quantidade 10 / retirada | Pedido aceito, total R$ 1.425,00 | "Quantidade indisponível em estoque." | Falhou |
| CT03 | Mouse / quantidade 5 / retirada | Desconto R$ 20,00, total R$ 380,00 | Desconto R$ 0,00, total R$ 400,00 | Falhou |
| CT04 | Mouse / quantidade 10 / SENAI10 / retirada | Desconto exibido R$ 120,00, total R$ 680,00 | Desconto exibido R$ 80,00, total R$ 680,00 | Falhou |
| CT05 | Notebook / quantidade 1 / retirada | Total R$ 2.850,00, "Pedido de alto valor." | Total R$ 3.000,00, "Pedido de alto valor." | Falhou |
| CT06 | Notebook / quantidade 1 / expresso | Total R$ 2.907,00, "Pedido de alto valor." | Total R$ 2.907,00, "Pedido calculado com sucesso." | Falhou |


### Depois da correção

| Teste | Entrada | Resultado Esperado | Resultado Obtido | Situação |
|---|---|---|---|---|
| CT01 | Mouse / quantidade 0 / normal | "Quantidade inválida." | "Quantidade inválida." | Passou |
| CT02 | Teclado / quantidade 10 / retirada | Pedido aceito, total R$ 1.425,00 | Subtotal R$ 1.500,00, desconto R$ 75,00, frete R$ 0,00, total R$ 1.425,00 | Passou |
| CT03 | Mouse / quantidade 5 / retirada | Desconto R$ 20,00, total R$ 380,00 | Subtotal R$ 400,00, desconto R$ 20,00, total R$ 380,00 | Passou |
| CT04 | Mouse / quantidade 10 / SENAI10 / retirada | Desconto exibido R$ 120,00, total R$ 680,00 | Subtotal R$ 800,00, desconto R$ 120,00, total R$ 680,00 | Passou |
| CT05 | Notebook / quantidade 1 / retirada | Total R$ 2.850,00, "Pedido de alto valor." | Desconto de alto valor R$ 150,00, total R$ 2.850,00, "Pedido de alto valor." | Passou |
| CT06 | Notebook / quantidade 1 / expresso | Total R$ 2.907,00, "Pedido de alto valor." | Desconto de alto valor R$ 153,00, total R$ 2.907,00, "Pedido de alto valor." | Passou |

---

## Análise dos Resultados

### ERRO 1

- **Nível**: Fácil (valores-limite)
- **Trecho do código**:
  ```js
  if (qtd < 0) {
  ```
- **Comportamento esperado**: Que ele só aceitasse inteiros que fossem maiores que zero. O zero, campo vazio e decimais devem ser recusados.
- **Dados utilizados no teste**: Mouse, quantidade 0, sem cupom, frete normal.
- **Caminho percorrido**: D6 falso (`0 < 0`), D7 falso (`0 >= 20`), D5 falso (`0 >= 500`), D8 falso, D9 falso, D10 falso, D11 falso.
- **Resultado esperado**: "Quantidade inválida."
- **Resultado que foi obtido**: "Pedido calculado com sucesso", subtotal R$ 0,00, frete R$ 30,00 e total R$ 30,00.
- **Erro identificado**: o zero é o primeiro valor inválido, mas `0 < 0` é falso e ele passa. O campo vazio vira 0 e o decimal (2,5) também passa, porque o código não verifica se o número é inteiro. 
- **Correção realizada**:
  ```js
  if (!Number.isInteger(qtd) || qtd <= 0) {
  ```
- **Resultado após a correção**: "Quantidade inválida." para 0, vazio e 2,5.

**Fluxograma:**

```
 ( INÍCIO )
     |
     v
 /Mouse, qtd = 0, normal/
     |
     v
 < qtd < 0 ? >
   0 < 0 = FALSO   <-- ERRO: o zero deveria ser barrado
     |
     v
 [subtotal = 0, frete = 30]
 [total = 0 - 0 + 30 = 30]
     |
     v
 /Sucesso, total R$ 30,00/
     |
     v
 ( FIM )
```

---

### ERRO 2

- **Nível:** Fácil (valores-limite)
- **Trecho do código:**
  ```js
  if (qtd >= estoque[produtoSelecionado]) {
  ```
- **Comportamento esperado:** se existem 10 teclados em estoque, é possível comprar os 10.
- **Dados utilizados no teste:** Teclado (estoque 10), quantidade 10, sem cupom, retirada.
- **Caminho percorrido:** D6 falso (`10 < 0`), D7 verdadeiro (`10 >= 10`), o programa mostra a mensagem e encerra com `return`, sem calcular nada.
- **Resultado esperado:** pedido aceito, subtotal 1.500,00, desconto 75,00 e total 1.425,00.
- **Resultado obtido:** "Quantidade indisponível em estoque."
- **Erro identificado:** o `>=` bloqueia justamente o pedido que usa todo o estoque. O correto é `>`. Com isso, o notebook (estoque 5) nunca poderia ser comprado em 5 unidades.
- **Correção realizada:**
  ```js
  if (qtd > estoque[produtoSelecionado]) {
  ```
- **Resultado após a correção:** quantidade 10 é aceita (total R$ 1.425,00) e quantidade 11 continua bloqueada.

**Fluxograma:**

```
 ( INÍCIO )
     |
     v
 /Teclado, qtd = 10, retirada/
     |
     v
 < qtd >= estoque ? >
   10 >= 10 = VERDADEIRO   <-- ERRO: no limite deveria ser FALSO
     |
     v
 /Quantidade indisponível em estoque/
     |
     v
 [return: nada é calculado]
     |
     v
 ( FIM )
```

---

### ERRO 3

- **Nível:** Médio (valores-limite e cobertura de decisões)
- **Trecho do código:**
  ```js
  if (qtd > 5) {
    total = total - subtotal * 0.05;
  }
  ```
- **Comportamento esperado:** o desconto de 5% vale a partir de 5 unidades.
- **Dados utilizados no teste:** Mouse, quantidade 5, sem cupom, retirada.
- **Caminho percorrido:** D6 falso, D7 falso (`5 >= 20`), subtotal 400, desconto 0, frete 0, total 400. D8 falso (`5 > 5`), então o desconto não é aplicado. D9, D10 e D11 falsos, resultado "Sucesso".
- **Resultado esperado:** desconto de R$ 20,00 e total de R$ 380,00.
- **Resultado obtido:** total de R$ 400,00, sem desconto.
- **Erro identificado:** o `>` deixa de fora exatamente a quantidade que abre a faixa de desconto. O certo é `>=`.
- **Correção realizada:**
  ```js
  const QTD_MINIMA_DESCONTO = 5;

  if (qtd >= QTD_MINIMA_DESCONTO) {
    desconto += subtotal * 0.05;
  }
  ```
- **Resultado após a correção:** 5 unidades, total R$ 380,00. Com 4 unidades continua sem desconto.

**Fluxograma:**

```
 ( INÍCIO )
     |
     v
 /Mouse, qtd = 5, retirada/
     |
     v
 [subtotal = 400, frete = 0]
 [total = 400]
     |
     v
 < qtd > 5 ? >
   5 > 5 = FALSO   <-- ERRO: 5 unidades deveria ter desconto
     |
     v
 /Sucesso, total R$ 400,00 (sem desconto)/
     |
     v
 ( FIM )
```

---

### ERRO 4

- **Nível:** Médio (rastreamento de variáveis)
- **Trecho do código:**
  ```js
  const desconto = calcularDesconto(subtotal, codigo);
  let total = subtotal - desconto + valorFrete;

  if (qtd > 5) {
    total = total - subtotal * 0.05;
  }
  ```
- **Comportamento esperado:** o desconto mostrado na tela deve somar todos os descontos (cupom e quantidade), para que subtotal − desconto + frete dê o total exibido.
- **Dados utilizados no teste:** Mouse, quantidade 10, cupom SENAI10, retirada.
- **Caminho percorrido:** subtotal 800. D1 verdadeiro, `desconto` = 80. Frete 0, total = 800 − 80 = 720. D8 verdadeiro (`10 > 5`), total = 720 − 40 = 680. A variável `desconto` continua valendo 80.
- **Resultado esperado:** desconto de R$ 120,00 (80 + 40) e total de R$ 680,00.
- **Resultado obtido:** subtotal 800,00, desconto 80,00, frete 0,00 e total 680,00.
- **Erro identificado:** o desconto por quantidade altera só o `total` e nunca entra na variável `desconto`. A variável exibida e a usada no cálculo ficam diferentes, e a conta na tela não fecha (800 − 80 + 0 = 720, mas o total é 680).
- **Correção realizada:**
  ```js
  let desconto = calcularDesconto(subtotal, codigo);

  if (qtd >= QTD_MINIMA_DESCONTO) {
    desconto += subtotal * 0.05;
  }

  const totalParcial = subtotal - desconto + valorFrete;
  ```
- **Resultado após a correção:** desconto de R$ 120,00 e total de R$ 680,00. A conta fecha.

**Fluxograma:**

```
 ( INÍCIO )
     |
     v
 /Mouse, qtd = 10, SENAI10, retirada/
     |
     v
 [subtotal = 800]
 [desconto = 80]
 [total = 800 - 80 + 0 = 720]
     |
     v
 < qtd > 5 ? >
   10 > 5 = VERDADEIRO
     |
     v
 [total = 720 - 40 = 680]
 [desconto continua 80]   <-- ERRO: os 40 não entraram em "desconto"
     |
     v
 /Desconto 80, Total 680 (a conta não fecha)/
     |
     v
 ( FIM )
```

---

### ERRO 5

- **Nível:** Difícil (condições e limites em decisões que comparam o mesmo valor)
- **Trecho do código:**
  ```js
  if (total > 3000) {
    total = total * 0.95;
  }

  ...

  } else if (total >= 3000) {
    mensagem = "Pedido de alto valor.";
  }
  ```
- **Comportamento esperado:** o mesmo limite deve valer para o desconto extra e para a mensagem de alto valor (a partir de R$ 3.000).
- **Dados utilizados no teste:** Notebook, quantidade 1, sem cupom, retirada. O total fica em 3000.
- **Caminho percorrido:** subtotal 3000, desconto 0, frete 0, total 3000. D8 falso. D9 falso (`3000 > 3000`), então não há desconto extra. D10 falso. D11 verdadeiro (`3000 >= 3000`), mensagem "Alto valor".
- **Resultado esperado:** "Pedido de alto valor." com 5% extra, total de R$ 2.850,00.
- **Resultado obtido:** "Pedido de alto valor." com total de R$ 3.000,00.
- **Erro identificado:** duas decisões testam o mesmo limite com operadores diferentes (`>` em D9 e `>=` em D11). Com o total em 3000, o pedido é chamado de alto valor, mas não recebe o benefício. O erro só aparece nesse valor exato.
- **Correção realizada:**
  ```js
  const LIMITE_ALTO_VALOR = 3000;

  const altoValor = totalParcial >= LIMITE_ALTO_VALOR;
  const descontoAltoValor = altoValor ? totalParcial * 0.05 : 0;
  ```
- **Resultado após a correção:** "Pedido de alto valor." com desconto de alto valor R$ 150,00 e total R$ 2.850,00.

**Fluxograma:**

```
 ( INÍCIO )
     |
     v
 /Notebook, qtd = 1, retirada/
     |
     v
 [subtotal = 3000, frete = 0]
 [total = 3000]
     |
     v
 < total > 3000 ? >
   3000 > 3000 = FALSO   <-- ERRO: não aplica os 5% extras
     |
     v
 < total >= 3000 ? >
   3000 >= 3000 = VERDADEIRO   <-- mas aqui diz que é alto valor
     |
     v
 /Alto valor, total R$ 3.000,00 (sem os 5%)/
     |
     v
 ( FIM )
```

---

### ERRO 6

- **Nível:** Difícil (análise de caminhos e rastreamento de variáveis)
- **Trecho do código:**
  ```js
  if (total > 3000) {
    total = total * 0.95;
  }

  if (total <= 0) {
    mensagem = "Valor do pedido inválido.";
  } else if (total >= 3000) {
    mensagem = "Pedido de alto valor.";
  }
  ```
- **Comportamento esperado:** a classificação de alto valor deve olhar o valor do pedido antes do desconto extra.
- **Dados utilizados no teste:** Notebook, quantidade 1, sem cupom, frete expresso (R$ 60).
- **Caminho percorrido:** subtotal 3000, desconto 0, frete 60, total 3060. D8 falso. D9 verdadeiro (`3060 > 3000`), total = 3060 × 0,95 = 2907. D10 falso. D11 falso (`2907 >= 3000`), mensagem fica "Sucesso".
- **Resultado esperado:** "Pedido de alto valor." com total de R$ 2.907,00.
- **Resultado obtido:** "Pedido calculado com sucesso." com total de R$ 2.907,00.
- **Erro identificado:** ordem das operações. D9 reduz o `total` e logo depois D11 usa a mesma variável para classificar o pedido. Uma decisão altera o dado que a próxima vai ler. Todo pedido entre R$ 3.000,00 e cerca de R$ 3.157,89 recebe o desconto, cai abaixo de 3000 e perde a classificação.
- **Correção realizada:**
  ```js
  const altoValor = totalParcial >= LIMITE_ALTO_VALOR;
  const descontoAltoValor = altoValor ? totalParcial * 0.05 : 0;
  const total = totalParcial - descontoAltoValor;

  if (total <= 0) {
    mensagem = "Valor do pedido inválido.";
  } else if (altoValor) {
    mensagem = "Pedido de alto valor.";
  }
  ```
- **Resultado após a correção:** "Pedido de alto valor." com desconto de alto valor R$ 153,00 e total R$ 2.907,00.

**Fluxograma:**

```
 ( INÍCIO )
     |
     v
 /Notebook, qtd = 1, expresso/
     |
     v
 [subtotal = 3000, frete = 60]
 [total = 3000 - 0 + 60 = 3060]
     |
     v
 < total > 3000 ? >
   3060 > 3000 = VERDADEIRO
     |
     v
 [total = 3060 x 0,95 = 2907]   <-- o total muda aqui
     |
     v
 < total >= 3000 ? >
   2907 >= 3000 = FALSO   <-- ERRO: usa o total já reduzido
     |
     v
 /Sucesso, total R$ 2.907,00/
     |
     v
 ( FIM )
```

---

## Conclusão

A atividade mostrou que um código pode rodar sem erros visíveis e mesmo assim estar incorreto. As seis falhas não travavam o sistema e só foram detectadas na análise passo a passo das decisões e dos valores das variáveis. Os erros encontrados foram quatro de valor-limite (<, >= e >, além de um caso em que > e >= comparavam o mesmo valor), um de variável desatualizada, em que o desconto por quantidade não entrava na variável exibida, e um de ordem das operações, em que uma decisão alterava o dado usado pela seguinte. Esse último ficou claro com o fluxograma.
O teste de caixa branca foi importante porque permitiu escolher os testes a partir dos caminhos do código, e não por tentativa. Antes das correções, os seis casos de teste não apresentaram o resultado esperado. Depois das correções, os seis passaram e o comportamento correspondeu às regras definidas.

