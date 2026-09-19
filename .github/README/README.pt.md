# 1P1A (One Page One App) & OJapp Dynamic PWA Suite

> Transforme uma página, um diretório ou um site inteiro em um Progressive Web App (PWA) independente com um script leve executado no cliente e um Manifest Data URL gerado dinamicamente.

---

## 🌟 Visão geral

**1P1A (One Page One App)** é uma abordagem de PWA no lado do cliente que elimina a necessidade de criar e manter um arquivo `manifest.json` estático para cada página ou ferramenta.

Adicione um script do OJapp ao `<head>` da página e ele gerará dinamicamente um Web App Manifest como uma URL `data:application/manifest+json`. Dependendo do modo selecionado, os visitantes podem adicionar à tela inicial uma página individual, um grupo de diretórios ou um site inteiro, com nome, descrição, ícone, identidade, URL inicial, escopo de navegação e apresentação de instalação próprios.

Os scripts principais não exigem etapa de build, cadastro de usuário, API externa ou geração de Manifest no servidor.

---

## 🚀 Principais recursos

- **Sem build e sem backend:** funciona em hospedagens estáticas como Cloudflare Pages, GitHub Pages, Vercel e servidores web tradicionais.
- **Manifests Data URL dinâmicos:** gera o Web App Manifest no navegador em tempo de execução.
- **Três estruturas de aplicativo:** oferece suporte a aplicativos por página (1P1A), por diretório (1G1A) e para todo o site (1S1A).
- **Tratamento opcional de query:** adiciona a query string atual a `id`, `start_url` e `scope` quando ativado.
- **Metadados personalizados:** configure título, descrição, ícone, identidade, URL inicial e escopo de navegação com meta tags.
- **Apresentação de instalação:** adicione uma descrição exclusiva e uma ou várias capturas de tela (até cinco) sem manter um Manifest estático.
- **Execução totalmente no cliente:** os scripts Free funcionam sem contas de usuário ou dependências de APIs externas.

---

## 💻 Início rápido e estruturas de aplicativo

O OJapp lê suas meta tags quando o script é executado. Coloque todas as meta tags do OJapp **antes** da tag script.

### 1. 1P1A: One Page One App

Transforme a página atual — ou uma instância dessa página configurada por query — em um aplicativo na tela inicial do usuário.

```html
<!-- As configurações opcionais devem vir antes do script -->
<meta name="ojapp:query" content="true">
<meta name="ojapp:title" content="Minha ferramenta personalizada">
<meta name="ojapp:icon" content="/icon.png">

<script src="https://ojapp.app/js/ojapp_1p1a.js"></script>
```

Sem meta tags opcionais, a URL, o título e o ícone disponível da página são usados automaticamente.

### 2. 1S1A: One Site One App

Transforme toda a origem em um único aplicativo unificado.

```html
<script src="https://ojapp.app/js/ojapp_1s1a.js"></script>
```

Por padrão, `id`, `start_url` e `scope` usam a raiz da origem (`/`).

### 3. 1G1A: One Group One App

Transforme um diretório como `/dashboard/`, `/tools/` ou `/docs/` em seu próprio aplicativo na mesma origem.

```html
<meta name="ojapp:id" content="/dashboard/">
<meta name="ojapp:start-url" content="/dashboard/">
<meta name="ojapp:scope" content="/dashboard/">

<script src="https://ojapp.app/js/ojapp_1s1a.js"></script>
```

Cada grupo de diretórios pode usar um ID e um scope diferentes, permitindo vários aplicativos de grupo na mesma origem sem arquivos de Manifest ou pipelines de build separados.

---

## 🔗 Aplicativos configurados por query

Ative o tratamento de query com:

```html
<meta name="ojapp:query" content="true">
```

Por exemplo:

```text
/timer/?time=5&mode=down&seconds=on&icon=blue
```

