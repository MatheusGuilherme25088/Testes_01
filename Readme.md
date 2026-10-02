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

## 1. Contextualização sobre Teste de Caixa Branca

O teste de caixa branca analisa a **estrutura interna do código**: os `if`, os operadores, a ordem dos cálculos e os caminhos que o programa pode seguir. Em vez de olhar só a entrada e a saída, escolho entradas que obriguem o programa a passar por cada decisão. Assim é possível achar falhas que o uso comum não mostra, principalmente nos valores-limite.

O sistema analisado é uma loja com três produtos. O usuário escolhe produto, quantidade, cupom e frete, e o `script.js` calcula o valor final do pedido. O código tem seis erros de lógica colocados de propósito (2 fáceis, 2 médios e 2 difíceis).

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

## 2. Análise das Estruturas de Decisão

O código usa só `if`. O único operador lógico é o `&&` do cupom SENAI20.

| # | Onde | Condição | Verdadeiro | Falso |
|---|---|---|---|---|
| D1 | `calcularDesconto` | `codigo === "SENAI10"` | desconto de 10% | vai para D2 |
| D2 | `calcularDesconto` | `codigo === "SENAI20" && subtotal >= 1000` | desconto de 20% | sem desconto |
| D3 | `calcularFrete` | `tipo === "retirada"` | frete 0 | vai para D4 |
| D4 | `calcularFrete` | `tipo === "expresso"` | frete 60 | vai para D5 |
| D5 | `calcularFrete` | `subtotal >= 500` | frete 0 | frete 30 |
| D6 | `finalizarPedido` | `qtd < 0` | "Quantidade inválida" | vai para D7 |
| D7 | `finalizarPedido` | `qtd >= estoque` | "Indisponível" | segue o cálculo |
| D8 | `finalizarPedido` | `qtd > 5` | aplica 5% | não aplica |
| D9 | `finalizarPedido` | `total > 3000` | aplica 5% extra | não aplica |
| D10 | `finalizarPedido` | `total <= 0` | "Valor inválido" | vai para D11 |
| D11 | `finalizarPedido` | `total >= 3000` | "Alto valor" | "Sucesso" |

---

## 3. Fluxograma do Exemplo

Legenda: `( )` início/fim, `[ ]` processo, `< >` decisão, `/ /` entrada/saída.

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

## 4. Casos de Teste

| Identificação | Entrada (produto, qtd, cupom, frete) | Condição / Caminho | Resultado esperado |
|---|---|---|---|
| CT01 | Mouse, 0, sem cupom, normal | D6 no limite (qtd = 0) | "Quantidade inválida." |
| CT02 | Teclado, 10, sem cupom, retirada | D7 no limite (qtd = estoque) | Aceito, total R$ 1.425,00 |
| CT03 | Mouse, 5, sem cupom, retirada | D8 no limite (qtd = 5) | Desconto R$ 20,00, total R$ 380,00 |
| CT04 | Mouse, 10, SENAI10, retirada | D1 verdadeiro e D8 verdadeiro | Desconto R$ 120,00, total R$ 680,00 |
| CT05 | Notebook, 1, sem cupom, retirada | D9 e D11 com total = 3000 | Total R$ 2.850,00, "Pedido de alto valor." |
| CT06 | Notebook, 1, sem cupom, expresso | D9 verdadeiro, depois D11 | Total R$ 2.907,00, "Pedido de alto valor." |
| CT07 | Teclado, 10, SENAI20, retirada | D2 verdadeiro (subtotal 1500) | Desconto R$ 375,00, total R$ 1.125,00 |
| CT08 | Mouse, 10, SENAI20, normal | D2 falso (subtotal 800) | Cupom ignorado, desconto R$ 40,00, total R$ 760,00 |
| CT09 | Mouse, 3, sem cupom, normal | D5 falso (subtotal 240) | Frete R$ 30,00, total R$ 270,00 |
| CT10 | Teclado, 4, sem cupom, normal | D5 verdadeiro (subtotal 600) | Frete R$ 0,00, total R$ 600,00 |

