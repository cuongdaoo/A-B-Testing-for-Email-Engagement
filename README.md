# A-B-Testing-for-Email-Engagement

## 1. 📌 Introduction

This project focuses on conducting an **A/B Testing experiment in an email marketing campaign** to evaluate the effectiveness of two different email subject lines in attracting user engagement.

The campaign was split into two groups:

* **Subject Line A**: *"Exclusive offer just for you today!"*
* **Subject Line B**: *"We have a special gift for you 🎁"*

Each subject line was tested on a randomly selected subset of recipients. The goal is to determine **which subject line is more compelling and drives better user interaction**, measured through:

* **Open Rate** – whether the subject line encourages recipients to open the email.
* **Click-Through Rate (CTR)** – whether recipients engage with content inside the email.
* **Conversion Rate (CR)** – whether users ultimately take the desired action (e.g., making a purchase or filling out a form).

By applying statistical analysis, this A/B test aims to identify whether **Subject Line A or B is significantly more effective** in influencing user behavior. The results will guide future marketing strategies and subject line optimization.


---

## 2. 📊 Data Description

The dataset contains 400 records, each representing one sent email. Below is a description of the attributes and how performance metrics are derived:

### 🔹 Columns

1. **Email ID**: Unique identifier for each email sent.
2. **Campaign Name**: Experimental group (A or B) used for A/B testing.
3. **Subject Line**: The subject line of the email (used to test audience engagement.).
4. **Sent Time**: The timestamp when the email was sent.
5. **Delivered**: Indicates whether the email was successfully delivered to the recipient.
6. **Opened**: Indicates whether the recipient opened the email.
7. **Clicked**: Indicates whether the recipient clicked on any link inside the email.
8. **Bounced**: Indicates whether the email failed to be delivered (hard or soft bounce).
9. **Marked Spam**: Indicates whether the recipient marked the email as spam.
10. **Unsubscribed**: Indicates whether the recipient unsubscribed from future emails.
11. **Replied**: Indicates whether the recipient replied to the email.
12. **Converted**: Indicates whether the recipient performed the desired conversion action (e.g., purchase, signup).
13. **Conversion Target**: The specific goal of the campaign — e.g., `Purchase`, `Signup`, `Download`, etc.
14. **Device**: The device type used to open the email (e.g., `Mobile`, `Desktop`, `Tablet`).
15. **Open Time**: Timestamp of when the email was opened (if applicable).
16. **Location**: The city or region inferred from IP or user profile (e.g., `Hanoi`, `Ho Chi Minh City`, etc.).   

### 🔹 Derived Metrics
📖 **Open Rate**

**Definition:**
Open Rate is the **percentage of successfully delivered emails that were actually opened** by recipients.

**Formula:**

$$
\text{Open Rate} = \frac{\text{Number of Opens}}{\text{Number of Delivered Emails}} \times 100
$$

**Meaning:**
Open Rate indicates **how effective the subject line and sender name are at capturing the recipient's attention**. A high open rate suggests that the email stood out in the inbox and sparked curiosity or trust, prompting the recipient to open it.

 

📬 **Click-Through Rate (CTR)**

**Definition:**
Click-Through Rate is the **percentage of recipients who clicked on a link within an email** out of the total number of emails that were **successfully delivered**.

**Formula:**

$$
\text{CTR} = \frac{\text{Number of Clicks}}{\text{Number of Delivered Emails}} \times 100
$$

**Meaning:**
CTR measures **how engaging the email content and call-to-action (CTA)** are. A high CTR indicates that recipients are **interested and motivated to learn more** after opening the email.


🎯 **Conversion Rate (CR)**

**Definition:**
Conversion Rate is the **percentage of recipients who completed a desired action** (e.g., making a purchase, filling out a form, signing up) after clicking a link in the email.

**Formula:**

$$
\text{CR} = \frac{\text{Number of Conversions}}{\text{Number of Clicks}} \times 100
$$

**Meaning:**
CR reflects the **ultimate effectiveness of the email campaign**, showing how many people who clicked actually **completed the intended goal**. A high CR suggests that the **landing page or offer is persuasive and well-aligned with user expectations**.

---

## 3. ⚙️ Data Processing

To enable proper numeric analysis, boolean columns were converted from `True/False` to `1/0`.

