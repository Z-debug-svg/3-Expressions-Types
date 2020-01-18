# 3-Expressions-Types
Prefix / Infix / Postfix Expressions


Infix expression:The expression of the form a op b. When an operator is in-between every pair of operands.

Postfix expression:The expression of the form a b op. When an operator is followed for every pair of operands.

(a 、b : operand   op : operator)

# Why postfix representation of the expression?
The compiler scans the expression either from left to right or from right to left.
Consider the below expression: a op1 b op2 c op3 d
If op1 = +, op2 = *, op3 = +

The compiler first scans the expression to evaluate the expression b * c, then again scan the expression to add a to it. The result is then added to d after another scan.

The repeated scanning makes it very in-efficient. It is better to convert the expression to postfix(or prefix) form before evaluation.

The corresponding expression in postfix form is: abc*+d+.

# The postfix expressions can be evaluated easily using a stack. We will cover postfix expression evaluation in a separate post.

# Algorithm
1. Scan the infix expression from left to right.
2. If the scanned character is an operand, output it.
3. Else,
…..3.1 If the precedence of the scanned operator is greater than the precedence of the operator in the stack(or the stack is empty or the stack contains a ‘(‘ ), push it.
…..3.2 Else, Pop all the operators from the stack which are greater than or equal to in precedence than that of the scanned operator. After doing that Push the scanned operator to the stack. (If you encounter parenthesis while popping then stop there and push the scanned operator in the stack.)
4. If the scanned character is an ‘(‘, push it to the stack.
5. If the scanned character is an ‘)’, pop the stack and and output it until a ‘(‘ is encountered, and discard both the parenthesis.
6. Repeat steps 2-6 until infix expression is scanned.
7. Print the output
8. Pop and output from the stack until it is not empty.

# Following is the implementation of the above algorithm ：
// C program to convert infix expression to postfix  
#include <stdio.h> 
#include <string.h> 
#include <stdlib.h> 
  
// Stack type 
struct Stack 
{ 
    int top; 
    unsigned capacity; 
    int* array; 
}; 
  
// Stack Operations 
struct Stack* createStack( unsigned capacity ) 
{ 
    struct Stack* stack = (struct Stack*) malloc(sizeof(struct Stack)); 
  
    if (!stack)  
        return NULL; 
  
    stack->top = -1; 
    stack->capacity = capacity; 
  
    stack->array = (int*) malloc(stack->capacity * sizeof(int)); 
  
    if (!stack->array) 
        return NULL; 
    return stack; 
} 
int isEmpty(struct Stack* stack) 
{ 
    return stack->top == -1 ; 
} 
char peek(struct Stack* stack) 
{ 
    return stack->array[stack->top]; 
} 
char pop(struct Stack* stack) 
{ 
    if (!isEmpty(stack)) 
        return stack->array[stack->top--] ; 
    return '$'; 
} 
void push(struct Stack* stack, char op) 
{ 
    stack->array[++stack->top] = op; 
} 
  
  
// A utility function to check if the given character is operand 
int isOperand(char ch) 
{ 
    return (ch >= 'a' && ch <= 'z') || (ch >= 'A' && ch <= 'Z'); 
} 
  
// A utility function to return precedence of a given operator 
// Higher returned value means higher precedence 
int Prec(char ch) 
{ 
    switch (ch) 
    { 
    case '+': 
    case '-': 
        return 1; 
  
    case '*': 
    case '/': 
        return 2; 
  
    case '^': 
        return 3; 
    } 
    return -1; 
} 
  
  
// The main function that converts given infix expression 
// to postfix expression.  
int infixToPostfix(char* exp) 
{ 
    int i, k; 
  
    // Create a stack of capacity equal to expression size  
    struct Stack* stack = createStack(strlen(exp)); 
    if(!stack) // See if stack was created successfully  
        return -1 ; 
  
    for (i = 0, k = -1; exp[i]; ++i) 
    { 
        // If the scanned character is an operand, add it to output. 
        if (isOperand(exp[i])) 
            exp[++k] = exp[i]; 
          
        // If the scanned character is an ‘(‘, push it to the stack. 
        else if (exp[i] == '(') 
            push(stack, exp[i]); 
          
        // If the scanned character is an ‘)’, pop and output from the stack  
        // until an ‘(‘ is encountered. 
        else if (exp[i] == ')') 
        { 
            while (!isEmpty(stack) && peek(stack) != '(') 
                exp[++k] = pop(stack); 
            if (!isEmpty(stack) && peek(stack) != '(') 
                return -1; // invalid expression              
            else
                pop(stack); 
        } 
        else // an operator is encountered 
        { 
            while (!isEmpty(stack) && Prec(exp[i]) <= Prec(peek(stack))) 
                exp[++k] = pop(stack); 
            push(stack, exp[i]); 
        } 
  
    } 
  
    // pop all the operators from the stack 
    while (!isEmpty(stack)) 
        exp[++k] = pop(stack ); 
  
    exp[++k] = '\0'; 
    printf( "%s", exp ); 
} 
  
// Driver program to test above functions 
int main() 
{ 
    char exp[] = "a+b*(c^d-e)^(f+g*h)-i"; 
    infixToPostfix(exp); 
    return 0; 
} 

Output:
abcd^e-fgh*+^*+i-


# The postfix expressions can be evaluated easily using a stack ：
The expressions written in postfix form are evaluated faster compared to infix notation as parenthesis are not required in postfix.
Following is algorithm for evaluation postfix expressions.
1) Create a stack to store operands (or values).
2) Scan the given expression and do following for every scanned element.
…..a) If the element is a number, push it into the stack
…..b) If the element is a operator, pop operands for the operator from stack. Evaluate the operator and push the result back to the stack
3) When the expression is ended, the number in the stack is the final answer

