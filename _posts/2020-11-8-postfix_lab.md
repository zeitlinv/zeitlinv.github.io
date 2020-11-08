---
layout: post
title: Postfix Lab
---

#### Average of all evaluated postfix expressions: 

591.91

#### Explain how a stack is used to evaluate postfix notation.

Postfix notation is a list of numbers and operators, where, in this case, the numbers are integers and the operators are ```"+"```, ```"-"```, or ```"*"```. Each operator applies to the two numbers directly before it in the expression, and then the result of the evaluated two numbers replaces them in the list. A stack has last-in first-out access, so it is useful for accessing the most recent two numbers first, which are the last numbers in the list before an operator. The postfix expression is iterated through as a list, and if the element is a number, then it is pushed onto the stack, and the list moves on to the next element. If the element is an operator, then the two last values on the stack will be added, subtracted, or multiplied, depending on what type the operator element is. The resultant value is then pushed back onto the stack, maintaining its place in the order of the expression. Once an operator is "used," the next element in the list is checked. Essentially, the stack contains the values that must be evaluated, and the rest of the list contains the operators to be used on that stack and the raw numbers that have not yet been pushed. When the entire postfix expression (list) has been iterated through, there will be only one value left in the stack, since all of the other values would have been evaluated using the operators, assuming the postfix expression was written correctly. This final value will be the result of the postfix expression.

#### Process

For this lab, my objective was to evaluate each of the postfix notation expressions from a dataset containing 100 postfix expressions, then find the average of the evaluated values. First, I attempted to create an evaluate() function, which would take an expression given in postfix notation as a list and return what it evaluated to. 

```python
def evaluate(str_list):
    str_stack = Stack([])
    for i in str_list:
        if check_if_operator(i):
            calculated_value = complete_operation(str_stack.pop(),str_stack.pop(),i)
            str_stack.push(calculated_value)
        else:
            str_stack.push(i)
    final_value = str_stack.peek()
    return float(final_value)
```

The function first creates an empty stack, which will contain the numbers in the list as they are evaluated by the operators. The for loop iterates through all of the elements of the postfix expression list, pushing them onto the stack if they are numbers, or, if the element is an operator, an operation of that type is used to evaluate the sum, product, or difference of the top two numbers of the stack. In order to find this sum, product, or difference, the last two values of the stack (the most recently added values) are popped, and then the sum, product, or difference is pushed back onto the stack, until only one value remains in the stack. Since there were many cases to check involving identifying and using the operators, I realized that my function was becoming difficult to follow. I decided to create two separate functions to handle the operators. check_if_operator() takes a string (the element of the list) and returns true if that element has the value of ```“+”```, ```“-“```, or ```“*”```, which are the only operations used in the dataset of postfix expressions.  complete_operation() takes three strings, the first two of which are the two elements popped from the stack to be evaluated, and the last of which is the operator to be used (only ```“+”```, ```“-“```, or ```“*”```). It returns the result of the operation. The function also converts the elements from the stack into ints, as math cannot be done on numbers as strings. One issue I ran into with this method was that the two numbers were added, multiplied, or subtracted in the wrong order. In the case of subtraction, this would lead to incorrect results. Postfix expressions are read left to right, but stacks have their last element removed first. The stack is already in the same order as the original postfix expression list, so when the last element is popped and added, multiplied, or subtracted by the next last element, the elements are actually evaluated in reverse order from the original expression. In solving this problem, I switched the order of the first two parameters in the complete_operation() function such that the first parameter (the first popped element) would be the second number used in multiplication, subtraction, and addition, and the second parameter (the second popped element) would be the first number. After the entire list is iterated though, only one number will remain in the stack, since the operators would have combined all of the values into a single result. This value is then returned as the evaluated version of the postfix expression. I made this returned value into a float, ensuring that the average I later calculated would be in the correct float format.

Next, I had to read the postfix expressions from the dataset, which was contained in a .csv file, and evaluate each one. 

```python
with open("calculate_me.csv", "r") as f:
    data = csv.reader(f) 
    for row in data: 
        num_list.append(evaluate(row))
```

After the data is read, the rows of the data are iterated through. The each row had a postfix expression in the format of a list, so I could immediately use the evaluate() function on each row to obtain the result of the expression. I wanted to have all of the results in a single list to more easily calculate the average, so I created an empty list, num_list. As each row is iterated through, the evaluated version of that row is appended to the list. When all of the data is processed, I would have a list containing all of the evaluated versions of the 100 postfix expressions in the dataset. I created an avg() function to find the average of this final list. The function takes a list as a parameter and returns the average of all of the numerical values in the list. It uses sum() to find the sum of all elements in the list, then divides that sum by the length of the list.
