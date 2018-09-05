---
layout: post
title:  "Pointers as return values in C"
date:   2018-09-05 10:00:00 +0500
categories: blog post
---

Hello. It has been a while.<br>
In this post I will try to write something interesting about pointers in C programming language.
First, let's have a look at this code:

{% highlight c++ %}
#include <stdio.h>

void aimless_func()
{
    int tmp = 11;
    printf("aimless print: %d\n", tmp);
}

int* square(int num)
{
    int sqr = num * num;
    printf("square(): square of %d is %d\n", num, sqr);
    return &sqr;
}

int main()
{
    int num = 9;
    int *ret_val;

    ret_val = square(num);
    aimless_func();
    printf("main(): square of %d is %d\n", num, *ret_val);
    
    return 0;
}
{% endhighlight %}

## What is the problem?
The point of the above code is to show that in the function `main` it will print correct result, but in the `square` it will not.
Because, the variable `sqr` in function `square` is stored in **stack memory**, and when function `square` ends all the memory it uses becomes available automatically.
And when function `aimless_func` starts, the variable `tmp` will override `sqr`. Simple as that.

## How to fix it?
Then how to properly write functions that return pointers? Well, use dynamic memory. In the above example keep `sqr` in **heap memory**:
{% highlight c++ %}
int* square(int num)
{
    int *sqr = (int*)malloc(sizeof(int));
    *sqr = num * num;
    printf("square(): square of %d is %d\n", num, *sqr);
    return sqr;
}
{% endhighlight %}
Also, keep in mind that you can return the address of a function argument, like this:

{% highlight c++ %}
int* get_max(int *m, int *n)
{
    if (*m > *n)
        return m;
    return n;
}
{% endhighlight %}

## Where to use it?
What are the use cases of functions that return pointers? So, if you look into [this](http://www.zentut.com/c-tutorial/c-linked-list/) implementation of Linked List, you can see some nice examples of functions that return pointers. For example, when you write a function that creates a linked list header, it will return a pointer to the head. Or when you write a function that tries to get the head of a linked list, it will also return a pointer to the head.
{% highlight c++ %}
node* create(int data, node* next)
{
    node* new_node = (node*)malloc(sizeof(node));
    if(new_node == NULL) {
        printf("Error creating a new node.\n");
        exit(0);
    }
    new_node->data = data;
    new_node->next = next;
 
    return new_node;
}
{% endhighlight %}

That's all I have for now. Have a nice day!