```python
bool_cols = ['Delivered', 'Opened', 'Clicked', 'Bounced', 
             'Marked Spam', 'Unsubscribed', 'Replied', 'Converted']

df[bool_cols] = df[bool_cols].astype(int)
````

Also, NaN values were handled where necessary, and derived metrics such as CTR and CR were calculated.

---

## 4. 📈 Comparing Campaigns Performance

We analyzed each metric (Delivered, Opened, Clicked, Converted, etc.) across both groups:

* Pie chart to show **group distribution**
* Bar chart comparison of total events by **Group A vs. Group B**
* Additional charts for **CTR** and **CR** comparisons

These visualizations help identify which campaign performs better in various aspects.

---
## 5. 💡 Theoretical Foundations

### **a. Shapiro-Wilk Test**

**Purpose:**

To assess whether a dataset is **normally distributed**.

**When to use:**

* When you need to check the **normality assumption** before applying parametric tests (like a t-test).
* Suitable for **small to moderate sample sizes** (n ≤ 2000).

**Hypotheses:**

* **H₀ (Null Hypothesis):** The data follows a normal distribution.
* **H₁ (Alternative Hypothesis):** The data does **not** follow a normal distribution.

**Interpreting results:**

* If **p-value > α** (commonly 0.05): fail to reject H₀ → data **may be normally distributed**.
* If **p-value ≤ α**: reject H₀ → data is **not normally distributed**.



### **b. Mann-Whitney U Test**

(Also known as the **Wilcoxon rank-sum test**)

**Purpose:**

To compare the **central tendencies** (typically medians) of **two independent groups** when the data is **not normally distributed**.

**When to use:**

* When comparing two independent groups.
* When the normality assumption is **violated**.
* It is a **non-parametric** test, so it does not assume normal distribution.

**Hypotheses:**

* **H₀:** The distributions of the two groups are the same (equal medians).
* **H₁:** The distributions are different (different medians).

**How it works:**

* Data from both groups is combined and **ranked**.
* The **sum of ranks** for each group is computed, and then the **U statistic** is calculated.
* The **p-value** is derived from U to decide whether to reject H₀.


### **c. Why the t-test is not appropriate in these cases**

The **t-test is a parametric test**, which relies on several key assumptions:

1. **Normal distribution** of the data in each group.
2. **Equal variances** between the two groups (for independent samples t-test).
3. Data should be on an **interval or ratio scale**.

**Why t-test may be inappropriate:**

* If the **Shapiro-Wilk test** shows that the data is **not normally distributed**, then the assumptions for the t-test are **violated**.
* In such cases, the **Mann-Whitney U test** is a better alternative because it does **not require normality**.
* The t-test is **sensitive to outliers and skewed data**, whereas the Mann-Whitney U test is **more robust** in such scenarios.


## 6. 🧪 Hypothesis Testing

To statistically determine if there's a significant difference between Group A and B, we followed these steps:

### ✅ Step 1: Check Normality of Distributions

Used **Shapiro-Wilk Test** to test if the distribution of metrics (e.g. CTR, CR) is normal.

```python
from scipy.stats import shapiro
shapiro(df['CTR'])  # Example
```

### ✅ Step 2: Use Appropriate Statistical Test

Since distributions were **not normal**, we used the **Mann-Whitney U Test** (a non-parametric test):

```python
from scipy.stats import mannwhitneyu
mannwhitneyu(group_A, group_B, alternative='greater')
```

### 📌 Hypotheses Example for CTR:

* **H₀ (Null Hypothesis)**: There is no difference between Group A and B
* **H₁ (Alternative Hypothesis)**: Group A has higher CTR than Group B

We conducted tests for:

* **Open Rate**
* **Click-Through Rate (CTR)**
* **Conversion Rate (CR)**

### 🧠 Definitions:

* **Shapiro-Wilk Test**: Assesses normality of the distribution
* **Mann-Whitney U Test**: Compares central tendencies of two independent groups
* **P-Value**: If less than 0.05, reject the null hypothesis

---


## 7. ✅ Conclusions and Recommendations

🔍 **Conclusions**
* A/B testing is an effective approach to evaluate different variations of email elements (e.g., subject lines, design, calls-to-action).
* Key performance metrics such as **Open Rate**, **Click-Through Rate (CTR)**, and **Conversion Rate (CR)** provide valuable insights into recipient behavior and engagement.
* Statistical testing (e.g., Mann-Whitney U Test) helps ensure that observed differences are meaningful and not due to random chance.
* Conducting proper data cleaning and ensuring a balanced distribution of test groups enhances the reliability of results.

💡 **Recommendations**
* Continue using A/B testing regularly to refine email content, layout, and targeting strategies.
* Optimize subject lines to improve open rates, but also consider content and call-to-action clarity for better CTR and CR.
* Segment audiences for more personalized testing and improved performance.
* Ensure consistent tracking of key metrics and establish benchmarks to measure success over time.
* Consider multi-variate testing in the future for a deeper understanding of combined element performance.


## 📬 Contact

Feel free to reach out or connect on [LinkedIn](https://www.linkedin.com/in/daovietcuong/) for any questions or collaborations.

