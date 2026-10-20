# ✋ STOP! Multiplayer

Jogo online inspirado no **Stop / Adedonha** (e no Stopots), feito só com **HTML, CSS e JavaScript**. Os jogadores entram em uma sala com um código, recebem uma letra sorteada e preenchem as categorias antes dos outros. Quem terminar primeiro grita **STOP!**

> 🤖 **Este projeto foi criado com Inteligência Artificial.**
> Todo o código foi gerado pelo **Claude** (Anthropic) a partir de pedidos e ajustes feitos por Tainara em conversa. Revise o código antes de usar em contextos importantes.

## Como jogar

1. Uma pessoa digita o apelido e clica em **Criar sala**. Aparece um código de 4 letras.
2. As outras pessoas abrem a mesma página, digitam o apelido e entram com o código.
3. O anfitrião escolhe o número de rodadas (3, 5 ou 8) e o tempo (45, 60 ou 90 segundos) e inicia o jogo.
4. A letra é sorteada com uma animação. Todos preenchem **Nome, Animal, Cor, Fruta, País e Profissão** começando com ela.
5. Quando alguém grita STOP, ou o tempo acaba, os campos travam para todos.
6. Todos avaliam as respostas dos outros e o anfitrião mostra a pontuação.

## Regras de pontuação

| Situação | Pontos |
|---|---|
| Resposta válida e única | 10 |
| Resposta válida igual à de outro jogador | 5 |
| Em branco, que não começa com a letra, ou recusada pelo grupo | 0 |

**Validação automática:** respostas em branco ou que não começam com a letra sorteada são anuladas (acentos e maiúsculas são ignorados).

**Avaliação pelos jogadores:** cada um pode tocar em **Contestar** nas respostas dos outros. Se mais da metade dos *outros* jogadores contestar, a resposta perde os pontos. Ninguém vota nas próprias respostas.

## Tecnologias

- HTML, CSS e JavaScript puro, em um único arquivo (`index.html`)
- [Supabase](https://supabase.com) (Postgres + Realtime) para sincronizar as salas
- [supabase-js](https://github.com/supabase/supabase-js) carregado por CDN
- Fontes Bowlby One e Nunito (Google Fonts)

## Como configurar o seu próprio projeto

### 1. Criar o banco no Supabase

Crie um projeto em [supabase.com](https://supabase.com). No **SQL Editor**, execute:

```sql
create table public.docs (
  path text primary key,
  parent text not null,
  data jsonb not null default '{}'::jsonb
);
create index on public.docs (parent);

alter table public.docs enable row level security;
create policy "stop_salas" on public.docs
  for all using (path like 'rooms/%') with check (path like 'rooms/%');

alter publication supabase_realtime add table public.docs;
```

### 2. Colocar a URL e a chave no código

Em **Project Settings → API**, copie a **Project URL** e a chave **anon / publishable**. No começo do script do `index.html`, preencha:

```js
const SUPABASE_URL="https://SEU-PROJETO.supabase.co";  // só o endereço, sem /rest/v1/
const SUPABASE_KEY="SUA_CHAVE_ANON";
```

> A chave `anon` pode ficar no código público, ela foi feita para isso. **Nunca** coloque a chave `service_role`.

### 3. Publicar no GitHub Pages

1. Envie o `index.html` para um repositório.
2. Vá em **Settings → Pages**, escolha **Deploy from a branch**, branch `main`, pasta `/ (root)`.
3. Compartilhe o link `https://seu-usuario.github.io/nome-do-repositorio/` com os jogadores.

## Limitações e segurança

- O jogo **não consulta dicionário**: ele só confere se a palavra começa com a letra sorteada. A votação entre os jogadores serve para barrar respostas inventadas.
- As regras do banco permitem que qualquer pessoa com o link leia e escreva nas salas. É adequado para um jogo de sala de aula ou entre amigos, **não** para guardar dados sensíveis.
- O anfitrião controla o ritmo da partida. Se ele fechar a página, a partida para.
- Quem recarrega a página volta sozinho para a mesma sala.

## Ideias para evoluir

- Escolher as categorias na sala de espera
- Limite de tempo para a votação
- Login para identificar os jogadores
- Regras mais restritas no banco de dados

## Créditos

- Ideia e direção: Tainara Martins
- Código: Claude (Anthropic), IA
- Inspirado no jogo de papel Stop/Adedonha e no Stopots. Este projeto não é afiliado a nenhum deles.
