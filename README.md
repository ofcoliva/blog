# blog ofcoliva

Blog pessoal construído com Hugo e tema Hextra.

## 🛠 Estrutura do Projeto

```
blog/
├── content/             # Conteúdo do site
│   ├── posts/           # Artigos do blog
│   ├── projects/        # Projetos
│   ├── tags/            # Taxonomias/tags
│   ├── _index.md        # Página inicial
│   └── about.md         # Página sobre
├── layouts/             # Templates personalizados
│   └── shortcodes/      # Shortcodes para conteúdo reutilizável
└── hugo.yaml            # Configuração do Hugo
```

## 🚀 Executando Localmente

1. Instale o [Hugo](https://gohugo.io/installation/)

2. Clone o repositório com submódulos:
```bash
git clone --recurse-submodules https://github.com/ofcoliva/blog.git
cd blog
```

3. Crie o diretório themes
```bash
mkdir themes
```

4. Clone o repositório do Hextra para themes
```bash
git clone https://github.com/imfing/hextra.git themes
```

5. Inicie o servidor de desenvolvimento:
```bash
hugo server -D
```

6. Acesse http://localhost:1313

## 📝 Criando Conteúdo

Para criar um novo post:
```bash
hugo new content posts/nome-do-post.md
```

### Formato dos Posts
```markdown
---
title: "Título do Post"
date: 2023-10-27
draft: false
tags: ["tag1", "tag2"]
---

Conteúdo do post aqui...
```

## 🎨 Tema

Este blog utiliza o tema [Hextra](https://github.com/imfing/hextra) com algumas personalizações.

## 📄 Licenças

Este projeto utiliza:

- [Hugo](https://gohugo.io) - [Apache License 2.0](https://github.com/gohugoio/hugo/blob/master/LICENSE)
- [Hextra Theme](https://github.com/imfing/hextra) - [MIT License](https://github.com/imfing/hextra/blob/main/LICENSE)

O conteúdo do blog está sob [Apache License 2.0](LICENSE).