CT01 a CT06 são os testes dos seis erros. CT07 a CT10 cobrem o cupom SENAI20 e as faixas de frete.

---

## 5. Resultados dos Testes

Os testes foram testados no Node.js com a mesma lógica do `script.js`, primeiro no código original e depois no corrigido.

| Teste | Resultado esperado | Obtido (original) | Situação | Obtido (corrigido) | Situação |
|---|---|---|---|---|---|
| CT01 | Quantidade inválida | "Sucesso", total R$ 30,00 | Falhou | "Quantidade inválida." | Passou |
| CT02 | Aceito, total 1.425,00 | "Indisponível em estoque" | Falhou | Total 1.425,00 | Passou |
| CT03 | Desconto 20,00, total 380,00 | Desconto 0,00, total 400,00 | Falhou | Desconto 20,00, total 380,00 | Passou |
| CT04 | Desconto 120,00, total 680,00 | Desconto 80,00, total 680,00 | Falhou | Desconto 120,00, total 680,00 | Passou |
| CT05 | Total 2.850,00, alto valor | Total 3.000,00, alto valor | Falhou | Total 2.850,00, alto valor | Passou |
| CT06 | Total 2.907,00, alto valor | Total 2.907,00, "sucesso" | Falhou | Total 2.907,00, alto valor | Passou |
| CT07 | Desconto 375,00, total 1.125,00 | "Indisponível em estoque" | Falhou | Desconto 375,00, total 1.125,00 | Passou |
| CT08 | Desconto 40,00, total 760,00 | Desconto 0,00, total 760,00 | Falhou | Desconto 40,00, total 760,00 | Passou |
| CT09 | Frete 30,00, total 270,00 | Frete 30,00, total 270,00 | Passou | Frete 30,00, total 270,00 | Passou |
| CT10 | Frete 0,00, total 600,00 | Frete 0,00, total 600,00 | Passou | Frete 0,00, total 600,00 | Passou |

---

## 6. Análise dos Resultados

### ERRO 1

- Nível: Fácil (valores-limite)
- Trecho do código:
  ```js
  if (qtd < 0) {
  ```
- Comportamento esperado: Que só aceitasse inteiros que fossem maiores que zero. O zero, campo vazio e decimais devem ser recusados.

- Dados utilizados no teste: Mouse, quantidade 0, sem cupom, frete normal.

- Caminho percorrido: D6 falso (`0 < 0`), D7 falso (`0 >= 20`), D5 falso (`0 >= 500`), D8 falso, D9 falso, D10 falso, D11 falso.

- Resultado esperado: "Quantidade inválida."

- Resultado que foi obtido: "Pedido calculado com sucesso", subtotal R$ 0,00, frete R$ 30,00 e total R$ 30,00.

- Erro identificado: o zero é o primeiro valor inválido, mas `0 < 0` é falso e ele passa. O campo vazio vira 0 e o decimal (2,5) também passa, porque o código não verifica se o número é inteiro. O cliente pagaria frete sem comprar nada.

- Correção realizada:
  ```js
  if (!Number.isInteger(qtd) || qtd <= 0) {
  ```

- Resultado após a correção: "Quantidade inválida." para 0, vazio e 2,5.

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

### Comparação entre o comportamento anterior e o posterior

| Erro | Nível | Antes | Depois |
|---|---|---|---|
| 1 | Fácil | Aceita quantidade 0, vazia ou decimal e cobra frete | Recusa 0, vazio e decimais |
| 2 | Fácil | Bloqueia o pedido igual ao estoque | Aceita até o estoque e bloqueia acima |
| 3 | Médio | 5 unidades sem desconto | Desconto a partir de 5 unidades |
| 4 | Médio | Desconto mostrado não fecha com o total | Desconto mostrado soma cupom e quantidade |
| 5 | Difícil | `>` e `>=` divergem em 3000 | Mesmo limite nas duas decisões |
| 6 | Difícil | Perde "alto valor" depois do desconto | Classificação feita antes do desconto |

