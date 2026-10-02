# Mapa Elétrico da Casa

Sistema web para mapear tomadas, interruptores, caixas elétricas e circuitos de uma residência.
Um único arquivo (`index.html`), sem etapa de build. Funciona no celular e no computador.

## Funcionalidades
- Cômodos → paredes → caixas (4x2 / 4x4) → módulos (padrão brasileiro, NBR 14136)
- Planta com símbolos inspirados na NBR 5444 e elevação de cada parede (com foto)
- Quadro de distribuição, planejador de circuitos (arrastar pontos entre disjuntores)
- Teste do disjuntor, status de cada ponto, relatórios e lista de materiais
- Exporta PDF, PNG, SVG, CSV e projeto .json
- Login por e-mail/senha (Supabase) ou uso sem conta (salva no navegador)

## Publicação
Projeto estático: basta servir o `index.html`. Na Vercel, importe o repositório com framework "Other" e sem comando de build.

## Supabase
Crie a tabela abaixo no SQL Editor e adicione a URL do site em Authentication → URL Configuration.

```sql
create table if not exists public.me_casas (
  id text primary key,
  user_id uuid not null default auth.uid() references auth.users(id) on delete cascade,
  nome text not null default 'Minha casa',
  dados jsonb not null,
  atualizado_em timestamptz not null default now()
);
alter table public.me_casas enable row level security;
create policy "ler" on public.me_casas for select using (auth.uid() = user_id);
create policy "criar" on public.me_casas for insert with check (auth.uid() = user_id);
create policy "editar" on public.me_casas for update using (auth.uid() = user_id) with check (auth.uid() = user_id);
create policy "excluir" on public.me_casas for delete using (auth.uid() = user_id);
create index if not exists me_casas_user on public.me_casas(user_id);
```
