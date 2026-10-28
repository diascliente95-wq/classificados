# Classificados Dias

Marketplace responsivo em HTML/CSS/JavaScript, com Firebase Authentication e Cloud Firestore, preparado para publicar pelo Vercel conectado ao GitHub.

## Estado atual

- Interface mobile-first, pesquisa e categorias.
- Cadastro/login por e-mail e senha (quando Firebase estiver configurado).
- Formulário de anúncios e lista do vendedor.
- Anúncios ficam como `pending` até um administrador aprovar.
- Planos iniciais: Gratuito (3 anúncios), Mensal (R$ 19,90; 30 anúncios) e Premium (R$ 39,90; 100 anúncios).
- Pedidos dos planos pagos são registrados como pendentes. **Não existe cobrança real nem confirmação automática de pagamento nesta versão.**
- Painel de administração só aparece para usuário que tenha o custom claim `admin: true`. Não existe botão público para virar administrador.

## 1. Configurar Firebase pelo Android

1. Abra https://console.firebase.google.com/ no navegador do celular e crie um projeto.
2. Em Authentication > Sign-in method, habilite **E-mail/senha**.
3. Em Firestore Database, crie um banco de dados.
4. Nas configurações do projeto, adicione um aplicativo Web (ícone `</>`) e copie o objeto de configuração.
5. No GitHub, abra `index.html`, toque no lápis Edit, localize `const firebaseConfig = {...}` e substitua os quatro valores de exemplo pelos valores do seu app Web Firebase. Não publique chaves de serviço/admin; a configuração web Firebase é pública por desenho, a proteção vem das regras.
6. No repositório, abra `firestore.rules`, copie o conteúdo. No Firebase Console > Firestore Database > Rules, cole e publique as regras.
7. Para testar, abra o site, crie uma conta, faça login e tente cadastrar um anúncio.

## 2. Importar e publicar pelo Vercel

1. Abra https://vercel.com/ e entre usando a conta GitHub que tem acesso ao repositório.
2. Toque em Add New... > Project e importe `diascliente95-wq/classificados`.
3. Como é um site HTML simples, não precisa de comando de build. Se solicitado, deixe Build Command vazio e Output Directory como padrão/root.
4. Toque em Deploy. Depois de pronto, abra o domínio `.vercel.app`.
5. As alterações feitas em `main` no GitHub acionam novos deploys quando a integração está conectada.

## 3. Dar acesso de administrador com segurança

O painel exige o custom claim Firebase Authentication `admin: true`. Não tente fazer isso pelo navegador nem coloque uma senha de administrador dentro de `index.html`.

Um administrador técnico deve conceder o claim por um ambiente confiável com Firebase Admin SDK, por exemplo uma Cloud Function protegida ou script executado localmente por pessoa autorizada. Exemplo de lógica no ambiente confiável:

```js
await getAuth().setCustomUserClaims("UID_DO_ADMIN", { admin: true });
```

Depois, o administrador deve sair e entrar novamente (ou atualizar o token). O UID é encontrado em Firebase Console > Authentication > Users.

## 4. Pagamentos reais (etapa futura)

Os botões de plano pago **apenas registram uma solicitação**. Para cobrar com segurança, escolha um provedor de pagamento que atenda ao Brasil e implemente:
- criação de cobrança no servidor (não no HTML);
- webhook autenticado do provedor;
- verificação do pagamento no servidor antes de mudar o plano;
- registro de validade, renovação, cancelamento e falhas de cobrança;
- idempotência e logs para evitar ativações duplicadas.

Nunca use um comprovante enviado pelo cliente como única confirmação. Não coloque tokens secretos do provedor no GitHub nem no código do navegador.

## 5. Limitações e notas

- A URL da imagem do produto deve ser HTTPS. Esta versão não faz upload de fotos para armazenamento; isso pode ser adicionado com Firebase Storage e regras próprias.
- As regras Firestore são uma base inicial e precisam ser testadas antes de uso comercial. Revise regras e limites do banco antes de abrir o serviço ao público.
- A aprovação de anúncios é manual.
- O site não processa pagamentos ainda; não anuncie os planos como cobrança automática até a integração ser concluída.
