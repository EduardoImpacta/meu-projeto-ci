# meu-projeto-ci

# Laboratório Prático — GitHub Actions com act

## Objetivo

O objetivo desta atividade foi criar e testar um pipeline de CI/CD localmente utilizando a ferramenta `act`, simulando o funcionamento do GitHub Actions antes de realizar o push para o repositório remoto.

---

## Estrutura do projeto

Durante o desenvolvimento, foi criada a seguinte estrutura:

```bash id="b1c9x2"
.github/
 └── workflows/
      ├── 01-hello-local.yml
      └── 02-pipeline-principal.yml

comandos.txt
README.md
```

---

## Parte 2 — Workflow básico

Foi criado um workflow simples para validar se o `act` estava funcionando corretamente.

O comando utilizado para listar os jobs foi:

```bash id="a82ksl"
act -l
```

Saída obtida:

```bash id="k19xla"
Stage  Job ID         Job name        Workflow name
0      teste-basico   teste-basico    01-hello-local
```

O job aparece no **Stage 0**, pois não depende de nenhum outro.

Para executar o workflow e visualizar apenas as mensagens:

```bash id="p3ls92"
act -W .github/workflows/01-hello-local.yml | grep "|"
```

Saída:

```bash id="x92lsd"
| Iniciando testes locais
| v20.11.0
| Finalizado com sucesso!
```

---

## Parte 3 — Pipeline principal

Foi criado um pipeline com quatro jobs e dependências entre eles.

Para validar a estrutura, utilizei:

```bash id="q82ksl"
act -l
```

Resultado:

```bash id="z82ksl"
Stage  Job ID              Job name              Workflow name
0      setup-e-lint        setup-e-lint          pipeline-principal
1      testes-unitarios    testes-unitarios      pipeline-principal
1      scan-de-seguranca   scan-de-seguranca     pipeline-principal
2      build-e-deploy      build-e-deploy        pipeline-principal
```

Interpretação:

* O job `setup-e-lint` inicia o fluxo
* Os jobs `testes-unitarios` e `scan-de-seguranca` rodam depois dele e ao mesmo tempo
* O job `build-e-deploy` só executa depois que os dois anteriores terminam

---

## Parte 4 — Execuções e testes

### Execução com imagem customizada

Para rodar o pipeline usando uma imagem diferente:

```bash id="w82ksl"
act -W .github/workflows/02-pipeline-principal.yml -P ubuntu-latest=node:slim
```

---

### Execução isolada de um job

Para rodar apenas o job de segurança:

```bash id="e82ksl"
act -j scan-de-seguranca
```

---

### Paralelismo

Durante a execução completa, foi possível perceber que dois jobs rodaram ao mesmo tempo.

Trecho da saída:

```bash id="r82ksl"
[testes-unitarios] Starting container
[scan-de-seguranca] Starting container

[testes-unitarios] | Rodando testes unitários...
[scan-de-seguranca] | Procurando vulnerabilidades...
```

Isso mostra que:

* Os dois jobs iniciaram juntos
* Cada um rodou em um container diferente
* As mensagens aparecem intercaladas

Ou seja, o `act` conseguiu simular corretamente o paralelismo do GitHub Actions.

---

## comandos.txt

Foi gerado com:

```bash id="t82ksl"
history 50 > comandos.txt
```

Esse arquivo contém os principais comandos usados, incluindo:

* listagem de jobs (`act -l`)
* validação de sintaxe (`act -n`)
* execução com imagem customizada
* execução de job isolado

---

## Conclusão

Com essa atividade foi possível entender melhor como funciona a execução de pipelines no GitHub Actions e como utilizar o `act` para testar tudo localmente antes de enviar para o repositório.
