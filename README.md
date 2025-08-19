# MetaMetrics: Item/Test Analysis in R

Starter template for evaluating multiple-choice test data (0/1 scored items) in R.  
Includes code for item difficulty, discrimination, reliability (Cronbach’s alpha), and score distribution plots.

## Dataset Format
- **Column 1:** `student_id` (character ID)  
- **Columns 2–11:** Binary item responses (0 = incorrect, 1 = correct)  
- **Rows:** One per student (~1,000 students)

---

## How to Run

1. Put your dataset at:

2. In R, install required packages (first time only):
```r
install.packages(c("dplyr", "psych", "ggplot2"))
library(dplyr)
library(psych)
library(ggplot2)

# Load dataset
data <- read.csv("data/student_responses.csv", stringsAsFactors = FALSE)

# Remove student ID
responses <- data[, -1]

# 1) Item difficulty (proportion correct)
difficulty <- colMeans(responses)
print(difficulty)

# 2) Total test score
data$total_score <- rowSums(responses)
summary(data$total_score)

# Histogram of total scores
hist(
  data$total_score,
  main = "Distribution of Total Scores",
  xlab = "Score"
)

# 3) Item–total correlations
item_total_corr <- cor(responses, data$total_score)
print(item_total_corr)

# 4) Reliability (Cronbach’s alpha / KR-20)
alpha_results <- psych::alpha(responses)
print(alpha_results)

# 5) Item analysis table
item_analysis <- data.frame(
  Item = colnames(responses),
  Difficulty = difficulty,
  ItemTotalCorr = alpha_results$item.stats$r.drop
)
print(item_analysis)

# Save item analysis table
dir.create("outputs", showWarnings = FALSE)
write.csv(item_analysis, "outputs/item_analysis.csv", row.names = FALSE)

# 6) Plots
## Item difficulty
ggplot(item_analysis, aes(x = Item, y = Difficulty)) +
  geom_bar(stat = "identity", fill = "steelblue") +
  ylim(0, 1) +
  labs(title = "Item Difficulties", y = "Proportion Correct")

## Item–total correlations
ggplot(item_analysis, aes(x = Item, y = ItemTotalCorr)) +
  geom_bar(stat = "identity", fill = "darkred") +
  labs(title = "Item–Total Correlations", y = "Correlation")
