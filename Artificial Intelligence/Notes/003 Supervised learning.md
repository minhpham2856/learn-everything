# What is Supervised Learning?

Supervised learning teaches a computer to make predictions or decisions based on historical data. Basically, a model learns from labelled data which are (feature, target), (question, answer), or (x, y) pairs. ^9e0a1d

Imagine a student preparing for an exam using practice tests that include both questions and answer keys. The questions are the input data, and the answer keys are the correct labels. By attempting each question, comparing their answers with the answer keys, and adjusting their reasoning after mistakes, the student learns the patterns to solve new, unseen questions.
## Problem types in SL

Supervised learning problems generally fall into two main categories based on the nature of the output target:
1. **Classification**: The target output is a discrete categorical variable. The model predicts which category or class an input belongs to. (yes/ no $\to$ spam emails/ not spam; churn/ not churn)
2. **Regression**: The target output is a continuous numerical variable. The model predicts a specific quantity along a continuous numerical scale. (House price forecasting, wind speed estimation, stock price prediction, and salary prediction)
## The workflow

The process of building and using a SL model follows 5 main sequential steps:
1. **Collect labelled data**: Gather a dataset that consists of input features alongside a correct target label $(x, y)$.
2. **Split the dataset**: Divide the collected data into a **training set** (~80%) used to teach the model and a **testing set** (~20%) kept hidden during training to evaluate performance.
3. **Train the model**: Pass the training features and labels to a supervised learning algorithm.
4. **Validate and test**: Evaluate the trained model on the unseen testing data. Compare the model's predictions with the actual labels to measure error and accuracy.
5. **Deploy and predict**: Once the model performs well, deploy it :)

## Advantages and limitations

### Advantages
* **High accuracy**: Delivers reliable predictions when provided with clean, representative, and abundant labelled data.
* **Clear evaluation**: Performance can be directly measured by comparing predictions against known labels.
* **Versatility**: Applies effectively to a broad range of continuous forecasting and discrete classification tasks.
### Limitations
* **Data availability**: Requires large volumes of labelled data, which can be expensive, time-consuming, or difficult to collect.
* **Vulnerability to bias**: Imbalanced or unrepresentative training data leads to biased or inaccurate predictions.
* **Risk of overfitting**: Complex models may memorise training examples rather than learning generalisable patterns.

# ==Algo1: Linear regression==

LR is one of the simplest and most widely used algorithms in supervised learning. It's used to model the relationship between features and a continuous target variable by fitting a straight line or [[#^58c412|hyperplanes]] through the data points. it assumes that the target has a linear relationship with the features.

Suppose we want to predict a student's exam score based on the number of hours they studied. As study hours increase, exam scores generally increase. We call the number of hours the **independent variable** (feature $x$), and the exam score the **dependent variable** (target $y$).
## 2 types of LR

- **Simple LR**: Uses a single input feature $x$ to predict the target $y$. $$\hat{y} = w x + b$$$\hat{y}$ is the predicted value, $x$ is the input feature, $w$ is the slope (weight or coefficient), and $b$ is the y-intercept (bias).
- **Multiple LR**: Uses multiple input features $x_1, x_2, \dots, x_n$ to predict the target $y$. $$\hat{y} = w_0 + w_1 x_1 + w_2 x_2 + \dots + w_n x_n$$In vector notation, this can be written concisely as: $$\hat{y} = \mathbf{w}^T \mathbf{x} + b$$Where $\mathbf{w}$ represents the vector of feature weights and $\mathbf{x}$ represents the input feature vector.
## Finding the best-fit line

The objective of LR is to find the **best-fit line**, a line that minimises the total difference between the actual data points and the model's predictions. The difference between an actual target $y_i$ and the predicted $\hat{y}_i$ for a given data point is called a **residual**: $$\text{Residual} = y_i - \hat{y}_i$$To find the best-fit line, linear regression uses the **least squares method**, which minimises the sum of the squared residuals across all $n$ training examples: $$\text{Sum of Squared Errors (SSE)} = \sum_{i=1}^{n} (y_i - \hat{y}_i)^2$$Squaring the residuals ensures that positive and negative errors do not cancel each other out, while also penalising larger errors more heavily.
## The cost function

To quantify how well our model fits the overall dataset, we define a **cost function**. For LR, the standard cost function is the **Mean Squared Error (MSE)**: $$J(w, b) = \frac{1}{2n} \sum_{i=1}^{n} (\hat{y}_i - y_i)^2$$Here, $n$ is the total number of training samples, $\hat{y}_i$ is the predicted output, and $y_i$ is the actual target label. The factor of $\frac{1}{2}$ is included to simplify calculations when taking derivatives during parameter updates.
## Parameter optimisation with gradient descent

