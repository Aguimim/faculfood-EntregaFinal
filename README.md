<p align="center"><img src="assets/icon-192.png" width="120" alt="FaculFood"></p>

<h1 align="center">FaculFood</h1>

<p align="center">
Carolina Moura de Mattos, Tatiana Papanis, Guilherme Cano<br>
Pontifícia Universidade Católica do Rio de Janeiro<br>
Rio de Janeiro — Outubro de 2026
</p>

## Acesso rápido

**App online:** https://aguimim.github.io/faculfood-EntregaFinal/

**Vídeo de apresentação (2 min):** https://youtu.be/f7J-SdJABGg (cópia do arquivo no repositório: [`FaculFood-video-v3-2min.mp4`](FaculFood-video-v3-2min.mp4))

**Relatório em PDF:** [`docs/Relatorio-FaculFood.pdf`](docs/Relatorio-FaculFood.pdf)

**Documentos da entrega:** [Roteiro](docs/ROTEIRO.md) · [AI Log](docs/AI-LOG.md) · [Relatório Final](docs/RELATORIO-FINAL.md) (também reproduzidos nas seções 1, 2 e 3 abaixo)

### Contas de demonstração

A base de demonstração é criada automaticamente no primeiro acesso, então dá para testar os três perfis sem cadastro:

| Perfil | E-mail | Senha |
|---|---|---|
| Cliente | demo@puc-rio.br | demo1234 |
| Restaurante (Cardeal Cafés & Lanches) | cardeallanches@gmail.com | cardeallanches123 |
| Gestor PUC-Rio | gestor@puc-rio.br | gestor2026 |

Fluxo sugerido para avaliar: entre como cliente, faça um pedido, saia, entre como o restaurante do pedido e avance o status no kanban, depois entre como gestor para ver os indicadores. Como os dados ficam no armazenamento local do navegador, os três perfis precisam ser testados no mesmo navegador.

## Como reproduzir do zero

O FaculFood é um site estático (HTML, CSS e JavaScript em um único `index.html`), sem build, sem dependências para instalar e sem servidor próprio.

**Opção 1: publicar a sua própria cópia online (GitHub Pages)**

1. Faça um fork deste repositório (botão "Fork" no topo da página).
2. No seu fork, vá em **Settings > Pages**.
3. Em "Build and deployment", escolha **Deploy from a branch**, branch **main**, pasta **/ (root)**, e clique em **Save**.
4. Em um ou dois minutos o app fica disponível em `https://SEU-USUARIO.github.io/faculfood-EntregaFinal/`.

**Opção 2: rodar localmente**

```bash
git clone https://github.com/Aguimim/faculfood-EntregaFinal.git
cd faculfood-EntregaFinal
python -m http.server 8000
```

Depois abra `http://localhost:8000` no navegador. Usar um servidor local (em vez de abrir o arquivo com duplo clique) é necessário para o service worker e a instalação como PWA funcionarem.

**Recursos opcionais com IA.** O assistente de gosto e a leitura de cardápio por imagem funcionam sem configuração, com lógica local e OCR (Tesseract.js). Para usar o Claude nessas funções, cole sua própria chave da API da Anthropic no Perfil (cliente) ou em Loja (restaurante). A chave fica salva apenas no navegador de quem a inseriu e nunca é enviada ao repositório.

**Estrutura do repositório**

| Caminho | Conteúdo |
|---|---|
| `index.html` | Aplicação completa (interface, lógica e base de demonstração) |
| `data/cardapios.json` | Cardápios estruturados (5 restaurantes reais do campus + afiliados fictícios marcados como exemplo) |
| `manifest.json`, `sw.js`, `assets/` | Configuração de PWA, cache offline e ícones |
| `docs/` | Relatório, AI Log, roteiros, jornada do app e avaliação com usuários |
| `apresentacoes/` | Slides do pitch e da sprint |

---

## Sumário

