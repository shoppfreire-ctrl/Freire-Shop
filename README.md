# Freire Shop — loja virtual (versão 1)

React + TypeScript + Vite. Catálogo em arquivos, carrinho no navegador, pedido finalizado pelo WhatsApp.
Sem servidor, sem banco de dados, sem serviços pagos.

## Estrutura

```
freire-shop/
├─ index.html              título, descrição e metadados de SEO
├─ package.json            dependências e scripts
├─ vercel.json             reescrita de rotas (SPA) para a Vercel
├─ public/favicon.svg
│  └─ products/            (crie esta pasta) fotos reais dos produtos
└─ src/
   ├─ config/store.ts      ⚙️ WhatsApp, contatos e dados comerciais (ÚNICO lugar)
   ├─ data/products.ts     📦 catálogo de produtos
   ├─ data/categories.ts   🗂️ categorias
   ├─ data/pages.ts        📄 textos de contato, entrega, condições e privacidade
   ├─ lib/                 formatação, WhatsApp, leitura do catálogo
   ├─ context/             estado do catálogo e do carrinho (localStorage)
   ├─ components/          Header, Footer, ProductCard, etc.
   ├─ pages/               Home, Catálogo, Produto, Carrinho, Institucionais
   └─ styles.css           visual (verde-escuro + verde-limão)
```

## Rodar localmente

Requer Node.js 18 ou superior.

```bash
npm install
npm run dev        # abre em http://localhost:5173
npm run build      # confere os tipos e gera a pasta dist/
npm run preview    # testa o build final
```

## Testar a loja

1. Página inicial: banner, categorias, destaques, novidades e benefícios.
2. Busque "cortador" (cabeçalho ou página Produtos).
3. Filtre por categoria, ordene por menor/maior preço, marque "Só novidades".
4. Pesquise "xyz": deve aparecer "Nenhum produto encontrado".
5. A categoria Moda mostra "Em breve".
6. Adicione itens, mude quantidades, remova, atualize a página (F5): o carrinho continua.
7. No carrinho, com `whatsappNumber: 'PENDENTE'` o botão fica desativado e há um aviso.
   Informe um número real (veja abaixo) para testar: o WhatsApp abre com a mensagem pronta.
8. Abra as páginas do rodapé (contato, entrega, condições, privacidade).
9. No celular: com o servidor rodando, abra `http://IP-DO-SEU-PC:5173` (use `npm run dev -- --host`).

## Configurar

**WhatsApp** — em `src/config/store.ts`:
```ts
whatsappNumber: '5598912345678',  // 55 + DDD + número, só dígitos
```
Preencha também `contact` e `business` (e-mail, Instagram, endereço, horário, razão social, CNPJ/CPF).
Campos "PENDENTE" aparecem como "a configurar" no site.

**Produtos** — em `src/data/products.ts`. Exemplo:
```ts
{ id: 'cortador-legumes', name: 'Cortador de legumes', categoryId: 'casa-cozinha',
  description: '...', price: 24.9, available: true, image: '/products/cortador.jpg' }
```
- `id` único; `categoryId` igual ao `id` da categoria.
- `promoPrice` (menor que `price`) mostra o preço promocional e ativa a seção "Ofertas" na página inicial.
- `stock` só se o número for real; limita a quantidade no carrinho.
- `available: false` mostra "Indisponível".
- `featured: true` = destaque; `isNew: true` = novidade.
- Remova `demo: true` quando o produto for real.
- Imagens: coloque em `public/products/` (JPG/WebP, ~800×800, idealmente abaixo de 150 KB). Sem `image`, aparece um espaço reservado "Imagem ilustrativa".

**Categorias** — `src/data/categories.ts`. Para liberar Moda, remova `comingSoon: true` e cadastre produtos com `categoryId: 'moda'`.

**Textos institucionais** — `src/data/pages.ts`. Trechos com `[PREENCHER]` precisam das suas regras. Peça revisão profissional de privacidade e condições.

## Publicar na Vercel

1. Crie um repositório no GitHub e envie o projeto (`node_modules` e `dist` já estão no `.gitignore`).
2. Em vercel.com, "Add New → Project" e importe o repositório.
3. A Vercel detecta Vite sozinha (build: `npm run build`, saída: `dist`). Clique em Deploy.
4. Domínio próprio: Project → Settings → Domains.
5. Cada alteração enviada ao GitHub publica uma nova versão.

Antes de publicar: preencha o WhatsApp, troque os produtos de demonstração, revise as páginas institucionais.

## Limites da versão 1 (o que fica para a etapa 2)

- **Sem painel administrativo**: os produtos se editam no código e a publicação é refeita. Um painel seguro exige login real e um banco de dados. Sugestão: Supabase (autenticação + banco + armazenamento de fotos, com plano gratuito inicial) ou Firebase; a troca é feita em `src/lib/catalog.ts`.
- **Sem controle de estoque automático**: o estoque é um número fixo no arquivo. Baixa automática por pedido exige banco e pedido registrado no servidor.
- **Sem pagamento online nem frete calculado**: pedido, frete e pagamento são combinados no WhatsApp. Para pagamento online seria preciso integrar Pix/cartão (Mercado Pago, Pagar.me etc.) com backend.
- **Pedidos não ficam registrados no site**: ficam só na conversa do WhatsApp.
