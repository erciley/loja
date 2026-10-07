# Akari v2 — versão organizada

Esta versão reorganiza o HTML fornecido em uma página, uma folha de estilos e arquivos JavaScript separados. O catálogo, carrinho, integração com a tabela `products`, busca, filtros e backup foram mantidos a partir do código recebido.

## Estrutura

- `index.html` — estrutura da loja
- `styles.css` — estilos agrupados e fontes
- `js/app.js` — catálogo, carrinho, pedidos por WhatsApp e painel
- `js/supabase-config.js` — URL e chave publicável do projeto Supabase
- `js/hero-logo.js` — logo embutido no HTML original

## Preparação necessária no Supabase

1. No Authentication, crie ou convide a conta de administrador. Não habilite cadastro público para o painel.
2. Defina o `app_metadata.role` dessa conta como `admin` por um fluxo confiável de administração do Supabase. O navegador não pode atribuir esse papel.
3. Ative RLS na tabela `public.products` e aplique políticas equivalentes a estas:

```sql
alter table public.products enable row level security;

create policy "Public can read products"
on public.products for select
using (true);

create policy "Admins can manage products"
on public.products for all to authenticated
using ((auth.jwt() -> 'app_metadata' ->> 'role') = 'admin')
with check ((auth.jwt() -> 'app_metadata' ->> 'role') = 'admin');
```

Se já houver políticas na tabela, revise-as antes de criar novas. Uma política permissiva antiga pode continuar permitindo gravações sem login.

## Antes de publicar

- O site original tinha uma senha de painel embutida no JavaScript. Ela foi removida desta cópia. Troque a senha antiga e não a reutilize.
- A chave `publishable`/`anon` fica no navegador por desenho do Supabase; não coloque uma `service_role` key nesta pasta ou no site.
- O código tem referências a `hero-bg.png` e `images/hero-bg.webp`; confirme se o arquivo de fundo correto existe no pacote de publicação. As fotos dos produtos usam os endereços salvos na tabela.
- Hospede o conteúdo desta pasta na raiz do site, preservando os caminhos relativos.

A conta e as políticas do banco precisam ser configuradas antes de o Admin funcionar. O código recebido não incluía um dump da tabela nem os arquivos de imagem locais.

