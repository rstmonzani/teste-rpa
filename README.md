# Teste Técnico — RPA + Full Stack

**Duração do teste:** 70 minutos  
**Stack:** Python + Playwright. Na Parte 3, use o framework web de sua preferência (sugerimos FastAPI).

## Contexto

O time de Operações precisa de um **Monitor de Catálogo** com três peças:

1. um robô que alimenta um sistema legado a partir de uma planilha;
2. uma rotina que coleta diariamente o catálogo completo de um fornecedor;
3. uma API que disponibiliza esses dados para outros sistemas.

## Como você será avaliado

- **Não esperamos que todos concluam tudo.** Siga a ordem das partes: uma parte bem feita vale mais do que três pela metade.
- Avaliamos funcionamento, qualidade do código, robustez e a forma como você explica suas decisões.

## Regras

- Mantenha a tela compartilhada durante todo o teste e, se possível, comente suas decisões enquanto desenvolve.
- É permitido consultar documentação oficial e buscadores.
- É permitido instalar bibliotecas auxiliares. Não é permitido usar pacotes ou scripts prontos que resolvam o desafio.
- Use Playwright para a automação web. Se optar por outra abordagem em alguma etapa, explique o motivo.
- Uso de assistentes de IA: **[NÃO PERMITIDO]**

---

## Parte 1 — Sistema legado (≈ 15 min)

**Site:** <https://rpachallenge.com/>

1. Pelo próprio robô, baixe a planilha pelo botão **Download Excel** e leia os registros.
2. Clique em **Start** e cadastre cada registro no formulário, clicando em **Submit** após cada um.
3. Ao final, converta a mensagem de resultado exibida na tela em um JSON com **taxa de acerto, campos corretos, total de campos e tempo**. Salve o JSON em arquivo e salve também um print da tela.

> **Atenção:** a posição dos campos muda a cada envio, assim como alguns atributos dos elementos. O robô precisa preencher o campo correto em todas as rodadas.

**Resultado esperado:** taxa de acerto de 100%.

---

## Parte 2 — Coleta do catálogo completo (≈ 30 min)

**Site:** <https://books.toscrape.com/>

Colete **todos os livros do catálogo**. Para cada livro, colete os seguintes dados:

- UPC
- título completo
- categoria
- preço (como número)
- avaliação (de 1 a 5)
- quantidade em estoque (como número)
- URL da página do livro

### Requisitos da rotina

1. **Persistência:** grave os dados em SQLite, sem duplicar livros.
2. **Desempenho:** a coleta deve ser feita em paralelo, com nível de concorrência configurável (padrão: 5). Exiba o tempo total da execução ao final.
3. **Retomada:** se a execução for interrompida (Ctrl+C), a próxima execução não deve coletar novamente os livros já salvos.
4. **Resiliência:** a falha em um livro não pode interromper a execução. Tente novamente até 3 vezes e registre os livros que falharem definitivamente.
5. **Testabilidade:** aceite um parâmetro `--limite N` para coletar apenas N livros.
6. **Saídas:**
   - exporte `livros.csv`;
   - imprima um resumo com:
     - total de livros e de categorias
     - preço médio
     - livro mais caro
     - quantidade de livros com 5 estrelas
     - estoque total
     - as 5 categorias com mais livros
     - quantos livros foram coletados e quantos falharam nesta execução

---

## Parte 3 — API (≈ 25 min)

Exponha os dados coletados na Parte 2:

| Método | Rota                 | Descrição                                                                                                                                                     |
| ------ | -------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| GET    | `/livros`            | Lista livros com filtros opcionais `categoria`, `avaliacao_min` e `preco_max`, e paginação por `pagina` e `tamanho`. Retorna também o total de resultados.    |
| GET    | `/categorias/resumo` | Para cada categoria: quantidade de livros, preço médio e estoque total.                                                                                       |
| POST   | `/execucoes`         | Dispara a coleta da Parte 2 em segundo plano (aceita `limite` opcional) e retorna um identificador. Se já houver uma execução em andamento, responde **409**. |
| GET    | `/execucoes/{id}`    | Retorna o status da execução (rodando, concluída ou erro), os horários e a quantidade de livros novos.                                                        |

### Requisitos

- Valide os parâmetros de entrada (ex.: `avaliacao_min` entre 1 e 5).
- A API deve continuar respondendo normalmente enquanto a coleta está em andamento.

---

## Bônus (opcional)

- Página web simples que consome a API: tabela com filtros e botão "Executar coleta" com o status atualizado na tela
- Testes automatizados
- README ou Dockerfile com instruções de execução
- Histórico de execuções persistido no banco

---

## Entrega

Ao final, demonstre com a tela compartilhada:

1. a Parte 1 rodando;
2. uma coleta sendo interrompida e depois retomada;
3. a API respondendo.

Depois, envie o código (zip ou link do repositório) para **[E-MAIL]**.

Boa sorte!