O OJapp inclui a query string atual nos seguintes valores do Manifest gerado:

- `id`
- `start_url`
- `scope`

Isso permite que a URL armazene o estado selecionado de um aplicativo, enquanto `id` diferencia as instâncias configuradas e `start_url` restaura esse estado quando o aplicativo é iniciado.

Os navegadores processam os campos do Manifest conforme sua própria implementação de plataforma. Em particular, o navegador pode normalizar ou remover a query do `scope` processado. Mesmo assim, o OJapp grava a query configurada nos três campos e deixa a normalização a cargo do navegador.

Se a página criar ou alterar a query após o carregamento inicial, acesse ou recarregue a URL final antes de pedir ao usuário que a adicione à tela inicial. Uma alteração feita apenas com a History API pode não atualizar a URL capturada pelo fluxo **Adicionar à Tela de Início** do iOS.

---

## 🖼️ Apresentação de instalação

O OJapp pode personalizar a descrição e as capturas de tela exibidas nas interfaces de instalação compatíveis do navegador.

### Descrição de instalação

Use `ojapp:description` quando a interface de instalação precisar de um texto diferente da descrição de busca da página.

```html
<meta
  name="ojapp:description"
  content="Instale esta ferramenta para acessá-la rapidamente pela tela inicial."
>
```

O OJapp usa a seguinte prioridade:

1. `ojapp:description`
2. `<meta name="description">` padrão
3. Nenhuma `description` no Manifest quando nenhuma das opções existe

Isso mantém o texto voltado à busca separado da mensagem de instalação.

### Uma captura de tela

Use a tag original sem numeração para uma única imagem.

```html
<meta
  name="ojapp:screenshot"
  content="/images/install.png"
>
```

### Várias capturas de tela

Use tags numeradas para adicionar até cinco imagens.

```html
<meta name="ojapp:screenshot-1" content="/images/install-1.png">
<meta name="ojapp:screenshot-2" content="/images/install-2.png">
<meta name="ojapp:screenshot-3" content="/images/install-3.png">
```

Se houver pelo menos uma tag de captura numerada, o conjunto numerado terá prioridade e a tag `ojapp:screenshot` sem numeração será ignorada.

Todas as capturas do conjunto devem usar a mesma proporção. Recomenda-se uma **imagem quadrada 1:1**, pois ela continua fácil de visualizar nas interfaces de instalação de desktop e dispositivos móveis. O OJapp atribui as capturas selecionadas a `wide` no desktop e a `narrow` no Android; o layout final é controlado pelo navegador.

---

## 🛠️ Referência de metadados

| Meta tag | Aplicável a | Descrição | Padrão / Alternativa |
| :--- | :--- | :--- | :--- |
| `ojapp:title` | 1P1A / 1S1A | Define o nome do aplicativo | 1P1A: `<title>` da página; 1S1A: hostname |
| `ojapp:description` | 1P1A / 1S1A | Define a descrição de instalação do Manifest | Meta description padrão da página; caso contrário, omitida |
| `ojapp:icon` | 1P1A / 1S1A | Define a URL do ícone | Ícone disponível da página e, em seguida, ícone padrão do OJapp |
| `ojapp:screenshot` | 1P1A / 1S1A | Define uma captura de instalação | Omitida |
| `ojapp:screenshot-1` a `ojapp:screenshot-5` | 1P1A / 1S1A | Define até cinco capturas; o conjunto numerado tem prioridade | Omitidas |
| `ojapp:query` | 1P1A / 1S1A | Defina como `"true"` para incluir a query atual em `id`, `start_url` e `scope` | Desativado; query removida |
| `ojapp:id` | 1S1A / 1G1A | Define a identidade do aplicativo no Manifest | Raiz da origem |
| `ojapp:start-url` | 1S1A / 1G1A | Define a URL aberta pela tela inicial | Raiz da origem |
| `ojapp:scope` | 1S1A / 1G1A | Define o escopo de navegação | Raiz da origem |
| `ojapp:exclude` | 1P1A / 1S1A | Defina como `"true"` para desativar o OJapp na página | Desativado |

