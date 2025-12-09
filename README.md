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

```Python
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

```Python
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

**11. Criar o dicionário .dict do FASTA**

***Cria o arquivo chr9.dict, necessário para ferramentas que exigem FASTA com índice e dicionário (como Mutect2). Ele descreve os contigs e tamanhos da referência.***

```Python
!./gatk-4.2.2.0/gatk CreateSequenceDictionary -R chr9.fa -O chr9.dict
```

***Exibe o conteúdo do dicionário para conferir se foi gerado corretamente.***

```Python
!cat chr9.dict
```

**12. Gerar lista de intervalos do cromossomo 9**

***Gera uma lista de intervalos contíguos do FASTA, ignorando regiões com Ns. Usada como entrada para Mutect2, limitando a análise apenas ao cromossomo 9.***

```Python
!./gatk-4.2.2.0/gatk ScatterIntervalsByNs -R chr9.fa -O chr9.interval_list -OT ACGT
```

**13. Listar arquivos do diretório**

***Mostra os arquivos gerados até o momento (FASTA, index, dict, intervalos, etc.).***

```Python
!ls
```

**output:**
```
chr9.dict  chr9.fa.fai	chr9.interval_list  gatk-4.2.2.0.zip  somatico
chr9.fa    chr9.fa.gz	gatk-4.2.2.0	    sample_data
```


**14. Visualizar o dicionário da referência**

***Esse comando apenas exibe o conteúdo do arquivo para conferência.***

```Python
!cat chr9.dict
```

**output:**

```
@HD	VN:1.6
@SQ	SN:9	LN:141213431	M5:3e273117f15e0a400f01055d9f393768	UR:file:/content/chr9.fa
```


**15. Obter o ID da amostra normal no BAM**

***Extrai o campo SM (Sample Name) do cabeçalho do BAM da amostra normal. Esse ID deve ser usado no parâmetro -normal do Mutect2.***

```Python
!samtools view -H somatico/normal_JAK2.bam | grep RG | cut -f6 | sed -e "s/SM://g"
```

**output:**

```
WP044
```

**16. Chamadas de variantes com Mutect2**

***Executa o Mutect2 para detectar variantes somáticas comparando tumor vs normal, usando o cromossomo 9 como alvo.***

***tumor_JAK2.bam: amostra tumoral***

***normal_JAK2.bam: amostra normal***

***gnomAD: base para remoção de variantes germinativas***

```bash
%%bash

./gatk-4.2.2.0/gatk Mutect2 \
	-R chr9.fa \
	-I somatico/tumor_JAK2.bam \
	-I somatico/normal_JAK2.bam \
	-normal WP044 \
	--germline-resource somatico/af-only-gnomad-chr9.vcf.gz \
	-O somatic.vcf.gz \
	-L chr9.interval_list
```

**Visualizar VCF gerado::**

```Python
!zgrep -v "\##" somatic.vcf.gz
```

**output:**

```
#CHROM	POS	ID	REF	ALT	QUAL	FILTER	INFO	FORMAT	WP043	WP044
9	5044364	.	C	A	.	.	AS_SB_TABLE=327,59|5,3;DP=450;ECNT=1;MBQ=31,20;MFRL=150,100;MMQ=60,60;MPOS=16;NALOD=-7.224e-01;NLOD=30.61;POPAF=6.00;TLOD=6.42	GT:AD:AF:DP:F1R2:F2R1:SB	0/1:258,6:0.022:264:88,3:130,1:212,46,4,2	0/0:128,2:0.016:130:48,0:69,1:115,13,1,1
9	5054761	.	A	G	.	.	AS_SB_TABLE=114,218|4,29;DP=387;ECNT=1;MBQ=30,30;MFRL=165,176;MMQ=60,60;MPOS=20;NALOD=2.05;NLOD=32.76;POPAF=6.00;TLOD=76.57	GT:AD:AF:DP:F1R2:F2R1:SB	0/1:207,33:0.148:240:76,14:100,16:71,136,4,29	0/0:125,0:8.880e-03:125:52,0:50,0:43,82,0,0
9	5073681	.	C	CT	.	.	AS_SB_TABLE=103,28|6,1;DP=162;ECNT=2;MBQ=31,30;MFRL=157,163;MMQ=60,60;MPOS=20;NALOD=0.125;NLOD=7.62;POPAF=6.00;RPA=10,11;RU=T;STR;TLOD=3.97	GT:AD:AF:DP:F1R2:F2R1:SB	0/1:93,6:0.063:99:28,2:47,3:74,19,5,1	0/0:38,1:0.048:39:11,1:18,0:29,9,1,0
9	5073770	.	G	T	.	.	AS_SB_TABLE=47,158|5,30;DP=247;ECNT=2;MBQ=31,29;MFRL=154,159;MMQ=60,60;MPOS=17;NALOD=1.86;NLOD=21.06;POPAF=3.40;TLOD=82.13	GT:AD:AF:DP:F1R2:F2R1:SB	0/1:130,35:0.241:165:12,3:25,4:35,95,5,30	0/0:75,0:0.014:75:6,0:9,0:12,63,0,0
```

