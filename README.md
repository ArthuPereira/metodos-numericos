## Documentação de Uso (`./main`)

### 1. Compilação e Atalhos (`Makefile`)

Requisitos: compilador `g++` com suporte a **C++17** (e `python3` com `matplotlib` + `numpy` apenas se for gerar os gráficos).

```bash
make            # Compila o projeto e gera o binário ./main
make run        # Compila (se necessário) e executa ./main
make csv        # Gera apenas os CSVs padrão na pasta saida/
make graficos   # Gera os CSVs e plota os gráficos PNG em saida/
make clean      # Remove o executável ./main
```

---

### 2. Modos de Execução

#### Sinopse
```bash
./main [--d0 <valor>]
./main --padrao
./main --csv [pasta] [a] [d0] [e]
./main --help
```

#### 2.1. Execução Interativa Padrão (`./main`)
Ao rodar `./main` sem flags, o programa executa duas etapas em sequência:

1. **Parte 1 — Teste Padrão (item d):**
   Executa automaticamente `teste_padrao()` com `a = 1`, `d0 = 0.5` (`d1 = 0.6` na Secante) e `e = 10^-4`, exibindo o Quadro Resposta, o Quadro Comparativo e salvando os CSVs base em `saida/`.
2. **Parte 2 — Testes Modificados (itens e, f, g):**
   Solicita no terminal:
   - **`n`**: quantidade de valores de `a` que deseja testar (digite `0` para encerrar e exibir direto a análise de variação de `a`);
   - **`a[1..n]`**: cada valor de amplitude `a`;
   - **`e`**: precisão desejada (`e > 0`, ex.: `1e-4` ou `0.0001`);
   - **`d0` *(opcional)***: pressione **Enter** para manter o padrão `0.5` (`d1 = 0.6` na Secante) ou informe um novo chute inicial (nesse caso, a Secante adota `d1 = d0 + 0.1`).

#### 2.2. Execução via Redirecionamento / Pipe (Entrada Automatizada)
Segue estritamente a ordem `n`, valores de `a` e `e` pedida no enunciado (sem aguardar o prompt opcional de `d0`):

```bash
# Passando n=4, valores de a={0.3, 0.8, 1.0, 1.8} e precisão e=1e-4
printf "4\n0.3 0.8 1.0 1.8\n1e-4\n" | ./main

# Ou lendo de um arquivo de entrada
./main < entrada.txt
```

Para alterar a aproximação inicial `d0` mesmo via pipe/arquivo, utilize `--d0`:
```bash
printf "3\n0.5 1.0 1.5\n1e-4\n" | ./main --d0 0.8
```

#### 2.3. Apenas Teste Padrão (`--padrao`)
Roda somente o caso padrão (`a = 1`, `d0 = 0.5`, `e = 10^-4`) seguido da análise completa de variação de `a` e encerra sem pedir entrada do usuário:
```bash
./main --padrao
```

#### 2.4. Geração Direta de CSVs (`--csv`)
Gera os arquivos CSV sem imprimir os relatórios de terminal. Todos os argumentos após `--csv` são opcionais (padrão: `saida 1 0.5 1e-4`):
```bash
./main --csv                       # Salva em saida/ com parâmetros padrão
./main --csv saida 1.5 0.5 1e-5    # Salva em saida/ com a=1.5, d0=0.5, e=1e-5
```

---

### 3. Leitura das Saídas e Arquivos Gerados

#### Legenda dos Relatórios no Terminal
- **`SEGURO (d <= 0.7 cm)`**: raiz válida encontrada dentro do limite seguro.
- **`AMPLIFICA (d > 0.7 cm)`**: raiz válida acima de `0.7 cm` (movimento amplifica).
- **`FALSA CONV. (|f| alto)` / `!`**: o método parou porque `|Δd| < e`, mas `|f(d)| > 10^-2` (comum para `a < 0`, onde não há raiz real).
- **`NÃO CONVERGIU` / `*`**: atingiu o número máximo de iterações, derivada nula ou divergência.

#### Arquivos CSV Criados em `saida/`

| Arquivo | Quando é gerado | Conteúdo |
| :--- | :--- | :--- |
| `parametros.csv` | Padrão / `--csv` | Valores de `a, d0, d1, e` usados na rodada de referência. |
| `historico.csv` | Padrão / `--csv` | Iterações passo a passo (`k = 1..n`) de cada método — lido por `ferramentas/grafico_iteracoes.py`. |
| `resumo.csv` | Padrão / `--csv` | Resultado final dos 3 métodos para os parâmetros da rodada. |
| `varredura_a.csv` | Padrão / `--csv` | Varredura de `a` em `[-1.0, 3.0]` com passo `0.02`. |
| `varredura_d0.csv` | Padrão / `--csv` | Varredura de `d0` em `[-2.0, 6.0]` com passo `0.02`. |
| `resultados_execucao.csv` | Parte 2 (`n >= 1`) | Todos os resultados dos `n` valores de `a` digitados pelo usuário. |
