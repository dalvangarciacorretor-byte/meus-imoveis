# Meus Imóveis — app do corretor

App para o corretor guardar **tudo de cada imóvel num lugar só**: dados, fotos, vídeos, localização e documentos (contratos, matrícula etc.). Quando o cliente pergunta, ele abre o imóvel e envia pronto pelo WhatsApp.

- Só o corretor usa. Ele entra com a conta Google dele.
- Fotos, vídeos e documentos ficam no **Google Drive dele** (não paga nada a mais; usa o espaço do Drive).
- Não tem servidor nem banco de dados para pagar. É um site (PWA) que se instala no celular como app.

## Arquivos

| Arquivo | Para que serve |
|---|---|
| `index.html` | O app inteiro (telas e lógica) |
| `manifest.webmanifest`, `icon-*.png` | Fazem o app poder ser instalado no celular |
| `sw.js` | Faz o app abrir mais rápido |

## 1. Testar no VS Code

1. Abra esta pasta no VS Code.
2. Instale a extensão **Live Server** (Ritwick Dey).
3. Clique com o botão direito em `index.html` → **Open with Live Server**.
4. Vai abrir em `http://127.0.0.1:5500`. O app vai pedir o **ID do cliente** do Google (passo 2).

> Ele só funciona pelo endereço do Live Server (ou hospedado). Não funciona dando duplo clique no arquivo.

## 2. Criar o ID do cliente do Google (uma vez só)

É o que permite o botão "Entrar com Google". É grátis e leva uns 10 minutos.

1. Entre em <https://console.cloud.google.com> com a conta Google do corretor e crie um **projeto** (nome: `Imoveis`).
2. Menu **APIs e serviços → Biblioteca** → procure **Google Drive API** → **Ativar**.
3. Abra **Google Auth Platform** (antes chamada "Tela de permissão OAuth") → **Começar**:
   - Nome do app: `Meus Imóveis`, e-mail de suporte: o seu.
   - Público: **Externo**. Informe seu e-mail de contato e conclua.
4. Em **Acesso a dados (Escopos)** → **Adicionar escopos** → marque `.../auth/drive.file` → Salvar.
   Esse escopo faz o app enxergar **só a pasta que ele mesmo cria**, nunca o resto do Drive.
5. Em **Público**, adicione o e-mail do corretor em **Usuários de teste**.
   Se o login começar a pedir permissão toda hora, clique em **Publicar app** nessa mesma tela (o escopo usado é de baixo risco).
6. Em **Clientes → Criar cliente**:
   - Tipo: **Aplicativo da Web**
   - **Origens JavaScript autorizadas** (só o endereço, sem barra no final nem caminho):
     - `http://127.0.0.1:5500`
     - `http://localhost:5500`
     - `https://SEU-USUARIO.github.io` (quando publicar, passo 3)
   - Não precisa preencher "URIs de redirecionamento".
7. Copie o **ID do cliente** (termina com `.apps.googleusercontent.com`) e cole na tela inicial do app.
   Se preferir deixar fixo, cole na linha `const DEFAULT_CLIENT_ID = ''` no começo do script do `index.html`.

Na primeira vez o Google mostra **"app não verificado"**. É normal para app de uso próprio: clique em **Avançado → Acessar Meus Imóveis**.

## 3. Colocar no ar (grátis) para usar no celular

O celular precisa de um endereço `https://`. O mais simples é o **GitHub Pages**:

1. Crie um repositório no GitHub e envie todos os arquivos desta pasta.
2. **Settings → Pages → Deploy from a branch → main / (root) → Save**.
3. Em alguns minutos o app fica em `https://SEU-USUARIO.github.io/NOME-DO-REPOSITORIO/`.
4. Volte ao passo 2.6 e inclua `https://SEU-USUARIO.github.io` nas origens autorizadas.

## 4. Instalar no celular

- **Android (Chrome):** abra o endereço → menu ⋮ → **Instalar app** / **Adicionar à tela inicial**.
- **iPhone (Safari):** botão Compartilhar → **Adicionar à Tela de Início**.

## Como funciona

No Drive do corretor o app cria:

```
Imobiliária - App/
├─ imoveis.json            (os dados de todos os imóveis; não apague nem edite)
├─ IM-001 - Casa 3 quartos no Centro/
│   ├─ Fotos/
│   ├─ Vídeos/
│   └─ Documentos/         (o corretor cria as subpastas: Contrato, Matrícula…)
└─ IM-002 - …
```

- **Enviar ao cliente:** marca o que quer mandar (valor e características, descrição, localização, link das fotos, link dos vídeos), edita a mensagem se quiser e envia pelo WhatsApp. Pode digitar o telefone do cliente para abrir a conversa direto.
- **Fotos como imagem:** na aba Fotos, toque em **Selecionar**, escolha as fotos e envie.
- **Documentos nunca vão** junto com o envio ao cliente e a pasta Documentos nunca é compartilhada.
- **Links de fotos/vídeos:** ao marcar, o app libera a pasta para "quem tiver o link". Fotos novas entram sozinhas no link. Dá para **desativar os links** na mesma tela.
- **Excluir** no app manda para a **lixeira do Drive** (dá para recuperar). Para liberar espaço de verdade, esvazie a lixeira do Drive.
- **Fotos** são reduzidas (2000 px) antes de enviar, para economizar espaço. Dá para desligar em Configurações.

## Limitações

- O app só enxerga o que **ele mesmo enviou**. Arquivo colocado direto na pasta pelo site do Drive não aparece no app. Envie sempre pelo app.
- Precisa de internet. A lista fica salva para consulta, mas enviar e abrir fotos exige conexão.
- Ao abrir o app depois de um tempo, pode aparecer o aviso para tocar em **Entrar** (o Google renova o acesso a cada hora).
- O Drive gratuito tem 15 GB, divididos com Gmail e Fotos. Vídeos grandes pesam; se encher, é só o corretor comprar mais espaço no Google.
- Vídeos grandes demoram para enviar: deixe a tela ligada até terminar.