1. [Roteiro](#1-roteiro)
   - [1.1 Entendimento do problema](#11-entendimento-do-problema)
   - [1.2 Escopo](#12-escopo)
   - [1.3 Decisões técnicas](#13-decisões-técnicas)
   - [1.4 Decomposição das atividades](#14-decomposição-das-atividades)
   - [1.5 Critérios de aceite](#15-critérios-de-aceite)
   - [1.6 Estratégia de utilização de IA](#16-estratégia-de-utilização-de-ia)
2. [AI Log](#2-ai-log)
   - [2.1 Desenvolvimento da arquitetura](#21-desenvolvimento-da-arquitetura)
   - [2.2 Desenvolvimento do fluxo de pedidos](#22-desenvolvimento-do-fluxo-de-pedidos)
   - [2.3 Correção da lógica de restrições alimentares](#23-correção-da-lógica-de-restrições-alimentares)
   - [2.4 Leitura de cardápio por imagem](#24-leitura-de-cardápio-por-imagem)
   - [2.5 Ajustes de responsividade](#25-ajustes-de-responsividade)
   - [2.6 Correção do fluxo de autenticação](#26-correção-do-fluxo-de-autenticação)
3. [Relatório Final](#3-relatório-final)
   - [3.1 O que foi entregue?](#31-o-que-foi-entregue)
   - [3.2 O que ficou de fora?](#32-o-que-ficou-de-fora)
   - [3.3 Principais decisões técnicas](#33-principais-decisões-técnicas)
   - [3.4 Maior erro produzido pela IA](#34-maior-erro-produzido-pela-ia)
   - [3.5 Parte menos confiável da solução](#35-parte-menos-confiável-da-solução)
   - [3.6 Próximas prioridades](#36-próximas-prioridades)
   - [3.7 Ferramentas de IA utilizadas](#37-ferramentas-de-ia-utilizadas)

---

## 1. Roteiro

### 1.1 Entendimento do problema

O projeto FaculFood foi desenvolvido a partir do Caso 4 — Restaurantes integrados, cujo objetivo é reunir cardápios, preços e pedidos dos restaurantes em uma única solução. Diferentemente dos demais casos apresentados no desafio, este caso não disponibilizava uma base de dados pronta, fazendo com que a construção e organização dos dados também fizessem parte do desenvolvimento da solução.

A partir desse contexto, a equipe identificou uma dificuldade presente na experiência de alimentação no ambiente universitário: o aluno precisa consultar diferentes opções de restaurantes e seus respectivos cardápios para decidir o que consumir e realizar seu pedido. Paralelamente, os estabelecimentos precisam organizar os pedidos recebidos e acompanhar sua preparação.

Dessa forma, o FaculFood foi concebido como uma plataforma integrada de alimentação para o campus, concentrando em um mesmo ambiente a consulta aos restaurantes, a visualização dos cardápios, a escolha dos produtos, a realização do pedido e seu acompanhamento.

A solução também considera diferentes perfis de utilização: aluno, restaurantes e gestão. O aluno utiliza a plataforma para consultar os restaurantes e realizar pedidos; o restaurante utiliza a aplicação para disponibilizar seu cardápio e gerenciar os pedidos; e o gestor possui uma visão consolidada das informações da plataforma. A gestão seria uma forma da PUC administrar o seu espaço de infra estrutura para atender melhor a experiência universitária no campus.

### 1.2 Escopo

O escopo inicial foi definido considerando como objetivo principal a construção de um protótipo funcional que representasse o fluxo completo de utilização da plataforma.

Para o cliente, foram priorizadas as funcionalidades de cadastro e login, consulta aos restaurantes e cardápios, busca de produtos, recomendações, carrinho, uso de comando de voz, entendimento dos gostos do usuário, realização do pedido, acompanhamento do status e comunicação com o restaurante.

Para os restaurantes, foram consideradas as funcionalidades de cadastro e afiliação, gerenciamento dos produtos do cardápio, controle de disponibilidade, estoque, descontos e gerenciamento dos pedidos recebidos.

Também foi incluída uma funcionalidade de leitura de cardápios a partir de imagens por IA dando autonomia ao administrador do restaurante. O objetivo é permitir que o restaurante envie uma fotografia de seu cardápio e obtenha uma primeira estrutura dos produtos, preços e categorias, que posteriormente pode ser revisada antes da publicação.

Por fim, foi desenvolvido um perfil de gestor com visão PUC, responsável por visualizar informações consolidadas de restaurantes e clientes, acompanhar indicadores e realizar ações administrativas.

Algumas funcionalidades foram mantidas fora do escopo do protótipo, principalmente aquelas que exigiriam uma infraestrutura de produção, como banco de dados compartilhado, autenticação institucional real da PUC-Rio, sincronização entre diferentes dispositivos e confirmação automática de pagamentos.

### 1.3 Decisões técnicas

A principal decisão técnica foi desenvolver a aplicação utilizando HTML, CSS e JavaScript, permitindo que o sistema fosse executado diretamente pelo navegador e publicado de maneira simples no GitHub Pages.

Para o armazenamento dos dados durante o desenvolvimento, foi utilizado o armazenamento local do navegador. Essa escolha permitiu representar os fluxos de utilização sem a necessidade de implementar um servidor próprio dentro do prazo disponível para o desafio.

A estrutura dos cardápios foi organizada em arquivos de dados próprios do projeto, uma vez que o Caso 4 não disponibilizava uma base pronta.

Também foi adotada uma arquitetura de aplicação web responsiva e compatível com PWA, permitindo que a solução pudesse ser acessada tanto pelo navegador quanto instalada como aplicativo.

### 1.4 Decomposição das atividades

O desenvolvimento foi dividido em etapas relacionadas à construção da interface, organização dos dados e implementação dos principais fluxos.

Inicialmente, foi estruturada a interface geral da aplicação e os diferentes perfis de usuário. Em seguida, foram desenvolvidos os cardápios e o fluxo de consulta dos produtos.

Na sequência, foram implementados o carrinho, o checkout, os pedidos e o acompanhamento dos respectivos status através dos cartões kanban. Paralelamente, foram desenvolvidas as interfaces específicas dos restaurantes e do gestor.

Após a implementação dos principais fluxos, foram acrescentadas funcionalidades complementares, como leitura de cardápio por imagem, controle de estoque, descontos, avaliações, indicadores e instalação como PWA.

Por fim, foram realizados testes de funcionamento, responsividade e integração entre as diferentes etapas do fluxo.

### 1.5 Critérios de aceite

A solução foi considerada funcional quando foi possível executar o fluxo principal de utilização sem interrupções: acessar a aplicação, entrar como cliente, consultar os restaurantes e produtos, adicionar itens ao carrinho, realizar um pedido e acompanhar sua evolução.

Também foram considerados critérios de aceite o funcionamento do fluxo de recebimento e atualização dos pedidos pelo restaurante, a alteração dos estados do pedido, a disponibilização das informações administrativas ao gestor e o funcionamento das principais funcionalidades desenvolvidas.

Além disso, foram realizados testes em diferentes larguras de tela para verificar a responsividade da aplicação.

### 1.6 Estratégia de utilização de IA

A inteligência artificial foi utilizada ao longo de todo o processo de desenvolvimento, principalmente como ferramenta de apoio à programação, interpretação de requisitos, identificação de erros, criação de testes e revisão da solução.

O Claude foi utilizado de forma iterativa: a equipe apresentava o problema ou funcionalidade desejada, analisava o resultado produzido e realizava novas interações para corrigir ou aprimorar a implementação.

A equipe não considerou a saída da IA como automaticamente correta. As alterações propostas foram testadas e avaliadas antes de serem incorporadas ao projeto.

## 2. AI Log

Durante o desenvolvimento, foram registradas principalmente as interações com a inteligência artificial que resultaram em mudanças relevantes na arquitetura, nas funcionalidades ou na lógica do sistema, conforme orientado pelo edital.

### 2.1 Desenvolvimento da arquitetura

**Objetivo:** definir uma estrutura adequada para o desenvolvimento e publicação do protótipo.

**Contexto:** o projeto precisava resultar em um artefato online e replicável, com possibilidade de execução sem uma infraestrutura complexa.

**Instrução:** foi solicitado à IA que analisasse a proposta e sugerisse uma estrutura compatível com as restrições do projeto.

**Resultado:** foi adotada uma aplicação web utilizando HTML, CSS e JavaScript, com armazenamento local para os dados do protótipo.

**Validação:** a aplicação foi executada e posteriormente publicada para acesso online.

**Decisão:** manter uma arquitetura simples e de fácil reprodução durante o desafio.

### 2.2 Desenvolvimento do fluxo de pedidos

**Objetivo:** criar um fluxo que permitisse ao cliente realizar o pedido e ao restaurante acompanhar seu andamento.

**Contexto:** era necessário representar as diferentes etapas entre a realização e a conclusão do pedido.

**Instrução:** a IA foi utilizada para estruturar a lógica de atualização dos pedidos e seus respectivos estados.

**Resultado:** foi implementado um fluxo no qual o pedido passa por diferentes estados até ficar disponível para retirada.

**Validação:** o fluxo foi executado utilizando os perfis de cliente e restaurante.

**Decisão:** manter a atualização de status como elemento central da experiência do pedido.

### 2.3 Correção da lógica de restrições alimentares

**Objetivo:** melhorar a precisão das recomendações realizadas para o usuário.

**Contexto:** durante os testes, foi identificado que considerar somente o nome do prato poderia gerar uma recomendação inadequada quando informações relevantes estivessem descritas no subtítulo.

**Instrução:** a IA foi orientada a revisar a lógica de identificação das restrições alimentares.

**Resultado:** a lógica passou a considerar também informações complementares do item.

**Validação:** foram realizados testes com produtos que apresentavam ingredientes relevantes no subtítulo.

**Decisão:** incorporar a alteração e utilizar o caso identificado como referência para validação da funcionalidade.

### 2.4 Leitura de cardápio por imagem

**Objetivo:** facilitar o processo de cadastro de produtos pelos restaurantes.

**Contexto:** o restaurante deveria conseguir utilizar uma imagem de seu cardápio para iniciar o cadastro dos produtos.

**Instrução:** a IA recebeu a estrutura de dados esperada e foi orientada a desenvolver um fluxo de interpretação da imagem.

**Resultado:** foi criado um processo de leitura que gera uma estrutura inicial de produtos, preços e categorias.

**Validação:** o resultado foi apresentado em uma etapa de revisão antes de sua publicação.

**Decisão:** manter a revisão humana como etapa obrigatória, evitando que uma interpretação incorreta da imagem seja publicada automaticamente.

### 2.5 Ajustes de responsividade

**Objetivo:** garantir que a interface pudesse ser utilizada em diferentes tamanhos de tela.

**Contexto:** a aplicação deveria funcionar em computadores e dispositivos móveis.

**Instrução:** a IA foi utilizada para identificar problemas de dimensionamento e propor alterações no CSS e na organização dos elementos.

**Resultado:** foram realizados ajustes na disposição dos componentes, principalmente na tela inicial e nos filtros.

**Validação:** a aplicação foi testada em diferentes larguras de tela.

**Decisão:** manter a estrutura responsiva como requisito do produto final.

### 2.6 Correção do fluxo de autenticação

**Objetivo:** garantir que a aplicação não abrisse automaticamente uma sessão anterior.

**Contexto:** durante os testes, foi identificado que a persistência da sessão poderia fazer com que o usuário retornasse diretamente para uma conta anteriormente utilizada.

**Instrução:** a IA foi orientada a alterar o comportamento da sessão.

**Resultado:** a entrada passou a ocorrer pela tela de login/cadastro, mantendo a sessão apenas durante a utilização atual.

**Validação:** o comportamento foi testado após abrir e recarregar a aplicação.

**Decisão:** manter o login como porta de entrada padrão da aplicação.

## 3. Relatório Final

### 3.1 O que foi entregue?

Ao final do desenvolvimento, foi entregue o FaculFood, uma aplicação web voltada à integração dos restaurantes do campus em uma única plataforma.

A solução reúne funcionalidades destinadas aos três principais perfis envolvidos no sistema: cliente, restaurante e gestor.

O cliente pode acessar os restaurantes disponíveis, consultar seus cardápios, buscar produtos, utilizar recomendações, montar um carrinho e realizar um pedido. Após a realização do pedido, é possível acompanhar sua evolução até a disponibilização para retirada.

Para os restaurantes, foram desenvolvidas ferramentas de gerenciamento de cardápio, produtos, disponibilidade, estoque e pedidos. Também foi desenvolvido o recurso de leitura de cardápio por imagem, acompanhado de uma etapa de revisão.

O perfil de gestor permite consultar informações relacionadas aos restaurantes e clientes e acompanhar indicadores da plataforma.

Além da aplicação principal, foram desenvolvidos os arquivos de dados utilizados pelo projeto, o README com as instruções de reprodução e os documentos referentes ao processo de desenvolvimento.

### 3.2 O que ficou de fora?

A principal limitação do produto está relacionada à infraestrutura necessária para uma operação real.

O protótipo utiliza armazenamento local do navegador. Dessa forma, embora seja possível demonstrar o fluxo entre cliente e restaurante durante os testes, ainda não existe um banco de dados centralizado capaz de sincronizar usuários e pedidos entre diferentes dispositivos.

Também não foi implementado um sistema real de autenticação institucional da PUC-Rio, nem a confirmação automática de pagamentos.

Essas funcionalidades foram mantidas fora do escopo do protótipo devido ao tempo disponível e à necessidade de priorizar a demonstração do fluxo principal da solução.

### 3.3 Principais decisões técnicas

As três principais decisões técnicas foram:

**1. Desenvolvimento como aplicação web estática.**
A utilização de HTML, CSS e JavaScript permitiu desenvolver e publicar o protótipo de maneira simples, sem depender de um processo de build complexo.

**2. Utilização de armazenamento local.**
O localStorage e o sessionStorage permitiram representar os principais fluxos do sistema durante o desenvolvimento, mesmo sem a implementação de um servidor.

**3. Utilização da IA como ferramenta de desenvolvimento com validação humana.**
A IA foi utilizada para apoiar a programação, depuração e criação de funcionalidades, mas as respostas foram avaliadas e testadas antes de serem incorporadas ao produto.

### 3.4 Maior erro produzido pela IA

Um dos erros identificados durante o desenvolvimento ocorreu na lógica utilizada para avaliar restrições alimentares.

Inicialmente, a lógica poderia considerar apenas o nome do produto. Durante a validação, a equipe identificou que essa abordagem não era suficiente, pois algumas informações importantes estavam presentes no subtítulo ou na descrição do item.

A partir desse problema, a lógica foi alterada para considerar também essas informações complementares. O caso identificado foi utilizado como referência para novos testes.

Esse episódio reforçou a necessidade de validar as respostas produzidas pela inteligência artificial antes de incorporá-las ao sistema.

### 3.5 Parte menos confiável da solução

A parte considerada menos confiável é a interpretação automática das informações dos cardápios e das restrições alimentares.

No caso da leitura de imagens, a qualidade do resultado depende diretamente da qualidade da fotografia utilizada. Já no caso das restrições, a identificação depende das informações que estão efetivamente descritas no cardápio.

Por isso, a solução foi estruturada de forma que o resultado da leitura de um cardápio possa ser revisado antes da publicação.

A parte de pagamentos depende de negociação com as operadoras de cartão.

### 3.6 Próximas prioridades

Caso a equipe tivesse mais duas horas para continuar o desenvolvimento, as três principais prioridades seriam:

**1. Implementar um back-end mínimo.**
A criação de um banco de dados centralizado permitiria sincronizar os pedidos entre diferentes dispositivos e aproximaria o protótipo de uma aplicação real.

**2. Realizar testes com usuários.**
A equipe poderia observar alunos utilizando o fluxo completo de escolha e pedido, identificando dificuldades que não são necessariamente encontradas durante os testes técnicos.

**3. Aprofundar a validação das recomendações.**
Seria importante testar diferentes combinações de preferências e restrições alimentares para avaliar a consistência das recomendações apresentadas.

**4. Habilitar possibilidade de entrega na plataforma**

### 3.7 Ferramentas de IA utilizadas

A principal ferramenta de inteligência artificial utilizada durante o desenvolvimento foi o Claude/Claude Code.

A ferramenta foi utilizada como apoio nas etapas de programação, interpretação de requisitos, desenvolvimento de funcionalidades, identificação e correção de erros, criação de testes e revisão da documentação.

A utilização da IA ocorreu de forma iterativa, com análise dos resultados pela equipe e posterior validação das alterações realizadas no sistema.

---

> Relatório original em PDF: [`docs/Relatorio-FaculFood.pdf`](docs/Relatorio-FaculFood.pdf)
