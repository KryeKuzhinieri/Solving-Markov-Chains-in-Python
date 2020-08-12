# Required Libraries
* Here are all the libraries that we will be using in this project.

```python
import numpy as np #For matrix manipulations
import warnings #To suppress some warnings. 
from numpy.linalg import matrix_power #To take the nth power of a matrix
from discreteMarkovChain import markovChain #Markov Chain library
from pysal.explore.giddy.ergodic import fmpt #Computing Mean Passage Times

warnings.filterwarnings('ignore')

```

# Problem Description:
Suppose each box of Corn Snaps cereal contains one of 5 different Harry Potter trading cards. Moreover, suppose that once you complete a set of cards, you give decide to sell it and restart collecting them again. The probability of getting a card is  1/5. <b>(Winston, 2004)</b>

1.  Represent the game as Markov Chain.
2.	Determine the probability you will complete the set in 10 purchases.
3.	Determine the fraction of the time that you end up with 3 Harry Potter trading cards.
4.	Determine the average number of purchases needed before you complete a set of cards.


# Solution

Let's take the problem step by step. Firstly, let's define our states and our decision variable.
1. <b>X(t)</b>: The unique number of trading cards. 
2. <b>States: (0, 1, 2, 3, 4, 5)</b> <i>(You cannot have nonpositive or more than 5 unique cards since we are given a total of 5 cards in the problem.) </i>

### Question 1:
1. We know that our matrix will be a square matrix of size 6x6. Hence let's create a matrix full of zeros.


```python
#Creating an empty 6 by 6 array
matrix = np.zeros((6, 6))
matrix
```




    array([[0., 0., 0., 0., 0., 0.],
           [0., 0., 0., 0., 0., 0.],
           [0., 0., 0., 0., 0., 0.],
           [0., 0., 0., 0., 0., 0.],
           [0., 0., 0., 0., 0., 0.],
           [0., 0., 0., 0., 0., 0.]])



2. Next, let's add the transition probabilities to the matrix.


```python
#If I have zero cards in my hand, with probability of 1.0, I will have 1 unique card after one purchase.
matrix[0,1]=1.0

#If I have completed a set of cards, I will restart the game with probability of 1.0 and I will have zero cards again.
matrix[5,0]=1.0

#If I have 1 unique card in my hand, then with probabilty of 1/5 I will get the same card. 
#Else with probability of 4/5 I will have 2 unique cards.
i = 1
while i < (len(matrix) - 1):
    matrix[i,i]=i/5
    matrix[i,i+1]=1-(i/5)
    i += 1

print(matrix)
```

    [[0.  1.  0.  0.  0.  0. ]
     [0.  0.2 0.8 0.  0.  0. ]
     [0.  0.  0.4 0.6 0.  0. ]
     [0.  0.  0.  0.6 0.4 0. ]
     [0.  0.  0.  0.  0.8 0.2]
     [1.  0.  0.  0.  0.  0. ]]
    

### Question 2:
1. In order to find the probability of completing the set of cards in 10 purchases we need to compute the nth power.
2. We are looking for N-Step Transition Probabilities:  <img src="https://render.githubusercontent.com/render/math?math=P(0,5)^{10}">


```python
#Taking the 10th power of the matrix. Returing only the probability of completing the set.
stp = matrix_power(matrix, 10)
print("The probability of completing a set of cards in 10 purchases is: ", round(stp[0,5], 3))
```

    The probability of completing a set of cards in 10 purchases is:  0.095
    

### Question 3: 
1. The question wants us to find the long term probabilities. That is, after buying countless boxes of cereals, what is the probability that you will end up with 3 unique cards?
2. We are looking for Steady State Probabilites: <img src="https://render.githubusercontent.com/render/math?math=\pi_3=\sum_{i=1}^{5}\pi_i*p_{i3} \quadfor \quad j=0,1,2,....,5">


```python
#Markov Chain library requires from us to convert the matrix into its own Markov Chain type
markovchainmodel = markovChain(matrix)

#Finding all pi's.
markovchainmodel.computePi('linear')
c = markovchainmodel.pi
#Returing only for 3 boxes.
print("The probability that in the long term I get 3 unique cards is ", round(c[3], 3))
```

    The probability that in the long term I get 3 unique cards is  0.201
    

### Question 4:
1. We are asked to find the number of purchases needed to complete a set of cards; that is, collect 5 cards.
2. We are looking for Mean Passage Times: <img src="https://render.githubusercontent.com/render/math?m_{05}=1+\sum_{k\neq5}P_{0k}*m_{k5}">


```python
#computing the mean first passage times:
fm = fmpt(matrix)
print("The number of times I have to buy a box of cereals to complete a set of cards is ", round(fm[0,5], 3))
```

    The number of times I have to buy a box of cereals to complete a set of cards is  11.417
    

### References
<i>Winston, W. L., & Goldberg, J. B. (2004). Operations research: Applications and algorithms. Belmont, CA: Thomson/Brooks/Cole.</i>

### Happy Cooking!
