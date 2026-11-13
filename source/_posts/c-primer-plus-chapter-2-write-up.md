---
title: "C Primer Plus: Chapter 2 Write-up"
date: 2026-11-12 06:43:43
tags: [C Primer Plus, Learning Notes, C Basics, Debugging]
categories: [C Programming]
lang: en
---


# Introducing C

## Review Questions

1. What are the basic modules of a C program called?
   **Answer:**  Functions.

2. What is a syntax error? Give an example of one in English and one in C.
   **Answer:**  Syntax error means you didn't obey the language rules.

   **English with a syntax error:**

   She have a warm dog.

   **C with a syntax error:**
   <!-- more -->
   
   ```c
   #include [stdio.h] // "[]" is wrong. It should be "<>" instead.
   int main(void)
   {
       printf("This program has a syntax error.");
   }
   ```
   
3. What is a semantic error? Give an example of one in English and one in C.
   **Answer:** A semantic error is the error in meaning .
   **English with a semantic: error**
   Human beings consume gasoline to move.
   **C with a semantic error:**

   ```c
   #include <stdio.h>
   int main(void)
   {
       /* The height of the triangle. */
       float h=2;
       /* The length of the base corresponding to the height. */
       float a=3;
       /* There is a semantic error. The area of any triangle is ah/2 instead of ah. */
       float triangleArea = a * h;
       printf("The are of a triangle with hight %f and base length %f is %f.\n", h, a, triangleArea);
       return 0;
   }
   ```
   
   
   
4. Indiana Sloth has prepared the following program and brought it to you for approval. Please help him out.
   ```c
   include studio.h
   int main{void} /* This prints the number of weeks in a year. /*
   {
   int s
   s:=56;
   print(There are s weeks in a year.);
   return 0;
   ```

   **Answer:** 
   There are several errors in this program.

   1. The preprocessor syntax is  "`#include <headername.h>`".
   2. The header name for standard input and output is" `stdio.h`".
   3. The main function syntax is "`int main(arguments){body}`", where "arguments" could be "void".
   4. The symbols /* and */ enclose comments.
   5. Each assignment or declaration end with a semicolon ";".
   6. The assignment symbol is "=" instead of ":=".
   7. There is 52 weeks in a year instead of 56.
   8. The syntax of information output is "`printf("information");`"
   9. The syntax of  embedding variable in `printf()` is `printf("%d", variable)`, where "%d" and variable are in same type.

   Therefore, the fixed version of this program is following:
   ```c
   #include <stdio.h>
   int main(void) /* This prints the number of weeks in a year. */
   {
       int s;
       s = 52;
       printf("There are %d weeks in a year", s);
       return 0;
   }
   ```

5. Assuming that each of the following examples is part of a complete program, what will each one print?
   a. `printf("Baa Baa Black sheep.");`
       `printf("Have you any wool?\n");`
   b. `printf("Begone!\nO creature of lard!\n");`
   c. `printf("What?\nNo/nfish?\n");`
   d. `int num;`
       `num = 2;`
       `printf("%d + %d = %d", num, num, num + num);`

   **Answer:** 

   ```c
   /* a */
   Baa Baa Black sheep.Have you any wool?
   /* b */
   Begone!
   O creature of lard!
   
   /* c */
   What?
   No/nfish?
   
   /* d */
   2 + 2 = 4
   ```

6. Which of the following are c keywords? **main, int function, char , =**
   **Answer:** int and char.

7. How would you print the values of the variables **words** and **lines** so they appear in the following form:
   ```
   There were 3020 words and 350 lines.
   ```

   Here , 3020 and 350 represent the values of the two variables.
   **Answer:** 

   ```c
   #include <stdio.h>
   int main(void)
   {
       int words, lines;
       words = 3020;
       lines = 350;
       printf("There were %d words and %d lines.\n", words, lines);
   }
   ```

8. Consider the following program:
   ```c
   #include <stdio.h>
   int main(void)
   {
   	int a, b;
   	a = 5;
   	b = 2; /* line 7 */
   	b = a; /* line 8 */
   	a = b; /* line 9 */
   	printf("%d %d\n", b, a);
   	return 0;
   }
   ```

   What is the program state after line 7?Line 8? Line 9?

   **Answer:** 

   ```
   a	b
   5	2 /* after line 7*/
   5	5 /* after line 8*/
   5	5 /* after line 9*/
   ```

   After line 7, a is 5 and b is 2; after line 8 or 9, both a and b are 5.

## Programming Exercises

