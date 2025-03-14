# League of Legends Early Game Objectives Statistical Analysis

## Introduction

### General Introduction
This project analyzes the 2021 League of Legends Esports Match Data, I choose this year because my favorite team EDG won the S series championship. 

We will work on a comprehensive dataset containing detailed statistics from professional League of Legends matches played throughout 2021. League of Legends is one of the world's most popular esports, featuring professional competitions across various regional leagues globally. The game involves two teams of five players battling to destroy the opponent's base while securing various objectives that provide advantages throughout the match.

## Research Question

**How do early game objectives influence match outcomes in professional League of Legends games?**

This analysis specifically focuses on four key early game objectives:
- **First Blood** - The team that secures the first kill in the game
- **First Tower** - The team that destroys an enemy tower first
- **First Dragon** - The team that defeats the dragon objective first
- **First Herald** - The team that defeats the Rift Herald objective first

## Dataset Information

The dataset contains **24,716 rows** of team-level data extracted from professional matches. Each row represents one team's performance in a single game, with corresponding statistics.

### Relevant Columns:

| Column | Description |
|--------|-------------|
| `firstblood` | Whether the team secured the first kill (1) or not (0) |
| `firsttower` | Whether the team destroyed the first tower (1) or not (0) |
| `firstdragon` | Whether the team killed the first dragon (1) or not (0) |
| `firstherald` | Whether the team killed the first Rift Herald (1) or not (0) |
| `result` | Match outcome - win (1) or loss (0) |
| `objective_advantage` | Total number of early game objectives secured by the team |
| `side` | Which side of the map the team played on (Blue/Red) |
| `gamelength` | Duration of the game in seconds |
| `league` | The professional league in which the match was played |

This binary classification analysis examines how well early game achievements correlate with victory, providing insights into which objectives most significantly impact a team's chances of winning.

## Data Cleaning and Exploratory Data Analysis

### Data Cleaning

The raw dataset contained information about individual players and teams across various professional League of Legends matches. The following cleaning steps were performed to prepare the data for analysis:

1. **Filtering Team-Level Data**: The original dataset contained 148,296 rows with 161 columns, including both team and player statistics. I filtered this to extract only team-level data (24,716 rows) by selecting rows where `position` equals 'team'.

2. **Handling Missing Values**: I identified and addressed missing values in the key early game objective columns:
   - `firstblood`: 12 missing values
   - `firsttower`: 2,110 missing values
   - `firstdragon`: 2,154 missing values
   - `firstherald`: 2,148 missing values

   Missing values were replaced with 0, indicating the team did not secure the objective. This approach aligns with the data-generating process, as missing objectives often indicate they weren't achieved by either team during the match.

3. **Feature Selection**: I selected only relevant columns for the analysis, focusing on match identifiers, early game objectives, and key performance metrics.

4. **Feature Engineering**: Several new columns were created to enhance the analysis:
   - `game_duration_mins`: Converted game length from seconds to minutes for better interpretability
   - `objective_advantage`: The total number of early game objectives secured by a team (ranging from 0 to 4)
   - `gold_per_min_15`: Gold accumulated per minute by the 15-minute mark
   - For each objective, created corresponding win rate features (e.g., `firstblood_win_rate`)

5. **Data Type Conversion**: Ensured all binary objective columns were properly formatted as integers (0 or 1).

These cleaning steps resulted in a structured dataset ready for analysis, where each row represents a team's performance in a match with clear indicators of early game objective control and match outcomes.

### Univariate Analysis

<iframe
  src="asset/fig5_plot.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>

The distribution of early game objectives reveals a relatively balanced acquisition pattern across professional teams. Approximately 50% of teams secure First Blood, while the other early game objectives (First Tower, First Dragon, and First Herald) are each obtained by roughly 45-46% of teams. This near-even distribution suggests that professional teams are competing closely for these important resources, with no single objective being significantly easier or harder to secure than others.

### Bivariate Analysis

<iframe
  src="asset/fig3_plot.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>

