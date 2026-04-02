# Projeto CI com act

## Objetivo

O objetivo deste projeto foi configurar e testar pipelines de CI/CD localmente utilizando a ferramenta `act`, simulando o funcionamento do GitHub Actions antes de realizar o envio para o repositório remoto.

---

## Estrutura do projeto

```bash
.github/
 └── workflows/
      ├── 01-hello-local.yml
      └── 02-pipeline-principal.yml

comandos.txt
README.md
```

---

## Validação da Parte 2

Foi criado um workflow básico para validar o funcionamento do `act`.

Comando utilizado:

```bash
act -l
```

Saída:

```bash
Stage  Job ID         Job name        Workflow name
0      teste-basico   teste-basico    01-hello-local
```

O job `teste-basico` está no Stage 0 pois não possui dependências.

Execução do workflow:

```bash
act -W .github/workflows/01-hello-local.yml | grep "|"
```

Saída:

```bash
| Iniciando testes locais
| v20.11.0
| Finalizado com sucesso!
```

---

## Validação da Parte 3

Para validar a estrutura do pipeline principal, foi utilizado:

```bash
act -l
```

Saída:

```bash
Stage  Job ID              Job name              Workflow name
0      setup-e-lint        setup-e-lint          pipeline-principal
1      testes-unitarios    testes-unitarios      pipeline-principal
1      scan-de-seguranca   scan-de-seguranca     pipeline-principal
2      build-e-deploy      build-e-deploy        pipeline-principal
```

Interpretação:

* Stage 0: `setup-e-lint`
* Stage 1: `testes-unitarios` e `scan-de-seguranca`
* Stage 2: `build-e-deploy`

Isso confirma que o DAG foi montado corretamente conforme solicitado.

---

## Validação da Parte 4

### Execução com imagem customizada

```bash
act -W .github/workflows/02-pipeline-principal.yml -P ubuntu-latest=node:slim
```

---

### Execução isolada do job de segurança

```bash
act -j scan-de-seguranca
```

---

### Comprovação de paralelismo

Trecho da saída do terminal:

```bash
[testes-unitarios] Starting container
[scan-de-seguranca] Starting container

[testes-unitarios] | Rodando testes unitários...
[scan-de-seguranca] | Procurando vulnerabilidades...
```

Os dois jobs iniciam ao mesmo tempo e suas saídas aparecem intercaladas.

Isso demonstra que foram executados em paralelo, cada um em um container separado.

---

## comandos.txt

O arquivo foi gerado com o comando:

```bash
history 50 > comandos.txt
```

Ele contém os principais comandos utilizados durante o desenvolvimento, incluindo:

* `act -l`
* `act -n`
* execução com imagem customizada
* execução de job isolado

---

## Conclusão

Foi possível validar todo o pipeline localmente utilizando o `act`, garantindo que o fluxo de CI/CD funcione corretamente antes de ser executado no GitHub Actions.