To find the parameter values $w$ and $b$ that minimise the cost function $J(w, b)$, we use an optimisation algorithm called **gradient descent**. Gradient descent works by iteratively adjusting the parameters in the direction that decreases the cost:
1. Initialise parameters $w$ and $b$ with arbitrary values (such as 0 or small random numbers).
2. Compute the predictions $\hat{y}_i$ for all training examples.
3. Calculate the gradient (partial derivatives) of the cost function with respect to each parameter.
4. Update each parameter by moving a small step in the opposite direction of the gradient:
   $$w \leftarrow w - \alpha \frac{\partial J}{\partial w}$$
   $$b \leftarrow b - \alpha \frac{\partial J}{\partial b}$$
   Here, $\alpha$ is the **learning rate**, which controls how large a step we take during each iteration.
5. Repeat this process until the cost function reaches its minimum value.
## Fundamental assumptions of LR

For LR to produce reliable results, several assumptions about the underlying data should hold true:
1. **Linearity**: The relationship between the input features and the target variable is linear.
2. **Independence of errors**: The residual errors of individual observations are independent of one another.
3. **Homoscedasticity**: The variance of the residual errors remains constant across all levels of the features. If the spread of errors widens or narrows significantly, the data exhibits heteroscedasticity.
4. **Normality of errors**: The residual errors follow a normal distribution.
5. **No multicollinearity**: In multiple linear regression, the input features should not be strongly correlated with one another.
6. **Additivity**: The total effect of the features on the target variable is the simple sum of their individual effects.
## Evaluation metrics for regression

To evaluate how accurately a LR model predicts continuous values, we use several standard evaluation metrics:
* **Mean Squared Error (MSE)**: Measures the average of the squared differences between actual and predicted values. $$\text{MSE} = \frac{1}{n} \sum_{i=1}^{n} (y_i - \hat{y}_i)^2$$
* **Mean Absolute Error (MAE)**: Measures the average of the absolute differences between actual and predicted values. It's less sensitive to extreme outliers than MSE. $$\text{MAE} = \frac{1}{n} \sum_{i=1}^{n} |y_i - \hat{y}_i|$$
* **Root Mean Squared Error (RMSE)**: The square root of the MSE. RMSE expresses error in the same units as the target variable, making it easy to interpret. $$\text{RMSE} = \sqrt{\text{MSE}}$$
* **R-squared ($R^2$)**: Represents the proportion of total variance in the target variable that is explained by the model features. Values range from 0 to 1, where higher values indicate a better fit. $$R^2 = 1 - \frac{\sum_{i=1}^{n} (y_i - \hat{y}_i)^2}{\sum_{i=1}^{n} (y_i - \bar{y})^2}$$
* **Adjusted R-squared**: A modified version of $R^2$ that adjusts for the number of predictors in the model, penalising the inclusion of irrelevant features.
## Regularisation techniques

When a linear model contains many features or suffers from multicollinearity, it may overfit the training data. **Regularisation** adds a penalty term to the cost function to constrain the size of the parameter weights:
* **Lasso regression (L1 regularisation)**: Adds a penalty proportional to the absolute value of the feature weights ($\lambda \sum |w_i|$). Lasso can drive irrelevant feature weights completely to 0, effectively performing automatic feature selection.
* **Ridge regression (L2 regularisation)**: Adds a penalty proportional to the square of the feature weights ($\lambda \sum w_i^2$). Ridge shrinks weights evenly, helping to manage multicollinearity without removing features entirely.
* **Elastic Net regression**: Combines both L1 and L2 penalties into a single objective function, balancing feature selection with weight shrinkage.
## Code implementation

Let's look at a Python implementation using `scikit-learn` to build and train a LR model:
```python
import numpy as np
import matplotlib.pyplot as plt
from sklearn.linear_model import LinearRegression

# Generate synthetic dataset
np.random.seed(42)
X = np.random.rand(50, 1) * 100
y = 3.5 * X + np.random.randn(50, 1) * 20

# Create and train the model
model = LinearRegression()
model.fit(X, y)

# Make predictions
y_pred = model.predict(X)

# Display model coefficients
print("Slope (w):", model.coef_[0][0])
print("Intercept (b):", model.intercept_[0])
```
## Advantages and limitations

