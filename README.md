# polish
infix to prefix polish notation
#include<iostream>

#include <cstdlib>
using namespace std;

struct Node
{
	int data;
	struct Node* link;
};

struct Node* top;

void push(int data)
{
	struct Node* temp;
	temp = new Node();
	if (!temp)
	{
		cout << "\nHeap Overflow";
		exit(1);
	}

	temp->data = data;
	temp->link = top;
	top = temp;
}

int isEmpty()
{
	return top == NULL;
}

int peek_up()
{
	if (!isEmpty())
		return top->data;
	else
		exit(1);
}

void pop()
{
	struct Node* temp;
	if (top == NULL)
	{
		cout << "\nStack Underflow" << endl;
		exit(1);
	}
	else
	{
		temp = top;
		top = top->link;
		temp->link = NULL;
		free(temp);
	}
}

bool isOperator(char c)
{
	if (c == '+' || c == '-' || c == '*' || c == '/' || c == '^' || c == 's' || c == 'c')
	{
		return true;
	}
	else
	{
		return false;
	}
}

int precedence(char c)
{
	if (c == 's' || c == 'c' || c == 'S' || c == 'C')
		return 4;
	else if (c == '^')
		return 3;
	else if (c == '*' || c == '/')
		return 2;
	else if (c == '+' || c == '-')
		return 1;
	else
		return -1;
}


int string_lenght(char* str)
{
	int len = 0;
	for (int i = 0; i < 20; i++)
	{
		if (str[i] != '\n')	len++;
		else break;
	}
	return len;
}

char* InfixToPostfix(char* infix, int inf_length)
{
	char* postfix = (char*)malloc(inf_length * sizeof(char));
	char* infix_temp = (char*)malloc(inf_length * sizeof(char));
	char* postfix_temp = (char*)malloc(inf_length * sizeof(char));
	*postfix = NULL;
	*infix_temp = NULL;
	*postfix_temp = NULL;
	int j = 0;
	int place = -1;

	for (int i = 0; i <= inf_length; i++) {
		if (((infix[i] == 's') && (infix[i + 1] == 'i') && (infix[i + 2] == 'n')) || ((infix[i] == 'S') && (infix[i + 1] == 'i') && (infix[i + 2] == 'n')) || ((infix[i] == 'c') && (infix[i + 1] == 'o') && (infix[i + 2] == 's')) || ((infix[i] == 'C') && (infix[i + 1] == 'o') && (infix[i + 2] == 's')))
		{
			infix_temp[j] = infix[i];
			i = i + 2;
		}
		else infix_temp[j] = infix[i];
		j++;
	}

	int inf_new_length = string_lenght(infix_temp);

	for (int i = 0; i < inf_new_length; i++)
	{

		if ((infix_temp[i] >= '0') && (infix_temp[i] <= '9'))
		{
			place++;
			postfix[place] = infix_temp[i];
		}
		else if (infix_temp[i] == '(')
		{
			push(infix_temp[i]);
		}
		else if (infix_temp[i] == ')')
		{
			while ((peek_up() != '(') && (!isEmpty()))
			{
				place++;
				char temp = peek_up();
				postfix[place] = temp;
				pop();
			}
			if (peek_up() == '(')
			{
				pop();
			}
		}
		else if (isOperator(infix_temp[i]))
		{
			if (isEmpty())
			{
				push(infix_temp[i]);
			}
			else
			{
				if (precedence(infix_temp[i]) > precedence(peek_up()))
				{
					push(infix_temp[i]);
				}
				else if ((precedence(infix_temp[i]) == precedence(peek_up())) && (infix_temp[i] == '^'))
				{
					push(infix_temp[i]);
				}
				else
				{
					while ((!isEmpty()) && (precedence(infix_temp[i]) <= precedence(peek_up())))
					{
						place++;
						postfix[place] = peek_up();
						pop();
					}
					push(infix_temp[i]);
				}
			}
		}
	}
	while (!isEmpty())
	{
		place++;
		postfix[place] = peek_up();
		pop();

	} 
	postfix[place+1] = '\0';
	int j1 = 0;
	for (int i = 0; i <= inf_length; i++) {
		if ((postfix[j1] == 's') || (postfix[j1] == 'S'))
		{
			postfix_temp[i] = postfix[j1];
			postfix_temp[i + 1] = 'i';
			postfix_temp[i + 2] = 'n';
			i = i + 2;
			
		}
		else if ((postfix[j1] == 'c') || (postfix[j1] == 'C'))
		{
			postfix_temp[i] = postfix[j1];
			postfix_temp[i + 1] = 'o';
			postfix_temp[i + 2] = 's';
			i = i + 2;
			
		}
		else postfix_temp[i] = postfix[j1];
		j1++;
	}
	postfix[j1] = '\0';
	
	return postfix_temp;
}

int main()
{
	setlocale(LC_ALL, "Russian");
	system("chcp 1251");
	system("cls");
	string infix_str = "";
	cout << "Введите выражение в инфиксной форме (без пробелов):" << endl;
	cin >> infix_str;
	infix_str += '\n';
	const int length = infix_str.length();
	char* infix_exp = const_cast<char*>(infix_str.c_str());
	
	int inf_length = string_lenght(infix_exp);

	char* postfix_exp = (char*)malloc(inf_length * sizeof(char));
	*postfix_exp = NULL;
	
	postfix_exp = InfixToPostfix(infix_exp, length);
	cout << endl << "Постфиксная форма: \n" << postfix_exp << "\n";

	return 0;
}