The analysis of win rates across different early game objectives reveals a clear correlation between objective control and match outcomes. First Tower has the strongest association with victory, with teams securing this objective winning approximately 68% of their matches. First Dragon and First Herald show similar win rates around 60%, while First Blood has the weakest correlation at 58%. This suggests that controlling map objectives (towers and neutral monsters) has a more substantial impact on match outcomes than securing early kills.

### Interesting Aggregates

The following table shows the relationship between the number of early game objectives secured and match outcomes:

| Number of Objectives | Number of Matches | Win Rate |
|----------------------|-------------------|----------|
| 0                    | 3,865             | 0.25     |
| 1                    | 6,488             | 0.37     |
| 2                    | 6,170             | 0.50     |
| 3                    | 5,397             | 0.68     |
| 4                    | 2,796             | 0.81     |

This aggregate table reveals a strong positive relationship between objective advantage and win rate. Teams that secure no early game objectives win only 25% of their matches, while teams that control all four early objectives win an impressive 81% of their matches. The progression shows an approximately linear increase in win probability with each additional objective secured. This clearly demonstrates the cumulative advantage that early game objectives provide, with the 50% win threshold crossed when a team secures exactly 2 objectives.

## Assessment of Missingness

### NMAR Analysis

I believe the missingness in the `firsttower`, `firstdragon`, and `firstherald` columns is **Not Missing At Random (NMAR)**. These columns have notably higher missing rates (around 8.5-8.7%) compared to `firstblood` (only 0.05%). 

This pattern suggests that the missingness might depend on the actual values themselves or on unobserved factors related to how the data was collected. In professional League of Legends matches, some games end before certain objectives can be taken - for example, a team might surrender before any towers are destroyed or before the dragon or herald spawns. In these cases, the missing values directly depend on the match dynamics and game duration, which are not fully captured in the available variables.

### Missingness Dependency

I conducted permutation tests to determine whether the missingness in the `firstblood` column depends on other variables in the dataset. The tests examined the relationship between `firstblood` missingness and two potential predictors: the match result (`result`) and the league (`league`).

<iframe
  src="asset/fig_result_fb.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>

The permutation test for dependency between `firstblood` missingness and `league` yielded a p-value of 1.0, far above the conventional significance threshold of 0.05. This indicates that the likelihood of first blood data being missing is not significantly affected by which league the match was played in, despite the visualization showing slightly higher missing percentages in LDL and LPL leagues.

This is consistent with the overall low missingness rate for first blood data (only 12 missing values out of 24,716 entries, or 0.05%). The missingness appears to be randomly distributed across leagues and match outcomes, suggesting that it's likely due to random recording errors rather than systematic factors related to these variables.

## Hypothesis Testing

### Formulating the Hypotheses

To address my research question about the relationship between early game objectives and match outcomes, I formulated the following hypotheses:

- **Null Hypothesis (H₀)**: There is no relationship between the total number of early objectives secured and the win rate.
- **Alternative Hypothesis (H₁)**: There is a relationship between the total number of early objectives secured and the win rate.

### Test Statistic and Significance Level

For this hypothesis test, I chose the **Pearson correlation coefficient** between `objective_advantage` (the number of early game objectives secured, ranging from 0-4) and `result` (win or loss) as my test statistic. 

I set the significance level at **α = 0.05**, which is a standard threshold in statistical analysis that balances the risk of Type I errors (false positives) and Type II errors (false negatives).

### Permutation Test Approach

To test the hypothesis without making distributional assumptions, I implemented a permutation test:

1. Calculated the observed correlation coefficient between `objective_advantage` and `result`
2. Repeatedly shuffled the `result` column while keeping the `objective_advantage` column fixed
3. For each permutation, calculated a new correlation coefficient
4. Determined the p-value as the proportion of permuted statistics that were as extreme as or more extreme than the observed statistic

<iframe
  src="asset/fig_perm.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>

### Results and Conclusion

The observed correlation coefficient between objective advantage and win rate was **0.3591**, indicating a moderate positive relationship. The permutation test yielded a **p-value of 0.000** (based on 1,000 permutations), which is less than the significance level of 0.05.