1. Write a program that uses one `printf()` call to print your first name and last name on one line, uses a second `printf()` call to print your first and last names on two separate lines, and uses a pair of `printf()` calls to print your first and last names on one line. The output should look like this(but using your name):
   ```
   Gustav Mahler	<- First print statement
   Gustav			<- Still the second print statement
   Gustav Mahler	<- Third and fourth print statements
   ```

   **Answer:**

   ```c
   #include <stdio.h>
   
   int main(void)
   {
   	char firstName[] = "Will";
   	char lastName[] = "Lee";
   	printf("%s %s\n", firstName, lastName);
   	printf("%s\n%s\n", firstName, lastName);
   
   	printf("%s ", firstName);
   	printf("%s\n", lastName);
   	return 0;
   }
   
   ```

2. Write a program to print your name and address.
   **Answer:**

   ```c
   #include <stdio.h>
   
   int main(void)
   {
   	char myName[] = "Will Lee";
   	char myAddress[] = "Henan, China";
   
   	printf("%s\n%s\n", myName, myAddress);
   
   	return 0;
   }
   ```

3. Write a program that converts your age in years to days and displays both values.
   **Answer:**

   ```c
   #include <stdio.h>
   
   int main(void)
   {
   	int ageYears = 29;
   	int daysPerYear = 365;
   	int ageDays = ageYears * daysPerYear;
   
   	printf("My ages in years is %d.\nMy ages in days is %d.\n", ageYears, ageDays);
   
   	return 0;
   }
   ```

4. Write a program that produces the following output:
   ```
   For he's a jolly good fellow!
   For he's a jolly good fellow!
   For he's a jolly good fellow!
   Which nobody can deny!
   ```

   Have the program use two user-defined functions in addition to `main()`: one named `jolly()` that prints the "jolly good" message once, and one named `deny()` that prints the final line once.
   **Answer:** 

   ```c
   #include <stdio.h>
   
   int jolly(void);
   int deny(void);
   int main(void)
   {
   	jolly();
   	jolly();
   	jolly();
   	deny();
   	return 0;
   }
   
   int jolly(void)
   {
   	printf("For he's a jolly good fellow!\n");
   	return 0;
   }
   
   int deny(void)
   {
   	printf("Which nobody can deny!\n");
   	return 0;
   }
   ```

5. Write a program that produces the following output:
   ```
   Brazil, Russia, India, China
   India, China,
   Brazil, Russia
   ```

   Have the program use two user-defined functions in addition to `main()`: one named `br()` that prints "Brazil, Russia" once, and one named `ic()` that prints "India, China" once. Let `main()` take care of any additional printing tasks.
   **Answer:**

   ```c
   #include <stdio.h>
   
   int br(void);
   int ic(void);
   
   int main(void)
   {
   	br();
   	printf(", ");
   	ic();
   	printf("\n");
   
   	ic();
   	printf(",\n");
   	br();
   	printf("\n");
   	return 0;
   }
   
   int br(void)
   {
   	printf("Brazil, Russia");
   	return 0;
   }
   
   int ic(void)
   {
   	printf("India, China");
   	return 0;
   }
   ```

6. Write a program that creates an integer variable called `toes`. Have the program set `toes` to `10`. Also have the program calculate what twice `toes` is and what toes squared is. The program should print all three values, identifying them.
      **Answer:** 
      
   ```c
   #include <stdio.h>
   
   int main(void)
   {
   	int toes = 10;
   
   	printf("toes = %d\ntoes×2 = %d\ntoes×toes = %d\n", toes, toes * 2, toes * toes);
   	return 0;
   }
   ```
   
7. Many studies suggest that smiling has benefits . Write a program that produces the following output:
   ```c
   Smile!Smile!Smile!
   Smile!Smile!
   Smile!
   ```

   Have the program define a function that displays the string `Smile!` once, and have the program use the function as often as needed.
   **Answer:**

   ```c
   #include <stdio.h>
   
   int smile(void);
   int main(void)
   {
   	for (int i = 0; i < 3; i++)
   	{
   		for (int j = 0; j < 3; j++)
   		{
   			if (j >= i)
   			{
   				smile();
   			}
   		}
   		printf("\n");
   	}
   	return 0;
   }
   
   int smile(void)
   {
   	printf("Smile!");
   	return 0;
   }
   ```

8. In C, one function can call another. Write a program that calls a function named `one_three()`. This function should display the word `one` on one line, call a second function named `two()`, and then display the word `three` on one line. The  function `two()` should display the word `two` on one line. The `main()` function should display the phrase `starting now:` before calling `one_three()` and display `done!` after calling it. Thus, the output should look like the following:
   ```c
   starting now:
   one
   two
   three
   done!
   ```

   **Answer:**

   ```c
   #include <stdio.h>
   
   int one_three(void);
   int two(void);
   
   int main(void)
   {
   	printf("starting now:\n");
   	one_three();
   	printf("done!\n");
   	return 0;
   }
   
   int one_three(void)
   {
   	printf("one\n");
   	two();
   	printf("three\n");
   	return 0;
   }
   
   int two(void)
   {
   	printf("two\n");
   	return 0;
   }
   ```
