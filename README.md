# Funil 

Quiz → análise → resultado → VSL → botão intermediário → oferta. Uma página só, sem framework, publicada como Worker com assets estáticos no Cloudflare.

```
lowticketmulheres/
├── wrangler.jsonc      ← diz ao Cloudflare que o site está em ./public
├── README.md
└── public/
    ├── index.html      ← o funil inteiro
    └── _headers        ← cabeçalhos de segurança e cache
```

Todo push na `main` publica sozinho 

## Onde editar

Tudo que muda no dia a dia fica no bloco `CONFIG`, no fim do `public/index.html`:

| Campo | O que é |
|---|---|
| `checkoutUrl` | Link da oferta na Cakto |
| `price` | Preço mostrado na página |
| `vsl.mp4` | Link direto do vídeo `.mp4` (R2 ou Bunny) — toca sozinho, sem som, com o aviso "toque para ouvir" |
| `vsl.iframe` | **Ou** o código de embed inteiro do Panda / VTurb / Bunny |
| `vsl.ratio` | `"9/16"` pra vídeo vertical, `"16/9"` pra horizontal |
| `ctaAt` | Fallback em segundos para embeds sem evento de fim. No `.mp4`, o botão intermediário aparece no evento real de encerramento do vídeo |
| `hideOfferUntilCta` | `false` deixa a oferta visível desde o início |
| `skipQuiz` | `true` abre direto no vídeo |
| `tiktokPixelId` | ID do pixel do TikTok |
| `testimonials` | Depoimentos reais, com autorização. Vazio = seção escondida |

Textos do quiz, perfis, módulos e perguntas frequentes ficam logo abaixo, em `QUESTIONS`, `PROFILES`, `MODULES` e `FAQ`.

## Fluxo da VSL

Com `hideOfferUntilCta: true`, a oferta fica totalmente escondida durante a VSL. Quando o vídeo `.mp4` termina, a página mostra apenas o botão intermediário **"Quero o guia"**. Só depois desse clique aparecem preço, módulos, garantia, FAQ e os botões finais para a Cakto.

Se o vídeo falhar, a oferta não é liberada. A tela mostra o erro real do navegador e tenta ler os headers do arquivo para apontar problemas como arquivo ausente, arquivo pequeno demais, `Content-Type` errado ou falta de suporte a Range.

O arquivo `public/vsl.mp4` precisa ser um MP4 válido. Um placeholder vazio ou arquivo corrompido quebra principalmente em mobile. Antes de publicar, confira se ele tem tamanho real de vídeo e toca localmente.

## Cronômetro

Só aparece se `fullPrice` **e** `checkoutUrlFull` estiverem preenchidos. Ele é de verdade: começa quando a oferta aparece, dura `offerMinutes` e, quando zera, o botão passa pro link do preço cheio — mesmo que ela recarregue a página.

Pra usar, crie duas ofertas do mesmo produto na Cakto (uma com o valor especial, outra com o valor cheio) e cole os dois links.

Não coloque um cronômetro que zera e não muda nada: isso é propaganda enganosa (CDC, art. 37).

## Rastreamento

Eventos enviados ao pixel do TikTok:

- `CompleteRegistration` — terminou o quiz
- `ViewContent` — abriu o vídeo / a oferta apareceu
- `ClickButton` — ativou o som da VSL
- `InitiateCheckout` — clicou em comprar

Os parâmetros `utm_*`, `ttclid`, `src` e `sck` da URL de entrada são repassados pro link da Cakto, pra venda voltar atribuída.

## Testar

Sem vídeo configurado, aparece o link "Modo teste: simular fim da VSL" embaixo do player. Ele mostra apenas o botão intermediário; a oferta só aparece depois do clique em "Quero o guia".

Pra zerar o que ficou salvo no celular (resultado, oferta liberada, prazo do cronômetro), abra em aba anônima.
