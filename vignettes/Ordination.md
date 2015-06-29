## Ordination examples

Here some examples with HITChip data. See also [phyloseq ordination tutorial](joey711.github.io/phyloseq/plot_ordination-examples.html).

Load example data:


```r
library(microbiome)
library(ggplot2)
pseq <- download_microbiome("atlas1006")

# Convert signal to relative abundances
pseq.rel <- transform_sample_counts(pseq, function (x) {x/sum(x)})

# Pick OTUs that are present with >1 percent relative abundance 
# in >10 percent of the samples
pseq2 <- filter_taxa(pseq.rel, function(x) sum(x > .01) > (0.1*nsamples(pseq.rel)), TRUE)
```


### Principal component analysis (PCA)

Project data on the first two PCA axes:


```r
set.seed(423542)
x <- t(otu_table(pseq2)@.Data)
proj <- project.data(log10(x), type = "PCA") # MDS.classical etc.
```


Visualize and highlight:


```r
# Highlight gender
p <- densityplot(proj, col = sample_data(pseq2)$gender, legend = T)
print(p)
```

![plot of chunk ordination4](figure/ordination4-1.png) 

```r
# Highlight low/high Prevotella subjects
prevotella.abundance  <- log10(x[, "Prevotella melaninogenica et rel."]) 
p <- densityplot(proj, col = prevotella.abundance, legend = T)
print(p)
```

![plot of chunk ordination4](figure/ordination4-2.png) 

Projection with sample names:


```r
ggplot(aes(x = Comp.1, y = Comp.2, label = rownames(proj)), data = proj) + geom_text(size = 2)
```

![plot of chunk visu-example2](figure/visu-example2-1.png) 


### Unifrac with PCoA

Not implemented with HITChip but popular with sequencing data. See [phyloseq tutorial](http://joey711.github.io/phyloseq/plot_ordination-examples.html). 


### NMDS with Bray-Curtis distances


```r
pseq.ord <- ordinate(pseq2, "NMDS", "bray")
```

```
## Run 0 stress 0.2028085 
## Run 1 stress 0.2236351 
## Run 2 stress 0.4205223 
## Run 3 stress 0.2159774 
## Run 4 stress 0.2131121 
## Run 5 stress 0.2135156 
## Run 6 stress 0.219663 
## Run 7 stress 0.2202646 
## Run 8 stress 0.2202117 
## Run 9 stress 0.2179414 
## Run 10 stress 0.213931 
## Run 11 stress 0.420529 
## Run 12 stress 0.2142954 
## Run 13 stress 0.2198069 
## Run 14 stress 0.2185281 
## Run 15 stress 0.2098309 
## Run 16 stress 0.2191812 
## Run 17 stress 0.2115131 
## Run 18 stress 0.2171189 
## Run 19 stress 0.2164978 
## Run 20 stress 0.2187442
```

Ordinate the taxa in NMDS plot with Bray-Curtis distances


```r
p <- plot_ordination(pseq2, pseq.ord, type = "taxa", color = "Genus", title = "taxa")
print(p)
```

![plot of chunk pca-ordination21](figure/pca-ordination21-1.png) 

Grouping the plots by Phylum


```r
p + facet_wrap(~Phylum, 5)
```

![plot of chunk pca-ordination22](figure/pca-ordination22-1.png) 


### Multidimensional scaling (MDS)


```r
plot_ordination(pseq, ordinate(pseq, "MDS"), color = "DNA_extraction_method") + geom_point(size = 5)
```

![plot of chunk ordinate23](figure/ordinate23-1.png) 


### Canonical correspondence analysis (CCA)

With samples:


```r
p <- plot_ordination(pseq, ordinate(pseq, "CCA"), type = "samples", color = "gender")
p + geom_point(size = 5) + geom_polygon(aes(fill = gender))
```

![plot of chunk ordinate24a](figure/ordinate24a-1.png) 

With taxa:


```r
p <- plot_ordination(pseq, ordinate(pseq, "CCA"), type = "taxa", color = "Phylum")
p <- p + geom_point(size = 4)
print(p)
```

![plot of chunk ordinate24b](figure/ordinate24b-1.png) 


### Split plot


```r
plot_ordination(pseq, ordinate(pseq, "CCA"), type = "split", color = "gender", 
    shape = "Phylum", label = "gender")
```

![plot of chunk ordinate25](figure/ordinate25-1.png) 


### Ordination biplot


```r
plot_ordination(pseq, ordinate(pseq, "CCA"), type = "biplot", shape = "Phylum")
```

![plot of chunk ordinate26](figure/ordinate26-1.png) 





