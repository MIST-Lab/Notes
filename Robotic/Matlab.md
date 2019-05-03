





# Matlab

"%" represents the comments

";" prevents output from being displayed



![image-20190404155641147](/Users/junchengzhu/Library/Application Support/typora-user-images/image-20190404155641147.png)

Use plot(x,y) 

and array x=[10,3,5,7] y=[5,3,2,4]

to draw a graph( a line made up by points ) with the given data



![image-20190404215108621](/Users/junchengzhu/Library/Application Support/typora-user-images/image-20190404215108621.png)

While working on vectors

"," are used to separate row values

";" are used to separate column values 

x= startValue:spacing:endValue;    — is used to represent a vector start with "startValue" and end with "endValue" spacing is the "spacing"



![image-20190404215626091](/Users/junchengzhu/Library/Application Support/typora-user-images/image-20190404215626091.png)

if spacing is "1" then the middle parameter can be omit



the following pic shows how to represent a column vector

![image-20190404215733354](/Users/junchengzhu/Library/Application Support/typora-user-images/image-20190404215733354.png)

Function  linspace() can be used to get a few sample points from a sequence(range)

![image-20190404220354797](/Users/junchengzhu/Library/Application Support/typora-user-images/image-20190404220354797.png)



Matrices

![image-20190404220548058](/Users/junchengzhu/Library/Application Support/typora-user-images/image-20190404220548058.png)

Using length() function to measure a array while using a size() function to measure a matrices

![image-20190405144324067](/Users/junchengzhu/Library/Application Support/typora-user-images/image-20190405144324067.png)

Access Elements of a Vector

![image-20190405144723633](/Users/junchengzhu/Library/Application Support/typora-user-images/image-20190405144723633.png)



![image-20190405144853463](/Users/junchengzhu/Library/Application Support/typora-user-images/image-20190405144853463.png)



Matrices Operations

![image-20190405145042175](/Users/junchengzhu/Library/Application Support/typora-user-images/image-20190405145042175.png)



![image-20190405145316500](/Users/junchengzhu/Library/Application Support/typora-user-images/image-20190405145316500.png)

![image-20190405145503732](/Users/junchengzhu/Library/Application Support/typora-user-images/image-20190405145503732.png)

Using conditions to pick data from a data set (Matrices)

![image-20190405145708239](/Users/junchengzhu/Library/Application Support/typora-user-images/image-20190405145708239.png)



![image-20190405150459208](/Users/junchengzhu/Library/Application Support/typora-user-images/image-20190405150459208.png)



For Loop:

![image-20190405151202731](/Users/junchengzhu/Library/Application Support/typora-user-images/image-20190405151202731.png)



Logical Variables && Operators 



![image-20190405151414108](/Users/junchengzhu/Library/Application Support/typora-user-images/image-20190405151414108.png)

![image-20190405151542899](/Users/junchengzhu/Library/Application Support/typora-user-images/image-20190405151542899.png)



### if else statement

```matlab


if hours <= 1
	fee = 0;
elseif hours > 1 & hours < 7
	fee = 35;
else
	fee = 5*(hours-1);
end
```



 

Custom function

![image-20190405152207023](/Users/junchengzhu/Library/Application Support/typora-user-images/image-20190405152207023.png)



![image-20190405152255637](/Users/junchengzhu/Library/Application Support/typora-user-images/image-20190405152255637.png)



Using " \ " to divided two matrices and use " x = A\B " this to equation "Ax = B"

 

