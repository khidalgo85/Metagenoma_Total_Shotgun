
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

## 0. Instalação

### 0.1. Anaconda

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

### 0.2. FastQC

🇧🇷 [FastQC](http://www.bioinformatics.babraham.ac.uk/projects/fastqc/) é
uma ferramenta para avaliar graficamente a qualidade das sequencias de
Illumina.

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

### 0.3. Trimmomatic v0.39

🇧🇷 [Trimmomatic](http://www.usadellab.org/cms/?page=trimmomatic) é um
programa pra filtrar (remover) leituras ou *reads* curtas de baixa
qualidade.

Como se trata de uma ferramenta que participa dentro do processo de
control de qualidade, será instalada dentro do ambiente virtual
**QualityControl**.

> 🇪🇸 [Trimmomatic](http://www.usadellab.org/cms/?page=trimmomatic) es un
> programa para filtrar (remover) lecturas o *reads* cortas de baja
> calidad.
>
> Como se trata de una herramienta que participa dentro del proceso de
> control de calidad, será instalada dentro del ambiente virtual
> **QualityControl**

    # Si no está activado el ambiente
    conda activate QualityControl

    # Instale Trimmomatic
    conda install -c bioconda trimmomatic

### 0.4. NonPareil v3.3.3

🇧🇷 [NonPareil](https://nonpareil.readthedocs.io/en/latest/) é uma
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

### 0.5. Mash v2.3

🇧🇷 [Mash](https://mash.readthedocs.io/en/latest/) é uma ferramenta que
usa a técnica de redução da dimensionalidade *MinHash* para calcular as
distâncias um a um entre os datasets, assim, é possível determinar se os
metagenomas são similares ou não para serem montados usando
*co-assembly*. Por ser considerada uma ferramenta que participa no
processo de assembly, será instalada dentro de um ambiente virtual
chamado **Assembly**.

> 🇪🇸 [Mash](https://mash.readthedocs.io/en/latest/) es una herramienta
> que usa la técnica de reducción de dimensionalidad *MinHash* para
> calcular las distancias un a un entre los datasets, así, es posible
> determinar si los metagenomas son similares o no para ser ensamblados
> usando *co-assembly*.
>
> Por ser considera una herramienta que participa en el proceso de
> ensamble, será instalada dentro de un ambiente virtual llamado
> **Assebly**.

    # Crie o ambiente virtual
    conda create -n Assembly

    # Instale Mash
    conda install -c bioconda mash

------------------------------------------------------------------------

## 1. Organizando os dados

### 1.1. Sequências

🇧🇷 En este tutorial serão usados 4 meteganomas, pode decarregá-los
[aqui](colgarlosdatos.com).

Antes de descarregar os *datasets*, crie os seguintes directórios para a
organização dos dados.

> 🇪🇸 En este tutorial serán usados 4 metagenomas, puede descargarlos
> [aqui](colgarlosdatos.com).
>
> Antes de descargar los *datasets*, cree los siguientes directorios
> para la organización de los datos.

    # Crie uma pasta raíz chamada metagenomica
    mkdir metagenomica
    cd metagenomica/

    # Crie um diretório para colocar os dados
    mkdir 00.RawData
    cd 00.RawData/

🇧🇷 Use o comando `wget` para descarregar os dados desde este
[link](colgarlosdatos.com).

> 🇪🇸 Use el comando `wget` para descargarlos los datos desde este
> [link](colgarlosdatos.com).

## 2. Controle de Qualidade

### 2.1. FastQC

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

🇧🇷 Deve etsar em `~/metagenomica/`.. Se esse não é o resultado del
comando `pwd`, use o comando `cd` para chegar no diretório desejado.

> 🇪🇸 Debe estar em `~/metagenomica/`. Si ese no es el resultado del
> comando `pwd`, use el comando `cd` para llegar en el directorio base.

Execute **FastQC**:

    ## Crie um directório para salvar o output do FastQC
    mkdir 01.FastqcReports
    ## Run usando 10 threads
    fastqc -t 10 00.RawData/* -o 01.FastqcReports/

**Sintaxis** fastqc \[opções\] input -o output

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

Descaregue os arquivos `html` e explore no seu *web browser*.

Observe as estatísticas básicas que se encontram na primeira tabela.
Alí, você pode saber quantas sequências tem, o tamanho e o %GC. O
gráfico mais importante para saber a quealidade das leituras, é o
primeiro, *Per base sequence quality*. Este gráfico é um boxplot com a
distribuição dos valores de qualidade *Phred Score* (eje y) em cada um
dos nucleotídeos das leituras (eje x). Se consideram sequências de
excelente qualidade quando o *P**h**r**e**d**S**c**o**r**e* &gt; 30. É
norla que o pair 2 apresente uma qualidade um pouco inferior ao pair 1.

> 🇪🇸 Observe las estadísticas básicas que se encuentran en la primera
> tabla. Allí, ud puede saber cuantas secuencias tiene, el tamaño y el
> %GC. El gráfico más importante para saber la calidad de las lecturas
> es el primero, *Per base sequence quality*. Este gráfico es un boxblot
> con la distribución de los valores de calidad *Phred Score* (eje y) en
> cada uno de los nucleótidos de las lecturas (eje x). Se consideran
> secuencias de excelente calidad cuando el
> *P**h**r**e**d**S**c**o**r**e* &gt; 30. Es normal que el pair 2
> presente una calidad un poco inferior al pair 1.

### 2.2. Trimmomatic

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
    mkdir 02.CleandData

    # Crie uma pasta para salvar as reads não pareadas
    mkdir unpaired

    # Corra Trimmomatic
    trimmomatic PE -threads 10 00.RawData/sample1_1.fastq.gz 00.RawData/sample1_2.fastq.gz 02.CleandData/sample1_1_paired.fastq.gz unpaired/sample1_1_unpaired.fastq.gz 02.CleandData/sample1_2_paired.fastq.gz unpaired/sample1_2_unpaired.fastq.gz LEADING:3 TRAILING:3 SLIDINGWINDOW:4:15 MINLEN:150

🇧🇷 Com o comando anterior você tem que rodar a linha de comando para
cada amostra. Se quiser rodar todas as amostras de maneira automâtica é
possível usar um *loop* `for` para executar esta tarefa.

> 🇪🇸 Con el comnado anterior ud tiene que correr esa línea de comando
> para cada muestra. Si quiere correr todas las muestras de manera
> automática es posible usar un *loop* `for` para ejecutrar esta tarea.

    # loop
    for i in 00.RawData/*1.fq.gz 
    do
    BASE=$(basename $i 1.fq.gz)
    trimmomatic PE -threads 20 $i  00.RawData/${BASE}2.fq.gz 02.CleanData/${BASE}1_paired.fq.gz unpaired/${BASE}1_unpaired.fq.gz 02.CleanData/${BASE}2_paired.fq.gz unpaired/${BASE}2_unpaired.fq.gz LEADING:3 TRAILING:3 SLIDINGWINDOW:4:15 MINLEN:150
    done

**Sintaxis** trimmomatic PE -threads input\_forward input\_reverse
output\_forward\_paired output\_forward\_unpaired
output\_reverse\_paired output\_reverse\_unpaired \[opções\]

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
com tamanho menor ao informado.

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
> tamaño menor al informado.
>
> Después de correr Trimmomatic es necesario evaluar la calidad de las
> secuencias generadas (“limpias”) usando nuevamente FastQC.

    fastqc -t 10 02.CleandData/* -o 01.FastqcReports/

Descargue los reportes `.html` de las secuencias pareadas
(i.e. `01.FastqcReports/sample1_1_paired_fastqc.html` y
`01.FastqcReports/sample1_2_paired_fastqc.html`).

### 2.3 Cobertura dos Metagenomas

🇧🇷 Além de limpar e trimar as sequências com baixa qualidade, é
necessário calcular a cobertura dos metagenomas.Este programa usa a
redundância de reads nos metagenomas para estimar a cobertura média e
prediz a quantidade de sequências que são requeridas para atingir o
*“nearly complete coverage”*, definida como  ≥ 95% ou  ≥ 99% de
cobertura média. A ferramenta **NonPareil** será usada nesta etapa.

Como *input* para esta análise só é necessário um pair de cada amostra,
e deve estar sem compressão.

> 🇪🇸 Además de limpiar y *trimar* las secuencias con baja calidad, es
> necesario calcular la cobertura de los metagenomas. Este programa usa
> la redundancia de las *reads* en los metagenomas para estimar la
> cobertura promedio y predice la cantidade de secuencias que son
> requeridas para conseguir el *“nearly complete coverage”*, definida
> como  ≥ 95% o  ≥ 99% de la cobertura promedio. La herramienta
> **NonPareil** será usada en esta etapa.
>
> Como *input* para este análisis solo es necesario un par de cada
> muestra, y debe estar sin compresión.

    # Crie o diretório pra o output
    mkdir 02.NonPareil

    # Copie todos os pair 1 ao novo diretório

    cp 00.RawData/*_1.fq.gz 02.NonPareil

    # Entre no diretório

    cd 02.NonPareil 

    # Descomprima os arquivos 
    gunzip *.gz 

    # Loop para convertir todos os arquivos de fastq to fasta
    for i in ./*
    do
    SAMPLE=$(basename $i _1.fq)
    cat $i | paste - - - - | awk 'BEGIN{FS="\t"}{print ">"substr($1,2)"\n"$2}' > ${SAMPLE}_1.fasta
    done

🇧🇷 Agora está tudo pronto para rodar a análise, mas antes disso tome-se
o tempo para entender o comando que vai usar. Para conhecer que é cada
um dos argumentos, explore o menú de ajuda da ferramenta.

> 🇪🇸 Ahora está todo listo para correr el análisis, pero antes de eso
> tómese el tiempo para entender el comando que va a usar. Para conocer
> que es cada uno de los argumentos, explore el menú de ayuda de la
> herramienta.

    # Explore o menú da ferramenta
    nonpareil --help

    # Comando do NonPareil para cada amostra
    nohup nonpareil -s 1d0SE_1.fasta -T alignment -f fasta -b 1d0SE -t 3 &

🇧🇷 Ao terminar esse processo, o programa terá criado varios
[*outputs*](https://nonpareil.readthedocs.io/en/latest/redundancy.html#output)
por cada amostra. Descarregue os arquivos `.npo`. Use o R, para gráficar
as curvas de saturação. A continuação se encontra o script.

> 🇪🇸 Al terminar este proceso, el programa habrá creado varios
> [*outputs*](https://nonpareil.readthedocs.io/en/latest/redundancy.html#output)
> por cada muestra. Descargue los archivos `.npo`. Use R, para gráficar
> las curvas de saturación. A continuación se encuentra el script.

``` r
library(Nonpareil)
setwd("~/NonPareil")
samples <- read.table('samples.txt', sep='\t', header=TRUE, as.is=TRUE);

attach(samples);
nps <- Nonpareil.set(File, col=Col, labels=Name, 
                     plot.opts=list(plot.observed=FALSE, 
                                    ylim = c(0, 1.05),
                                    legend.opts = FALSE))
  
detach(samples);
summary(nps)
```

Vai obter um gráfico com as curvas de saturação de cada amostra, como
este:

.center\[<img src="imgs/nonpareil.webp" width="80%">\]

🇧🇷 As linhas tracejadas <font color='red'> vermelha </font> e
<font color='gray'> cinza </font> representam os *threshold* de 95% e
99% da cobertura média, respeitivamente. O circulo em cada curva
representa a cobertura atual das amostras, o ideal é que esteja por cima
do primeiro *threshold*. As curvas também apresentam a estimação de
quanto esforço de sequenciamento é necessário.

> 🇪🇸 Las líneas punteadas <font color='red'> roja </font> y
> <font color='gray'> gris </font> representam los *threshold* de 95% y
> 99% de cobertura promedio, respectivamente. El círculo en cada curva
> representa la cobertura actual de las muestras, lo ideal es que estén
> por encima del primer *threshold*. Las curvas también presentan la
> estimación de cuanto esfuerzo de secuenciación es necesario.

## 3. Montagem dos Metagenomas

### 3.1 MinHash

🇧🇷 Após obter as sequências limpas, de boa qualidade, e determinar a
cobertura dos metagenomas, é possível fazer a montagem. No entanto, pode
ser incluído um passo extra antes da montagem e é verificar a
similaridade dos datasets para determinar se pode ser usada a abordagem
de *co-assembly*, onde são misturadas as *reads* de vários metagenomas
para gerar os contigs. O programa **Mash** usa uma técnica chamada
redução de dimensionalidad *MinHash* que avalia as distâncias um a um
entre os datasets.

> 🇪🇸 Después de obtener las secuencias limpias, de buena calidad, y
> determinar la cobertura de los metagenomas, es posible hacer el
> montaje. Sin embargo, puede ser incluído un paso extra antes del
> montaje y es verificar la similaridade de los datasets para determinar
> si puede ser usado el abordaje de *co-assembly*, donde son mezcladas
> las *reads* de varios metagenomas para generar los contigs. El
> programa **Mash** usa una técnica llamada reducción de dimensionalidad
> *MinHash* que evalua las distancias un a un entre los datasets.

    ## Crie uma pasta para o output
    mkdir 04.MinHash

O primeiro paso é concatenar os reads 1 e 2, e armazenar eles na nova
pasta criada `04.MinHash/`.

**Nota:** Se você trimou suas sequências, deve usar os arquivos gerados
pelo **Trimmomatic** na pasta `03.CleanData`, se pelo contrário suas
sequências estavam de boa qualidade e não foi necessário trimar, use os
arquivos originais, que estão dentro da pasta `00.RawData`.

> 🇪🇸
>
> **Nota:** Si usted filtró sus secuencias, debe usar los archivos
> generados por **Trimmomatic** en el directorio `03.CleanData`, si por
> el contrario sus secuencias estaban de buena calidade y no fue
> necesario filtrar, use los archivos originales, que están dentro de la
> carpeta `00.RawData`.

    for i in 03.CleanData/*_1_paired.fq
    do
    BASE=$(basename $i _1_paired.fq)
    cat $i 03.CleanData/${BASE}_2_paired.fastq > 04.MinHash/${BASE}.fq
    done

Then, the samples were sketched to create a combined file, it was used
`mash info` to verify its content, and then estimate pairwise distances:

    mash sketch -o 04.MinHash/reference 04.MinHash/6111_O.fq 04.MinHash/6111_W.fq 04.MinHash/AJ_5.fq 04.MinHash/DQ.fq 04.MinHash/I1.fq 04.MinHash/I2.fq 04.MinHash/K2.fq 04.MinHash/K3.fq 04.MinHash/production_well.fq 04.MinHash/QH.fq 04.MinHash/SB1.fq 04.MinHash/SB2.fq 04.MinHash/jiangsu_W15_metagenome.fq 04.MinHash/jiangsu_W2-71_metagenome.fq 04.MinHash/jiangsu_W9-18_metagenome.fq 04.MinHash/BA1.fq

    #verifiyng
    mash info 04.MinHash/reference.msh

For the last, the distances were calculate using `mash dist` and printed
in the `distancesOutput.tsv` file.

    mash dist 04.MinHash/reference.msh 04.MinHash/reference.msh -p 18 > 04.MinHash/distancesOutputFinal.tsv

### 3.2 Megahit
