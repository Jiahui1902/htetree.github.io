## Welcome to the homepage for `htetree`
`htetree` is used to estimate heterogeneous treatment effect with tree-based machine learning algorithms and visualize estimated results in flexible and presentation-ready ways (see more in Brand et al., 2020). 

### Installation
```
install.packages("htetree",
                 repos = "https://jiahui1902.github.io/drat/",
                 type = "source")
library("rpart")
library("htetree")
library(causalTree)
```

### Examples

```
library(htetree)
data("simulation.1")
# estimate the propensity score
fit <- glm(treatment~x1+x2+x3+x4+x5+x6+x7+x8+x9+x10,
           data=simulation.1,
           family = "binomial")
simulation.1$ps_score <- predict(fit,type = "response")
linear_terms <- paste0("x",1:10)

# estimate the model with htetree package
set.seed(1)
lb <- c(paste0("var",1:10),"propensity score")
names(lb) <- c(paste0("x",1:10),"ps_score")

tree1 <- htetree::hte_ipw(outcomevariable = 'y',
               minsize=20,crossvalidation = 40,negative = TRUE,
               data = simulation.1,
               ps_indicator = "ps_score",
               drawplot = TRUE,treatment_indicator = "treatment",
               # no_indicater = '_IPW_simulation',
               legend.x = 0.1,legend.y = 0.25,varlabel = lb)
```
![alt text](./figures/figure1.png?raw=true "Static Visualization for the Estimated Results")

### Interactive visualization using ShinyApp
```
# The saveFiles function saves all the necessary files for the shiny app in a user specified directory
# By default it saves in your current working directory, in a folder named "shinyapp"
# Be careful if you already have a folder named "shinyapp", it will overwrite
# To check your working directory, use getwd()
saveFiles(tree1, simulation.1, outcomevariable='y', treatment_indicator='treatment',
          propensity_score="ps_score")

# library(shiny) must be loaded to use runApp after creating the folder of files
directory <- getDefaultPath()
runApp(appDir = directory)

# The runDynamic function runs the visualization without saving any of the files
runDynamic(tree1, simulation.1, outcomevariable='y', treatment_indicator='treatment',
           propensity_score="ps_score")

# The files for runDynamic are saved in the temporary directory
# The files can be cleared manually using the clearTemp() function, or will automatically be cleared when you close R
clearTemp()
```

### References

Brand, Jennie E., Jiahui Xu, Bernard Koch, and Pablo Geraldo. “Uncovering Sociological Effect Heterogeneity Using Tree-Based Machine Learning.”     *Sociological Methodology* 51, no. 2 (August 2021): 189–223. https://doi.org/10.1177/0081175021993503.

