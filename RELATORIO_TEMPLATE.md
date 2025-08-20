# Relatório do Laboratório 2 - Chamadas de Sistema

---

## Exercício 1a - Observação printf() vs 1b - write()

### Comandos executados:
```bash
strace -e write ./ex1a_printf
strace -e write ./ex1b_write
```

### Análise

**1. Quantas syscalls write() cada programa gerou?**
- ex1a_printf: 9 syscalls
- ex1b_write: 7 syscalls

**2. Por que há diferença entre printf() e write()?**

```
O comando printf() é um comando de alto nível, ou seja, que não conversa diretamente com a máquina e que possui formatação e utiliza um buffer para otimizar o sistema, mas que pode variar, enquanto que o write() já é um comando de baixo nível, sem formatação, sem buffer e com mais controle sobre o que se quer fazer
```

**3. Qual implementação você acha que é mais eficiente? Por quê?**

```
Devido ao menor número de chamadas de sistema e por ser mais simples, o write() é mais eficiente
```

---

## Exercício 2 - Leitura de Arquivo

### Resultados da execução:
- File descriptor: 3
- Bytes lidos: 127

### Comando strace:
```bash
strace -e open,read,close ./ex2_leitura
```

### Análise

**1. Por que o file descriptor não foi 0, 1 ou 2?**

```
O file descriptor é 3, pois 0, 1 e 2 já estão reservados paras stdin (teclado), stdout (tela) e strderr (erro), então ele pega o próximo número para designar a abertura do arquivo
```

**2. Como você sabe que o arquivo foi lido completamente?**

```
Sabemos que o arquivo foi completamente lido, pois em nenhum momento a estrutura de condição "if" foi executada caso o número de bytes lidos fosse menor que 0.
```

---

## Exercício 3 - Contador com Loop

### Resultados (BUFFER_SIZE = 64):
- Linhas: 24 (esperado: 25)
- Caracteres: 1299
- Chamadas read(): 21
- Tempo: 0.000425 segundos

### Experimentos com buffer:

| Buffer Size | Chamadas read() | Tempo (s) |
|-------------|-----------------|-----------|
| 16          | 82              | 0.001835  |
| 64          | 21              | 0.000508  |
| 256         | 6               | 0.000352  |
| 1024        | 2               | 0.000189  |

### Análise

**1. Como o tamanho do buffer afeta o número de syscalls?**

```
Quanto maior o tamanho do buffer, menos syscalls o sistema precisa fazer
```

**2. Como você detecta o fim do arquivo?**

```
O fim do arquivo é detectado quando a função read() retorna 0, com a execução do strace
```

---

## Exercício 4 - Cópia de Arquivo

### Resultados:
- Bytes copiados: 1364
- Operações: 6
- Tempo: 0.000200 segundos
- Throughput: 6660.16 KB/s

### Verificação:
```bash
diff dados/origem.txt dados/destino.txt
```
Resultado: [X] Idênticos [ ] Diferentes

### Análise

**1. Por que devemos verificar que bytes_escritos == bytes_lidos?**

```
Para sabermos se o sistema realmente conseguiu ler a mesma quantidade de bytes escritos e nos dar um resultado preciso
```

**2. Que flags são essenciais no open() do destino?**

```
O_WRONLY, O_CREAT e O_TRUNC são flags essenciais para podermos copiar o conteúdo de um arquivo para outro
```

---

## Análise Geral

### Conceitos Fundamentais

**1. Como as syscalls demonstram a transição usuário → kernel?**

```
Ao chamar as syscalls no modo usuário, o kernel recebe e executa a operação chamada que retorna o seu resultado e muda novamente para o modo usuário
```

**2. Qual é o seu entendimento sobre a importância dos file descriptors?**

```
Os files descriptors são uma forma de identificar e gerenciar os recursos que o kernel controla
```

**3. Discorra sobre a relação entre o tamanho do buffer e performance:**

```
O buffer é um espaço na memória utilizado para acumular dados e enviá-los de uma só vez, em vez de chamar o sistema operacional toda vez que é passado uma informação. Essa relação melhora o desempenho do sistema.
```

### Comparação de Performance

```bash
# Teste seu programa vs cp do sistema
time ./ex4_copia
time cp dados/origem.txt dados/destino_cp.txt
```

**Qual foi mais rápido?** O meu programa

**Por que você acha que foi mais rápido?**

```
Acredito que tenha sido mais rápido, pois no programa trabalhamos com comandos de baixo nível, com menos abstração.
```

---

## Entrega

Certifique-se de ter:
- [ ] Todos os códigos com TODOs completados
- [ ] Traces salvos em `traces/`
- [ ] Este relatório preenchido como `RELATORIO.md`

```bash
strace -e write -o traces/ex1a_trace.txt ./ex1a_printf
strace -e write -o traces/ex1b_trace.txt ./ex1b_write
strace -o traces/ex2_trace.txt ./ex2_leitura
strace -c -o traces/ex3_stats.txt ./ex3_contador
strace -o traces/ex4_trace.txt ./ex4_copia
```