**Mostra variantes (Mostra metadados do arquivo VCF.).**

```Python
!zgrep "##" somatic.vcf.gz
```

**17. GetPileupSummaries (tumor e normal)**

***Usado para estimar contaminação.***

**Tumor:**

```Python
!./gatk-4.2.2.0/gatk GetPileupSummaries \
	-I somatico/tumor_JAK2.bam \
	-V somatico/af-only-gnomad-chr9.vcf.gz \
	-L chr9.interval_list \
	-O tumor_JAK2.table
```
***Exemplo de visualização:***

```Python
!head tumor_JAK2.table
```

**output:**

```
#<METADATA>SAMPLE=WP043
contig	position	ref_count	alt_count	other_alt_count	allele_frequency
9	5029611	6	0	0	0.012
9	5030080	3	0	0	0.078
9	5033977	1	0	0	0.197
9	5035069	1	0	0	0.075
9	5041562	14	0	0	0.017
9	5043050	1	0	0	0.016
9	5044331	89	0	1	0.02
9	5045664	2	0	0	0.078
```

**Normal:**

```Python
!./gatk-4.2.2.0/gatk GetPileupSummaries \
	-I somatico/normal_JAK2.bam \
	-V somatico/af-only-gnomad-chr9.vcf.gz \
	-L chr9.interval_list \
	-O normal_JAK2.table
```

**18. Calcular contaminação**

***Gera uma estimativa de contaminação do tumor, usada no filtro final.***

```bash
%%bash
./gatk-4.2.2.0/gatk CalculateContamination \
	-I tumor_JAK2.table \
	-matched normal_JAK2.table \
	-O contamination.table
```

**19. Filtrar variantes (FilterMutectCalls)**

***Aplica filtros automáticos do Mutect2 para remover falsos positivos.***

```bash
%%bash
./gatk-4.2.2.0/gatk FilterMutectCalls \
-R chr9.fa \
-V somatic.vcf.gz \
--contamination-table contamination.table \
-O filtered.vcf.gz
```

**Visualizar variantes filtradas:**

***Permite ver as variantes finais apuradas no gene JAK2.***

```Python
# JAK2
!zgrep -v "##" filtered.vcf.gz
```

**output:**

```
#CHROM	POS	ID	REF	ALT	QUAL	FILTER	INFO	FORMAT	WP043	WP044
9	5044364	.	C	A	.	normal_artifact;strand_bias;weak_evidence	AS_FilterStatus=weak_evidence,strand_bias;AS_SB_TABLE=327,59|5,3;DP=450;ECNT=1;GERMQ=93;MBQ=31,20;MFRL=150,100;MMQ=60,60;MPOS=16;NALOD=-7.224e-01;NLOD=30.61;POPAF=6.00;TLOD=6.42	GT:AD:AF:DP:F1R2:F2R1:SB	0/1:258,6:0.022:264:88,3:130,1:212,46,4,2	0/0:128,2:0.016:130:48,0:69,1:115,13,1,1
9	5054761	.	A	G	.	strand_bias	AS_FilterStatus=strand_bias;AS_SB_TABLE=114,218|4,29;DP=387;ECNT=1;GERMQ=93;MBQ=30,30;MFRL=165,176;MMQ=60,60;MPOS=20;NALOD=2.05;NLOD=32.76;POPAF=6.00;TLOD=76.57	GT:AD:AF:DP:F1R2:F2R1:SB	0/1:207,33:0.148:240:76,14:100,16:71,136,4,29	0/0:125,0:8.880e-03:125:52,0:50,0:43,82,0,0
9	5073681	.	C	CT	.	normal_artifact;slippage;strand_bias;weak_evidence	AS_FilterStatus=weak_evidence,strand_bias;AS_SB_TABLE=103,28|6,1;DP=162;ECNT=2;GERMQ=93;MBQ=31,30;MFRL=157,163;MMQ=60,60;MPOS=20;NALOD=0.125;NLOD=7.62;POPAF=6.00;RPA=10,11;RU=T;STR;STRQ=1;TLOD=3.97	GT:AD:AF:DP:F1R2:F2R1:SB	0/1:93,6:0.063:99:28,2:47,3:74,19,5,1	0/0:38,1:0.048:39:11,1:18,0:29,9,1,0
9	5073770	.	G	T	.	PASS	AS_FilterStatus=SITE;AS_SB_TABLE=47,158|5,30;DP=247;ECNT=2;GERMQ=93;MBQ=31,29;MFRL=154,159;MMQ=60,60;MPOS=17;NALOD=1.86;NLOD=21.06;POPAF=3.40;TLOD=82.13	GT:AD:AF:DP:F1R2:F2R1:SB	0/1:130,35:0.241:165:12,3:25,4:35,95,5,30	0/0:75,0:0.014:75:6,0:9,0:12,63,0,0
```

**Colab**
> https://colab.research.google.com/drive/1rMhlp-wxLKlBxETXxxzR-6rbNLbHVLYj#scrollTo=trqDUrt9EbNp
