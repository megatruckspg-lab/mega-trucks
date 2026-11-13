# Mega Truck's Acessórios: loja com pedidos e painel do administrador

## O que tem nesta pasta

| Arquivo | Para que serve |
|---|---|
| `index.html` | O site inteiro: loja, carrinho, pedido e painel do administrador |
| `firestore.rules` | Regras de segurança do banco de dados (quem pode ler e escrever) |
| `firebase.json` | Configuração para publicar no Firebase Hosting |

## 1. Testar agora, sem Firebase

Abra o `index.html` no navegador. O site funciona em **modo demonstração**: produtos, pedidos e configurações ficam salvos só naquele navegador.

- Loja: abra o arquivo normalmente.
- Painel do administrador: acrescente `#admin` ao endereço. Em demonstração, use qualquer e-mail e a senha `demo123`.

Na demonstração, ninguém além de você vê as mudanças. Para os clientes verem, siga a parte 2.

## 2. Conectar ao Firebase (para valer)

### 2.1 Criar o projeto
1. Entre em https://console.firebase.google.com com a sua conta Google.
2. Clique em **Adicionar projeto** e dê um nome (por exemplo, `mega-trucks`).

### 2.2 Registrar o app web e colar a configuração
1. Na tela inicial do projeto, clique no ícone **`</>`** (Web) e registre o app.
2. O Firebase mostra um bloco `firebaseConfig` com `apiKey`, `authDomain`, `projectId` e outros campos.
3. Abra o `index.html` em um editor de texto, procure `window.MEGA_CONFIG` no começo do arquivo e troque `firebase: null` pelos seus dados:

```js
window.MEGA_CONFIG = { firebase: {
  apiKey: "...",
  authDomain: "...",
  projectId: "...",
  appId: "..."
}};
```

A `apiKey` do Firebase não é uma senha. Quem protege os dados são as regras do passo 2.5.

### 2.3 Criar o seu login de administrador
1. No menu, vá em **Authentication**, clique em **Vamos começar** e ative o método **E-mail/senha**.
2. Na aba **Usuários**, clique em **Adicionar usuário** e cadastre o seu e-mail e uma senha forte.
3. Copie o **UID do usuário** dessa conta. Você vai usar no passo 2.5.

### 2.4 Criar o banco de dados
1. No menu, vá em **Firestore Database** e clique em **Criar banco de dados**.
2. Escolha o modo **produção** e uma região próxima (por exemplo, `southamerica-east1`, São Paulo).

### 2.5 Publicar as regras de segurança
1. Abra o `firestore.rules`, troque `COLE_AQUI_O_UID_DO_ADMINISTRADOR` pelo UID copiado no passo 2.3 e salve.
2. No Firestore, abra a aba **Regras**, apague o conteúdo, cole o do `firestore.rules` e clique em **Publicar**.

Com isso, qualquer pessoa vê o catálogo e cria pedidos, mas só a sua conta altera produtos, lê pedidos e muda configurações.

### 2.6 Publicar o site
1. Instale o Node.js (https://nodejs.org).
2. No terminal, dentro desta pasta:

```
npm install -g firebase-tools
firebase login
firebase use --add
firebase deploy
```

3. O comando mostra o endereço do site, algo como `https://seu-projeto.web.app`. O painel fica em `https://seu-projeto.web.app/#admin`.

## 3. Usando o painel

- **Pedidos:** cada pedido novo aparece no topo, com cliente, itens, endereço, forma de pagamento e total. Mude a situação (novo, em separação, pronto, concluído, cancelado). Com o painel aberto, os pedidos chegam sozinhos.
- **Produtos:** crie, edite e exclua produtos, com título, preço, categoria, referência, estoque e foto. O botão "Mostrar na loja" oculta um item sem apagá-lo. As mudanças aparecem para os clientes na hora.
- **Configurações:** número do WhatsApp da loja e a chave "Aceitando pedidos", que fecha a loja temporariamente.

## 4. Pontos de atenção

- **Fotos:** o site reduz cada foto para no máximo 900 px e guarda dentro do próprio produto no Firestore. Isso evita usar o Firebase Storage, mas pede fotos de tamanho razoável.
- **Total do pedido:** o valor é calculado no navegador do cliente. Ao aceitar um pedido, confira se o total bate com os preços do catálogo.
- **Pagamento:** não há pagamento online. O cliente escolhe Pix, cartão ou dinheiro, e o pagamento acontece na retirada ou na entrega. O frete é combinado com o cliente pelo WhatsApp.
- **Aviso de novo pedido:** o painel mostra "(1 novo)" na aba Pedidos, mas não envia notificação para o celular.
- **Segurança extra:** em Authentication > Configurações, desative o cadastro de novos usuários, se essa opção estiver disponível no seu projeto. As regras já impedem que outras contas leiam pedidos ou alterem produtos.