Todos os valores personalizados de `id`, `start_url` e `scope` devem ser resolvidos para a mesma origem da página atual.

---

## 📱 Comportamento por plataforma

### iOS / iPadOS (Safari)

As entradas de tela inicial configuradas por query foram verificadas no iPhone. Várias entradas criadas a partir da mesma página podem manter diferentes estados iniciais, nomes e ícones.

O ícone instalado também pode depender de `apple-touch-icon`; portanto, páginas que selecionam dinamicamente um ícone devem defini-lo antes da execução do script do OJapp.

### Android (Chrome)

O OJapp inclui a query nos valores gerados de `id`, `start_url` e `scope`. O Chrome pode normalizar o escopo de navegação processado conforme a especificação Web App Manifest. A identidade de múltiplas instalações baseadas em query pode variar de acordo com a versão do navegador e da plataforma; portanto, teste o comportamento desejado no dispositivo Android de destino.

---

## 🧭 Escolhendo um modo

| Modo | Unidade do aplicativo | Identidade / início / scope padrão | Uso típico |
| :--- | :--- | :--- | :--- |
| **1P1A** | Página atual | Caminho da página atual | Ferramentas, produtos, artigos, perfis |
| **1G1A** | Grupo de diretórios | Caminho de diretório explícito | Painéis, documentação, ferramentas agrupadas |
| **1S1A** | Site inteiro | Raiz da origem `/` | PWA convencional para todo o site |

---

## OJapp FREE

O OJapp FREE oferece uma maneira simples de implementar os padrões de design de PWA 1P1A (One Page. One App.) e 1S1A (One Site. One App.).

Para uso normal, o OJapp FREE é fornecido pelos scripts oficiais hospedados em:

https://ojapp.app/

O runtime de produção do OJapp FREE não é distribuído como arquivo JavaScript neste repositório.

O código-fonte de referência pode ser disponibilizado neste repositório como arquivos `.txt` para aprendizado, pesquisa, modificação e implementação independente.

Se você deseja apenas usar o OJapp FREE, utilize o script oficial hospedado descrito na documentação.

---

## Licença

Salvo indicação em contrário, o código-fonte contido neste repositório é licenciado sob a Licença MIT.

Consulte o arquivo `LICENSE` para obter detalhes.

A Licença MIT deste repositório aplica-se apenas ao código-fonte efetivamente publicado nele.

O runtime hospedado do OJapp FREE fornecido por `ojapp.app` e o OJapp PRO são separados deste repositório e não são cobertos pela Licença MIT dele.

O OJapp PRO é um software comercial proprietário.

---

## 1P1A e UDA são conceitos de design

**1P1A (One Page. One App.)** e **UDA (User Defined App)** são conceitos de design de PWA, não produtos.

1P1A descreve uma abordagem em que o limite do aplicativo é definido no nível da página, e não do site inteiro.

UDA amplia essa ideia ao permitir que os usuários definam um aplicativo por meio de um estado específico da URL, como parâmetros de query.

O OJapp é uma implementação e um serviço desenvolvidos com base nesses conceitos.

A Licença MIT deste repositório aplica-se apenas ao código-fonte publicado aqui. Ela não se aplica aos conceitos 1P1A ou UDA em si.

---

## 🤝 Comunidade e feedback

Perguntas, resultados de testes, casos extremos e solicitações de recursos são bem-vindos neste repositório.
As implementações de referência podem diferir da versão atual de produção do OJapp FREE e não se destinam a acompanhar todas as atualizações do runtime oficial hospedado.

- **Site / Utilitários:** [OJapp 1P1A](https://ojapp.app/one-page-one-app/en/)
- **Desenvolvedor:** OJapp / Ojach