Example:
Let the given expression be “2 3 1 * + 9 -“. We scan all elements one by one.
1) Scan ‘2’, it’s a number, so push it to stack. Stack contains ‘2’
2) Scan ‘3’, again a number, push it to stack, stack now contains ‘2 3’ (from bottom to top)
3) Scan ‘1’, again a number, push it to stack, stack now contains ‘2 3 1’
4) Scan ‘*’, it’s an operator, pop two operands from stack, apply the * operator on operands, we get 3*1 which results in 3. We push the result ‘3’ to stack. Stack now becomes ‘2 3’.
5) Scan ‘+’, it’s an operator, pop two operands from stack, apply the + operator on operands, we get 3 + 2 which results in 5. We push the result ‘5’ to stack. Stack now becomes ‘5’.
6) Scan ‘9’, it’s a number, we push it to the stack. Stack now becomes ‘5 9’.
7) Scan ‘-‘, it’s an operator, pop two operands from stack, apply the – operator on operands, we get 5 – 9 which results in -4. We push the result ‘-4’ to stack. Stack now becomes ‘-4’.
8) There are no more elements to scan, we return the top element from stack (which is the only element left in stack).

// C program to evaluate value of a postfix expression 
#include <stdio.h> 
#include <string.h> 
#include <ctype.h> 
#include <stdlib.h> 
  
// Stack type 
struct Stack 
{ 
    int top; 
    unsigned capacity; 
    int* array; 
}; 
  
// Stack Operations 
struct Stack* createStack( unsigned capacity ) 
{ 
    struct Stack* stack = (struct Stack*) malloc(sizeof(struct Stack)); 
  
    if (!stack) return NULL; 
  
    stack->top = -1; 
    stack->capacity = capacity; 
    stack->array = (int*) malloc(stack->capacity * sizeof(int)); 
  
    if (!stack->array) return NULL; 
  
    return stack; 
} 
  
int isEmpty(struct Stack* stack) 
{ 
    return stack->top == -1 ; 
} 
  
char peek(struct Stack* stack) 
{ 
    return stack->array[stack->top]; 
} 
  
char pop(struct Stack* stack) 
{ 
    if (!isEmpty(stack)) 
        return stack->array[stack->top--] ; 
    return '$'; 
} 
  
void push(struct Stack* stack, char op) 
{ 
    stack->array[++stack->top] = op; 
} 
  
  
// The main function that returns value of a given postfix expression 
int evaluatePostfix(char* exp) 
{ 
    // Create a stack of capacity equal to expression size 
    struct Stack* stack = createStack(strlen(exp)); 
    int i; 
  
    // See if stack was created successfully 
    if (!stack) return -1; 
  
    // Scan all characters one by one 
    for (i = 0; exp[i]; ++i) 
    { 
        // If the scanned character is an operand (number here), 
        // push it to the stack. 
        if (isdigit(exp[i])) 
            push(stack, exp[i] - '0'); 
  
        // If the scanned character is an operator, pop two 
        // elements from stack apply the operator 
        else
        { 
            int val1 = pop(stack); 
            int val2 = pop(stack); 
            switch (exp[i]) 
            { 
            case '+': push(stack, val2 + val1); break; 
            case '-': push(stack, val2 - val1); break; 
            case '*': push(stack, val2 * val1); break; 
            case '/': push(stack, val2/val1); break; 
            } 
        } 
    } 
    return pop(stack); 
} 
  
