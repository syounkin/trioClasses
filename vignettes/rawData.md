
```r
opts_chunk$set(fig.width = 7, fig.height = 5, comment = "", warning = FALSE, 
    tidy = TRUE, highlight = TRUE, fig.path = "figures/", dev = "png", background = "red")
library("trioClasses")
library("GWASTools")
library("CleftCNVAssoc")
```


```r
data("beaty.scanAnnot")
data("beaty.snpAnnot")
data("fe")
intensfile <- NcdfIntensityReader("/media/sgy/lockdown/GWASTools/beaty.intensity.nc")
genofile <- NcdfGenotypeReader("/media/sgy/lockdown/GWASTools/beaty.geno.nc")
xyfile <- NcdfIntensityReader("/media/sgy/lockdown/GWASTools/beaty.intensity.xy.nc")
scan.ids <- as.character(getScanID(beaty_scanAnnot))
scan.names <- as.character(getVariable(beaty_scanAnnot, "scanName"))
names(scan.names) <- scan.ids
names(scan.ids) <- as.character(scan.names)
```



```r
fa.id <- as.character(completeTrios(fe.beaty)$fid)
ma.id <- as.character(completeTrios(fe.beaty)$mid)
names(fa.id) <- as.character(completeTrios(fe.beaty)$id)
names(ma.id) <- as.character(completeTrios(fe.beaty)$id)
```


```r
offspring.vec <- as.character(completeTrios(fe.beaty)$id)
```


```r
gr <- GRanges(seqnames = rep("chr16", length(offspring.vec)), ranges = IRanges(start = 32404517, 
    end = 32530051), id = offspring.vec)
```


```r
raw.df.list <- getRaw(gr, intensfile = intensfile, snpAnnot = beaty_snpAnnot, 
    scan.id = scan.ids, fa.id = fa.id, ma.id = ma.id, genofile = genofile, xyfile = xyfile)
```


```r
head(raw.df.list[[1]])
```

```
     logr    baf      pos logr.fa logr.ma baf.fa baf.ma geno geno.fa
1 -0.0034 0.0021 32404517  0.3408  0.0461 0.0041 0.0000   NA      NA
2 -0.0105 0.0044 32404959  0.2262 -0.0421 0.0155 0.0261   NA      NA
3  0.1060 0.0000 32405177  0.2159 -0.1033 0.0038 0.0082   NA      NA
4  0.1355 1.0000 32405464  0.1135  0.0823 0.9981 1.0000   NA      NA
5  0.4216 0.0000 32405894  0.3769  0.4550 0.0000 0.0000   NA      NA
6  0.2457 0.0007 32406646  0.1938  0.1694 0.0227 0.0221   NA      NA
  geno.ma     x     y  x.fa  y.fa  x.ma  y.ma     snpname
1      NA 1.181 0.028 1.495 0.039 1.229 0.022 cnvi0022821
2      NA 1.194 0.106 1.387 0.143 1.137 0.134 cnvi0022826
3      NA 1.539 0.134 1.633 0.172 1.302 0.144 cnvi0020617
4      NA 0.035 1.039 0.040 1.018 0.034 1.001 cnvi0022830
5      NA 0.933 0.028 0.911 0.021 0.953 0.032 cnvi0020621
6      NA 1.801 0.076 1.688 0.123 1.661 0.120 cnvi0022831
```

```r
head(raw.df.list[[2]])
```

```
    logr    baf      pos logr.fa logr.ma baf.fa baf.ma geno geno.fa
1 0.1870 0.0009 32404517  0.3145  0.0147 0.0041 0.0000   NA      NA
2 0.1410 0.0000 32404959  0.3573 -0.0979 0.0000 0.0089   NA      NA
3 0.2580 0.0116 32405177  0.3956  0.2541 0.0000 0.0000   NA      NA
4 0.1061 1.0000 32405464  0.2630 -0.0574 1.0000 1.0000   NA      NA
5 0.1743 0.0000 32405894  0.3977  0.0065 0.0000 0.0000   NA      NA
6 0.0612 0.0056 32406646  0.3595  0.1363 0.0130 0.0097   NA      NA
  geno.ma     x     y  x.fa  y.fa  x.ma  y.ma     snpname
1      NA 1.350 0.029 1.468 0.039 1.200 0.024 cnvi0022821
2      NA 1.338 0.105 1.553 0.124 1.117 0.106 cnvi0022826
3      NA 1.666 0.192 1.883 0.162 1.701 0.152 cnvi0020617
4      NA 0.024 1.029 0.023 1.151 0.020 0.920 cnvi0022830
5      NA 0.787 0.023 0.926 0.020 0.701 0.020 cnvi0020621
6      NA 1.575 0.077 1.918 0.114 1.650 0.090 cnvi0022831
```



```r
pos.vec <- raw.df.list[[1]]$pos
logr.list <- lapply(raw.df.list, function(df) df$logr)
logr.fa.list <- lapply(raw.df.list, function(df) df$logr.fa)
logr.ma.list <- lapply(raw.df.list, function(df) df$logr.ma)
```


```r
par(bg = "lightyellow")
plot(1, type = "n", xlim = range(pos.vec), ylim = c(-2, 2))
for (i in 1:length(logr.list)) {
    points(jitter(pos.vec, amount = 1000), logr.list[[i]], pch = 20, col = "orange")
}
```

![plot of chunk logrplot](figures/logrplot1.png) 

```r
plot(1, type = "n", xlim = range(pos.vec), ylim = c(-2, 2))
for (i in 1:length(logr.fa.list)) {
    points(jitter(pos.vec, amount = 1000), logr.fa.list[[i]], pch = 20, col = "red")
}
```

![plot of chunk logrplot](figures/logrplot2.png) 

```r
plot(1, type = "n", xlim = range(pos.vec), ylim = c(-2, 2))
for (i in 1:length(logr.ma.list)) {
    points(jitter(pos.vec, amount = 1000), logr.ma.list[[i]], pch = 20, col = "blue")
}
```

![plot of chunk logrplot](figures/logrplot3.png) 

Now plot only offspring with an untransmitted deletion.