# EXP-04-Implement Apriori algorithm program in python 
```
Name : Bharathganesh S
Reg No : 212222230022
```
## Aim:
To discover strong association rules (e.g., items frequently bought together) in the market basket data by identifying frequent itemsets using the Apriori algorithm .

## Algorithm

Apriori Algorithm
The Apriori algorithm works in an iterative, breadth-first manner:

Generate Frequent 1-Itemsets (L 
1
​
 ): Calculate the support for every single item and keep only those meeting the Minimum Support threshold.



Generate Candidate Itemsets (C 
k
​
 ): Use the frequent (k−1)-itemsets (L 
k−1
​
 ) to generate candidate k-itemsets (C 
k
​
 ) . This is typically done by joining two frequent itemsets from the previous level that share all but one item.


Prune C 
k
​
 : Check the support for all itemsets in C 
k
​
  and eliminate those whose support is below the Minimum Support threshold, resulting in the frequent k-itemsets (L 
k
​
 ) . The Apriori principle states that if an itemset is frequent, all its subsets must also be frequent.


Repeat: Increment k and repeat steps 2 and 3 until no more frequent itemsets can be found.

Generate Rules: Once all frequent itemsets are found, generate association rules (X→Y) from them. For each rule, calculate its Confidence (P(Y∣X)) and Lift .

## Procedure

### Step 1:
Load Data & Set Thresholds: Load the market.csv data and parse transactions; define the Minimum Support (0.3) and Minimum Confidence (0.6) thresholds .
### Step 2:
Generate Frequent 1-Itemsets ($L_1$): Calculate the Support for every single item and keep only those that meet the Minimum Support threshold (0.3) 
### Step 3:
Iteratively Find Frequent Itemsets ($L_k$): Use the frequent itemsets ($L_{k-1}$) to generate candidate itemsets ($C_k$); prune $C_k$ based on Minimum Support to find the next level of frequent itemsets ($L_k$) 
### Step 4:
Identify All Frequent Itemsets: Repeat the generation and pruning process until no more frequent itemsets can be found (i.e., when $k$ cannot be incremented further) 
### Step 5:
Generate Candidate Association Rules: For every frequent itemset $X \cup Y$, systematically generate all possible rules of the form $X \rightarrow Y$
### Step 6:
Filter Strong Rules by Confidence: Calculate the Confidence ($\text{Support}(X \cup Y) / \text{Support}(X)$) for each rule and keep only those meeting the Minimum Confidence threshold (0.6) 6.
### Step 7:
Identify Strongest Rule: Calculate the Lift for all strong rules and select the rule with the highest Lift value as the strongest association rule .

## Program:
```py
import pandas as pd
from itertools import combinations
# Step 1: Load Dataset
df = pd.read_csv('market.csv')
transactions = df['Items'].apply(lambda x: x.split(','))
print(" 🧾  Sample Transactions:")
for i, t in enumerate(transactions[:5], 1):
    print(f"Transaction {i}: {t}")
# Step 2: Define Minimum Support and Confidence
min_support = 0.3   # 30%
min_confidence = 0.6
# Step 3: Generate All Possible Items
all_items = sorted({item for trans in transactions for item in trans})
# Step 4: Function to Calculate Support
def calculate_support(itemset):
    count = sum(1 for trans in transactions if itemset.issubset(set(trans)))
    return count / len(transactions)
# Step 5: Generate Frequent Itemsets
def apriori(transactions, min_support):
    freq_itemsets = []
    k = 1
    current_itemsets = [frozenset([item]) for item in all_items]
    while current_itemsets:
        valid_itemsets = []
        for itemset in current_itemsets:
            support = calculate_support(itemset)
            if support >= min_support:
                valid_itemsets.append((itemset, support))
                print(f"Frequent Itemset: {set(itemset)}, Support: {round(support,2)}")
        freq_itemsets.extend(valid_itemsets)
        # Generate next-level candidates
        next_items = []
        for i in range(len(valid_itemsets)):
            for j in range(i+1, len(valid_itemsets)):
                union = valid_itemsets[i][0] | valid_itemsets[j][0]
                if len(union) == k + 1 and union not in next_items:
                    next_items.append(union)
        current_itemsets = next_items
        k += 1
    return freq_itemsets
# Step 6: Run Apriori
print("\nFrequent Itemsets (Support ≥ 0.3):")
frequent_itemsets = apriori(transactions, min_support)
# Step 7: Generate Association Rules
print("\n Association Rules (Confidence ≥ 0.6):")
rules = []
for itemset, support in frequent_itemsets:
    if len(itemset) > 1:
        for i in range(1, len(itemset)):
            for antecedent in combinations(itemset, i):
                antecedent = set(antecedent)
                consequent = itemset - antecedent
                if consequent:
                    support_X = calculate_support(antecedent)
                    support_XY = calculate_support(itemset)
                    confidence = support_XY / support_X
                    lift = confidence / calculate_support(consequent)
                    if confidence >= min_confidence:
                        rules.append((antecedent, consequent, support_XY, confidence, lift))
                        print(f"Rule: {antecedent} → {consequent} | "
                              f"Support: {round(support_XY,2)} | "
                              f"Confidence: {round(confidence,2)} | "
                              f"Lift: {round(lift,2)}")
# Step 8: Identify the Strongest Rule
if rules:
    strongest_rule = max(rules, key=lambda x: x[4])  # highest lift
    print("\nStrongest Association Rule:")
    print(f"{strongest_rule[0]} → {strongest_rule[1]}")
    print(f"Support: {round(strongest_rule[2],2)}, "
          f"Confidence: {round(strongest_rule[3],2)}, "
          f"Lift: {round(strongest_rule[4],2)}")
# Step 9: Conclusion
    print("\nConclusion:")
    print(f"The pair {strongest_rule[0]} → {strongest_rule[1]} is the strongest association rule.")
    print("It indicates that when customers buy", list(strongest_rule[0])[0],
          "they are highly likely to buy", list(strongest_rule[1])[0], "together.")
else:
    print("\nNo strong rules found with the given thresholds.")
[cite_start]``` [cite: 23-100]
```

## Output:

<img width="1176" height="157" alt="image" src="https://github.com/user-attachments/assets/d50eb5ba-228e-4fac-b7ea-9f2f654b9fee" />


<img width="1206" height="393" alt="image" src="https://github.com/user-attachments/assets/35008555-8ee0-4edc-911d-479fbad10480" />


<img width="1230" height="389" alt="image" src="https://github.com/user-attachments/assets/fd246d84-80cb-47c0-acb2-2fbabd9ee492" />


<img width="1134" height="203" alt="image" src="https://github.com/user-attachments/assets/a9b14283-67a8-4f70-bcba-19d1fd4bfdb4" />

## Result:

The provided code executes the Apriori algorithm and prints the frequent itemsets, the strong association rules, and the strongest rule.

