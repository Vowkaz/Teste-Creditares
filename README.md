# Test Creditares<br>( TRAINEE/JR DEVELOPER CHALLENGE)
Este desafio consiste em desenvolver um sistema de busca Frontend para CEP'S via API com um
CRUD para os endereços, o layout fica ao critério do candidato, lembrando de sempre considerar
usabilidade do usuário.
## Iniciando o projeto
Clone este repositório
```bash
git clone https://github.com/Vowkaz/Teste-Creditares.git
```
Instale as dependências
```bash
yarn
# ou
npm instalar

```
Inicie o aplicativo no modo de desenvolvimento
```bash
npm run dev
#ou
quasar dev

```
Eslint para corrigir erros
```bash
yarn run lint
# ou
npm run lint

```
## Funcionalidades

### Pesquise endereços
Ao buscar um endereço, é renderizado a partir do LocalStorage, caso não seja encontrado, é direcionado para uma busca na API ViaCEP e seus
resultado será salvo no LocalStorage, após isso será exibido na tela para o usuário
ver.

## Listar endereços
O sistema listará todos os endereços cadastrados na visibilidade do cartão, tratando da capacidade de resposta para as seguintes resoluções, HD (1366x768), FHD (1920x1080) e uso Mobile
iphone XR/12 do Google Chrome.

## Registrar endereço
Ao clicar no botão “Novo Endereço”, o sistema exibirá um modal contendo um formulário para cadastro
um novo endereço, com os campos CEP, Endereço, Bairro, Cidade e UF. e 2 botões que são eles

### "Salvar"
Ao clicar neste botão, o sistema registrará o novo endereço no LocalStorage, fechará o modal e exibirá uma mensagem no
tela informando "Endereço salvo com sucesso".
### "Cancelar"
Ao clicar, o sistema cancelará o cadastro do endereço e fechará o modal, limpando os campos caso tenham sido preenchidos.


## Editar Endereço
O sistema abrirá um modal semelhante ao de cadastro, porém com os dados escolhidos já preenchidos, dando as mesmas opções para salvar e cancelar
<br>
<br>
botão “Salvar”, o sistema salvará os dados atualizados no LocalStorage, fechará o modal e voltará para
seguinte mensagem ao usuário “Endereço alterado com sucesso.”,
<br>
<br>
Se clicar em “Cancelar” o modal será fechado, sem realizar nenhuma outra ação.

## excluir endereço
O sistema perguntará ao usuário se ele tem certeza que deseja deletar o endereço selecionado, caso o
opção escolhida é “Sim” sistema remove o endereço escolhido do LocalStorage, fecha o modal aberto
e mostrará uma mensagem “Endereço removido com sucesso.”.

## Precisar
O sistema precisa ser desenvolvido em Vuejs ou React e entregar apenas o link público do repositório Git.
## Entrega
o repositório precisa ter uma descrição detalhada de como instalar o projeto, use o README para isso
tutorial, prazo para entrega até dia 17/04/2023 as 12h de Brasília.
# Extras:

Use o Toast para mensagens CRUD, deixando todas no canto superior direito e com seus respectivos
cores, verde para sucesso e vermelho para erros, Axios para fazer solicitações à API selecionada, você pode
também utilizamos frameworks para Frontend, para facilitar o desenvolvimento, inclusive frameworks em
Javascripts também serão permitidos, recomendamos NextJs e Quasar Framework.