### Advantages
* Simple to understand, implement, and interpret.
* Computationally efficient and fast to train on large datasets.
* Serves as an excellent baseline model for regression tasks.
### Limitations
* Assumes a linear relationship between features and targets, struggling with non-linear patterns.
* Highly sensitive to extreme outliers, which can heavily distort the fitted line.
* Performance degrades if input features suffer from strong multicollinearity.

# ==Algo2: Logistic regression==

Despite its name, **LogR is a SL algorithm used for classification problems**. Instead of predicting a continuous numerical value like LR, LogR predicts the probability that a given input belongs to a specific class.
## The sigmoid function

If we try to use standard linear LR for binary classification, the model can produce output values < 0 or > 1. Because probabilities must always be  $[0, 1]$, we need a mathematical function to transform continuous linear outputs into valid probabilities. LogR achieves this by passing the linear equation $z = \mathbf{w}^T \mathbf{x} + b$ through the **sigmoid function** (logistic function):
$$\sigma(z) = \frac{1}{1 + e^{-z}}$$
The sigmoid function maps any real number into the range $[0, 1]$, producing an "S"-shaped curve:
* As $z \to \infty$, $\sigma(z) \to 1$.
* As $z \to -\infty$, $\sigma(z) \to 0$.
* When $z = 0$, $\sigma(z) = 0.5$.

To convert this output into a final class prediction, we choose a decision threshold (~0.5):
$$\hat{y} = \begin{cases} 1 & \text{if } \sigma(z) \ge 0.5 \\ 0 & \text{if } \sigma(z) < 0.5 \end{cases}$$
## Odds and log-odds

LogR models the **odds** of an event occurring, which is defined as the ratio of the probability of the event occurring ($p$) to the probability of it not occurring ($1 - p$): $$\text{Odds} = \frac{p}{1 - p}$$Taking the natural log of the odds gives the **log-odds** (or **logit**): $$\ln\left(\frac{p}{1 - p}\right) = \mathbf{w}^T \mathbf{x} + b$$This relationship shows that while the probability output is constrained between 0 and 1, the log-odds vary linearly with the input features.
## Cost function: Log-loss

In LR, we used MSE as our cost function. However, passing the sigmoid function through MSE results in a non-convex cost function with many local minima, making gradient descent unreliable. To solve this, LogR uses **Log-Loss** (also called **Binary Cross-Entropy**): $$J(\mathbf{w}, b) = -\frac{1}{n} \sum_{i=1}^{n} \left[ y_i \ln(\hat{y}_i) + (1 - y_i) \ln(1 - \hat{y}_i) \right]$$Here, $y_i$ is the actual binary label ($0$ or $1$), and $\hat{y}_i = \sigma(\mathbf{w}^T \mathbf{x}_i + b)$ is the predicted probability.

Log-Loss heavily penalises confident incorrect predictions:
* If the true label $y_i = 1$ and the model predicts $\hat{y}_i \to 1$, the loss is nearly $0$. If the model predicts $\hat{y}_i \to 0$, the loss approaches infinity.
* If the true label $y_i = 0$ and the model predicts $\hat{y}_i \to 0$, the loss is nearly $0$. If the model predicts $\hat{y}_i \to 1$, the loss approaches infinity.

Model parameters are trained using **Maximum Likelihood Estimation (MLE)** or gradient descent on this log-loss function.
## Types of LogR

* **Binomial LogR**: Used when the target variable has 2 possible categories (Pass/Fail, Spam/Not Spam, or 0/1).
* **Multinomial LogR**: Used when the target variable has $\geq$ 3 unordered categories (such as classifying an image as Cat, Dog, or Bird). It replaces the sigmoid function with the **Softmax function**: $$P(Y = c \mid \mathbf{x}) = \frac{e^{\mathbf{w}_c^T \mathbf{x} + b_c}}{\sum_{k=1}^{K} e^{\mathbf{w}_k^T \mathbf{x} + b_k}}$$
* **Ordinal LogR**: Used when the target variable has $\geq$ 3 categories with a natural ranking or order (Low, Medium, and High risk).
## Evaluation metrics

To assess the performance of a classification model, we use metrics derived from the **confusion matrix** (True Positives `TP`, True Negatives `TN`, False Positives `FP`, False Negatives `FN`).

|                | Predicted (+) | Predicted (-) |
| :------------: | :-----------: | :-----------: |
| **Actual (+)** |     `TP`      |     `FN`      |
| **Actual (-)** |     `FP`      |     `TF`      |

