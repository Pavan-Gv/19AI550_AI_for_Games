# Ex.No: 10  Implementation of Negamax Search
### DATE:                           
### Slot: 4H2-1
### Name: G Venkata Pavan Kumar
### REGISTER NUMBER : 212221240013
### AIM: 
Write a Nega-max search algorithm to find the root value of Player from the  graph.
### Algorithm:
1. Start the program
2. import the math package
3. Specify the score value of leaf nodes and find the depth of binary tree from leaf nodes.
4. Define the minimax function
5. If maximum depth is reached then get the score value of leaf node.
6. Every level, score is evaluated by negamax function 
8. Call the negamax function  and print the optimum value of Max player.
9. Stop the program. 

### Program:

```python
import math

def negamax(curDepth, nodeIndex, scores, targetDepth):
    # Base case: target depth reached
    if curDepth == targetDepth:
        return scores[nodeIndex]

    # Negamax assumes max turn is represented by positive values
    value1 = negamax(curDepth + 1, nodeIndex * 2, scores, targetDepth)
    value2 = negamax(curDepth + 1, nodeIndex * 2 + 1, scores, targetDepth)

    return max(-value1, -value2)  # Flip the sign for the other player's turn

# Driver code
scores = [3, 5, 2, 9, 12, 5, 23, 20]
treeDepth = math.log(len(scores), 2)  # Calculate depth of node, log(8, base 2) = 3
print("The optimal value is: ", end="")
print(negamax(0, 0, scores, int(treeDepth)))
```
### Output:


![Screenshot 2024-11-15 103459](https://github.com/user-attachments/assets/21b9be1d-09c1-4536-8288-b3ff3f00c98d)


### Result:
Thus the root value of player was found using negamax search.
