
# Rubricate Canvas

By: [Canvas - Davi Ferreira](http://www.daviferreira.com/posts/canvas-nova-classe-para-manipulacao-e-redimensionamento-de-imagens-com-php)

Agora falando da parte técnica, a classe canvas apresenta as seguintes novas funcionalidades:

* Encadear métodos
* Carregar imagens sem extensão de imagens
* Carregar imagens diretamente de um formulário, utilizando apenas o arquivo temporário do PHP
* Carregar imagens externas, via URL (vai depender do seu servidor)
* Criar uma nova imagem “vazia”, apenas com cor de preenchimento
* Adicionar legendas com posições fixas, assim como já acontecia com marcas d’água (topo_centro, baixo_esquerda etc.)
* Adicionar cor de fundo nas legendas
* Adicionar/configurar cores passando como parâmetro um valor hexadecimal




```php
use Rubricate\Canvas\ImageCanvas;

$img = ImageCanvas::Instance();
```

### CARREGAR E REDIMENSIONAR, UTILIZANDO O MÉTODO PREENCHIMENTO

```php
$img->carrega( 'cats.jpg' )
  ->hexa( '#FF005C' )
  ->redimensiona( 400, 200, 'preenchimento' )
  ->grava();
```
![redimensiona e preenche](https://rubricate.github.io/asset/image/canvas/img01.jpg)

Notem a utilização do encadeamento de métodos, tornando o código muito mais elegante e legível. O método redimensiona, que antes recebia o tipo como string, agora recebe este parâmetro na forma de constantes. ‘fill’ passou a ser ‘preenchimento’. O tipo padrão ainda é o redimensionamento simples, distorcendo caso sejam passados ambos largura e altura.

Quando só é passado um valor, altura ou largura, a classe calcula automaticamente o valor ausente na proporção. E, pra finalizar, também é possível redimensionar por porcentagem, bastando acrescentar o símbolo % nas dimensões.




### lorem ipsum dolor sit amet

```php
$img->hexa('#ff0000')
  ->novaImagem(300, 300 )
  ->hexa('#ff0000')
  ->legenda('Sport Club Internacional', 20, 'meio', 'centro', '#ffffff', true, 'font/Aller_Bd.ttf' )
  ->grava();

```
![Sport Club Internacional](https://rubricate.github.io/asset/image/canvas/img02.jpg)

Este novo método permite a criação de uma nova imagem “do zero”. Você passa uma cor de fundo através dos métodos hexa ou rgb e utiliza a função novaImagem com os parâmetros largura e altura. No exemplo também adiciono um texto no centro da imagem - mais abaixo falo das novidades do método legenda.



### CARREGAR IMAGEM UTILIZANDO UMA URL

```php
$img->carregaUrl('https://rubricate.github.io/asset/image/canvas/img03.jpg')
  ->redimensiona( 400, 200, 'crop' )
  ->grava();

```
![In URL](https://rubricate.github.io/asset/image/canvas/img03.jpg)


Muita gente pediu isso via comentários ou e-mail, então decidi implementar, mesmo sendo um pouco contra. :) As funções de criação de imagem do php/gd já permitem por natureza carregar um endereço externo (imagecreatefromjpeg, imagecreatefrompng etc.), no entanto, por questões de segurança, seu servidor provavelmente vai ter desabilitado este acesso (como no caso aqui do mediatemple).

Ainda aconselho desenvolver uma outra função para salvar imagens externas e carregar a imagem normalmente na classe canvas.



### LEGENDAS COM POSIÇÃO FIXA (EXPERIMENTAL)

```php
$img->carrega('dogs.jpg')
  ->redimensiona(400, 400, 'crop' )
  ->hexa('#000')
  ->legenda('Lorem Ipsum', 20, 'meio', 'centro', '#FF005C', true, 'font/Aller_Bd.ttf')
  ->grava();
```
![legenda](https://rubricate.github.io/asset/image/canvas/img04.jpg)

O método marcaFixa, de autoria do leitor Giolvani, fez grande sucesso. A ideia foi duplicada para as legendas. Agora, também é possível passar as posições topo, meio e baixo - esquerda, centro e direita nas coordenadas X e Y da legenda. Ainda não está funcionando 100%, eu acho, principalmente com fontes truetype. A função marcaFixa, aliás, passou a ser private e agora é chamada na própria marca() - basta passar os parâmetros X e Y como strings.

Outra novidade nas legendas é a possibilidade de adicionar uma cor de fundo. É o quinto parâmetro do método legenda(), podendo ser uma cor hexadecimal como string ou um array com as cores RGB. Aliás, tô achando que o legenda() está com muitos parâmetros. Talvez seja melhor separar em legenda normal e truetype, o que acham?




##### FILTROS:
Pesquisando por métodos de blur, de conversão para preto e branco etc. acabei descobrindo a função nativa imagefilter() - é curioso como sempre tem alguma coisa nova no PHP pra descobrir.

O método recebe como parâmetro, além do tipo de filtro, a quantidade de vezes que ele será aplicado. Algumas opções de filtragem possuem parâmetros extras. Aqui segui o padrão da função original: arg1, arg2, arg3 e arg4. Mais detalhes no manual da imagefilter().


canvas    | imagefilter()             | Descrição
----------|---------------------------|-----------
blur      | IMG_FILTER_GAUSSIAN_BLUR  | “Embaça” a imagem, com desenfoque gaussiano.
blur2     | IMG_FILTER_SELECTIVE_BLUR | “Embaça” a imagem, com desenfoque seletivo.
brilho    | IMG_FILTER_BRIGHTNESS     | Altera o brilho da imagem, definido através do parâmetro arg1.
cinzas    | IMG_FILTER_GRAYSCALE      | Converte a imagem para tons de cinza.
contraste | IMG_FILTER_CONTRAST       | Altera o contraste da imagem, definido através do parâmetro arg1
colorir   | IMG_FILTER_COLORIZE       | Converte a imagem para tons da cor especificada. Arg1, arg2 e arg3 corrspondem às cores R, G e B - e o arg4 é o canal alfa.
edge      | IMG_FILTER_EDGEDETECT     | Destaca os traços da imagem.
emboss    | IMG_FILTER_EMBOSS         | “Acimenta” (ugh) a imagem.
negativo  | IMG_FILTER_NEGATE         | Inverte todas as cores da imagem.
pixel     | IMG_FILTER_PIXELATE       | Pixeliza a imagem. Arg1 é o tamanho do bloco (pixel) e arg2 é o modo de pixelização (avançado - true, ou normal - false). só funciona com PHP 5.3 ou superior
ruido     | IMG_FILTER_MEAN_REMOVAL   | Adiciona ruído, aplicando um efeito do tipo rascunho.
suave     | IMG_FILTER_SMOOTH         | Suaviza a imagem conforme o valor especificado em arg1.








### BLUR 
```php
$img->carrega('dogs.jpg')
  ->redimensiona('50%')
  ->filtra('blur', 20)
  ->grava();
```
![blur](https://rubricate.github.io/asset/image/canvas/img05.jpg)




### COR

```php
$img->carrega('dogs.jpg')
  ->redimensiona('50%')
  ->filtra('colorir', 1, 255, 0, 92, 60)
  ->grava();
```
![colorir](https://rubricate.github.io/asset/image/canvas/img06.jpg)




### NEGATIVO

```php
$img->carrega('dogs.jpg')
  ->redimensiona('50%')
  ->filtra('negativo', 2, 4)
  ->grava();
```
![negativo](https://rubricate.github.io/asset/image/canvas/img07.jpg)




### EMBOSS

```php
$img->carrega('dogs.jpg')
  ->redimensiona('50%')
  ->filtra('emboss')
  ->grava();
```
![emboss](https://rubricate.github.io/asset/image/canvas/img08.jpg)




### RUIDO

```php
$img->carrega('dogs.jpg')
  ->redimensiona('50%')
  ->filtra('ruido', 2)
  ->grava();
```
![ruido](https://rubricate.github.io/asset/image/canvas/img09.jpg)




### EXEMPLO COMBINANDO VÁRIAS OPÇÕES DA CLASSE CANVAS

```php
$img->carrega('dogs.jpg')
  ->redimensiona('50%', '', 'crop' )
  ->filtra('colorir', 1, 255, 70, 60, 40 )
  ->flip()
  ->hexa('#fff')
  ->gira(45)
  ->filtra('ruido', 1, 3)
  ->marca('ubuntu.png', 'topo', 'esquerda' )
  ->legenda('Create At: '. date( 'd/m/Y H:i:s' ), 5, 'baixo', 'direita', '#000' )
  ->legenda('Lorem Ipsum', 20, 'topo', 'direita', '#ff9900', true, 'font/Aller_Bd.ttf' )
  ->grava();
```
![mix](https://rubricate.github.io/asset/image/canvas/img10.jpg)








### Requisitos:

*   requer PHP 5.4 or +;
* a extensão GD não está habilitada;
* permissao de escrita para imagens;
* PHP com freetype (para o uso de fontes truetype) ou com suporte à função imagefilter();

no caso de retornar as imagens direto na tela, o script está retornando alguma coisa antes da imagem, um texto, um código perdido - o que não pode acontecer;


