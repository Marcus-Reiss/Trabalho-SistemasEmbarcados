# Trabalho final

## SAA0356 - Sistemas Embarcados para Veículos Aéreos

### Professor: Glauco A. P. Caurin

### Alunos:

- João Henrique Vale Farias (12549922)
- Marcus Vinícius Costa Reis (12549384)
- Rafael Romaquela Baptista (12549508)
- Samuel Aissa da Silva (12549745)

***

## Passo a passo utilizado para os commits

1. **Adicionar o repositório do Grilo como submódulo**

   Dessa forma, tem-se um *backup* prático dentro do repositório principal.

   ```
   git submodule add https://github.com/griloHBG/eesc-aero-embedded-systems.git
   ```

2. **Copiar o repositório do Grilo para outra pasta**

   Dentro dessa nova pasta, será possível modificar os arquivos conforme desejado.

   ```
   mkdir repo-grilo
   cp -r ./eesc-aero-embedded-systems/* ./repo-grilo/   
   ```

3. **Realizar as modificações no código principal**

   Foram feitas algumas pequenas modificações no código principal, de modo a obter ganhos de performance.

4. **Modificar o *Dockerfile***

   O *Dockerfile* precisou ser modificado para incluir as ferramentas de desenvolvimento comumente chamadas *autotools (autoconf, automake e libtool)*, bem como o pacote *crossbuild-essential-arm64*, responsável pela compilação cruzada. Além disso, adicionou-se um caminho para a pasta *lely-core* e executou-se o comando *autoreconf*, que constitui uma boa prática para configuração em *Debian*. Abaixo são apresentadas as linhas adicionadas:

   ```
   RUN apt-get update && apt-get install -y autoconf automake libtool crossbuild-essential-arm64
   RUN cd /lely-core && autoreconf -i
   ```

5. **Instalar a biblioteca Lely-core**

   Dentro da pasta dockerfile, clonou-se o repositório do gitlab da *lely-core*:

   ```
   git clone https://gitlab.com/lely_industries/lely-core.git
   ```

6. **Realizar o *build* do Dockerfile, rodar o contêiner e fazer o *cross-compiling* do código principal**

   Na pasta dockerfile, executa-se o comando de *build*:

   ```
   docker build . -t build_manopla -f ./Dockerfile
   ```

   Em seguida, retorna-se ao *root* do repositório e mapeia-se um volume entre o diretório atual no *host* e um diretório correspondente dentro do contêiner, criado com base na imagem *build_manopla*. Esse mapeamento garante que os arquivos e diretórios do *host* fiquem acessíveis e possam ser manipulados dentro do contêiner:

   ```
   cd ..
   docker run --rm -it -v $(pwd):/projeto build_manopla bash
   ```

   Agora, dentro do contêiner, muda-se para o diretório 'projeto' e realiza-se a compilação cruzada do código fonte para a arquitetura *arm64*:

   ```
   cd /projeto
   mkdir build_arm && cd build_arm
   cmake -DARM_TARGET=1 ..
   make
   ```

7. **Adicionar este README ao repositório**

   Inferiu-se que faria mais sentido adicionar este README ao final das alterações, como forma de relato do que foi feito durante os commits prévios.