| Metric           | Definition                                                                                                                                                                      | Formula                                                                                                     |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :---------------------------------------------------------------------------------------------------------- |
| **Accuracy**     | The proportion of all correct predictions.                                                                                                                                      | $\text{Accuracy} = \frac{TP + TN}{TP + TN + FP + FN}$                                                       |
| **Precision**    | The proportion of correct positive predictions.                                                                                                                                 | $\text{Precision} = \frac{TP}{TP + FP}$                                                                     |
| **Recall (TPR)** | The proportion of correctly identified positive cases.                                                                                                                          | $\text{Recall} = \frac{TP}{TP + FN}$                                                                        |
| **F1 score**     | The harmonic mean of precision and recall $\to$ a balanced metric when dealing with imbalanced datasets.                                                                        | $\text{F1 Score} = 2 \times \frac{\text{Precision} \times \text{Recall}}{\text{Precision} + \text{Recall}}$ |
| **AUC-ROC**      | Measures the area under the **Receiver Operating Characteristic** curve, evaluating the model's ability to distinguish between classes across all possible decision thresholds. | ![AUC-ROC](https://media.geeksforgeeks.org/wp-content/uploads/20250804094411616734/111.webp)                |
Think of a model like a teacher checking 100 exam papers and deciding whether each answer is correct or incorrect. **Accuracy** tells us how many papers the teacher got right overall, **precision** tells us how often the papers the teacher marked as correct were actually correct, and **recall** tells us how many of the actually correct answers the teacher managed to identify. **F1 score** combines precision and recall into one balanced measure. **AUC-ROC** is like checking the teacher at every possible strictness level, and measuring how well they can separate correct answers from incorrect ones overall.
## Code implementation

Here is a Python implementation of binomial LogR using `scikit-learn`:
```python
from sklearn.datasets import load_breast_cancer
from sklearn.linear_model import LogisticRegression
from sklearn.model_selection import train_test_split
from sklearn.metrics import accuracy_score

# Load data and split into training and testing sets
X, y = load_breast_cancer(return_X_y=True)
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.20, random_state=42)

# Train logistic regression classifier
clf = LogisticRegression(max_iter=10000, random_state=42)
clf.fit(X_train, y_train)

# Evaluate model performance
acc = accuracy_score(y_test, clf.predict(X_test))
print(f"Model accuracy: {acc * 100:.2f}%")
```
## LR vs LogR

