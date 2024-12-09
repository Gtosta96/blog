---
title: Como automatizar o seu perfil do GitHub para exibir lista de artigos e artigos
description: 'Atualizando a apresentação do meu perfil no GitHub pensei: "Seria
  possível gerar uma lista com os artigos que eu posto aqui no blog, e também
  dos meus vídeos no YouTube?'
date: 2021-10-11 08:32:15
modified_date: 2021-10-11 08:32:15
image: /assets/images/posts/1632833102817.jpeg
---
E aí meu povo, tudo jóia?

Ao longo de toda a minha carreira, sempre que possível eu criei muita coisa e contribui com a comunidade - principalmente local, aqui na cidade onde eu moro - porém meu marketing pessoal sempre foi bem dos mixuruca e deixado em segundo plano - \~besta\~ péssimo que fala, né?\
Ultimamente fui indicado para o [Microsoft MVP Award](https://mvp.microsoft.com/pt-br/) e com isso veio uma motivação de construir uma base no *online* um pouco mais sólida e um dos principais pontos falhos sobre como eu posiciono na internet como programador estava no meu GitHub.

Atualizando a[ apresentação do meu perfil no GitHub](https://github.com/gtosta96) pensei: "Seria possível gerar uma lista com os artigos que eu posto aqui no blog, e também dos meus [vídeos no YouTube](https://www.youtube.com/channel/UCnn6QwXCmb5kR5rqSQZAo2w)?". Decidi buscar na internet alguma forma de fazer isso, e cá estamos!

Após muita pesquisa, dor de cabeça e *copy/paste* cheguei em um resultado satisfatório e gostaria de compartilhar aqui para quem tenha o mesmo desejo e talvez enfrente a mesma dificuldade. Vamos lá?

Para conseguirmos construir nosso perfil que atualiza automaticamente com nosso conteúdo precisamos ter publicado na internet um arquivo no formato \`XML\` no padrão [RSS](https://pt.wikipedia.org/wiki/RSS), que resumidamente segue uma estrutura de dados e regras. Como o foco desse artigo não é a criação desse arquivos, vamos utilizar o [RSS aqui do blog](https://gabrieltosta.com.br/sitemap.xml), e o [canal do Youtube](https://www.youtube.com/feeds/videos.xml?channel_id=UCnn6QwXCmb5kR5rqSQZAo2w) também - sinta-se livre para utilizar o seu proprio, ok? =)

Já com nossos arquivos RSS em mãos, agora precisamos [criar o perfil de apresentação no GitHub](https://docs.github.com/pt/account-and-profile/setting-up-and-managing-your-github-profile/customizing-your-profile/managing-your-profile-readme), que basicamente se resume em criar um repositório um repositório com o mesmo nome do seu usuário do GitHub. Segue abaixo a estrutura que você deve criar:

```shell
Gtosta96 # O nome do seu repositório no GitHub
├── .github # Pasta de configuração do GitHub
│    └── workflows # Pasta de configuração do GitHub Actions
│        └─── update-readme.md # Arquivo de configuração para atualizar o README.md
│
└─ README.md # Sua apresentação
```

Uma vez criado o repositório vamos adicionar as seguintes linhas de código no README.md

```markdown
<!-- SEU CONTEÚDO -->

### 📕 Blog posts
<!-- BLOG:START -->
<!-- BLOG:END -->

### 📺 Youtube Videos
<!-- YOUTUBE:START -->
<!-- YOUTUBE:END -->
```

Esses *Markups* serão utilizados para conter os links gerados automaticamente através de uma ferramenta que vamos ver um pouco mais a frente.

Agora é a hora de mexermos com a automatização desse processo, nesse momento vamos implementar uma job no [GitHub Actions](https://docs.github.com/pt/actions) que será executado automaticamente a cada 1 semana, que irá atualizar para nós o README.md. Para isso, devemos criar a seguinte estrutura na nossa aplicação:

```yaml
name: Update readme workflow # Nome do workflow
on: # Motivos que façam os serviços descritos abaixo serem executados
  schedule: # Através de um agendamento automático do serviço às 22:00 horas, todos os dias
    - cron: '0 22 * * *'
  workflow_dispatch: # Através da execução manual através da página do GitHub

jobs: # Lista de serviços
  update-blog: # Atualiza a lista de artigos
    runs-on: ubuntu-latest

    steps:
    - name: Checkout # Faz checkout do código através da biblioteca "actions/checkout@v2"
      uses: actions/checkout@v2

    - name: Updating blog posts # Atualiza a lista de artigos através da biblioteca "gautamkrishnar/blog-post-workflow@master"
      uses: gautamkrishnar/blog-post-workflow@master
      with:
        comment_tag_name: BLOG # Parseia e substitui o Markup "BLOG" do README.md com os dados do feed_list
        feed_list: "https://gabrieltosta.com.br/rss/feed.xml"

  update-youtube: # Atualiza a lista de videos
    runs-on: ubuntu-latest

    steps:
    - name: Checkout # Faz checkout do código através da biblioteca "actions/checkout@v2"
      uses: actions/checkout@v2

    - name: Update youTube videos # Atualiza a lista de artigos através da biblioteca "gautamkrishnar/blog-post-workflow@master"
      uses: gautamkrishnar/blog-post-workflow@master
      with:
        comment_tag_name: YOUTUBE # Parseia e substitui o Markup "YOUTUBE" do README.md com os dados do feed_list
        feed_list: "https://www.youtube.com/feeds/videos.xml?channel_id=UCnn6QwXCmb5kR5rqSQZAo2w"
```

Com todos esses arquivos devidamente configurados, agora só dar \`push\` para o GitHub.

![GitHub Gtosta96](/assets/images/posts/screen-shot-2021-10-11-at-21.18.29.png "GitHub Gtosta96")