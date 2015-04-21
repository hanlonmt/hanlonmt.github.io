---
layout: post
title: Finding genes around a specific position in the rice genome
description: This is something I do every day (almost). Here's how.
tags: [R]
comments: true
---

In my lab, we're working to further understand the results of the Genome Wide Association Studies (GWAS) that we've been completing on various rice root traits. One task that I do quite often is finding the identity of all of the genes around SNPs of interest. It's fairly simple (now), but definitely took an investment of my time to figure out. Here's how I do it:

I'll simplify this, by starting with four SNPs that I'm interested in:


{% highlight r %}
SNP.NAME <- c("SNP-2.26133435.", "SNP-4.19760929.", "SNP-12.11555551.")
CHROMOSOME <- c(2, 4, 12)
POSITION <- c(26139305, 19932894, 11558216)
rice <- data.frame(SNP.NAME, CHROMOSOME, POSITION)
rice
{% endhighlight %}



{% highlight text %}
##           SNP.NAME CHROMOSOME POSITION
## 1  SNP-2.26133435.          2 26139305
## 2  SNP-4.19760929.          4 19932894
## 3 SNP-12.11555551.         12 11558216
{% endhighlight %}

Now, the fun begins. First, I need to load in the packages that I need to access the rice genome. I already have bioconductor and the associated packages installed on my system. If you don't, see [these instructions](http://www.bioconductor.org/install/). I'll be getting both the rice genes and similar arabidopsis genes, so this code includes both. 



{% highlight r %}
library(dplyr)

#source("http://bioconductor.org/biocLite.R")
#biocLite()
library("GenomicRanges")
library("biomaRt")
# set the mart or the genome that you're using
rice_mart <- useMart("plants_mart_26", dataset="osativa_eg_gene")
arabi_mart <- useMart("plants_mart_26", dataset="athaliana_eg_gene")

# I want all genes within 10kb of the SNP
search.area <- 10000
rice$start <- rice$POSITION - search.area ; rice$end <- rice$POSITION + search.area
rice$start <- as.numeric(rice$start); rice$end <- as.numeric(rice$end)
{% endhighlight %}

That's it for setup. Now, I have to take the ranges that I made and convert them to a GRanges format. It's kind of like a bedfile, but it's the R/bioconductor format. After that, I use this object to select genes from the genomes and then match them back up.




{% highlight r %}
rice_gr <- makeGRangesFromDataFrame(rice,
                                    keep.extra.columns=TRUE,
                                    ignore.strand=TRUE,
                                    seqinfo=NULL,
                                    seqnames.field="CHROMOSOME",
                                    start.field="start",
                                    end.field="end",
                                    starts.in.df.are.0based=FALSE)

# get rice gene positions - only get protein coding genes and get arabidopsis genes. getBM is the call to biomart. I'm getting all of the genes from the genome for this.
genes_r <- getBM(attributes = c("ensembl_gene_id","chromosome_name","start_position","end_position"), 
                 filters = "biotype", values = "protein_coding", mart=rice_mart)
genes_r2 <- getBM(attributes = c("ensembl_gene_id", "athaliana_eg_gene"), 
                  filters = "ensembl_gene_id", values = list(genes_r$ensembl_gene_id), mart = rice_mart)
genes_r <- left_join(genes_r, genes_r2)

# convert to a GRanges object from these biomart outputs
genes_r_gr <- makeGRangesFromDataFrame(genes_r,
                                       keep.extra.columns=TRUE,
                                       ignore.strand=TRUE,
                                       seqinfo=NULL,
                                       seqnames.field="chromosome_name",
                                       start.field="start_position",
                                       end.field="end_position",
                                       starts.in.df.are.0based=FALSE)



# find overlaps between biomart and my peaks of interest
overlaps_r <- mergeByOverlaps(rice_gr, genes_r_gr, type="any")
rice_genes <- as.data.frame(overlaps_r)
rice_genes <- distinct(rice_genes)
{% endhighlight %}