Based on these results, I can reject the null hypothesis. This suggests that there is evidence of a statistically significant relationship between the number of early game objectives secured and match outcomes in professional League of Legends games.

## Framing a Prediction Problem

### Prediction Problem Statement

The core prediction problem of this project is to determine whether a team will win or lose a League of Legends match based solely on information available during the early game phase. Specifically, I aim to predict the match outcome using only early game objective control data that would be known approximately 15 minutes into a match.

### Problem Type

This is a **binary classification** problem. I'm predicting a categorical outcome with two possible values:
- **Win** (1): The team wins the match
- **Loss** (0): The team loses the match

### Response Variable

The response variable is `result`, which represents the match outcome (win or loss). 

### Features Available at Prediction Time

For this prediction task, I only use features that would be available during the early phase of a League of Legends match (approximately the first 15 minutes). These include:

1. **First Blood** (`firstblood`): Whether the team secured the first kill in the match
2. **First Tower** (`firsttower`): Whether the team destroyed the first tower
3. **First Dragon** (`firstdragon`): Whether the team defeated the first dragon
4. **First Herald** (`firstherald`): Whether the team defeated the Rift Herald
5. **Objective Advantage** (`objective_advantage`): The total number of early objectives secured (0-4)
6. **Side** (`side`): Which side of the map the team played on (Blue/Red)

I excluded many features that would only be known after a match concludes, such as:
- Final gold difference
- Number of total kills
- Baron control 
- Total towers destroyed
- Final KDA (Kills/Deaths/Assists) ratios

### Evaluation Metric

I selected **accuracy** as the primary evaluation metric for this model. Accuracy measures the proportion of correct predictions (both wins and losses) out of all predictions made. For example, an accuracy of 65% would mean the model correctly predicted the match outcome in 65% of cases based only on early game objective information, which directly answers our research question about how strongly early objectives influence final results.

## Baseline Model

For the baseline prediction model, I developed a binary classifier to predict match outcomes (win/loss) using only two features:

1. **Objective Advantage** (`objective_advantage`): A quantitative feature representing the total number of early game objectives secured by a team (ranging from 0 to 4). This includes first blood, first tower, first dragon, and first herald.

2. **Map Side** (`side`): A nominal feature indicating which side of the map the team played on (Blue or Red).

### Feature Engineering and Preprocessing

The preprocessing pipeline consists of:

- **Numerical Features**: Standard scaling was applied to `objective_advantage` to normalize its range.
- **Categorical Features**: One-hot encoding was applied to the `side` feature (with `drop='first'` to avoid multicollinearity).

### Model Performance

The baseline model achieved the following performance metrics on the test set:
- **Accuracy**: 64.86%
- **ROC-AUC**: 70.07%

Classification report:
- **Precision** (Loss/Win): 0.65/0.63
- **Recall** (Loss/Win): 0.56/0.74
- **F1-Score** (Loss/Win): 0.62/0.68

### Model Assessment

The baseline model, despite its simplicity with only two features, demonstrates reasonable predictive power with nearly 65% accuracy. This is significantly better than random guessing (50% for a balanced dataset), indicating that early game objectives indeed have predictive value for match outcomes.

The model correctly predicts wins more often than losses (74% recall for wins vs. 56% for losses), suggesting it better captures patterns leading to victory.

The feature importance analysis shows that `objective_advantage` has a much higher coefficient (0.79) compared to `side_Red` (-0.16), confirming our hypothesis that early game objectives strongly influence match outcomes. The negative coefficient for Red side indicates a slight disadvantage when playing on that side of the map.

## Final Model

### Feature Engineering

I added several new features to enhance the model's predictive power, each designed to capture different aspects of the data-generating process in League of Legends matches:

1. **Weighted Objective Score**: Instead of treating all objectives equally (as in the `objective_advantage` feature), I created a weighted score that assigns different importance to each objective based on their typical impact on match outcomes:
   ```
   weighted_score = firstblood * 1.0 + firsttower * 2.0 + firstdragon * 1.5 + firstherald * 1.8
   ```
   This reflects the game's reality where securing the first tower typically provides more strategic advantage (map control, gold bonus) than securing first blood.