// Driver program to test above functions 
int main() 
{ 
    char exp[] = "231*+9-"; 
    printf ("postfix evaluation: %d", evaluatePostfix(exp)); 
    return 0; 
} 

Output:
postfix evaluation: -4
Time complexity of evaluation algorithm is O(n) where n is number of characters in input expression.

There are following limitations of above implementation.
1) It supports only 4 binary operators ‘+’, ‘*’, ‘-‘ and ‘/’. It can be extended for more operators by adding more switch cases.
2) The allowed operands are only single digit operands. The program can be extended for multiple digits by adding a separator like space between all elements (operators and operands) of given expression.

// C program to evaluate value of a postfix 
// expression having multiple digit operands 
#include <stdio.h> 
#include <string.h> 
#include <ctype.h> 
#include <stdlib.h> 
  
// Stack type 
struct Stack 
{ 
    int top; 
    unsigned capacity; 
    int* array; 
}; 
  
// Stack Operations 
struct Stack* createStack( unsigned capacity ) 
{ 
    struct Stack* stack = (struct Stack*) malloc(sizeof(struct Stack)); 
  
    if (!stack) return NULL; 
  
    stack->top = -1; 
    stack->capacity = capacity; 
    stack->array = (int*) malloc(stack->capacity * sizeof(int)); 
  
    if (!stack->array) return NULL; 
  
    return stack; 
} 
  
int isEmpty(struct Stack* stack) 
{ 
    return stack->top == -1 ; 
} 
  
int peek(struct Stack* stack) 
{ 
    return stack->array[stack->top]; 
} 
  
int pop(struct Stack* stack) 
{ 
    if (!isEmpty(stack)) 
        return stack->array[stack->top--] ; 
    return '$'; 
} 
  
void push(struct Stack* stack,int op) 
{ 
    stack->array[++stack->top] = op; 
} 
  
  
// The main function that returns value  
// of a given postfix expression 
int evaluatePostfix(char* exp) 
{ 
    // Create a stack of capacity equal to expression size 
    struct Stack* stack = createStack(strlen(exp)); 
    int i; 
  
    // See if stack was created successfully 
    if (!stack) return -1; 
  
    // Scan all characters one by one 
    for (i = 0; exp[i]; ++i) 
    { 
        //if the character is blank space then continue 
        if(exp[i]==' ')continue; 
          
        // If the scanned character is an  
        // operand (number here),extract the full number 
        // Push it to the stack. 
        else if (isdigit(exp[i])) 
        { 
            int num=0; 
              
            //extract full number 
            while(isdigit(exp[i]))  
            { 
            num=num*10 + (int)(exp[i]-'0'); 
                i++; 
            } 
            i--; 
              
            //push the element in the stack 
            push(stack,num); 
        } 
          
        // If the scanned character is an operator, pop two 
        // elements from stack apply the operator 
        else
        { 
            int val1 = pop(stack); 
            int val2 = pop(stack); 
              
            switch (exp[i]) 
            { 
            case '+': push(stack, val2 + val1); break; 
            case '-': push(stack, val2 - val1); break; 
            case '*': push(stack, val2 * val1); break; 
            case '/': push(stack, val2/val1); break; 
              
            } 
        } 
    } 
    return pop(stack); 
} 
  
// Driver program to test above functions 
int main() 
{ 
    char exp[] = "100 200 + 2 / 5 * 7 +"; 
    printf ("%d", evaluatePostfix(exp)); 
    return 0; 
} 
  
// This code is contributed by Arnab Kundu 
Output :
757


# Postfix to Infix Algorithm ：
Postfix notation, also known as reverse Polish notation, is a syntax for mathematical expressions in which the mathematical operator is always placed after the operands. Though postfix expressions are easily and efficiently evaluated by computers, they can be difficult for humans to read. Complex expressions using standard parenthesized infix notation are often more readable than the corresponding postfix expressions. Consequently, we would sometimes like to allow end users to work with infix notation and then convert it to postfix notation for computer processing. Sometimes, moreover, expressions are stored or generated in postfix, and we would like to convert them to infix for the purpose of reading and editing

Examples:
Input : abc++
Output : (a + (b + c))
Input  : ab*c+
Output : ((a*b)+c)

