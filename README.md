
<!-- README.md is generated from README.Rmd. Please edit that file -->
<!-- badges: start -->

![forthebadge](https://img.shields.io/badge/GEMM-Building-orange)
![forthebadge](https://forthebadge.com/images/badges/built-with-science.svg)

<!-- badges: end -->

# Análise do Metagenoma total - Shotgun <img src="imgs/1.png" align="right" width = "120px"/>

**Autor: MsC. Kelly Hidalgo**

🇧🇷 Pipeline para a montagem e anotação funcional de metagenomas totais.
Este pipeline contempla todas as etapas do processamento, desde a
avaliação da qualidade das sequências, trimagem, montagem, cálculo da
cobertura, predição e anotação funcional e taxonômica dos genes.

> 🇪🇸Pipeline para montaje y anotación funcional de metagenomas totales.
> Este pipeline contempla todas las etapas del procesamiento, desde la
> evaluación de la calidad de las secuencias, *trimming*, montaje,
> cálculo de la cobertura, predicción y anotación funcional y taxonómica
> de genes.

## Ferramientas bioinformáticas

### Instalação Anaconda

🇧🇷 É recomendável instalar Anaconda, pois é a forma mais fácil para
instalar as ferramentas bioinformáticas necessárias pro desenvolvimento
deste pipeline. Anaconda é uma distribuição livre e aberta das
linguagens *Python* e *R*, utilizada na ciência de dados e
bioinformática. As diferente versões dos programas se administram
mediante um sinstema de gestão chamado *conda*, o qual faz bastante
simples instalar, rodar e atualizar programas.
[Aqui](https://conda.io/projects/conda/en/latest/user-guide/install/index.html)
se encontram as instruções para a instalação de Anaconda.

Depois de instalado, *Anaconda* e o gestor *Conda*, podram ser criados
*ambientes virtuais* par a instalação das diferentes ferramentas
bioinformática que serão usadas.

> 🇪🇸 Es recomendable instalar Anaconda, pues es la forma más fácil para
> instalar las herramientas bioinformáticas necesarias para el
> desarrollo de este pipeline. Anaconda es una distribución libre y
> abierta de los lenguajes *Python* y *R*, utilizada en ciencia de datos
> y bioinformática. Las diferentes versiones de los programas se
> administran mediante un sistema de gestión llamado *conda*, el cual
> hace bastante sencillo instalar, correr y actualizar programas.
> [Aqui](https://conda.io/projects/conda/en/latest/user-guide/install/index.html)
> se encuentran las instrucciones para la instalación de Anaconda.
>
> Después de instalado *Anaconda* y su gestor *Conda*, podran ser
> creados *ambientes virtuales* para la instalación de las diferentes
> herramientas bioinformáticas que serán usadas.

------------------------------------------------------------------------

# I. Metagenômica

## 0. Organizando os dados

### 0.1. Sequências

🇧🇷 Em este tutorial serão usadas seis metagenomas exemplo para rodar
todo o *pipeline*. Descarregue os datasets usando o comando `wget`.

> 🇪🇸 En este tutorial serán usados seis metagenomas ejemplo para correr
> todo el *pipeline*. Descargue los datasets usando el comando `wget`.

    # Crie um diretório para este tutorail
    mkdir metagenomica 
    cd metagenomica/

Agora dentro de metagenomica crie outro diretório chamado `00.RawData`,
onde vai descarregar o dataset de exemplo para este tutorial

    mkdir 00.RawData

Para descarregar o dataset…

    curl -L https://figshare.com/ndownloader/articles/19015058/versions/1 -o 00.RawData/dataset.zip
    unzip 00.RawData/dataset.zip
    rm 00.RawData/dataset.zip

Com `ls`você pode ver o conteúdo descarregado.

    ls 00.RawData

Por último “listou” (`ls`) o conteúdo da pasta `00.RawData`, vai
observar que têm 4 amostras paired-end (R1 e R2)

    Sample1_1.fq.gz Sample1_2.fq.gz Sample2_1.fq.gz Sample2_2.fq.gz Sample3_1.fq.gz Sample3_2.fq.gz Sample4_1.fq.gz Sample4_2.fq.gz Sample5_1.fq.gz Sample5_2.fq.gz Sample6_1.fq.gz Sample6_2.fq.gz

É fortemente recomendado rodar os comandos desde o diretório base, que
neste caso é: `metagenomica/`

> ## **Nota importante: A maioria dos comandos que encontrará a continuação, terão um parâmetro para definir o número de núcleos/threads/cpus (`-t/--threads/`) que serão usados para o processamento de cada comando. Coloque o número de núcleos baseado na sua máquina o servidor que esteja usando para rodar as análises. Procure não usar todos os núcleos disponíveis.**

## 1. Controle da Qualidade

## 1.1. Avaliação da qualidade

🇧🇷 Para a avaliação da qualidade será usado o programa
[FastQC](http://www.bioinformatics.babraham.ac.uk/projects/fastqc/) que
é uma ferramenta que permite observar graficamente a qualidade das
sequencias de Illumina.

> 🇪🇸 Para la evaluación de la calidad será usado el programa
> [FastQC](http://www.bioinformatics.babraham.ac.uk/projects/fastqc/)
> que es una herramienta que permite observar graficamente la calidad de
> las secuencias de Illumina.

### 1.1.1. Instalação

Las instruções para a instalação usando conda se encontram
[aqui](https://anaconda.org/bioconda/fastqc). No entanto neste tutorial
também serão apresentados.

Como já foi explicado anteriormente, com conda é possível criar
ambientes virtuais para instalar as ferramentas bioinformáticas. O
primeiro ambiente que será criado se chamará **QualityControl**, onde se
instalaram os programas relacionados com esse processo.

> 🇪🇸 [FastQC](http://www.bioinformatics.babraham.ac.uk/projects/fastqc/)
> es una herramienta para evaluar graficamente la calidad de las
> secuencias de Illumina.
>
> Las instrucciones para instalación usando conda se encuentran
> [aqui](https://anaconda.org/bioconda/fastqc). Sin embargo aqui en este
> tutorial también serán presentadas
>
> Como ya fue explicado anteriorimente, con conda es posible crear
> ambientes virutuales para instalar las herramientas bioinformáticas.
> El primer ambiente que será creado se llamará **QualityControl**,
> donde se instalaran los programas relacionados con este proceso.

    conda create -n QualityControl

🇧🇷 Durante o processo, o sistema perguntará se deseja proceder com a
creação do ambiente, com as opções y/n (sim ou não). Escreva `y` e
depois disso o ambiente virutal estará criado.

Para instalar as ferramentas dentro do ambiente anteriormente criado, é
necessário ativá-lo.

> 🇪🇸 Durante el proceso, el sistema preguntará sí desea proceder con la
> creación del ambiente, con las opciones y/n (si o no). Escriba `y` y
> después de eso el ambiente virtual estará creado.
>
> Para instalar las herramientas dentro del ambiente anteriormente
> creado, es necesario activarlo

    conda activate QualityControl

🇧🇷 O ambiente estará ativo quando o nome se encontre ao começo da linha
do comando, asssim: `(QualityControl) user@server:~/$`. Posteriormente
se procede à instalação do programa:

> 🇪🇸 El ambiente estará activo cuando el nombre de éste se encuentra en
> el comienzo de la linea de comando, así:
> `(QualityControl) user@server:~/$`.
>
> Posteriormente se procede a la instalación del programa:

    conda install -c bioconda fastqc

### 1.1.2. Uso

🇧🇷 A primeira etapa do processo é a avaliação da qualidade das
sequências cortas (Illumina paired end) usando *FastQC*, com o objetivo
de determianr se é necessário trimar ou filtrar as sequências da baixa
qualidade para nos próximos pasos.

Esta etapa é para identificar principalmente as sequências *outlier* com
baixa qualidade (*Q* &lt; 20)

Ative o ambiente `QualityControl`:

> 🇪🇸 La primera etapa del proceso es la evaluación de la calidad de las
> secuencias cortas (Illumina paired end) usando *FastQC*, con el
> objetivo de determinar sí es necesario trimar o filtrar las secuencias
> de baja calidad en los próximos pasos.
>
> Ésta etapa es para identificar principalmente las secuencias *outlier*
> con baja calidad (*Q* &lt; 20).
>
> Active el ambiente `QualityControl`:

    conda activate QualityControl

    ## Onde vc está?
    pwd

🇧🇷 Deve estar em `~/metagenomica/`. Se esse não é o resultado del
comando `pwd`, use o comando `cd` para chegar no diretório desejado.

> 🇪🇸 Debe estar em `~/metagenomica/`. Si ese no es el resultado del
> comando `pwd`, use el comando `cd` para llegar en el directorio base.

Execute **FastQC**:

    ## Crie um directório para salvar o output do FastQC
    mkdir 01.FastqcReports
    ## Run usando 10 threads
    fastqc -t 10 00.RawData/* -o 01.FastqcReports/

**Sintaxe** `fastqc [opções] input -o output`

🇧🇷 O comando `fastqc` tem várias opções ou parâmetros, entre eles,
escolher o número de núcleos da máquina para rodar a análise, para este
exemplo `-t 10`. O input é o diretório que contem as sequências
`00.RawData/*`, o `*` indica ao sistema que pode analisar todos os
arquivos que estão dentro desse diretório. O output, indicado pelo
parâmtero `-o`, é o diretório onde se deseja que sejam guardados os
resultados da análise. A continuação se encontram uma explicação
detalhada de cada output gerado.

> 🇪🇸 El comando `fastqc` tiene varias opciones o parametros, entre
> ellas, escoger el número de núcleos de la máquina para correr el
> análisis, para este caso `-t 10`. El input es el directorio que
> contiene las secuencias `00.RawData/*`, el `*` indica al sistema que
> puede analizar todos los archivos que están dentro de ese directorio.
> El output, indicado por el parametro `-o`, es el directorio donde se
> desea que sean guardados los resultados del análisis. A continuación
> se encuentra una explicación detallada de cada output generado.

**Outputs**

🇧🇷

-   Reportes html `.html`: Aqui é possível ver toda informação de
    qualidade graficamente.

-   Zip files `.zip`: Aqui se encontram cada um dos gráficos de maneira
    separada. **IGNORE**

Descarregue os arquivos `html` e explore no seu *web browser*.

Observe as estatísticas básicas que se encontram na primeira tabela.
Alí, você pode saber quantas sequências tem, o tamanho e o %GC. O
gráfico mais importante para saber a quealidade das leituras, é o
primeiro, *Per base sequence quality*. Este gráfico é um boxplot com a
distribuição dos valores de qualidade *Phred Score* (eje y) em cada um
dos nucleotídeos das leituras (eje x). Se consideram sequências de
excelente qualidade quando o *Phred Score &gt; 30*. É norla que o pair 2
apresente uma qualidade um pouco inferior ao pair 1.

As amostras deste tutorial, apresentam qualidade um pouco baixa,
principalmente no pair2. Por tanto, será necessário fazer a fase da
trimagem.

> 🇪🇸 Observe las estadísticas básicas que se encuentran en la primera
> tabla. Allí, ud puede saber cuantas secuencias tiene, el tamaño y el
> %GC. El gráfico más importante para saber la calidad de las lecturas
> es el primero, *Per base sequence quality*. Este gráfico es un boxblot
> con la distribución de los valores de calidad *Phred Score* (eje y) en
> cada uno de los nucleótidos de las lecturas (eje x). Se consideran
> secuencias de excelente calidad cuando el *Phred Score &gt; 30*. Es
> normal que el pair 2 presente una calidad un poco inferior al pair 1.
>
> Las muestras de este tutorial, presentan calidad un poco baja,
> principalmente en el pair2. Por lo tanto, será necesario hacer la fase
> de depuración.

### 1.2. Trimagem

> 🇪🇸 1.2 Depuración

🇧🇷 [Trimmomatic](http://www.usadellab.org/cms/?page=trimmomatic) é um
programa pra filtrar (remover) leituras ou *reads* curtas de baixa
qualidade.

Trimmomatic tem vários parâmetros que podem ser considerados para
filtrar leituras com baixa qualidade. No presente tutorial usaremos
alguns deles. Se quiser saber que otros parâmetros e como funciona cada
um deles, consulte o
[manual](http://www.usadellab.org/cms/uploads/supplementary/Trimmomatic/TrimmomaticManual_V0.32.pdf).

> 🇪🇸 [Trimmomatic](http://www.usadellab.org/cms/?page=trimmomatic) es un
> programa para filtrar (remover) lecturas o *reads* cortas de baja
> calidad.
>
> Trimmomatic tiene vários parametros que pueden ser considerados para
> filtrar lecturas con baja calidad. Aqui usaremos algunos. Si quiere
> saber que otros parametros y como funciona cada uno de ellos, consulte
> el
> [manual](http://www.usadellab.org/cms/uploads/supplementary/Trimmomatic/TrimmomaticManual_V0.32.pdf).

### 1.2.1. Instalação

🇧🇷 Como se trata de uma ferramenta que participa dentro do processo de
control de qualidade, será instalada dentro do ambiente virtual
**QualityControl**.

> Como se trata de una herramienta que participa dentro del proceso de
> control de calidad, será instalada dentro del ambiente virtual
> **QualityControl**

    # Si no está activado el ambiente
    conda activate QualityControl

    # Instale Trimmomatic
    conda install -c bioconda trimmomatic

### 1.2.2. Uso

🇧🇷 Segundo foi avaliado no controle de qualidade, pode ser necessário
filtrar algumas leituras com qualidade baixa.

O programa Trimmomatic tem vários parâmetros que podem ser considerados
para filtrar reads com baixa qualidade. Aqui usaremos alguns. Se quer
saber que outros parâmetros e como funciona cada um deles, consulte o
[manual](http://www.usadellab.org/cms/uploads/supplementary/Trimmomatic/TrimmomaticManual_V0.32.pdf).

Para os dados aqui analizados se usara a seguinte linha de comando:

> 🇪🇸 Según fue evaluado en el control de calidad, puede ser necesario
> filtrar algunas lecturas con calidad baja.
>
> El programa Trimmomatic tiene vários parametros que pueden ser
> considerados para filtrar lecturas con baja calidad. Aqui usaremos
> algunos. Si quiere saber que otros parametros y como funciona cada uno
> de ellos, consulte el
> [manual](http://www.usadellab.org/cms/uploads/supplementary/Trimmomatic/TrimmomaticManual_V0.32.pdf).
>
> Para los datos aqui analizados se usará la siguiente linea de comando:

    # Activa o ambiente QualityControl
    conda activate QualityControl

    # Crie uma pasta para salvar as reads limpas
    mkdir 02.CleanData

    # Crie uma pasta para salvar as reads não pareadas
    mkdir unpaired

    # Corra Trimmomatic
    trimmomatic PE -threads 10 00.RawData/Sample1_1.fastq.gz 00.RawData/Sample1_2.fastq.gz 02.CleanData/Sample1_1_paired.fastq.gz unpaired/Sample1_1_unpaired.fastq.gz 02.CleanData/Sample1_2_paired.fastq.gz unpaired/Sample1_2_unpaired.fastq.gz LEADING:3 TRAILING:3 SLIDINGWINDOW:4:15 MINLEN:150

🇧🇷 Com o comando anterior você tem que rodar a linha de comando para
cada amostra. Se quiser rodar todas as amostras de maneira automâtica é
possível usar um *loop* `for` para executar esta tarefa.

> 🇪🇸 Con el comnado anterior ud tiene que correr esa línea de comando
> para cada muestra. Si quiere correr todas las muestras de manera
> automática es posible usar un *loop* `for` para ejecutrar esta tarea.

    # loop
    for i in 00.RawData/*1.fastq.gz 
    do
    BASE=$(basename $i 1.fastq.gz)
    trimmomatic PE -threads 10 $i  00.RawData/${BASE}2.fastq.gz 02.CleanData/${BASE}1_paired.fq.gz unpaired/${BASE}1_unpaired.fq.gz 02.CleanData/${BASE}2_paired.fq.gz unpaired/${BASE}2_unpaired.fq.gz LEADING:3 TRAILING:3 SLIDINGWINDOW:4:15 MINLEN:100
    done

**SINTAXE**
`trimmomatic PE -threads input_forward input_reverse output_forward_paired output_forward_unpaired output_reverse_paired output_reverse_unpaired [opções]`

🇧🇷 O comando anterior tem muitas partes. Primeiro, o nome do comando é
`trimmomatic`, a continuação a opção `PE` indica para o programa que as
sequências que irão ser analisadas são de tipo *paired end*. Depois se
encontram os inputs, forward (pair1) e reverse (pair2). Depois estão os
outputs, sendo o primeiro, as sequências forward pareadas (limpas) e não
pareadas (“descartadas”) e depois igual para as sequências reverse. Por
último se encontram os parâmetros de filtragem. Para este caso usamos os
parâmetros `SLIDINGWINDOW`, `LEADING` e `TRAILING`. O primeiro de eles,
gera uma janela deslizante, que em este caso vai de 4 em 4 bases,
cálcula a média do *Phred Score* e se estiver por baixo de 15 essas
bases serão cortadas. `LEADING` corta bases do começo da leitura que
estejam por debaixo do *threshold* de qualidade, igualmente faz o
`TRAILING` mas no final das leituras. `MINLEN` elimina todas as reads
com tamanho menor ao informado. Trimmomatic tem muitos mais parâmetros
para customizar, veja no
[manual](http://www.usadellab.org/cms/uploads/supplementary/Trimmomatic/TrimmomaticManual_V0.32.pdf).

Depois de rodar Trimmomatic é necessário avaliar a qualidade das
sequências limpas usando novamente FastQC.

> 🇪🇸 El comando anterior tiene muchas partes. Primero, el nombre del
> comando es `trimmomatic`, a continuación la opción `PE` indica para el
> programa que las secuencias que irán a ser analizadas son de tipo
> *paired end*. Después se encuentran los inputs, forward (pair1) y
> reverse (pair2). Después son los outputs, siendo primero las
> secuencias forward pareadas (limpias) y no pareadas (“descartadas”) y
> después las secuencias reverse. Por último se encuentran los
> parametros de filtrado. Para este caso usamos los parametros
> `SLIDINGWINDOW`, `LEADING` y `TRAILING`. El primero de ellos, genera
> una ventana deslizante, que en este caso va de 4 en 4 bases, cálcula
> el promedio del *Phred Score* y si está por debajo de 15 esas bases
> son cortadas. `LEADING` corta bases del comienzo de la lectura si
> están por debajo de *threshold* de calidad, lo mismo hace `TRAILING`
> pero al final de las lecturas. `MINLEN` elimina todas las lecturas con
> tamaño menor al informado. Trimmomatic tiene muchos más parámetros
> customizables, revise en el
> [manual](http://www.usadellab.org/cms/uploads/supplementary/Trimmomatic/TrimmomaticManual_V0.32.pdf).
>
> Después de correr Trimmomatic es necesario evaluar la calidad de las
> secuencias generadas (“limpias”) usando nuevamente FastQC.

    fastqc -t 10 02.CleanData/* -o 01.FastqcReports/

Descargue los reportes `.html` de las secuencias pareadas
(i.e. `01.FastqcReports/Sample1_1_paired_fastqc.html` y
`01.FastqcReports/Sample1_2_paired_fastqc.html`).

Faça uma tabela com o número de sequências antes e depois da trimagem
para calcular a porcentagem de *reads* que sobreveviveram ao processo.

> 🇪🇸 Haga una tabla con el número de secuencias antes y después de la
> depuración para calcular el porcentaje de *reads* que sobrevivieron al
> proceso.

### 1.3 Cobertura dos Metagenoma

🇧🇷 Além de limpar e trimar as sequências com baixa qualidade, é
necessário calcular a cobertura dos metagenomas.Este programa usa a
redundância de reads nos metagenomas para estimar a cobertura média e
prediz a quantidade de sequências que são requeridas para atingir o
*“nearly complete coverage”*, definida como  ≥ 95% ou  ≥ 99% de
cobertura média. A ferramenta [**NonPareil
v3.3.3**](https://nonpareil.readthedocs.io/en/latest/) será usada nesta
etapa.

> 🇪🇸 Además de limpiar y *trimar* las secuencias con baja calidad, es
> necesario calcular la cobertura de los metagenomas. Este programa usa
> la redundancia de las *reads* en los metagenomas para estimar la
> cobertura promedio y predice la cantidade de secuencias que son
> requeridas para conseguir el *“nearly complete coverage”*, definida
> como  ≥ 95% o  ≥ 99% de la cobertura promedio. La herramienta
> [**NonPareil v3.3.3**](https://nonpareil.readthedocs.io/en/latest/)
> será usada en esta etapa.

### 1.3.1. Instalação

🇧🇷 [NonPareil v3.3.3](https://nonpareil.readthedocs.io/en/latest/) é uma
ferramenta que será usada para o cálculo da cobertura dos metagenomas.
Devido a incompatibilidades com a versão do Python usado para escrever
esta ferramenta, ela será instalada em um ambiente diferente ao de
controle de qualidade, chamado **NonPareil**.

> 🇪🇸 [NonPareil](https://nonpareil.readthedocs.io/en/latest/) es una
> herramienta que será usada para el cálculo de la cobertura de los
> metagenomas. Debido a incompatibilidades con la versión de Python
> usado para escribir esta herramienta, será instalada en un ambiente
> diferente al de control de calidad, llamado **NonPareil**.

    # Crie o ambiente
    conda create -n NonPareil

    # Instale NonPareil
    conda install -c bioconda nonpareil

### 1.3.2. Uso

Como *input* para esta análise só é necessário um pair de cada amostra,
e deve estar sem compressão.

    # Crie o diretório pra o output
    mkdir 03.NonPareil

    # entre no directorio
    cd 03.NonPareil

    # Copie os pair 1 da pasta 02.CleanData

    cp ../02.CleanData/*_1* ./

    # Descomprimir 
    gunzip -d *

🇧🇷 Agora está tudo pronto para rodar a análise, mas antes disso tome-se
o tempo para entender o comando que vai usar. Para conhecer que é cada
um dos argumentos, explore o menú de ajuda da ferramenta.

> 🇪🇸 Ahora está todo listo para correr el análisis, pero antes de eso
> tómese el tiempo para entender el comando que va a usar. Para conocer
> que es cada uno de los argumentos, explore el menú de ayuda de la
> herramienta.

    # Ative o ambiente NonPareil
    conda activate NonPareil

    # Explore o menú da ferramenta
    nonpareil --help

    # Comando do NonPareil para cada amostra
    nohup nonpareil -s Sample1.fq -T kmer -f fastq -b Sample1 -t 6 &

No caso, se tiver várias amostras pode usar o seguinte loop para
facilitar o processo.

    for i in ./*.fq
    do
    BASE=$(basename $i .fq)
    nonpareil -s $i -T kmer -f fastq -b $i -t 6
    done

**Sintaxe**

-   `-s`: caminho para o *input*
-   `-T`: algorítmo a ser usado. `kmer` é recomendado para arquivos
    `.fastq` e `alignment` é recomendado para arquivos `.fasta`.
-   `-f`: indique aqui o formato do input (p.e. `fastq` ou `fasta`)
-   `-b`: prefixo para os *outputs*
-   `-t`: número de threads

🇧🇷 Ao terminar esse processo, o programa terá criado varios
[*outputs*](https://nonpareil.readthedocs.io/en/latest/redundancy.html#output)
por cada amostra. Descarregue os arquivos `.npo`. Os quais são tabelas
delimitadas por tabulações com seis colunas. A primeira coluna indica o
esforço de sequenciamento (em número de reads), as demais colunas têm
informação sobre a distribuição da redundância a determinado esforço de
sequenciamento. Usando os arquivos `.npo` e o R, pode gráficar as curvas
de saturação. A continuação se encontra o script. Além dos arquivos
`.npo` é necessário criar um arquivo chamado `Samples.txt`, o qual deve
ter três colunas (separadas por tabulações), a primeira terá o nome de
cada arquivo `.npo`, a segunda o nome da amostra, e a terceira a cor em
formato JSON que vai ser usada para a curva. A continuação se encontram
uma série de comandos no bash para gerar o arquivo, no entanto este
arquivo pode ser construido em um bloco de notas, ou incluso no excel.

> 🇪🇸 Al terminar este proceso, el programa habrá creado varios
> [*outputs*](https://nonpareil.readthedocs.io/en/latest/redundancy.html#output)
> por cada muestra. Descargue los archivos `.npo`. Los cuales son tablas
> delimitadas por tabulaciones con seis columnas. La primera columna
> indica el esfuerzo de secuenciación (en número de *reads*), las demás
> columnas tienen información sobre la distribución de la redundancia a
> determinao esfuerzo de secuenciación. Usando los archivos `.npo` e R,
> puede gráficar las curvas de saturación. A continuación se encuentra
> el script.
>
> Además de los archivos `.npo` es necesario crear un archivo llamado
> `Samples.txt`, el cual debe tener tres columnas (separadas por
> tabulaciones), la primera tendrá el nombre de cada archivo `.npo`, la
> segunda el nombre de la muestra, y la tercera el color en formato JSON
> que va a ser usado para la curva. A continuación se encontran una
> serie de comandos en bash para generar el archivo, sin embargo, este
> archivo puede ser construido en un bloc de notas, o incluso en excel.

    # Cria um arquivo com os nomes dos arquivos
    ls *.npo > files.txt

    # Cria um arquivo com os nomes das amostras

    ls *.npo | sed 's/_1_paired.fq.npo//g' > prefix.txt

Agora precisa criar uma lista de cores para diferenciar suas amostras no
gráfico. Use o site [IWantHue](http://medialab.github.io/iwanthue/) para
criar uma paleta com o número de cores igual ao númerop de amostras.
Copie os códigos **HEX json** das cores e coloque dentro de um arquivo
(elimine as vírgulas):

> 🇪🇸 Ahora necesita crear una lista de colores para diferencias sus
> muestras en el gráfico. Use el sitio de internet
> [IWantHue](http://medialab.github.io/iwanthue/) para crear una paleta
> con el número de colores igual al número de muestras. Copie los
> códigos **HEX json** de los colores e coloque dentro de un archivo
> (elimine las comas):

    # Crie o arquivo
    nano colors.txt

    # Copie e cole os códigos
    "#c151b6"
    "#5eb04d"
    "#7d65ce"
    "#b5b246"
    "#688ccd"
    "#4bb092"

Cree o arquivo final com os títulos de las columnas e una los três
arquivos gerados anteriormente:

    echo -e 'File\tName\tCol' > Samples.txt

    # Unindo os arquivos dentro de Samples.txt
    paste -d'\t' files.txt prefix.txt colors.txt >> Samples.txt

Use `less` para explorar o arquivo, ele deve se ver assim:

    File    Name    Col
    Sample1.npo   Sample1   "#c151b6"
    Sample2.npo   Sample2   "#5eb04d"
    Sample3.npo   Sample3   "#7d65ce"
    Sample4.pno   Sample4   "#b5b246"
    Sample5.npo   Sample5   "#688ccd"
    Sample6.npo   Sample6   "#4bb092"

Descarregue os arquivos `.npo` e o arquivo `Samples.txt`. Usando o
seguinte script do R, grafique as curvas de saturação. **Nota:** todos
os arquivos descarregados devem estar dentro de uma pasta só, p.e.
`03.NonPareil`.

Descarregue o script
[Nonpareil](https://github.com/khidalgo85/Metagenoma_Total_Shotgun/blob/master/nonpareil.R)

``` r
install.packages("Nonpareil") #para instalar o pacote
library(Nonpareil) # ativa o pacote
setwd("~/03.NonPareil") # determina seu diretório de trabalho (coloque o seu, onde colocou os arquivos .npo e o arquivo Samples.txt)

Samples <- read.table('Samples.txt', sep='\t', header=TRUE, as.is=TRUE); #lê o arquivo Samples.txt com a informação das amostras

attach(Samples);
nps <- Nonpareil.set(File, col=Col, labels=Name, 
                     plot.opts=list(plot.observed=FALSE, 
                                    ylim = c(0, 1.05),
                                    legend.opts = FALSE)) #grafica as curvas

Nonpareil.legengd(nps, x.intersp=0.5, y.intersp=0.7, pt.cex=0.5, cex=0.5) #coloca e personaliza a legenda
  
detach(Samples);
summary(nps) #mostra o resumo em forma de tabela
```

Vai obter um gráfico com as curvas de saturação de cada amostra, como
este:

<img src="imgs/nonpareil.png" align='center' width="80%">

🇧🇷 As linhas tracejadas <font color='red'> vermelha </font> e
<font color='gray'> cinza </font> representam os *threshold* de 95% e
99% da cobertura média, respeitivamente. O circulo em cada curva
representa a cobertura atual das amostras, o ideal é que esteja por cima
do primeiro *threshold*. As curvas também apresentam a estimação de
quanto esforço de sequenciamento é necessário (zetas no eixo x). Devido
a que se trata de um dataset exemplo que foi obtido apartir de um
subSample aleatorio de um conjunto de dados, a maioria das amostras não
conseguem uma boa cobertura. As curvas reais para as amostras originais
se apresentam a continuação:

> 🇪🇸 Las líneas punteadas <font color='red'> roja </font> y
> <font color='gray'> gris </font> representam los *threshold* de 95% y
> 99% de cobertura promedio, respectivamente. El círculo en cada curva
> representa la cobertura actual de las muestras, lo ideal es que estén
> por encima del primer *threshold*. Las curvas también presentan la
> estimación de cuanto esfuerzo de secuenciación es necesario (flechas
> en el eje x). Debido a que se trata de un dataset ejemplo que fue
> obtenido a partir de un subSample aleatorio de un conjunto de datos,
> la mayoria de las muestras no consiguen una buena cobertura. Las
> curvas reales para las muestras originais se presentan a continuación:

<img src="imgs/realnonpareil.png" align='center' width="80%">

### 1.4. Análise de Distâncias MinHash

🇧🇷 Após obter as sequências limpas, de boa qualidade, e determinar a
cobertura dos metagenomas, é possível fazer a montagem. No entanto, pode
ser incluído um passo extra antes da montagem e é verificar a
similaridade dos datasets para determinar se pode ser usada a abordagem
de *co-assembly*, onde são misturadas as *reads* de vários metagenomas
para gerar os contigs. O programa [**Mash
v2.3**](https://mash.readthedocs.io/en/latest/) usa uma técnica chamada
redução de dimensionalidad *MinHash* que avalia as distâncias um a um
entre os datasets.

> 🇪🇸 Después de obtener las secuencias limpias, de buena calidad, y
> determinar la cobertura de los metagenomas, es posible hacer el
> montaje. Sin embargo, puede ser incluído un paso extra antes del
> montaje y es verificar la similaridade de los datasets para determinar
> si puede ser usado el abordaje de *co-assembly*, donde son mezcladas
> las *reads* de varios metagenomas para generar los contigs. El
> programa [**Mash v2.3**](https://mash.readthedocs.io/en/latest/) usa
> una técnica llamada reducción de dimensionalidad *MinHash* que evalua
> las distancias un a un entre los datasets.

### 1.4.1. Instalação

🇧🇷 [Mash v2.3](https://mash.readthedocs.io/en/latest/) é uma ferramenta
que usa a técnica de redução da dimensionalidade *MinHash* para calcular
as distâncias um a um entre os datasets, assim, é possível determinar se
os metagenomas são similares ou não para serem montados usando
*co-assembly*.

🇧🇷 Por ser considerada uma ferramenta que participa no processo de
assembly, será instalada dentro de um ambiente virtual chamado
**Assembly**.

> 🇪🇸 [Mash](https://mash.readthedocs.io/en/latest/) es una herramienta
> que usa la técnica de reducción de dimensionalidad *MinHash* para
> calcular las distancias un a un entre los datasets, así, es posible
> determinar si los metagenomas son similares o no para ser ensamblados
> usando *co-assembly*.
>
> 🇪🇸 Por ser considera una herramienta que participa en el proceso de
> ensamble, será instalada dentro de un ambiente virtual llamado
> **Assebly**.

    # Crie o ambiente virtual
    conda create -n Assembly

    # Instale Mash
    conda install -c bioconda mash

### 1.4.2. Uso

    ## Crie uma pasta para o output
    mkdir 04.MinHash

🇧🇷 O primeiro paso é concatenar os reads 1 e 2, e armazenar eles na nova
pasta criada `04.MinHash/`.

**Nota:** Se você trimou suas sequências, deve usar os arquivos gerados
pelo **Trimmomatic** na pasta `02.CleanData`, se pelo contrário suas
sequências estavam de boa qualidade e não foi necessário trimar, use os
arquivos originais, que estão dentro da pasta `00.RawData/`.

> 🇪🇸
>
> **Nota:** Si usted filtró sus secuencias, debe usar los archivos
> generados por **Trimmomatic** en el directorio `02.CleanData`, si por
> el contrario sus secuencias estaban de buena calidade y no fue
> necesario filtrar, use los archivos originales, que están dentro de la
> carpeta `00.RawData`.

    for i in 02.CleanData/*_1_paired.fq.gz
    do
    BASE=$(basename $i _1_paired.fq.gz)
    cat $i 02.CleanData/${BASE}_2_paired.fq.gz > 04.MinHash/${BASE}.fq
    done

🇧🇷 Depois será criado um *sketch* para combinar todas as amostras.
Usando `mash info` pode verificar o conteúdo e, em seguida, estimar as
distâncias par a par:

> 🇪🇸
>
> Después será creado un *sketch* para combinar todas las muestras.
> Usando `mash info` puede verificar el contenido y, en seguida, estimar
> las distancias par a par:

    mash sketch -o 04.MinHash/reference 04.MinHash/Sample1.fq 04.MinHash/Sample2.fq 04.MinHash/Sample3.fq 04.MinHash/Sample4.fq 04.MinHash/Sample5.fq 04.MinHash/Sample6.fq

    #verifiyng
    mash info 04.MinHash/reference.msh

**Sintaxe**

`mash sketch -o reference [inputs]`

`mash info reference.msh`

-   `sketch`: Comando para criar um *sketch*, combinando todas as
    amostras, recomendado quando têm mais de três amostras.
-   `-o`: caminho pro *output*, criará um *sketch* `.msh`.
-   `inputs`: liste os inputs (sequencias concatenadas dos pair1 e
    pair2)
-   `info`: pode verificar o conteúdo do `sketch`
-   `reference.msh`: *sketch* criado

Por último, calcule as distâncias entre cada par de metagenomas usando
`mash dist` e salve o resultado no arquivo `distancesOutput.tsv`.

    mash dist 04.MinHash/reference.msh 04.MinHash/reference.msh -p 6 -t > 04.MinHash/distancesOutputFinal.tsv

**Sintaxe** `mash dist [reference] [query] [options]`

-   `dist`: comando para calcular as distâncias entre cada par de
    mategenomas, baseado na distância *MinHash*.
-   `reference`: aqui pode colocar o *sketch* criado, ou arquivos `.fq`,
    `fasta`.
-   `query`: ídem
-   `-p`: número de threads
-   `-t`: indica o tipo de formato matriz

Descarregue o output (`04.MinHash/distancesOutputFinal.tsv`) e use o
seguinte script do R para plotar um heatmap com as distâncias.

Descarregue o script para graficar o heatmap das distancias
[MinHash](https://github.com/khidalgo85/Metagenoma_Total_Shotgun/blob/master/minhash.R)

``` r
setwd("~/04.MinHash/")

# install.packages('dplyr')
library(dplyr)
# install.packages('stringr')
library(stringr)
# install.packages('tidyverse')
library(tidyverse)

data <- read.table("distancesOutputFinal.tsv", comment.char = '', 
                    header = TRUE ) %>% 
  rename(X = X.query) 
  

data$X <- str_remove_all(data$X, "04.MinHash/")
data$X <- str_remove_all(data$X, ".fq")

names <- c("X", data[,1])

colnames(data) <- names

data <- column_to_rownames(data, var="X")

library(pheatmap)


pheatmap(data)
```

Vai obter um heatmap com clusterização:

<img src="imgs/minhash.png" align='center' width="80%">

Como pode ser observado, se formaram vários clusters, por exemplo
Sample5, Sample4 e Sample1, Sample3 e 2, e Sample6 formou um cluster
aparte. Assim, poderiam ser feitos dois co-assemblies e um assembly
individual. No entanto para facilitar o processo no tutorial, será feito
um co-assembly só, com todas as amostras.

## 2. Montagem dos Metagenomas

🇧🇷 A montagem dos metagenomas é a etapa mais importante do processo,
porque os demais passos para adelante dependen de uma boa montagem. No
caso dos metagenomas, se trata de um proceso que não é para nada
trivial, requer um grande esforço computacional. Por este motivo, serão
testados vários parâmetros, para comparar cada montagem e decidir qual é
o melhor para ás análises *downstream*. Neste processo será usado o
montador [Spades v3.15.3](https://github.com/ablab/spades).

> 🇪🇸 El montaje de los metagenomas es la etapa más importante del
> proceso, porque los demás pasos para adelante dependen de un buen
> ensamble. En el caso de los metagenomas, se trata de un proceso que no
> es para nada trivial, requiere un gran esfuerzo computacional. Por
> este motivo serán testados varios parámetros, para comparar cada
> ensamble y decidir cual es el mejor para los análisis *downstream*. En
> este proceso será usado el montado [Spades
> v3.15.3](https://github.com/ablab/spades).

### 2.1. Instalação

🇧🇷 [Spades v3.15.3](https://github.com/ablab/spades) é um dos montadores
de genomas e metagenomas, mais conhecido e com melhores resultados, pode
ser usado tanto para leituras curtas como longas. Leia atentamente o
[manual](http://cab.spbu.ru/files/release3.15.2/manual.html), já que
este programa tem muitas opções diferentes. Spades usa o algorítmo do
*Grafo de Bruijn* para a montagem das secuências.

Siga as seguintes instruções para a instalação do **Spades** dentro do
ambiente virtual *Assembly*.

> 🇪🇸 [Spades v3.15.3](https://github.com/ablab/spades) es uno de los
> ensambladores de genomas y metagenomas, más conocido y con mejores
> resultados, puede ser usado tanto para lecturas cortas como largas.
> Lea atentamente el
> [manual](http://cab.spbu.ru/files/release3.15.2/manual.html), ya que
> este programa tiene muchas opciones diferentes. Spades usa el
> algorítmo del *Grafo de Bruijn* para el montaje de las secuencias.
>
> Siga las siguientes instrucciones para la instalación de **Spades**
> dentro del ambiente virtual *Assembly*.

    # Active el ambiente virtual
    conda activate Assembly

    # Instale Spades
    conda install -c bioconda spades

### 2.2. Uso

🇧🇷 Agora é momento de fazer as montagens. Use o resultado da análisis de
distâncias *MinHash* para decidir como serão feitos as montagens.
Amostras muito próximas pode fazer *co-assembly*, para amostras
distantes é recomendado montar individualmente. Opcionalmente podem ser
usadas as sequências no pareadas (sequências “descartadas” pelo
Trimmomatic). O montador usado neste método será
[Spades](https://github.com/ablab/spades).

A continuação se encontram os comandos se sua montagem for individual:

> 🇪🇸 Ahora es el momento de hacer los ensamblajes. Use el resultado del
> análisis de distancias *MinHash* para decidir como serán hechos los
> montajes. Muestras muy próxima puede hacer *co-assembly*, para
> muestras distantes es recomendado montar individualmente.
> Opcionalmente pueden ser las secuencias no pareadas (secuencias
> “descartadas” por Trimmomatic). El montador usado en este método será
> [Spades](https://github.com/ablab/spades).

1.  Criar um diretório para todas as montagens

<!-- -->

    mkdir 05.Assembly

2.  Se você quiser usar as *reads* no pareadas (saída do
    **Trimmomatic**), deve primeiro concatenarlas em um arquivo só

<!-- -->

    cat unpaired/Sample1_1_unpaired.fq.gz unpaired/Sample1_2_unpaired.fq.gz > unpaired/Sample1_12_unpaired.fq.gz

3.  Montagem com MetaSpades

<!-- -->

    metaspades.py -o 05.Assembly/Sample1/ -1 02.CleanData/Sample1_1_paired.fq.gz -2 02.CleanData/Sample1_2_paired.fq.gz -s unpaired/Sample1_12_unpaired.fq.gz -t 6 -m 100 -k 21,29,39,59,79,99,119

**Sintaxe**

-   `metaspades.py`: script para montar metagenomas
-   `-o`: caminho para diretório de saída
-   `-1`: caminho para diretório do pair1
-   `-2`: caminho para diretório do pair2
-   `-s`: caminho para diretório das *reads* no pareadas
-   `-t`: número de threads
-   `-m`: Memória em gigas (máximo)
-   `-k`: lista de *k-mers*

🇧🇷 Se sua montagem for no modo *co-assembly* deve fazer uma etapa
anterior, onde vai concatenar todos os pair1 das amostras que serão
montadas e todos os pair2 das mesmas.

> 🇪🇸 Si su ensamblaje es en el modo *co-assembly* debe hacer una etapa
> anterior, donde va a concatenar todos los pair1 de las muestras que
> serán montadas y todos los pair2 de las mismas.

1.  Concatene os pair 1

<!-- -->

    cat 02.CleanData/Sample1_1.fq.gz 02.CleanData/Sample2_1.fq.gz 02.CleanData/Sample3_1.fq.gz 02.CleanData/Sample4_1.fq.gz 02.CleanData/Sample5_1.fq.gz 02.CleanData/Sample6_1.fq.gz > 02.CleanData/Sample_all_1.fq.gz

2.  Concatene os pair 2

<!-- -->

    cat 02.CleanData/Sample1_2.fq.gz 02.CleanData/Sample2_2.fq.gz 02.CleanData/Sample3_2.fq.gz 02.CleanData/Sample4_2.fq.gz 02.CleanData/Sample5_2.fq.gz 02.CleanData/Sample6_2.fq.gz > 02.CleanData/Sample_all_2.fq.gz

3.  Se você quiser usar as *reads* no pareadas (saída do
    **Trimmomatic**), deve primeiro concatenarlas em um arquivo só

<!-- -->

    cat unpaired/Sample1_1_unpaired.fq.gz unpaired/Sample1_2_unpaired.fq.gz unpaired/Sample2_1_unpaired.fq.gz unpaired/Sample2_2_unpaired.fq.gz unpaired/Sample3_1_unpaired.fq.gz unpaired/Sample3_2_unpaired.fq.gz unpaired/Sample4_1_unpaired.fq.gz unpaired/Sample4_2_unpaired.fq.gz unpaired/Sample5_1_unpaired.fq.gz unpaired/Sample5_2_unpaired.fq.gz unpaired/Sample6_1_unpaired.fq.gz unpaired/Sample6_2_unpaired.fq.gz > unpaired/Sample_all_unpaired.fq.gz

4.  Montagem com MetaSpades

<!-- -->

    metaspades.py -o 05.Assembly/ -1 02.CleanData/Sample_all_1.fq.gz -2 02.CleanData/Sample_all_2.fq.gz-s unpaired/Sample_all_unpaired.fq.gz -t 10 -m 100 -k 21,29,39,59,79,99,119

**Outputs**

Para conhecer os demais parâmetros do comando que não foram modificados
(usados por *default*), consulte o
[manual](http://cab.spbu.ru/files/release3.15.2/manual.html).

-   `corrected/`: contém as reads corregidas por **BayesHammer** em
    `.fastq.gz`

-   `scaffolds.fasta`: contém os scaffolds obtidos

-   `contigs.fasta`: contém os contigis obtidos

-   `assembly_graph_with_scaffolds.gfa`: contém o grafo da montagem en
    formato GFA 1.0.

-   `assembly_graph.fastg`: contém o grafo da montagem em formato FASTG

## 3. Controle de Qualidade das montagens

🇧🇷 Para avaliar a qualidade das montagens será usada a ferramenta
[**Quast v5.0.2**](http://quast.sourceforge.net/docs/manual.html)
(*QUality ASsesment Tool*), especificamente o *script* `metaquast.py`,
com o qual é possível determinar as principais estatísticas da montagem
(i.e. N50, número de contigs, tamanho total da montagem, tamanho dos
contigs, etc). **Metaquast** gera uma série de arquivos e reportes onde
é possível observar essas estatísticas básicas da montagem. É uma
ferramente muito útil para comparar montagens e escolher a melhor pro
mesmo conjunto de dados.

> 🇪🇸 Para evaluar la calidad de los montajes será usada la herramienta
> [**Quast v5.0.2**](http://quast.sourceforge.net/docs/manual.html)
> (*QUality ASsesment Tool*), especificamente el *script*
> `metaquast.py`, con el cual es posible determinar las principales
> estadísticas del montaje (i.e. N50, número de contigs, tamaño total
> del montaje, tamaño de los contigs, etc). **Metaquast** genera una
> serie de archivos y reportes donde es posible observar esas
> estadísticas básicas del montaje. Es una herramienta muy útil para
> comparar monatajes y escoger el mejor del mismo conjunto de datos.

### 3.1. Instalação

Crie um novo ambiente virtual, chamado bioinfo, onde se instalará
**Quast**.

    # Crie o ambiente
    conda create -n bioinfo

    # Ative o ambiente bioinfo
    conda activate bioinfo

    # Instale Quast
    conda install -c bioconda quast

### 3.2. Uso

🇧🇷 Se você tiver várias montagens e quer comparar todas é necessário
trocar os nomes dos assemblies, já que eles tem todos o mesmo nome,
`contigs.fasta` ou `scaffolds.fasta`. Use o comando `mv` para trocar os
nomes. Siga o seguinte exemplo:

> 🇪🇸 Si usted tiene varios ensambles e quiere compararlos es necesario
> cambiar los nombres de los montajes, ya que todos tienen el mismo
> nombre, `contigs.fasta` ou `scaffolds.fasta`. Use el comando `mv` para
> cambiar los nombres. Siga el siguiente ejemplo:

Por exemplo:

    mv 05.Assembly/Sample1/scaffolds.fasta 05.Assembly/Sample1/Sample1.fasta

    mv 05.Assembly/Sample45/scaffolds.fasta 05.Assembly/Sample45/Sample45.fasta

Para as amostras deste tutorial não é necessário trocar os nomes porque
só é uma montagem:

    # Crie um diretório pro output
    mkdir 06.AssemblyQuality

    # Rode Quast
    metaquast.py 05.Assembly/scaffolds.fasta -o 06.AssemblyQuality/ --threads 10

**Sintaxis**
`metaquast.py path/to/assembly/contigs.fasta -o path/to/output/`

-   Pode colocar vários inputs (montagens) separados por espaço.

**Interpretação dos resultados**

🇧🇷 A ideia de usar **Metaquast**, a parte de avaliar as estatísticas
básicas das montagens, é comparar varias montagens para escolher a
melhor. Por exemplo: entre menor seja o número de contigs é melhor,
porque significa que a montagem está menos fragmentada. E isso será
refletido no tamanho dos contigs que serão maiores. O valor de N50, é
melhor entre maior seja. Além, também é ideal um menor número de gaps e
Ns. No entanto, estas estatísticas funcionam melhor para genomas que
para metagenomas, por se tratar de um conjunto de microrganismos.

> 🇪🇸 La idea de usar **Metaquast**, aparte de evaluar las estidísticas
> básicas de los montajes, es comparar varios montajes para escoger el
> mejor. Por ejemplo: entre menor sea el número de contigs es mejor,
> porque significa que el montaje está menos fragementado. Y eso se
> reflejará en el tamaño de los contigs que serán más grandes. El valor
> de N50, es mejor entre mayor sea. Así mismo, es ideal menor número de
> gaps y Ns. Sin embargo, éstas estadísticas funcionan mejor para
> genomas que para metagenomas, por tratarse de un grupo de
> microorganismos.

**Outputs**

Explore o diretório do output usando o comando `ls`.

-   `06.AssemblyQuality/report.html`: Este relatório pode ser aberto em
    um *web browser* e contem as informações mais relevantes. Como
    número de contigs, tamanho del maior contig, tamanho total da
    montagem, N50, etc.

> 🇪🇸 `06.AssemblyQuality/report.html`: reporte puede ser abierto en un
> *web browser* y contiene las informaciones más relevantes. Como número
> de contigs, tamaño del mayor contig, tamaño total del montaje, N50,
> etc.

<img src="imgs/report_quast1.png" align="center" width = "100%"/>

-   `06.AssemblyQuality/report.tex`, `06.AssemblyQuality/report.txt`,
    `06.AssemblyQuality/report.tsv`, `06.AssemblyQuality/report.pdf`: é
    o mesmo relatório porém em diferentes formatos.

-   `06.AssemblyQuality/transposed_report.tsv`,
    `06.AssemblyQuality/transposed_report.tex`,
    `06.AssemblyQuality/transposed_report.tex`: Também é o relatório
    porém em formato tabular.

-   `06.AssemblyQuality/icarus_viewers/contig_size_viewer.html`:
    Visualizador das contigs

-   `06.AssemblyQuality/basis_stats/`: Dentro desta pasta se encontram
    vários gráficos em formato `.pdf`.

## 4. Predição das ORFs (*Open Reading Frame*)

🇧🇷 O objetivo desta etapa é procurar os marcos abertos de leitura ou
ORFs (em inglês) dentro dos contig/scaffols. Ou seja, predizer onde
iniciam e terminam os genes. Basicamente o programa procura por codons
de inicio, principalmente **ATG**, porém, também são códons de iniciação
**GTG** e **TTG**. Depois, procura os códons de parada, como **TAA**,
**TAG** e **TGA**.

O programa a usar para a predição das ORFs em procariotos é [Prodigal
v2.6.3 (*Prokaryotic Dynamic Programming Genefinding
Algorithm*)](https://github.com/hyattpd/prodigal/wiki).

> 🇪🇸 El objetivo de esta etapa es buscar los marcos abiertos de lectura
> o ORF (en inglés) dentro de los contigs/scaffolds. O sea, predecir
> donde incian y terminan los genes. Basicamente el programa busca por
> códones de inicio, principalmente **ATG**, sin embargo también son
> códones de inico **GTG** e **TTG**. Después, busca los códones de
> parada, como **TAA**, **TAG** y **TGA**.
>
> El programa a usar para la predicción de ORFs en procariotos es
> [Prodigal v2.6.3 (*Prokaryotic Dynamic Programming Genefinding
> Algorithm*)](https://github.com/hyattpd/prodigal/wiki).

### 4.1. Instalação

Crie um novo ambiente para instalação das ferramentas relacionadas com a
anotação de genes, chamada `Annotation`.

    # Crie o ambiente
    conda create -n Annotation

    # Ative o ambiente
    conda activate Annotation

    # Instale Prodigal
    conda install -c bioconda prodigal

### 4.2. Uso

Se tiver várias montagens, passe todas as montagens para uma pasta só.
No caso deste tutorial só é uma montagem então não é necessário.

Crie uma pasta chamada `07.GenePrediction` para colocar a saída do
**Prodigal**.

`mkdir 07.GenePrediction`

A continuação encontrará o comando **individual**

    prodigal -i 05.Assembly/scaffolds.fasta -f gff -o 07.GenePrediction/GenesCoordenates.gff -a 07.GenePrediction/GenesAA.faa -d 07.GenePrediction/GenesNucl.fa -p meta

Se tiver várias amostras, pode usar o seguinte loop para automatizar o
processo com todas as amostras:

    for i in 05.Assembly/scaffolds/*.fasta
    do
    BASE=$(basename $i .fasta)
    prodigal -i $i -f gff -o 07.GenePrediction/${BASE}.gff -a 07.GenePrediction/${BASE}.faa -d 07.GenePrediction/${BASE}.fa -p meta
    done

**Sintaxe**

    prodigal -i assembly.fasta -f <gbk, gff, sqn, sco> -o coord -a proteins.faa -d nucleotides.fa

-   `-i`: caminho para a montagem em formato `.fasta`, `.fa` ou `.fna`
-   `-f`: formato de saída pro arquivo de coordenadas, default `.gbk`
    (*Genbank-like format*), `.gff` (*Gene Feature Format*), `.sqn`
    (*Sequin feature table format*) ou `.sco` (*Simple coordinate
    input*)
-   `-o`: arquivo output com as coordenadas das ORFs
-   `-a`: sequências das ORFs em proteína
-   `-d`: sequências das ORFs em nucleotídeos

**Formato `.gff` (Gene Feature Format)**

🇧🇷 Este formato guarda as informações dos genes preditos pelo Prodigal.
Explore-o (`less GenesCoordenates.gff`).

Cada sequência comença com um *header* com as infromações da sequência
analizada, seguido de uma tabela separada por tabulações com informações
dos genes encontrados em dita sequência.

O *header* contém os seguentes campos:

> 🇪🇸 Este formato guarda las informaciones de los genes predichos por
> Prodigal. Explorelo (`less GenesCoordenates.gff`).
>
> Cada secuencia comienza con un *header* con las informaciones de la
> secuencia analizada, seguido de una tabla separada por tabulaciones
> con informaciones de los genes encontrados en dicha secuencia.
>
> El *header* contiene los siguientes campos:

-   **seqnum**: O número da sequência, começando pelo número 1.
-   **seqlen**: tamanho em bases da sequência
-   **seqhdr**: título completo da sequência extraído do arquivo
    `.fasta`.
-   **version**: versão do Prodigal usado
-   **run\_type**: modo de corrida, p.e. m*metagenomic*
-   **model**: informação sob o arquivo de treinamento usado para a
    predição.
-   **gc\_cont**: % de GC na sequência
-   **transl\_table**: Tabela do código genético usada para analizar a
    sequência. Para bactérias e archaeas é usada a [tabela
    11](https://www.ncbi.nlm.nih.gov/Taxonomy/Utils/wprintgc.cgi#SG11).
-   **uses\_sd**: 1 se o Prodigal usa o
    *[RBS](https://parts.igem.org/Ribosome_Binding_Sites) finder*, ou 0
    se usa outros *motifs*.

Después do *header* se encuentra una tabla con las informaciones de los
genes encontrados:

-   **seqname**: nome da sequência, neste caso nome do scaffold/contig.

-   **source**: nome do programa que gerou a predição

-   **feature**: tipo de *feature*, p.e. CDS (*Coding DNA Sequence*)

-   **start**: primeira posição da *feature*

-   **end**: útlima posição da *feature*

-   **score**: Valor numerico que geralmente indica a confiança do
    programa na predição da ORF.

-   **strand**: fita do DNA que foi encontrado a *feature*. A fita
    *forward* é definida como ‘+’, e a *reverse* como ‘-’.

-   **frame**: 0 indica que a primeira base da *feature* é a primeira
    base do códon de inicio, 1, que a segunda base da *feature* é a
    primeira base do códon de inicio.

-   **atribute**: información adicional sobre la *feature*, parada por
    ponto e vìrgula “;”.

    -   **ID**: identificador único de cada gene, consistindo em um
        número ordinal ID da sequência e um número ordinal ID do número
        do gene separados por "\_“. Por exemplo”1\_688" siginifa que é o
        gene número 688 da sequência 1.
    -   **partial**: indica se o gene está completo ou não. “0” indica
        que no gene foi encontrado o códon de inicio ou de parada, “01”
        indica que no gene só foi encontrado o cóndon de inicio, “11”
        indica que não foram encontrados nenhum dos dois códons e “00”
        indica que foram encontrados ambos códons.
    -   **start\_type**: sequência do códon de inicio.
    -   **stop\_type**: sequências do códon de parada
    -   **rbs\_motif**: *RBS motif* encontrado pelo Prodigal
    -   **rbs\_spacer**: número de bases entre o códon de inicio e o
        *motif* observado.
    -   **gc\_cont**: Conetúdo de GC no gene
    -   **conf**: nota de confiança pra o gene, representa a
        probabilidade que esse gene seja real.
    -   **score**: *score* total pro gene
    -   **cscore**: fração hexamero do *score*, o quanto este gene se
        parece com uma proteína verdadeira.
    -   **sscore**: *score* para o sitio de inicio da tradução do gene.
        é a soma dos três seguintes *scores*.
    -   **rscore**: *score* pro *RBS motif*
    -   **uscores**: *score* pra sequência em torno do códon de início.
    -   **tscore**: *score* para o tipo de códon de inicio
    -   **mscore**: *score* pros sinais restantes (tipo de códon de
        parada e informações da fita principal / reversa).

## 5. Anotação de genes

🇧🇷 A anotação dos genes é feita alinhando as ORFs preditas contra bases
de dados. No caso da anotação funcional, será usado o alinhador
[**Diamond**](https://github.com/bbuchfink/diamond) e as bases de dados
serão [**EggNOG**](http://eggnog5.embl.de/#/app/home) e
[**KEGG**](https://www.kegg.jp/kegg/). No caso da anotação taxonômica,
podem ser usados dois programas, o
[**Kaiju**](https://github.com/bioinformatics-centre/kaiju) ou o
[**Kraken2**](https://github.com/DerrickWood/kraken2/wiki).

> 🇪🇸La anotación de los genes es realizada alineando las ORFs predichas
> contra bases de dados. En el caso de la anotación funcional será usado
> el programa para alineamiento
> [**Diamond**](https://github.com/bbuchfink/diamond) y las bases de
> datos [**EggNOG**](http://eggnog5.embl.de/#/app/home) y
> [**KEGG**](https://www.kegg.jp/kegg/). Ya en el caso de la anotación
> taxonómica, pueden ser usados dos programas,
> [**Kaiju**](https://github.com/bioinformatics-centre/kaiju) o
> [**Kraken2**](https://github.com/DerrickWood/kraken2/wiki).

### 5.1. Instalação

#### 5.1.1 Obtenção das Bases de Dados

🇧🇷Para a obtenção das bases de dados, pode ir nos sites e descarregar
diretamente. No entanto, tenha em conta que a base de dados **KEGG** é
paga. Se você descarregar direto da fonte, deverá formatar as DBs para o
seu uso com Diamond (anotação funcional). Isto é feito com o comando
`makedb --in reference.fasta -d reference`.

Para facilitar, no seguinte link, você encontrará as bases de dados
**KEGG**, **EggNOG**, previamente formatadas para o uso em Diamond e
**Kraken2**.

Use o programa `gdown` para descarregar as dbs que se encontram em um
GoogleDrive. Se não tiver o `gdown` instalado, siga o seguintes passos:

> 🇪🇸 Para la obtención de las bases de datos, puede ir directamente en
> las páginas web de cada una. Sin embargo, tenga en cuenta que la base
> de datos **KEGG** es paga. Si ud decide descargar directamente de la
> fuente, deberá hacer una formatación de las DBs para el uso con
> Diamond (anotación funcional). Este processo es realizado usando el
> comando `makedb --in reference.fasta -d reference`.
>
> Para facilitar, en el siguiente link, encontrará las bases de
> datos**KEGG**, **EggNOG**, previamente formatadas para su uso en
> Diamond e **Kraken2**.

-   [**Dbs**](https://drive.google.com/drive/folders/1GLP6vA4Gs0cce-nnBXCmZSgmONWybOSF?usp=sharing)

<!-- -->

    ## Se não tiver instalado pip
    sudo apt update
    sudo apt install python3-pip
    pip3 --verision

    ## Instale gdown
    pip install gdown

🇧🇷 Crie uma pasta, chamada `dbs/`, e use o programa `gdown` para
descarregar as dbs.

    # Crie o diretório
    mkdir dbs/

    # Descarregue as DBs

    ## KEGG
    gdown --id 1ZxjJdwh1izP32X5CH-B8SN0DK2WAAAvr

    ##EggNOG
    gdown --id 1x2Kp4PTX8GFFhkJm6EVDQLfi-xRSQ735

Serão descarregados os seguintes arquivos:

-   `eggnog.dmnd`: Base de dados EggNOG formatada para Diammond
-   `keggdb.dmnd`: Base de dados KEGG formatada para Diammond

🇧🇷 **Nota** É recomendável procurar os links originais para descarga das
bases de dados para assim obter a versão mais atualizada (p.e.
[Kraken2](https://ccb.jhu.edu/software/kraken2/index.shtml?t=downloads))

> 🇪🇸 **Nota** es recomendable buscar los links originales para descargar
> las bases de datos en sus versiones más actualizadas (p.e.
> [Kraken2](https://ccb.jhu.edu/software/kraken2/index.shtml?t=downloads))

    ## Kraken2
    mkdir Kraken2
    cd Kraken2

    ## Descarregando desde o servidor dos desenvolvedores
    wget  ftp://ftp.ccb.jhu.edu/pub/data/kraken2_dbs/old/minikraken2_v1_8GB_201904.tgz

    tar zxvf minikraken2_v1_8GB_201904.tgz

    ## Troque o nome da pasta de saída
    mv minikraken2_v1_8GB/ mainDB

    ## Elimine o arquivo original
    rm minikraken2_v1_8GB_201904.tgz

#### 5.1.2 Instalação Diammond

O [**Diamond**](https://github.com/bbuchfink/diamond) será usado para a
anotação funcional. Instale através do conda, no ambiente `Annotation`

    # Active o ambiente
    conda activate Annotation

    # Instalaçao
    conda install -c bioconda diamond=2.0.9

#### 5.1.3. Instalação Kraken2

[**Kraken2**](https://github.com/DerrickWood/kraken2/wiki) será usado
para a anotação taxonômica. Instale o programa no ambiente `Annotation`.

    # Se não estiver ativado
    conda activate Annotation

    # Instale 
    conda install -c bioconda kraken2

Após instalado, deve configurar a base de dados, isto é indicar pro
programa o caminho (*PATH*) onde se encontram a base de dados. Dígite o
seguinte comando: (*coloque o caminho que corresponda a onde você
descarregou suas bases de dados*)

> 🇪🇸 Después de instalado, debe ser configurada la base de datos, esto
> es, indicar para el programa el camino (*PATH*) donde se encuentra la
> base de datos. Dígite el siguiente comando: (\*coloque el camino que
> corresponda a donde ud descargó sus bases de datos)

    export KRAKEN2_DB_PATH="/home/metagenomica/dbs/Kraken2/"

### 5.2. Anotação Funcional

🇧🇷 Uma vez instaladas todas as ferramentas e descarregadas as bases de
dados, pode proceder à anotação. Neste caso será feita primeiro à
funcional, usando Diammond e as bases de dados **KEGG** e **EggNOG**. A
continuação se encontra o comando ndividual (*uma montagem e uma base de
dados por vez*)

> 🇪🇸 Una vez instaladas todas las herramientas y descargadas las bases
> de datos, puede proceder a la anotación. En este caso será hecha
> primero la anotación funcional, usando Diammond e las bases de datos
> **KEGG** e **EggNOG**

    ## Crie uma pasta pra saída
    mkdir 08.FunctionalAnnotation

    ## Diammond
    diamond blastx --more-sensitive --threads 6 -k 1 -f 6 qseqid qlen sseqid sallseqid slen qstart qend sstart send evalue bitscore score length pident qcovhsp --id 60 --query-cover 60 -d dbs/keggdb.dmnd --query 07.GenePrediction/GenesNucl.fa -o 08.FunctionalAnnotation/GenesNucl_kegg.txt --tmpdir /dev/shm

**SINTAXE**

    diamond blastx --more-sensitive --threads -k -f --id --query-cover -d dbs/db.dmnd --query orfs_nucleotides.fa -o annotation.txt --tmpdir /dev/shm

-   `blastx`: Alinha sequências de DNA contra uma base de dados de
    proteínas
-   `--more-sensitive`: este modo permite hits com &gt;40% de
    identidade. Existem outros modos
    `--fast --min-sensitive --very-sensitive --ultra-sensitive`. Clique
    [aqui](https://github.com/bbuchfink/diamond/wiki/3.-Command-line-options)
    para mais detalhes
-   `--threads`: número de núcleos
-   `-k/--max-target-seqs`: Número máximo de sequências *target* por
    *query* para reportar alinheamentos.
-   `-f/--outfmt`: Formato de saída. São aceptos os seguintes valores:
    -   `0` Formato BLAST *pairwise*
    -   `5` fomato BLAST XML
    -   `6` Formato do BLAST tabular (default), pode customizar as
        colunas com uma lista separada por espaços, das seguintes
        opções:
        -   `qseqid` id da sequência *query*
        -   `qlen` tamanho da sequência *query*
        -   `sseqid` id da sequência da base de dados
        -   `sallseqid` todas os id das sequências das bases de dados
        -   `slen` tamanho da sequência da base de dados
        -   `qstart` inicio do alinhamento no *query*
        -   `qend` fim do alinhamento no *query*
        -   `sstart` inicio do alinhamento na sequência da base de dados
        -   `send` fim do alinhamento na sequência da base de dados
        -   `evalue`
        -   `bitscore`
        -   `score`
        -   `length` tamanho do alinhamento
        -   `pident` porcentagem de matches identicos

Com o comando anterior foi feita a anotação do co-assembly de todas as
amostras `scaffolds.fasta` com a base de dados `kegg.dmnd` e os dados
foram guardados no arquivo `kegg_annotation.txt`.

> 🇪🇸 Con el comando anterior fue realizada la anotación del co-assembly
> de todas las muestras `scaffolds.fasta` con la base de datos
> `kegg.dmnd` y los datos fueron guardadas en el archivo
> `GenesNucl_kegg.txt`.

Se tiver mais de uma montagem e quiser rodar todas e as duas bases de
dados ao mesmo tempo, pode usar o seguinte loop `for`:

> 🇪🇸 Si tiene más de un ensamble y quiere correr todos e las dos bases
> de datos al mismo tiempo, puede usar el siguiente loop `for`:

    for i in 07.GenePrediction/*.fa
    do
    BASE=$(basename $i .fa)
      for j in dbs/*.dmnd
      do
      db=$(basename $j .dmnd)
    diamond blastx --more-sensitive --threads 6 -k 1 -f 6 qseqid qlen sseqid sallseqid slen qstart qend sstart send evalue bitscore score length pident qcovhsp --id 60 --query-cover 60 -d $j --query $i -o 08.FunctionalAnnotation/${BASE}_${db}.txt --tmpdir /dev/shm
    done
    done

Com o comando anterior, é feita a anotação em todas as ORFs preditas na
pasta `07.GenePrediction/` com todas as bases de dados para diammond
dentro da pasta `dbs/`. Veja que no loop foram declaradas duas
variavéis, `i` que corresponde a cada um dos arquivos das ORFs
(nucleotídeos) preditas com Prodigal e a variável `j` que corresponde a
cada um dos arquivos terminados em `.dmnd` dentro da pasta `dbs/`, ou
seja as bases de dados `kegg.dmnd` e `eggnog.dmnd`. Os arquivos de saída
são duas tabelas por cada montagem, uma da anotação com *eggnog* e outra
com *kegg*.

> 🇪🇸 Con el comado anterior, es realizada la anotación de todas las ORF
> predichas en el directorio `07.GenePrediction/` con todas las bases de
> datos para Diammond dentro de la carpeta `dbs/`. Vea que en el loop
> fueron declaradas dos variables, `i` que corresponde a cada uno de los
> archivos de las ORFs (nucleótidos) predichos con Prodigal e la
> variable `j` que corresponde a cada uno de los archivos terminados en
> `.dmnd` dentro de la carpeta `dbs/`, o sea las bases de datos
> `kegg.dmnd` y `eggnog.dmnd`. Los archivos de salida son dos tablas por
> cada ensamble, una con la anotación con *eggnog* e otra con *kegg*.

### 5.3 Anotação Taxonômica

Para a anotação taxonômica será usada a ferramenta Kraken2. Depois de
instalada a ferramenta, descarregada e configurada a base de dados, é
possìvel rodar o comando para anotação. Lembrando que este procedimento
deve ser feito para cada uma das predições de ORFs de cada montagem.

> 🇪🇸 Para la anotación taxonómica será usado la herramienta Kraken2.
> Despues de instalada la herramienta, descargada y configurada la base
> de dados, es posible correr el comando para anotación. Recordando que
> este procedimiento debe ser hecho apra cada una de las predicciones de
> ORFs de cada ensamble.

    mkdir 09.TaxonomicAnnotation

    kraken2 --db mainDB 07.GenePrediction/GenesNucl.fa > mkdir 09.TaxonomicAnnotation/GenesNucl_tax_annoted.tsv

**SINTAXE**

`kraken2 --db db orfs_nucleotides.fa`

-   `--db`: nome da pasta onde se encontra a base de dados e que foi
    configurada no PATH.
-   `orfs_nucleotides.fa`: Arquivo de saída da predição de ORFs, en
    formato `.fa` (nucleotídeos)

Para rodar num comando só todas as montagens, pode ser usado o seguinte
loop:

> 🇪🇸 Para correr en un solo comando todas los ensambles, puede ser usado
> el siguiente loop:

    for i in 07.GenePrediction/*.fa
    do
    BASE=$(basename $i .fa)
    kraken2 --db mainDB $i > 09.TaxonomicAnnotation/${BASE}_taxa_annoted.tsv
    done

O arquivo de saída é uma tabela `.tsv` por cada montagem. As colunas
estão organizadas da seguinte forma:

1.  “C”/“U”: Para indicar se a sequência foi classificada ou não
    classificada (*Unclassified*).
2.  Nome do contig
3.  Identificação Taxonômica
4.  Tamanho da sequência em bp.
5.  Mapeamento LCA de cada *k*-mer.

Depois de obtida a tabela de anotação taxonômica, é necessário ordenar
pela primeira coluna (IDs das sequências):

    for i in 09.TaxonomicAnnotation/*_taxa_annoted.tsv
    do
    BASE=$(basename $i _taxa_annoted.tsv)
    sort -k1,1 $i > 09.TaxonomicAnnotation/${BASE}_tax_annoted_sorted.tsv

## 6. Mapeamento

Agora precisamos mapear as ORFs anotadas nos contigs gerados no
assembly. A ferramenta principal durante este proceso se chama
[Bowtie2](https://github.com/BenLangmead/bowtie2), a qual é uma
ferramenta que permite o mapeamento de sequências. Também será usado o
programa [Samtools](https://github.com/samtools/samtools) para a
transformação e manipulação dos arquivos do mapeamento.

> 🇪🇸 Ahora necesitamos mapear los ORFs anotados en los contigs generados
> en el ensamblaje. La herramienta principal durantes este processo se
> llama [Bowtie2](https://github.com/BenLangmead/bowtie2), la cual es
> una herramienta que permite el mapeo de secuencias. También será usado
> el programa [Samtools](https://github.com/samtools/samtools) para la
> transformación y manipulación de los archivos del mapeo

### 6.1. Instalação

Para a instalação das ferramentas do mapeamento, crie um ambiente
virtual chamado **Mapping**.

    # Crie o ambiente
    conda create -n mapping

    # Ative o ambiente
    conda activate mapping

#### 6.1.1. Bowtie2

    conda install -c bioconda bowtie2

#### 6.1.2. SamTools

    conda install -c bioconda samtools=1.9

### 6.2 Uso

#### 6.2.1. Extração de Sequências anotadas

O primeiro passo é selecionar a coluna um das tabelas de anotações, que
correspondem aos nomes das sequências anotadas, para depois extraí-las.
Isto pode ser feito usando o comando `cut`.

> 🇪🇸 El primer paso es seleccionar la columna uno de las tablas de
> anotaciones que corresponde a los nombres de las secuencias anotadas,
> para después extraerlas. Esto puede ser realizado usando o comando
> `cut`.

    cut -f1 08.FunctionalAnnotation/GenesNucl_keggdb.txt > 08.FunctionalAnnotation/GenesNucl_keggdb_contigsIDs.txt

Com o loop, você consegue fazer ao mesmo tempo o processo para todas as
tabelas de anotações que tiver (p.e. mais de um assembly e/ou mais de
uma base de dados).

> 🇪🇸 Con el loop, usted consigue hacer al mismo tiempo el proceso para
> todas las tablas de anotaciones que tenga (p.e. más de un ensamble y/o
> más de una base de datos)

**Loop**

    for i in 08.FunctionalAnnotation/*.txt; do BASE=$(basename $i .txt); cut -f1 $i > 08.FunctionalAnnotation/${BASE}_contigsIDs.txt; done

**SINTAXE** `cut [options] file`

-   `-f`: Fields (colunas) selecionadas

No comando anterior, basicamente são criados novos arquivos
(`_contigsIDs.txt`) com a primeira coluna das tabelas de anotação, que
contém os IDs.

A continuação, use os últimos arquivos gerados
(`GenesNucl_keggdb_contigsIDs.txt`) para extrair as sequências desses
genes anotados. Ou seja, use a lista dos IDs, para que sejam procuradas
as sequências anotadas dentro dos arquivos dos genes preditos (i.e
`GenesNucl.fa`). Este processo é feito com um script escrito na
linguagem [*perl*](https://www.perl.org/). Rode este comando para cada
anotação ou base de dados usada (i.e. kegg e eggnog).

> 🇪🇸 Con el comando anterior, basicamente fueron creados los nuevos
> archivos (`_contigsIDs.txt`) con la primera columna de las tablas de
> anotación, que contienen los IDs.
>
> A continuación, use los ultimos archivos generados
> (`GenesNucl_keggdb_contigsIDs.txt`) para extraer las secuencias de
> esos genes anotados. O sea, use la lista de los IDs, para que sean
> buscadas las secuencias anotadas dentro de los archivos de predicción
> de genes (i.e. `GenesNucl.fa`). Este proceso es hecho con un script
> escrito en el lenguaje [*perl*](https://www.perl.org/). Corra este
> comando para cada anotación o base de datos usada (i.e. kegg y
> eggnog).

**KEGG**

    mkdir 10.Mapping

    perl -ne 'if(/^>(\S+)/){$c=$i{$1}}$c?print:chomp;$i{$_}=1 if @ARGV' 08.FunctionalAnnotation/GenesNucl_keggdb_contigsIDs.txt 07.GenePrediction/GenesNucl.fa > 10.Mapping/GenesNucl_keggdb_seqs.fa

**SINTAXE** `perl script input1 input2`

-   `input1`: IDs das sequências
-   `input2`: Sequências Genes preditos (output Prodigal)

**Loop**

    # Crie uma nova pasta
    mkdir 10.Mapping

    ## KEGG
    for i in 08.FunctionalAnnotation/*_ekeggdb_contigsIDs.txt; do BASE=$(basename $i _keggdb_contigsIDs.txt);   for j in 07.GenePrediction/${BASE}.fa;   do   ID=$(basename $j ${BASE}.fa); perl -ne 'if(/^>(\S+)/){$c=$i{$1}}$c?print:chomp;$i{$_}=1 if @ARGV' $i $j > 10.Mapping/${BASE}_keggdb_seqs.fa; done; done

    ## EggNOG
    for i in 08.FunctionalAnnotation/*_eggnog_contigsIDs.txt; do BASE=$(basename $i _eggnog_contigsIDs.txt);   for j in 07.GenePrediction/${BASE}.fa;   do   ID=$(basename $j ${BASE}.fa); perl -ne 'if(/^>(\S+)/){$c=$i{$1}}$c?print:chomp;$i{$_}=1 if @ARGV' $i $j > 10.Mapping/${BASE}_eggnog_seqs.fa; done; done

Use o comando `ls` para listar o conteúdo da pasta `10.Mapping/`.
Perceba que tem dois arquivos por cada amostras ou montagem, um com as
sequências anotadas com KEGG, e outra com as sequências anotadas com
EggNOG.

> 🇪🇸 Use el comando `ls` para listar el contenido de la carpeta
> `10.Mapping/`. Perciba que tiene dos archivos para cada muestra o
> ensamble, uno con las secuências anotadas con KEGG y otro con las
> secuencias anotadas con EggNOG.

#### 6.2.3. Mapeamento

**1. Criação do índice**

Após a formatação das tabelas de anotações e a extração das sequências,
será iniciado o processo de mapeamento. O primeiro paso é criar um índex
das sequências anotadas e extraídas (`GenesNucl_keggdb_seqs.fa`), usando
**Bowtie2**.

> 🇪🇸 Después de la formatación de las tablas de anotaciones e la
> extracción de las secuencias, será iniciado el proceso de mapeo. El
> primero paso es crear un índice de las secuencias anotadas y extraidas
> (`GenesNucl_keggdb_seqs.fa`), usando **Bowtie2**.

Entre na pasta do mapeamento:

    cd 10.Mapping/

Rode este comando para cada arquivo de sequências extraídas por base de
dados que tiver:

    bowtie2-build GenesNucl_keggdb_seqs.fa GenesNucl_keggdb_seqs.fa

**SINTAXE** `bowtie2-build referência índice_name`

-   `referência`: arquivo `.fasta` com as sequências
-   `índice_name`: nome do índice

**Loop**

O loop é muito útil se tiver mais de uma montagem. Assim, criará um
índice para cada montagem.

    for i in ./*_seqs.fa; do BASE=$(basename $i _seqs.fa); bowtie2-build $i $i; done

**2. Obtenção arquivos `.sam`**

Assim que criado(s) o(s) índice(s), pode se proceder ao comando do
mapeamento, que irá criar arquivos `.sam` (*Sequence Alignment Map*)
para cada amostra ou assembly e cada base de dados.

> 🇪🇸 Después de creados los índices, puede se proceder al comando de
> mapeo, que irá crear los archivos `.sam` (*Sequence Alignment Map*)
> para cada muestra o para cada ensamble y cada base de datos.

    bowtie2 -p 6 -x GenesNucl_keggdb_seqs.fa -1  ../02.CleanData/Sample1_1_paired.fq.gz -2 ../02.CleanData/Sample1_2_paired.fq.gz -S Sample1_kegg.sam

**SINTAXE** `bowtie2 -p threads -x index -1 pair1 -2 pair2 -S file.sam`

-   `-p`: Número de threads/ núcleos
-   `-x`: índice (bowtie2-build)
-   `-S`: Archivo de saída em formato `.sam`.

Se quise ter mais informações sobre os arquivos
[`.sam`](https://en.wikipedia.org/wiki/SAM_(file_format))

**Loop**

Com o seguinte loop, é possível fazer o mapeamento de várias amostras
usando o mesmo único índice que proveem de uma únicam montagem (caso
deste tutorial).

> 🇪🇸 Con el siguiente loop, es posible hacer el mapeo de varias muestras
> usando el mismo único índice que proviene de un único ensamble (el
> caso de este tutorial).

    ## KEGG
    for i in ../02.CleanData/*_1_paired.fq.gz; do BASE=$(basename $i _1_paired.fq.gz); for j in ./*keggdb_seqs.fa; do bowtie2 -p 6 -x $j -1 $i -2 ../02.CleanData/${BASE}_2_paired.fq.gz -S ${BASE}_kegg.sam; done; done

    ## EGGNOG
    for i in ../02.CleanData/*_1_paired.fq.gz; do BASE=$(basename $i _1_paired.fq.gz); for j in ./*eggnog_seqs.fa; do bowtie2 -p 6 -x $j -1 $i -2 ../02.CleanData/${BASE}_2_paired.fq.gz -S ${BASE}_eggnog.sam; done; done

Então ao finalizar o processo, terá dois arquivos `.sam` por cada
amostra (Kegg, EggNOG).

**3. `.sam` para `.bam`**

Para facilidade na manipulação, os arquivos `.sam` devem ser
transformados a `.bam` usando a ferramenta **SamTools**.

> 🇪🇸 Para facilidad en la manipulación, los archivos `.sam` deben ser
> transforamdos a `.bam` usando la herramienta **SamTools**.

    samtools view -b -S -o Sample1_kegg.bam Sample1_kegg.sam

**SINTAXE** `samtools view -b -S -o output input`

-   `-b`: transformar a \`.bam´
-   `-S`: desde `.sam`
-   `-o`: `output.bam`
-   `input`: `input.sam`

**Loop** Com o loop, consegue transformar todos os arquivos `.sam` para
`.bam`.

    for i in ./*.sam
    do
    BASE=$(basename $i .sam)
    samtools view -b -S -o ${BASE}.bam $i
    done

Agora você tem os mapeamentos tanto em formato `.sam` como `.bam`.

**4. Ordenando e Indexando**

O seguinte paso é ordenar os arquivos `.bam`

    # Ordenando
    samtools sort Sample1_kegg.bam -o Sample1_kegg_sorted.bam

    # Indexando
    samtools index Sample1_kegg_sorted.bam

**SINTAXE** `samtools sort input -o output_sorted.bam`

`samtools index output_sorted.bam`

**Loop**

    # Ordenando
    for i in ./*.bam
    do
    BASE=$(basename $i .bam)
    samtools sort $i -o ${BASE}_sorted.bam
    done

    # Indexando
    for i in ./*_sorted.bam
    do
    BASE=$(basename $i _sorted.bam)
    samtools index $i
    done

**5. Criando as estatísticas da anotação funcional**

Neste paso serão geradas as estatísticas da anotação funcional.

    # Estatísticas
    samtools idxstats Sample1_kegg_sorted.bam > ../08.FunctionalAnnotation/Sample1_kegg_annotation_stats.txt

    cd ../08.FunctionalAnnotation/

    # Ordenando
    sort -k1,1 Sample1_kegg_annotation_stats.txt > Sample1_kegg_annotation_stats_sorted.txt

**SINTAXE** `samtools idxstats sorted.bam > table.txt`

`sort -k table.txt > table_sorted.txt`: Classifique (ordene) o arquivo
`table.txt` com base na coluna 1 e salve no arquivo `table_sorted.txt`

**Loop**

    # Estatísticas
    for i in ./*_sorted.bam
    do
    BASE=$(basename $i _sorted.bam)
    samtools idxstats $i > ../08.FunctionalAnnotation/${BASE}_stats.txt
    done

    cd ../08.FunctionalAnnotation/

    # Ordenando
    for i in ./*_stats.txt
    do
    BASE=$(basename $i _stats.txt)
    sort -k1,1 $i > ${BASE}_sorted.txt
    done

Use o comando `head` para dar uma olhada em algum dos arquivos (i.e
`head Sample1_kegg_sorted.txt`).

Em cada um desses arquivos terá 4 colunas:

1.  nome da sequência
2.  tamanho da sequência
3.  quantidade de reads mapeados
4.  quantidade de reads não mapeados

## 7. Construção Tabela Final

Finalmente é necessário construir uma tabela final com todas as
anotações (taxonômica e funcional) com todas as bases de dados e de
todas as amostras

> 🇪🇸 Finalmente es necesario construir una tabla final con todas las
> anotaciones (taxonómica y funcional) con todas las bases de dados y de
> todas las muestras.

### 7.1. Análise Randômico do KEGG

Este processo consiste em completar a informação da anotação do Kegg. Na
anotação funcional foram obtidos os números KEGG ou KO (*Kegg
Orthologies*) das sequências anotadas. No entanto esse KO não nos dá
informação do nome do gene, ou do metabolismo ao que pertence. Para
isto, nesta fase serão adicionadas essas informações.

Faça [download](https://figshare.com/ndownloader/files/33953774) da
tabela `kegg.tsv` que contém todas as informações da base de dados KEGG

> 🇪🇸 Este proceso consiste en cpmpletar la información de la anotación
> de Kegg. En la anotación funcional fueron obtenidos los números KEGG o
> KO (*Kegg Otrhologies*) de las secuencias anotadas. Sin embargo ese KO
> no nos da información del nombre del gen, o del metabolismo al que
> pertenece. Para esto, en esta fase serán adicionadas esas
> informaciones
>
> Haga el [download](https://figshare.com/ndownloader/files/33953774) de
> la tabla `kegg.tsv` que contiene todas las informaciones de la base de
> datos KEGG.

    # Voltando na pasta base
    cd ../

    # Download
    curl -L https://figshare.com/ndownloader/files/33953774 -o kegg.tsv

A continuação ordene o arquivo pelo código KO:

    sort -k1,1 kegg.tsv > kegg_sorted.tsv

Agora é momento de trabalhar na formatação das tabelas de anotação de
cada amostra:

1.  **Separando a terceira coluna**

A terceira coluna da tabela original da anotação taxonômica, traz o
código de acesso do NCBI e separado por \| o número KO, por exemplo:
WP000000.0 \| K00001. O seguinte comando separa essas informações em
dois colunas usando um script de *Perl*:

> 🇪🇸 La tercera columna de la tabla original de la anotación taxonómica,
> tiene el código de acceso del NCBI y separada por \|, el número KO,
> por exemplo: WP000000.0 \| K00001. El siguiente comando separa esas
> informaciones en dos columnas usando un script de *Perl*:

    mkdir 11.RandomicAnalyses

    for i in 08.FunctionalAnnotation/*_keggdb.txt
    do
    BASE=$(basename $i _keggdb)
    perl -pe 's/\|?(?:\s+gi|ref)?\|\s*/\t/g' $i > 11.RandomicAnalyses/${BASE}.txt
    done

2.  **Extraindo os números KO**

<!-- -->

    for i in 11.RandomicAnalyses/*_keggdb.txt; do BASE=$(basename $i _keggdb.txt); cut -f1,4 $i > 11.RandomicAnalyses/${BASE}_kegg_ids.txt; done

O comando anterior usa o comando `cut` para cortar as colunas 1 (IDs das
sequências) e 4 (Kegg IDs/ Números KO).

3.  **Ordenando pelos números KO**

A continuação, usando o comando `sort`, será organizada a tabela pelos
números KO:

    for i in 11.RandomicAnalyses/*_ids.txt
    do
    BASE=$(basename $i _ids.txt)
    sort -k2,2 $i > 11.RandomicAnalyses/${BASE}_sorted.txt
    done

4.  **Adicionando a informação completa do KEGG**

A seguinte etapa, compreende o uso da tabela ordenada das informações do
KEGG `kegg_sorted.tsv`, que contem todas as categorias metabólicas para
cada número KO para adicionar estas informações na tabela saída do paso
anterior (`GeneNucl_sorted.txt`). A linha de comando a continuação
contém vários comando anidados separados por \|. Isto indica para o
sistema que a saída de um comando é a entrada do seguinte comando.

    for i in 11.RandomicAnalyses/*_kegg_sorted.txt; do BASE=$(basename $i _kegg_sorted.txt); cat $i | while read line ; do echo "$line" | join -1 2 -2 1 -e"NA" -o1.1,0,2.2,2.3,2.4,2.5 -t $'\t' - kegg_sorted.tsv | shuf -n1 >> 11.RandomicAnalyses/${BASE}_keggs_randomic_analysis.tsv; done; done

5.  **Ordenando a tabela pelos IDs das sequências**

Agora é momento de ordenar a tabela pela primeira coluna, IDs das
sequências.

    for i in 11.RandomicAnalyses/*_randomic_analysis.tsv
    do
    BASE=$(basename $i _keggs_randomic_analysis.tsv)
    sort -k1,1 $i > 11.RandomicAnalyses/${BASE}_keggs_randomic_analysis_sorted.tsv

------------------------------------------------------------------------

## Em construção…