| Property                  | LR                                                  | LogR                                |
| :------------------------ | :-------------------------------------------------- | :---------------------------------- |
| **Problem type**          | Regression (continuous target)                      | Classification (discrete target)    |
| **Output type**           | Continuous numerical values                         | Probabilities in $[0,1]$            |
| **Decision boundary**     | Best-fit straight line or [[#^58c412\|hyperplanes]] | S-shaped sigmoid probability curve  |
| **Primary cost function** | MSE                                                 | Log-Loss / Binary Cross-Entropy     |
| **Parameter estimation**  | Least Squares / Gradient Descent                    | Maximum Likelihood Estimation (MLE) |
# ==Algo3: Decision trees==

A DT is a non-parametric algorithm used for both classification and regression. It organises decision logic into a hierarchical, tree-like flowchart that makes predictions by following a sequence of conditional checks.
## Structure of a DT

A DT has of 3 core building blocks:
* **Root node**: The top-most node in the tree, representing the entire dataset and the initial feature test.
* **Internal (decision) nodes**: Nodes representing subsequent feature checks.
* **Branches**: Paths connecting nodes, representing the outcomes of a test condition.
* **Leaf nodes**: Terminal nodes that hold the final prediction or class label.
## How a DT works

A DT operates by recursively splitting the dataset into smaller subsets based on feature values. The goal at each splIt's to create child nodes that are as **pure** as possible, meaning all items in a child node belong to the same class. 

Consider deciding whether to go hiking based on weather conditions:
1. **Root node (Outlook)**: Is the outlook `Sunny`, `Overcast`, or `Rainy`?
	- If `Overcast` $\to$ Predict "Hiking" (Leaf node).
	- If `Sunny` $\to$ Check Humidity (Internal node).
	- If `Rainy` $\to$ Check Wind (Internal node).
2. **Internal node (Humidity)**: Is humidity `High` or `Normal`?
	- If `High` $\to$ Predict "Stay Inside".
	- If `Normal` $\to$ Predict "Hiking".
## Measuring node purity

### 1. Information gain and Entropy

**Entropy** measures the degree of impurity/randomness within a collection of data points $S$: $$\text{Entropy}(S) = -\sum_{i=1}^{C} p_i \log_2(p_i)$$Here, $p_i$ represents the proportion of samples belonging to class $i$. If a node contains only a single class, its entropy is $0$ (perfect purity). If a node has an equal mix of classes, its entropy reaches its maximum value of $1$. 

**Information gain** measures how much entropy decreases after splitting dataset $S$ using feature $A$:
$$\text{Gain}(S, A) = \text{Entropy}(S) - \sum_{v \in \text{Values}(A)} \frac{|S_v|}{|S|} \text{Entropy}(S_v)$$
The DT algorithm evaluates all available features and selects the feature with the highest Information Gain to perform the split.
### 2. Gini impurity

The **Gini impurity** (Gini index) measures how often a randomly chosen element from a set would be incorrectly labelled: $$\text{Gini}(S) = 1 - \sum_{i=1}^{C} p_i^2$$A Gini impurity of $0$ indicates perfect purity. The Gini impurity is computationally faster than entropy because it avoids calculating logarithms.

## Advantages and limitations

### Advantages
* Highly interpretable and easy to visualize.
* Requires minimal data preprocessing (no feature scaling required).
* Handles both continuous and categorical variables seamlessly.
### Limitations
* Highly prone to **overfitting** if allowed to grow deep without constraints.
* Sensitive to small variations in data; slight changes in the training set can produce a completely different tree.
* Biased toward features with many distinct categories.
# ==Algo4: Random forest==

RF is an ensemble learning method that improves accuracy and prevents overfitting by combining predictions from multiple DTs. (An ensemble method combines predictions from multiple individual models to produce a stronger, more reliable prediction)
## Working mechanism: Bagging and feature randomness

RF relies on two key techniques to build diverse DTs:
1. **Bootstrap Aggregating (Bagging)**: Instead of training all trees on the exact same dataset, each tree is trained on a random sample of the training data drawn with replacement (a bootstrap sample).
2. **Feature randomness**: When splitting a node inside an individual tree, the algorithm considers only a random subset of all available features rather than searching through every feature. This ensures that individual trees are not overly correlated.

To make a final prediction:
* **For classification**: Each tree in the forest casts a vote, and the class with the **majority vote** is selected.
* **For regression**: The outputs of all individual decision trees are **averaged**.
## Out-of-bag (OOB) error

Because each decision tree is trained on a bootstrap sample, roughly 36.8% of the training instances are left out of any given tree's training set. These left-out instances are called **(OOB) samples**. They serve as a built-in validation dataset. By evaluating each tree on its corresponding OOB samples, RFs can estimate its generalisation error without needing a separate validation set.
## Code implementation

Below is a Python implementation of RF regression using `scikit-learn`:
```python
from sklearn.ensemble import RandomForestRegressor
from sklearn.model_selection import train_test_split
from sklearn.metrics import mean_squared_error, r2_score
import numpy as np

# Generate synthetic dataset
X = np.random.rand(100, 3) * 10
y = 2 * X[:, 0] + 3 * X[:, 1] + np.random.randn(100) * 2

# Split data into training and testing sets
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

# Train Random Forest Regressor
regressor = RandomForestRegressor(n_estimators=100, random_state=42, oob_score=True)
regressor.fit(X_train, y_train)

# Evaluate model performance
y_pred = regressor.predict(X_test)
print("Out-of-Bag Score:", regressor.oob_score_)
print("R-squared:", r2_score(y_test, y_pred))
```
## Advantages and limitations

### Advantages
* Significantly reduces the risk of overfitting compared to a single decision tree.
* Delivers high prediction accuracy on complex, non-linear datasets.
* Robust to noisy data and handles missing values effectively.
### Limitations
* Less interpretable than a single decision tree due to the complexity of combining many trees.
* Computationally expensive and requires more memory to train and store.
* Slower prediction speed on large datasets.

# ==Algo5: Support vector machines==

SVM is a powerful algorithm used for classification and regression tasks. It works by finding the optimal decision boundary that separates data points of different classes with the largest possible margin.
## Key concepts

* **Hyperplane**: The decision boundary separating different classes in feature space. In 2D, it's a line; in 3D, it's a plane; and in higher dimensions, it's a $d-1$ dimensional subspace. ^58c412
* **Support vectors**: The data points located closest to the hyperplane. These specific points are critical because they define the position and orientation of the decision boundary.
* **Margin**: The distance between the hyperplane and the closest support vectors from any class. SVM explicitly attempts to **maximise this margin**.
## Hard margin versus soft margin

* **Hard margin SVM**: Enforces strict separation where no data points are allowed to cross the margin boundaries. Hard margins work only when data is perfectly linearly separable and are highly sensitive to outliers.
* **Soft margin SVM**: Introduces **slack variables** ($\xi_i$) to allow a controlled number of misclassifications or margin violations. This improves model generalization on noisy or overlapping datasets.

The trade-off between margin width and misclassification penalty is controlled by the regularization parameter $C$:
* A **high $C$ value** imposes a heavy penalty on misclassifications, forcing narrower margins (higher risk of overfitting).
* A **low $C$ value** allows more margin violations to achieve a wider margin (better generalisation, but risks underfitting).
## The Hinge loss function

To optimise a soft margin SVM, the algorithm minimises a cost function combining weight regularisation and **Hinge loss**: $$\text{Loss} = \max(0, 1 - y_i (\mathbf{w}^T \mathbf{x}_i + b))$$
- If a data point is correctly classified and outside the margin, its loss is $0$.
* If a point lies within the margin or is misclassified, its loss increases proportionally to its distance from the correct boundary.
## Non-linear SVM and the kernel trick

When data points cannot be separated by a straight line in their original feature space, SVM uses a technique called the **kernel trick**. A kernel function maps non-linearly separable data into a higher-dimensional feature space where a linear hyperplane can separate the classes. Common kernel functions include:
* **Linear kernel**: $K(\mathbf{x}_i, \mathbf{x}_j) = \mathbf{x}_i^T \mathbf{x}_j$
* **Polynomial kernel**: $K(\mathbf{x}_i, \mathbf{x}_j) = (\mathbf{x}_i^T \mathbf{x}_j + c)^d$
* **Radial Basis Function (RBF) kernel**: $K(\mathbf{x}_i, \mathbf{x}_j) = \exp(-\gamma \|\mathbf{x}_i - \mathbf{x}_j\|^2)$
## Code implementation

Below is a Python code example using `scikit-learn` to build a Support Vector Classifier:
```python
from sklearn.datasets import load_breast_cancer
from sklearn.svm import SVC
from sklearn.model_selection import train_test_split
from sklearn.metrics import accuracy_score

# Load dataset and split into train and test sets
X, y = load_breast_cancer(return_X_y=True)
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

# Train linear SVM model
svm_model = SVC(kernel="linear", C=1.0)
svm_model.fit(X_train, y_train)

# Calculate accuracy
acc = accuracy_score(y_test, svm_model.predict(X_test))
print(f"SVM Model Accuracy: {acc * 100:.2f}%")
```
## Advantages and limitations

### Advantages
* Highly effective in high-dimensional spaces (such as text classification and gene expression data).
* Versatile due to different kernel functions for non-linear decision boundaries.
* Memory efficient because the decision boundary depends only on support vectors.
### Limitations
* Slow to train on very large datasets.
* Sensitive to noise and overlapping classes.
* Requires careful feature scaling and hyperparameter tuning ($C, \gamma$).

# ==Algo6: K-nearest neighbors==

KNN is a simple, non-parametric algorithm used for both classification and regression. Unlike models that construct explicit mathematical equations during training, KNN is an **instance-based learner** (**lazy learner**). It simply stores the training dataset and performs all computations at the time of making a prediction.
## How it works

When a new, unseen data point is provided, KNN makes predictions using four simple steps:
1. **Choose $k$**: Select the number of nearest neighbors $k$ to consider.
2. **Calculate distances**: Compute the distance between the new data point and every point in the training dataset.
3. **Find nearest neighbors**: Identify the $k$ training points with the smallest distances to the test point.
4. **Make prediction**:
   * **For classification**: Assign the class label that appears most frequently among the $k$ neighbors (**majority voting**).
   * **For regression**: Take the **average** of the target values of the $k$ neighbors.
## Distance metrics

To measure proximity between data points, KNN uses mathematical distance formulas:
* **Euclidean distance**: The straight-line distance between two points in Euclidean space. $$d(\mathbf{x}, \mathbf{y}) = \sqrt{\sum_{i=1}^{n} (x_i - y_i)^2}$$
* **Manhattan distance**: The grid-like distance traveled along perpendicular axes (taxicab distance).
  $$d(\mathbf{x}, \mathbf{y}) = \sum_{i=1}^{n} |x_i - y_i|$$
* **Minkowski distance**: A generalised distance formula that encompasses Euclidean ($p=2$) and Manhattan ($p=1$) distances as special cases.  $$d(\mathbf{x}, \mathbf{y}) = \left( \sum_{i=1}^{n} |x_i - y_i|^p \right)^{1/p}$$
## Selecting the value of $k$

Choosing the right value for $k$ is crucial for model accuracy:
* **If $k$ is too small (e.g., $k=1$)**: The model is overly sensitive to noise and outliers, leading to **overfitting**.
* **If $k$ is too large**: The decision boundary becomes overly smooth, incorporating distant data points from other categories and leading to **underfitting**.
* **Rule of thumb**: Choose an odd number for $k$ in binary classification to prevent voting ties, and select optimal values using [[Suplementary/Concepts#Supplementary Technical Concepts|cross-validation]].
## Code implementation: KNN from scratch

Here is a Python implementation of KNN classification built from scratch using NumPy and `collections.Counter`:
```python
import numpy as np
from collections import Counter

# Define Euclidean distance function
def euclidean_distance(point1, point2):
    return np.sqrt(np.sum((np.array(point1) - np.array(point2)) ** 2))

# KNN prediction algorithm
def knn_predict(X_train, y_train, test_point, k=3):
    distances = []
    for i in range(len(X_train)):
        dist = euclidean_distance(test_point, X_train[i])
        distances.append((dist, y_train[i]))
    
    # Sort distances and take top k labels
    distances.sort(key=lambda x: x[0])
    k_nearest_labels = [label for _, label in distances[:k]]
    
    # Return majority vote label
    return Counter(k_nearest_labels).most_common(1)[0][0]

# Example dataset
X_train = [[1, 2], [2, 3], [3, 4], [6, 7], [7, 8]]
y_train = ['A', 'A', 'A', 'B', 'B']
test_point = [4, 5]

prediction = knn_predict(X_train, y_train, test_point, k=3)
print("Predicted Class:", prediction)
```
## Advantages and limitations

### Advantages
* Simple to understand and implement.
* Zero training time required since instances are stored directly.
* Adapts naturally as new data points are added.
### Limitations
* Slow prediction phase on large datasets because every test point must be compared against all stored training samples.
* Performance degrades significantly in high-dimensional space due to the **[[Suplementary/Concepts#003 Supervised Learning Concepts|curse of dimensionality]]**.
* Highly sensitive to unscaled features; features with large numerical ranges can dominate distance calculations.

# ==Algo7: Gradient boosting==

Gradient Boosting is a powerful ensemble learning technique that builds predictive models sequentially by combining many weak learners (typically decision trees). Unlike Random Forest, which builds independent decision trees in parallel using bagging, **Gradient Boosting builds trees sequentially, where each new tree is trained to correct the residual errors made by the previous trees**.
## How gradient boosting works

1. **Initial prediction**: Start with an initial base prediction $\hat{y}_0$, such as the mean of the target values.
2. **Calculate residuals**: Compute the difference between actual targets $y$ and current predictions $\hat{y}$. These residuals represent the negative gradients of the loss function.
3. **Train weak learner**: Train a new decision tree to predict these residual errors.
4. **Update model**: Add the new tree's predictions to the ensemble, scaled by a **learning rate** ($\eta$).
5. **Repeat**: Repeat this sequential process for a specified number of estimators ($M$).

The final ensemble prediction is given by:
$$\hat{y} = \hat{y}_0 + \eta \sum_{m=1}^{M} f_m(\mathbf{x})$$
Where $f_m(\mathbf{x})$ represents the prediction of the $m$-th individual decision tree.
## Learning rate and shrinkage

The **learning rate** ($\eta$) scales the contribution of each newly added tree to prevent overfitting:
* **Smaller learning rates** reduce individual tree contributions, requiring more trees to reach optimal performance, but improving generalization.
* **Larger learning rates** accelerate convergence, but increase the risk of overfitting the training data.
## AdaBoost vs Gradient boosting

| Feature | AdaBoost | Gradient boosting |
| :--- | :--- | :--- |
| **Error correction mechanism** | Increases weights of misclassified data points | Fits new models to residual errors (loss gradients) |
| **Base learners** | Uses [[Suplementary/Concepts#Supplementary Technical Concepts|decision stumps]] (trees with a single split) | Uses shallow decision trees with variable depth |
| **Loss function** | Uses an exponential loss function | Optimises any differentiable loss function |
| **Sensitivity to noise** | Highly sensitive to outliers due to sample reweighting | Less sensitive due to smooth updates via gradients |
## Code implementation

Below is a Python code example demonstrating Gradient Boosting for classification and regression using `scikit-learn`:
```python
from sklearn.ensemble import GradientBoostingClassifier, GradientBoostingRegressor
from sklearn.datasets import load_digits, load_diabetes
from sklearn.model_selection import train_test_split
from sklearn.metrics import accuracy_score, mean_squared_error
import numpy as np

# 1. Gradient Boosting Classifier
X_cls, y_cls = load_digits(return_X_y=True)
X_train_c, X_test_c, y_train_c, y_test_c = train_test_split(X_cls, y_cls, test_size=0.25, random_state=42)

gbc = GradientBoostingClassifier(n_estimators=300, learning_rate=0.05, max_features=5, random_state=42)
gbc.fit(X_train_c, y_train_c)
print(f"Classifier Accuracy: {accuracy_score(y_test_c, gbc.predict(X_test_c)):.4f}")

# 2. Gradient Boosting Regressor
X_reg, y_reg = load_diabetes(return_X_y=True)
X_train_r, X_test_r, y_train_r, y_test_r = train_test_split(X_reg, y_reg, test_size=0.25, random_state=42)

gbr = GradientBoostingRegressor(n_estimators=300, learning_rate=0.1, max_depth=1, random_state=42)
gbr.fit(X_train_r, y_train_r)
rmse = np.sqrt(mean_squared_error(y_test_r, gbr.predict(X_test_r)))
print(f"Regressor RMSE: {rmse:.2f}")
```
## Advantages and limitations

### Advantages
* Often provides state-of-the-art predictive performance on structured, tabular datasets.
* Supports arbitrary differentiable loss functions for diverse problem types.
* Handles non-linear feature relationships effectively.
### Limitations
* Prone to overfitting if parameters like tree depth, learning rate, and number of estimators are not tuned properly.
* Longer training times due to its sequential training process.
* Less interpretable compared to individual decision trees.

# ==Algo8: Naive Bayes==

Naive Bayes is a family of probabilistic classification algorithms based on **Bayes' Theorem**. It's widely used in text classification, spam filtering, and sentiment analysis due to its simplicity and computational efficiency.
## Bayes' theorem and the naive independence assumption

Bayes' Theorem calculates the conditional probability of a class label $y$ given an input feature vector $\mathbf{x} = (x_1, x_2, \dots, x_n)$: $$P(y \mid \mathbf{x}) = \frac{P(\mathbf{x} \mid y) P(y)}{P(\mathbf{x})}$$
Where:
* $P(y \mid \mathbf{x})$ is the **posterior probability** of class $y$ given features $\mathbf{x}$.
* $P(\mathbf{x} \mid y)$ is the **likelihood** of observing features $\mathbf{x}$ given class $y$.
* $P(y)$ is the **prior probability** of class $y$.
* $P(\mathbf{x})$ is the **marginal likelihood** (evidence).

The algorithm is called **"naive"** because it assumes that all input features are **conditionally independent** of each other given the class label $y$: $$P(x_1, x_2, \dots, x_n \mid y) = P(x_1 \mid y) \times P(x_2 \mid y) \times \dots \times P(x_n \mid y) = \prod_{i=1}^{n} P(x_i \mid y)$$Combining Bayes' Theorem with this independence assumption gives:
$$P(y \mid x_1, \dots, x_n) \propto P(y) \prod_{i=1}^{n} P(x_i \mid y)$$
To make a final prediction $\hat{y}$, the classifier computes the posterior probability for each possible class and selects the class with the highest value:
$$\hat{y} = \arg\max_{y} \left( P(y) \prod_{i=1}^{n} P(x_i \mid y) \right)$$
## Types of Naive Bayes models

1. **Gaussian Naive Bayes**: Used when features are continuous numbers. It assumes continuous features follow a Gaussian distribution within each class: $$P(x_i \mid y) = \frac{1}{\sqrt{2\pi \sigma_y^2}} \exp\left( -\frac{(x_i - \mu_y)^2}{2\sigma_y^2} \right)$$
   where $\mu_y$ and $\sigma_y^2$ are the mean and variance of feature $x_i$ for class $y$.
2. **Multinomial Naive Bayes**: Used when features represent discrete word counts or term frequencies in text classification.
3. **Bernoulli Naive Bayes**: Used when features are binary, indicating the presence or absence of a word in a document.
## Advantages and limitations

### Advantages
* Fast to train and predict, with low computational overhead.
* Performs exceptionally well on high-dimensional text classification tasks.
* Handles categorical features naturally.
### Limitations
* The feature independence assumption rarely holds true in real-world datasets.
* If a categorical feature value was not observed in the training set for a class, its probability is estimated as zero (**[[Suplementary/Concepts#003 Supervised Learning Concepts|zero-frequency problem]]**), which requires [[Suplementary/Concepts#003 Supervised Learning Concepts|Laplace smoothing]] to fix.