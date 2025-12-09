**1. Clonar repositório**

***Clona o repositório somatico do GitHub para o ambiente do Colab, permitindo acessar scripts, arquivos e códigos necessários para as próximas análises.***

```bash
%%bash
git clone https://github.com/renatopuga/somatico.git
```

**output:**
```
Cloning into 'somatico'...
```

**2. Baixar sequência do cromossomo 9 (hg19)**

***Faz o download do arquivo compactado contendo a sequência do cromossomo 9 do genoma humano (versão hg19), disponibilizado pelo UCSC Genome Browser.***

```
!wget -c https://hgdownload.soe.ucsc.edu/goldenPath/hg19/chromosomes/chr9.fa.gz
```

**output:**

```
--2025-12-08 23:52:16--  https://hgdownload.soe.ucsc.edu/goldenPath/hg19/chromosomes/chr9.fa.gz
Resolving hgdownload.soe.ucsc.edu (hgdownload.soe.ucsc.edu)... 128.114.119.163
Connecting to hgdownload.soe.ucsc.edu (hgdownload.soe.ucsc.edu)|128.114.119.163|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 39464200 (38M) [application/x-gzip]
Saving to: ‘chr9.fa.gz’

chr9.fa.gz          100%[===================>]  37.64M  29.6MB/s    in 1.3s    

2025-12-08 23:52:18 (29.6 MB/s) - ‘chr9.fa.gz’ saved [39464200/39464200]

```

**3. Substituir o prefixo "chr" por apenas "9"**

***zcat descompacta o arquivo .gz sem criar um arquivo intermediário.***

***sed remove o prefixo "chr" dos headers da sequência.***

***O arquivo final chr9.fa fica padronizado para ferramentas que não aceitam o prefixo chr.***

```
!zcat chr9.fa.gz | sed -e "s/chr//g" > chr9.fa
```


**4. Visualizar o início do arquivo original**

***Exibe as primeiras linhas do arquivo compactado para conferir o formato original da sequência antes da alteração.***

```Python
!zcat chr9.fa.gz | head
```

**output:**

```
>chr9
NNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN
NNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN
NNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN
NNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN
NNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN
NNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN
NNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN
NNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN
NNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN
```

**5. Visualizar o início do arquivo modificado**

***Mostra as primeiras linhas do novo arquivo, já sem o prefixo "chr", para confirmar se a alteração foi aplicada corretamente.***

```Python
!head chr9.fa
```

**output:**
```
>9
NNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN
NNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN
NNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN
NNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN
NNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN
NNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN
NNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN
NNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN
NNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN
```

**6. Instalar o Samtools**

***Instala o samtools, um conjunto essencial de ferramentas para manipulação de arquivos SAM/BAM/FASTA/FASTQ, muito usado em análises genômicas.***

```Python
!sudo apt-get install samtools
```

**7. Verificar funcionamento do Samtools**

***Executa o comando base do samtools para verificar se a instalação foi concluída e exibir o menu de opções disponíveis.***

```Python
!samtools
```

**8. Gerar índice para chr9.fa (arquivo .fai)**

***Gera o arquivo de índice chr9.fa.fai, necessário para acesso rápido a posições específicas dentro do FASTA.***

```Python
!samtools faidx chr9.fa
```

***Conteúdo do índice gerado, permitindo verificar se a criação foi bem-sucedida.***

```Python
! cat chr9.fa.fai
```

**output:**

```
9	141213431	3	50	51
```

**9. Fazer download do GATK4**

***Baixa o pacote do GATK4, um conjunto de ferramentas amplamente utilizado em análise de variantes.***

```Python
!wget -c https://github.com/broadinstitute/gatk/releases/download/4.2.2.0/gatk-4.2.2.0.zip
```

***Descompacta os arquivos do GATK para o diretório local.***

```Python
!unzip gatk-4.2.2.0.zip
```

***Executa o comando base para verificar se o GATK está acessível e funcionando corretamente.***

```Python
!./gatk-4.2.2.0/gatk
```

**10. Criar o arquivo de dicionário .dict**

***Obs.: Antes de criar o dicionário, é necessário garantir que o Java esteja instalado, pois o GATK depende dele.***

***Atualiza a lista de pacotes e instala o OpenJDK 11, garantindo compatibilidade com o GATK.***

***A etapa seguinte (que pode ser adicionada depois) usará o CreateSequenceDictionary para gerar o arquivo .dict correspondente ao FASTA.***

```bash
%%bash
apt-get update
apt-get install openjdk-11-jdk -y
```