2. **Objective Synergies**: I created an interaction feature `tower_herald` that captures when a team secures both first tower and first herald. This feature represents a common strategic pattern in professional play where the Rift Herald is often used to destroy the first tower, creating a snowball effect.

3. **Objective Dominance**: The `obj_dominance` feature identifies when a team secures both first tower and first dragon, representing complete early game control. Teams that control both major map objectives typically have superior map awareness and coordination.

4. **Individual Objectives**: Rather than relying solely on the aggregated count, the final model uses each individual objective (`firstblood`, `firsttower`, `firstdragon`, `firstherald`) as separate features, allowing the model to learn the specific impact of each objective.

These engineered features better represent the actual gameplay dynamics where certain combinations of objectives create synergistic effects that are greater than the sum of their parts.

### Model Algorithm and Hyperparameters

For the final model, I selected a **Random Forest Classifier** instead of the Logistic Regression used in the baseline model. 
To find the optimal hyperparameters, I implemented a grid search with 5-fold cross-validation, exploring various combinations of:
- Number of estimators (trees): [100, 200]
- Maximum depth: [None, 10, 20]
- Minimum samples split: [2, 5, 10]
- Minimum samples leaf: [1, 2, 4]

The best-performing configuration was:
- `n_estimators`: 200
- `max_depth`: None (allowing trees to grow until fully developed)
- `min_samples_split`: 2
- `min_samples_leaf`: 1

### Performance Improvement

The final model achieved significant improvements over the baseline model:

| Metric | Baseline Model | Final Model | Improvement |
|--------|---------------|-------------|-------------|
| Accuracy | 67.86% | 66.94% | +2.08% |
| ROC-AUC | 70.07% | 72.61% | +2.54% |

The confusion matrix shows more balanced predictions between wins and losses compared to the baseline model:
- Precision (Loss/Win): 0.68/0.69
- Recall (Loss/Win): 0.73/0.61

The model is slightly more accuracy now. The feature importance analysis revealed that `firsttower` (37%), `weighted_score` (22%), and `total_objectives` (16%) were the most influential predictors. The model's emphasis on these features validates the common understanding among professional players that early tower control is a critical factor in match outcomes, more so than securing first blood or other objectives in isolation.

## Fairness Analysis

For my fairness analysis, I examined whether my final prediction model performs equally well across different sides of the map:

- Teams playing on the **Blue side** of the map
- Teams playing on the **Red side** of the map

This comparison is particularly meaningful in League of Legends because the map is asymmetrical, with different geographical advantages for each side. Blue side typically has easier access to dragon objectives, while Red side has better baron access. Professional teams also develop side-specific strategies, potentially creating systematic differences that a model might learn unfairly.

I selected **precision** as the fairness evaluation metric, which measures the proportion of positive predictions (predicted wins) that are correct. This metric helps determine if the model is more reliable when predicting wins for one side compared to the other.

### Hypothesis Testing

- **Null Hypothesis (H₀)**: The model is fair. Its precision for Blue side teams and Red side teams are roughly the same, and any differences are due to random chance.
- **Alternative Hypothesis (H₁)**: The model is unfair. Its precision for one side is significantly different than its precision for the other side.

### Test Statistic and Methodology

The test statistic was the **absolute difference in precision** between predictions for Blue side teams and Red side teams. 
I set the significance level at **α = 0.05**, a standard threshold for hypothesis testing.

### Results and Conclusion

The permutation test yielded a **p-value of 0.0980**, which is greater than the significance level of 0.05. Therefore, I failed to reject the null hypothesis.

This suggests that there is no statistically significant difference in how well the model performs for teams on different sides of the map. While there was a small observed difference in precision between Blue and Red sides (approximately 0.029), this difference could reasonably occur by random chance.

The fairness analysis provides confidence that the model makes predictions based on the meaningful patterns in early game objectives rather than unfairly favoring teams based on which side of the map they play on. This is an important consideration for the practical application of the model in esports analytics, ensuring that predictions are equitable regardless of map side assignment.

<iframe
  src="asset/fig_fairness.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>