In the end, I get a data frame object with these genes that I can use further down the line. I also generally clean it up, but this is the basic output. Tables are a little funky going from R/knitr to md/html for the webpage. The R output is this. I'll improve it in the future


{% highlight r %}
rice_genes
{% endhighlight %}



{% highlight text %}
##    rice_gr.seqnames rice_gr.start rice_gr.end rice_gr.width rice_gr.strand
## 1                 2      26129305    26149305         20001              *
## 2                 2      26129305    26149305         20001              *
## 3                 4      19922894    19942894         20001              *
## 4                 4      19922894    19942894         20001              *
## 5                 4      19922894    19942894         20001              *
## 6                 4      19922894    19942894         20001              *
## 7                 4      19922894    19942894         20001              *
## 8                12      11548216    11568216         20001              *
## 9                12      11548216    11568216         20001              *
## 10               12      11548216    11568216         20001              *
## 11               12      11548216    11568216         20001              *
##    rice_gr.SNP.NAME rice_gr.POSITION         SNP.NAME POSITION
## 1   SNP-2.26133435.         26139305  SNP-2.26133435. 26139305
## 2   SNP-2.26133435.         26139305  SNP-2.26133435. 26139305
## 3   SNP-4.19760929.         19932894  SNP-4.19760929. 19932894
## 4   SNP-4.19760929.         19932894  SNP-4.19760929. 19932894
## 5   SNP-4.19760929.         19932894  SNP-4.19760929. 19932894
## 6   SNP-4.19760929.         19932894  SNP-4.19760929. 19932894
## 7   SNP-4.19760929.         19932894  SNP-4.19760929. 19932894
## 8  SNP-12.11555551.         11558216 SNP-12.11555551. 11558216
## 9  SNP-12.11555551.         11558216 SNP-12.11555551. 11558216
## 10 SNP-12.11555551.         11558216 SNP-12.11555551. 11558216
## 11 SNP-12.11555551.         11558216 SNP-12.11555551. 11558216
##    genes_r_gr.seqnames genes_r_gr.start genes_r_gr.end genes_r_gr.width
## 1                    2         26143311       26144729             1419
## 2                    2         26143311       26144729             1419
## 3                    4         19921005       19929376             8372
## 4                    4         19921005       19929376             8372
## 5                    4         19924408       19928041             3634
## 6                    4         19933241       19937491             4251
## 7                    4         19941027       19942075             1049
## 8                   12         11563795       11576773            12979
## 9                   12         11563795       11576773            12979
## 10                  12         11563795       11576773            12979
## 11                  12         11563795       11576773            12979
##    genes_r_gr.strand genes_r_gr.ensembl_gene_id
## 1                  *               OS02G0649300
## 2                  *               OS02G0649300
## 3                  *               OS04G0402200
## 4                  *               OS04G0402200
## 5                  *               OS04G0402250
## 6                  *               OS04G0402300
## 7                  *               OS04G0402500
## 8                  *               OS12G0295600
## 9                  *               OS12G0295600
## 10                 *               OS12G0295600
## 11                 *               OS12G0295600
##    genes_r_gr.athaliana_eg_gene ensembl_gene_id athaliana_eg_gene
## 1                     AT2G46680    OS02G0649300         AT2G46680
## 2                     AT3G61890    OS02G0649300         AT3G61890
## 3                     AT5G07350    OS04G0402200         AT5G07350
## 4                     AT5G61780    OS04G0402200         AT5G61780
## 5                                  OS04G0402250                  
## 6                     AT3G02070    OS04G0402300         AT3G02070
## 7                     AT4G35070    OS04G0402500         AT4G35070
## 8                     AT1G03730    OS12G0295600         AT1G03730
## 9                     AT4G28340    OS12G0295600         AT4G28340
## 10                    AT4G28330    OS12G0295600         AT4G28330
## 11                    AT4G03600    OS12G0295600         AT4G03600
{% endhighlight %}
