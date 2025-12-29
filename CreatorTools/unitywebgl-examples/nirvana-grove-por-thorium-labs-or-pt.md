---
description: >-
  Nirvana Grove é uma tranquila floresta de bambu projetada para o máximo
  bem-estar, desenvolvida com Unity 6 para a plataforma Viverse pela Thorium
  Labs.Nirvana Grove é uma tranquila floresta de bambu
hidden: true
---

# NIRVANA GROVE por Thorium Labs | PT

***

## Visão Geral do Projeto

Nirvana Grove é uma floresta de bambu desenvolvida com Unity 6. A ferramenta Terrain foi usada\
para criar a paisagem, escolhemos cuidadosamente a vegetação, composta por bambu, grama,\
samambaias e flores, as texturas corretas e um conjunto de elementos decorativos japoneses para\
compor o ambiente.

### Recursos do Projeto

O Nirvana Grove pode ser vivenciado aqui: [https://create.viverse.com/FPJWhRP](https://create.viverse.com/FPJWhRP)

Uma visão geral deste projeto pode ser encontrada aqui: {Link TBD}

### Conceitos e Técnicas Essencials

* Terreno montanhoso, entrecortado por trilhas e um grande lago.
* Vegetação exuberante e variada.
* Trilha sonora composta por três efeitos especiais da natureza e uma música relaxante.
* Elementos decorativos japoneses com significados especiais

***

## Criação de Projeto

### Geração de Terreno

Usamos a ferramenta Terrain do Unity para construir a paisagem, um terreno montanhoso,\
interseccionado por caminhos com um enorme lago como pano de fundo.

#### 1 - Topologia

O terreno tem 1.000 por 1.000 metros quadrados e chega a 600 metros de altura. Aqui está uma\
visão geral do terreno e dos principais cenários:

<figure><img src="../.gitbook/assets/Topology.png" alt="" width="452"><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/Terrain Settings.png" alt="" width="378"><figcaption></figcaption></figure>

#### 2 - Texturas

Utilizamos apenas quatro texturas para representar cada detalhe do terreno da seguinte maneira:

| Textura                                      | Nome      | Descrição                                                                                                                                 |
| -------------------------------------------- | --------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| ![](<../.gitbook/assets/Texture Soil.jpg>)   | Solo      | <p>É aqui que você encontrará<br>todas as plantas do terreno.</p>                                                                         |
| ![](<../.gitbook/assets/Texture Dirt.jpg>)   | Barro     | É utilizado para separar a área plantada da pavimentação.                                                                                 |
| ![](<../.gitbook/assets/Texture Paving.jpg>) | Pavimento | A área do caminho usada pelos avatares para caminhar.                                                                                     |
| ![](<../.gitbook/assets/Texture Sand.jpg>)   | Areia     | <p>Usado para o fundo dos corpos d'água. Esta área não pode ser acessada pelos avatares. Ela só é vista através do shader de<br>água.</p> |

#### 3 - Vegetação

A vegetação desempenha o papel mais proeminente no espaço, mas mesmo assim, nos\
preocupamos muito com o desempenho e minimizamos o uso de pré-fabricados. As texturas das\
plantas têm um papel crucial, pois os avatares também podem caminhar entre elas.

| Planta                                                                                                      | Nome           | Tipo    | Descrição                                                                                                                                     |
| ----------------------------------------------------------------------------------------------------------- | -------------- | ------- | --------------------------------------------------------------------------------------------------------------------------------------------- |
| <div><figure><img src="../.gitbook/assets/Bamboo 1 (1).png" alt=""><figcaption></figcaption></figure></div> | Bambu 1        | Prefab  | <p>Usado principalmente<br>na área de divisa entre o solo e o pavimento<br>para criar o efeito<br>“túnel”</p>                                 |
| <div><figure><img src="../.gitbook/assets/Bamboo 2 (1).png" alt=""><figcaption></figcaption></figure></div> | Bambu 2        | Prefab  | <p>É o elemento central<br>da floresta de bambu, cobrindo todos os principais bosques de<br>bambu</p>                                         |
| <div><figure><img src="../.gitbook/assets/Grass.png" alt=""><figcaption></figcaption></figure></div>        | Grama          | Textura | <p>O principal elemento<br>do solo, preenchendo<br>as lacunas entre o<br>bambu, as samambaias e as<br>flores</p>                              |
| <div><figure><img src="../.gitbook/assets/Fern 1.png" alt=""><figcaption></figcaption></figure></div>       | Samambaia 1    | Textura | <p>As samambaias estão distribuídas<br>aleatoriamente pelo<br>solo.</p>                                                                       |
| <div><figure><img src="../.gitbook/assets/Fern 2.png" alt=""><figcaption></figcaption></figure></div>       | Samambaia 2    | Textura | <p>As samambaias estão distribuídas<br>aleatoriamente pelo<br>solo.</p>                                                                       |
| <div><figure><img src="../.gitbook/assets/Fern 3.png" alt=""><figcaption></figcaption></figure></div>       | Samambaia 3    | Textura | <p>As samambaias estão distribuídas<br>aleatoriamente pelo<br>solo.</p>                                                                       |
| <div><figure><img src="../.gitbook/assets/Flower.png" alt=""><figcaption></figcaption></figure></div>       | Flor           | Textura | <p>Usado para criar áreas de destaque,<br>interrompendo a<br>sequência de áreas<br>verdes com seus tons<br>brancos.</p>                       |
| <div><figure><img src="../.gitbook/assets/Bamboo Leaf.png" alt=""><figcaption></figcaption></figure></div>  | Folha de Bambu | Textura | <p>Usado nos sistemas de partículas para simular a queda das folhas de bambu, trabalhando em sintonia com o<br>efeito sonoro do<br>vento.</p> |

### Sound Elements

The soundtrack is composed of three special nature effects and a calming song to compose the\
landscape and immerse the avatar.

| Som                            | Tipo          | Descrição                                                                       |
| ------------------------------ | ------------- | ------------------------------------------------------------------------------- |
| Cosmic Breaths of the Universe | Música        | <p>Uma música de baixa<br>frequência que inspira<br>relaxamento e calma.</p>    |
| Pássaros                       | Efeito Sonoro | Vários pássaros cantando.                                                       |
| Vento                          | Efeito Sonoro | <p>Vento soprando sobre a<br>floresta e balançando<br>suavemente as folhas.</p> |
| Água                           | Efeito Sonoro | Um fluxo suave de água.                                                         |

### Decoração

A decoração é outro ponto importante do espaço, pois faz com que o usuário se sinta em um jardim\
japonês.

| Item                                                                                                            | Nome              | Descrição                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| --------------------------------------------------------------------------------------------------------------- | ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <div><figure><img src="../.gitbook/assets/Torii Gate.png" alt=""><figcaption></figcaption></figure></div>       | Portão Torii      | <p>Um portão Torii é um portão tradicional<br>japonês, mais comumente encontrado na<br>entrada ou dentro de um santuário xintoísta,<br>marcando a transição do mundano para o<br>sagrado. Simboliza a fronteira entre o mundo<br>humano e o mundo dos kami (espíritos ou<br>divindades). Passar por um torii significa entrar<br>em um espaço sagrado.</p>                                                                                                                                              |
| <div><figure><img src="../.gitbook/assets/Dog Statues.png" alt=""><figcaption></figcaption></figure></div>      | Estátuas de Cães  | <p>Nos jardins japoneses, estátuas de cães,<br>frequentemente chamadas de Komainu,<br>simbolizam proteção e servem para afastar<br>espíritos malignos. Elas são normalmente<br>colocadas aos pares na entrada de santuários,<br>templos e, às vezes, até mesmo de casas,<br>atuando como guardiões. Uma estátua tem a<br>boca aberta (A-gyo), representando o início,<br>enquanto a outra tem a boca fechada (Un-gyo),<br>representando o fim, simbolizando, juntas, a<br>totalidade da existência.</p> |
| <div><figure><img src="../.gitbook/assets/Shrine.png" alt=""><figcaption></figcaption></figure></div>           | Santuário         | <p>Em um jardim japonês, um pequeno santuário<br>(ou hokora) é uma representação em miniatura<br>de um santuário xintoísta, servindo como um<br>espaço sagrado para venerar kami, espíritos ou<br>divindades associadas à natureza, ancestrais ou<br>figuras históricas. Esses santuários são<br>frequentemente integrados ao projeto do<br>jardim para conectar o espaço físico com o<br>reino espiritual e promover um senso de<br>harmonia e respeito pela natureza.</p>                             |
| <div><figure><img src="../.gitbook/assets/Lanterns.png" alt=""><figcaption></figcaption></figure></div>         | Lanternas         | <p>As lanternas japonesas, ou tōrō, são mais do<br>que meros elementos decorativos; elas<br>simbolizam luz, esperança e estão profundamente conectadas à cultura e<br>espiritualidade japonesas. Elas costumam<br>aparecer em jardins, templos e durante festivais, guiando espíritos e criando uma<br>atmosfera serena.</p>                                                                                                                                                                            |
| <div><figure><img src="../.gitbook/assets/Stairs and Walls.png" alt=""><figcaption></figcaption></figure></div> | Escadas e Paredes | <p>Esses elementos são meramente decorativos e<br>ajudam a conduzir o avatar pela cena. Não têm<br>nenhum significado especial além do<br>estrutural.</p>                                                                                                                                                                                                                                                                                                                                               |

### Illuminação, Skubox e Sistem de Nuvens

Esses são os elementos finais que encerram a cena. Eles criam o cenário dramático para criar uma\
experiência ainda mais imersiva

| Item                                                                                                                             | Tipo              | Descrição                                                                                                                                                                                   |
| -------------------------------------------------------------------------------------------------------------------------------- | ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <div><figure><img src="../.gitbook/assets/Directional Light Settings I.png" alt=""><figcaption></figcaption></figure></div>      | Directional Light | <p>Esta é a principal fonte de luz<br>da cena e a que cria toda a<br>atmosfera do pôr do sol. Ela<br>está alinhada com o sol no<br>Skybox.</p>                                              |
| <div><figure><img src="../.gitbook/assets/Directional Light Settings II (1).png" alt=""><figcaption></figcaption></figure></div> | Directional Light | <p>Esta é a segunda fonte de luz<br>da cena e ajuda a criar mais<br>iluminação e sombras contra a fonte de luz principal.</p>                                                               |
| <div><figure><img src="../.gitbook/assets/Skybox.png" alt=""><figcaption></figcaption></figure></div>                            | Skybox            | <p>Um Skybox de pôr do sol com<br>uma lua distante e estrelas.</p>                                                                                                                          |
| <div><figure><img src="../.gitbook/assets/Cloud System.png" alt=""><figcaption></figcaption></figure></div>                      | Sistema de Nuvens | <p>Um dos meus itens favoritos<br>para compor uma cena. Possui dois níveis de nuvens, baixo e alto, com configurações separadas, como formato, densidade, quantidade, velocidade e cor.</p> |

***

## Publicar para a VIVERSE

Aqui estão os próximos passos para que seu espaço seja publicado no VIVERSE.

{% stepper %}
{% step %}
### Login no VIVERSE

Abra uma sessão de terminal e digite `viverse-cli auth login` e forneça suas\
credenciais.

<div align="left"><figure><img src="../.gitbook/assets/Login to Viverse.png" alt=""><figcaption></figcaption></figure></div>
{% endstep %}

{% step %}
### Mude para o directório

Mude para o diretório onde sua compilação está localizada `cd D:\Unity\4 - HTC Viverse\Viverse Test\WebGL Builds`
{% endstep %}

{% step %}
### Crie o aplicativo

Crie o aplicativo VIVERSE digitando `viverse-cli app create`. Você receberá o ID do aplicativo do seu espaço para ser usado ao publicá-lo.
{% endstep %}

{% step %}
### Publique

Publique no VIVERSE digitando `viverse-cli app publish D:\Unity\4 - HTC Viverse\Viverse Test\WebGL Builds\Build --app-id c47rbqg6v8`, com o caminho completo ou `viverse-cli app publish --app-id c47rbqg6v8` se você já estiver localizado no diretório correto.

<div align="left"><figure><img src="../.gitbook/assets/Publish to Viverse.png" alt=""><figcaption></figcaption></figure></div>
{% endstep %}

{% step %}
### Visualização

Navegue até a URL de visualização criada para o mundo. Você também pode acessar o mundo e suas configurações em studio.viverse.com/content

<figure><img src="../.gitbook/assets/Navigate to the preview.png" alt=""><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Envie-os para análise

Por padrão, os mundos enviados só serão acessíveis por meio de URLs de pré-visualização. Para posicionamento e curadoria em nossas páginas, facilitando o compartilhamento da sua experiência, envie-os para análise.

<figure><img src="../.gitbook/assets/Submit for review.png" alt=""><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}