# Algorithm ： 
1.While there are input symbol left
…1.1 Read the next symbol from the input.
2.If the symbol is an operand
…2.1 Push it onto the stack.
3.Otherwise,
…3.1 the symbol is an operator.
…3.2 Pop the top 2 values from the stack.
…3.3 Put the operator, with the values as arguments and form a string.
…3.4 Push the resulted string back to stack.
4.If there is only one value in the stack
…4.1 That value in the stack is the desired infix string.

Below is the implementation of above approach:
// CPP program to find infix for 
// a given postfix. 
#include <bits/stdc++.h> 
using namespace std; 
  
bool isOperand(char x) 
{ 
   return (x >= 'a' && x <= 'z') || 
          (x >= 'A' && x <= 'Z'); 
} 
  
// Get Infix for a given postfix 
// expression 
string getInfix(string exp) 
{ 
    stack<string> s; 
  
    for (int i=0; exp[i]!='\0'; i++) 
    { 
        // Push operands 
        if (isOperand(exp[i])) 
        { 
           string op(1, exp[i]); 
           s.push(op); 
        } 
  
        // We assume that input is 
        // a valid postfix and expect 
        // an operator. 
        else
        { 
            string op1 = s.top(); 
            s.pop(); 
            string op2 = s.top(); 
            s.pop(); 
            s.push("(" + op2 + exp[i] + 
                   op1 + ")"); 
        } 
    } 
  
    // There must be a single element 
    // in stack now which is the required 
    // infix. 
    return s.top(); 
} 
  
// Driver code 
int main() 
{ 
    string exp = "ab*c+"; 
    cout << getInfix(exp); 
    return 0; 
} 

Output:
((a*b)+c)


# Expression Tree Algorithm ：
Expression tree is a binary tree in which each internal node corresponds to operator and each leaf node corresponds to operand so for example expression tree for 3 + ((5+9)*2) would be:
Inorder traversal of expression tree produces infix version of given postfix expression (same with preorder traversal it gives prefix expression)
Evaluating the expression represented by expression tree:

Let t be the expression tree
If  t is not null then
      If t.value is operand then  
                Return  t.value
      A = solve(t.left)
      B = solve(t.right)
 
      // calculate applies operator 't.value' 
      // on A and B, and returns value
      Return calculate(A, B, t.value)  
      
# Construction of Expression Tree:
Now For constructing expression tree we use a stack. We loop through input expression and do following for every character.
1) If character is operand push that into stack
2) If character is operator pop two values from stack make them its child and push current node again.
At the end only element of stack will be root of expression tree.

// C++ program for expression tree 
#include<bits/stdc++.h> 
using namespace std; 
  
// An expression tree node 
struct et 
{ 
    char value; 
    et* left, *right; 
}; 
  
// A utility function to check if 'c' 
// is an operator 
bool isOperator(char c) 
{ 
    if (c == '+' || c == '-' || 
            c == '*' || c == '/' || 
            c == '^') 
        return true; 
    return false; 
} 
  
// Utility function to do inorder traversal 
void inorder(et *t) 
{ 
    if(t) 
    { 
        inorder(t->left); 
        printf("%c ", t->value); 
        inorder(t->right); 
    } 
} 
  
// A utility function to create a new node 
et* newNode(int v) 
{ 
    et *temp = new et; 
    temp->left = temp->right = NULL; 
    temp->value = v; 
    return temp; 
}; 
  
// Returns root of constructed tree for given 
// postfix expression 
et* constructTree(char postfix[]) 
{ 
    stack<et *> st; 
    et *t, *t1, *t2; 
  
    // Traverse through every character of 
    // input expression 
    for (int i=0; i<strlen(postfix); i++) 
    { 
        // If operand, simply push into stack 
        if (!isOperator(postfix[i])) 
        { 
            t = newNode(postfix[i]); 
            st.push(t); 
        } 
        else // operator 
        { 
            t = newNode(postfix[i]); 
  
            // Pop two top nodes 
            t1 = st.top(); // Store top 
            st.pop();      // Remove top 
            t2 = st.top(); 
            st.pop(); 
  
            //  make them children 
            t->right = t1; 
            t->left = t2; 
  
            // Add this subexpression to stack 
            st.push(t); 
        } 
    } 
  
    //  only element will be root of expression 
    // tree 
    t = st.top(); 
    st.pop(); 
  
    return t; 
} 
  
// Driver program to test above 
int main() 
{ 
    char postfix[] = "ab+ef*g*-"; 
    et* r = constructTree(postfix); 
    printf("infix expression is \n"); 
    inorder(r); 
    return 0; 
} 

Output:
infix expression is
a + b - e * f * g