### Cobertura

- Todas as decisões (D1 a D11) foram executadas pelos dois lados, exceto D10 verdadeiro (`total <= 0`). Depois da correção do Erro 1 ele não é alcançável pela interface, pois com quantidade ≥ 1 o subtotal é sempre positivo.
- Valores-limite testados: 0 (Erro 1), 10 e 11 (Erro 2), 4 e 5 (Erro 3), 3000 e 3060 (Erros 5 e 6).
- A condição composta do SENAI20 foi testada com subtotal acima e abaixo de R$ 1.000 (CT07 e CT08).

---

## 7. Conclusão

A atividade mostrou que um código pode rodar sem erros visíveis e mesmo assim calcular errado. Nenhuma das seis falhas travava o sistema, e elas só apareceram quando li cada decisão, testei os valores nos limites e acompanhei as variáveis passo a passo.

Quatro erros eram de valor-limite (`<`, `>=`, `>` e `>` contra `>=`), um era de variável desatualizada (o desconto por quantidade que não entrava em `desconto`) e um era de ordem das operações (uma decisão alterando o dado que a seguinte lê). Os dois últimos são difíceis de achar só olhando entrada e saída, e os fluxogramas ajudaram a ver onde o caminho real saía do esperado.

Depois das correções, os dez casos de teste passaram e o resultado correspondeu ao comportamento esperado. Isso mostra a importância do teste de caixa branca: ele permite escolher os testes a partir dos caminhos do código.

---

## Anexo – Código corrigido

O arquivo completo é o [`scriptarrumado.js`](./scriptarrumado.js). A função `finalizarPedido` depois das seis correções:

```js
const LIMITE_ALTO_VALOR = 3000;
const QTD_MINIMA_DESCONTO = 5;

function finalizarPedido() {
  const produtoSelecionado = produto.value;
  const qtd = Number(quantidade.value);
  const codigo = cupom.value.trim().toUpperCase();

  // ERRO 1
  if (!Number.isInteger(qtd) || qtd <= 0) {
    resultado.innerHTML = "<p>Quantidade inválida.</p>";
    return;
  }

  // ERRO 2
  if (qtd > estoque[produtoSelecionado]) {
    resultado.innerHTML = "<p>Quantidade indisponível em estoque.</p>";
    return;
  }

  const subtotal = precos[produtoSelecionado] * qtd;
  let desconto = calcularDesconto(subtotal, codigo);

  // ERRO 3 e ERRO 4
  if (qtd >= QTD_MINIMA_DESCONTO) {
    desconto += subtotal * 0.05;
  }

  const valorFrete = calcularFrete(frete.value, subtotal);
  const totalParcial = subtotal - desconto + valorFrete;

  // ERRO 5 e ERRO 6
  const altoValor = totalParcial >= LIMITE_ALTO_VALOR;
  const descontoAltoValor = altoValor ? totalParcial * 0.05 : 0;
  const total = totalParcial - descontoAltoValor;

  let mensagem = "Pedido calculado com sucesso.";

  if (total <= 0) {
    mensagem = "Valor do pedido inválido.";
  } else if (altoValor) {
    mensagem = "Pedido de alto valor.";
  }

  resultado.innerHTML = `
    <p>${mensagem}</p>
    <p>Subtotal: R$ ${subtotal.toFixed(2)}</p>
    <p>Desconto: R$ ${desconto.toFixed(2)}</p>
    ${altoValor ? `<p>Desconto alto valor: R$ ${descontoAltoValor.toFixed(2)}</p>` : ""}
    <p>Frete: R$ ${valorFrete.toFixed(2)}</p>
    <p class="total">Total: R$ ${total.toFixed(2)}</p>
  `;
}
```