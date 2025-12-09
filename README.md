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

```
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
