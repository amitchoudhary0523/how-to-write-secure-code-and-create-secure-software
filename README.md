
-------------------------------------------------------------------------------
How to write secure code (and create secure software) and why everyone should
write secure code.
-------------------------------------------------------------------------------

Author: Amit Choudhary
Email: amitchoudhary0523 AT gmail DOT com

These days, lots of software is getting hacked, and software hacking has become
quite common, so everyone should write secure code so that their software
doesn't get hacked.

"Insecure code/software" gets hacked, and this leads to the loss of millions of
dollars, and also exposes users' private data, like - SSNs, mobile numbers, etc.
So, it is "necessary" to write secure code these days.
 
Below, I have given the main points of writing secure code.

********************
I can write secure code, and no one can hack my code unless the
language/OS/hardware has some issues. You can challenge me on this.

Ultimately, a software is made up of functions/methods. Although scripting
languages allow writing code outside of functions/methods but we will skip that
part because in scripting languages also, it is possible to write all code
inside functions/methods.

Now, the main point is that if all the functions/methods of a software are
secure, then the whole software is secure.

So, you should focus only on your functions/methods to make the whole software
secure. You need not bother about how the software is going to fend off hacking
attempts, etc. If all of your functions/methods are secure, then your software
becomes secure automatically.

Below, I have given the main points of writing secure functions/methods.
********************

From here, in all the following text, the word function will mean both function
and method.

I am listing the main points of writing secure functions below. These points
apply to almost all computer languages.

1. The first point is that all the arguments of your function should always be
   checked whether they are in the allowed range or not, even if your function
   is called from other trusted functions and even if your function is
   private/protected or static. The value of a function argument should not be
   unbounded; it should be within a bounded range. All function arguments should
   have a MIN value and a MAX value. You should try to choose MIN and MAX values
   such that they satisfy a majority of users. It is not possible to satisfy all
   the users, so you should try to satisfy the majority of them. In case some
   users don't like your limits, then they can implement their own versions of
   the function. The main point here is - don't satisfy the minority at the
   expense of the majority.

   Now, a counterpoint can be that checking all the function arguments will
   degrade the performance of the function/software "significantly", but
   actually, no research/study/tests have been done on this.

   I did some experiments with glibc's qsort() function, and I found out that
   the performance degradation is only 0.6% - 0.8% per function argument. So,
   let's say that a function has 5 arguments, then the maximum performance
   degradation will be only 4%. So, if all functions in a software have 5
   arguments, then argument validation of all functions will make the whole
   software slower by only 4%.

   To find the MIN and the MAX values of your arguments, you can check how much
   your function/software can support and also how much the underlying hardware
   can support. Another factor to consider would be that,
   "practically/realistically", how much would actually be needed by the
   majority of users. For example, in general, in real life, I haven't heard of
   someone needing to sort an integer array having 1 billion elements (in RAM,
   not on disk). So, there is no point in supporting that many elements. Now,
   let's say that you have a sorting function that sorts an integer array, and
   it takes the number of array elements as an input. In this case, you can
   limit the maximum number of elements to around 10% of the RAM size. On Linux,
   you can get the RAM size through /proc/meminfo. Maybe you can implement a
   function called get_ram_size() so that your code can run on all systems
   having different RAM sizes. For example, if your system has 2 GB of RAM, then
   the max limit would be 214,748,364 elements (around 214 million elements).
   This should be enough to satisfy almost all users, but if some people are not
   happy with this limit, then they can implement their own versions of the
   function. Again, please don't satisfy the minority at the expense of the
   majority.

   Similarly, for strings, you can set the maximum length to 1 KB, or 100 KB, or
   1% of the RAM size, and then you can validate the length of the input by
   using the strnlen() function provided by the C standard library. If you are
   using a different language, then you have to find an equivalent function in
   that language or implement your own strnlen() function in that language.

2. The function body also should be secure. After writing code, you should
   review your code for security issues and also get it peer reviewed for
   security issues. In general, you should always get your code peer reviewed
   for security issues, bugs, company coding guidelines, etc. Some common
   security issues are - infinite loops that were actually not intended to be
   infinite loops, NULL pointer dereference, accessing memory that is not part
   of your program, copying more data in a buffer than the actual size of the
   buffer (aka buffer overflow), stack overflow, out-of-bounds access (for
   arrays), using memory/pointer after freeing it, etc. If you are programming
   in C language, then you shouldn't use unbounded C functions like - strcpy(),
   strlen(), strcat(), gets(), etc., but instead you should use bounded C
   functions like - strncpy(), strnlen(), strncat(), fgets(), etc.

3. Whenever the code is tested (unit testing, integration testing, system
   testing, etc.), please make sure that you and your testing team do security
   testing also. For example, if a function is taking a string as an input, then
   you/your testing team can do the following testing - give NULL as input, give
   an unterminated string as input, give an input having a length of 10 MB or
   100 MB, etc.

4. Always check the return values of the functions that you are calling before
   proceeding ahead. Don't assume that all functions will always succeed. It is
   quite possible that a function always succeeds in internal testing, but it
   may fail when customers start using your software. If the function that you
   called returned an error and if you didn't check it and proceeded ahead, then
   wrong things can happen, and these wrong things can open a security hole in
   your software, and your software may get hacked.

5. Don't use unsigned data types unless you are dealing with binary data (like -
   raw bits, raw bytes, network data, data from hardware devices, etc.).
   Although unsigned data type in itself doesn't present any security issue but
   if the usage of the unsigned data type is wrong, then it can lead to security
   issues. For example, if you use an unsigned integer variable in a loop and
   the loop will exit when the unsigned integer variable becomes negative, then
   this loop will never exit because an unsigned integer variable will never
   become negative, and this infinite loop can open some security holes in your
   software. Another issue with unsigned data type is that when you convert an
   unsigned data type to a signed data type then although the bit values don't
   change but the interpretation of the bit values changes because the data
   types are different. So, let's say that you have an unsigned integer variable
   'u' and in 'u' the most significant bit is set. But, 'u' is still positive
   because it is of unsigned type. Now, if you typecast 'u' to a signed integer
   variable 's', then 's' will be interpreted as being negative because the most
   significant bit is set. So, "u > 0" will be true, but "s > 0" will be false.
   So, now, if you use 's' in some comparison, then the results may not be as
   expected, and this may open a security hole in your software.

6. Don't typecast between data types of different lengths. This is because the
   values may change if you typecast from a longer data type to a shorter data
   type. For example, let's say that you have a 'long' variable 'j' that has a
   value of 8589934591 (0x1FFFFFFFF), and now if you typecast it to an 'int'
   variable 'i', then the value of 'i' will become -1 (0xFFFFFFFF). Now, when
   you use 'i' in your program, then unexpected things may happen, and these may
   lead to security issues in your software.

   When you typecast from a shorter data type to a longer data type, and if the
   shorter data type is negative, then signed extension will happen in the
   longer data type, and you may not be expecting that, and this may probably
   open a security hole in your software.

7. Don't hard-code passwords or any other credentials in your program/software.
   Hard-coding these is a big security issue. Also, although not related to
   coding, you should not keep the password of your device as "root" or as your
   username.

8. Don't use recursive functions, unless absolutely necessary, because recursive
   functions can lead to stack overflow, and stack overflow is a security issue.

9. Avoid using global variables. In object-oriented languages, avoid using
   public variables as much as possible. In C language also you should avoid
   global variables, but in case you need to use some, then make them 'static'
   so that they are not visible outside of the file.

10. Initialize all variables (global/local/static/private/protected/public) to
    proper values before using them (in C/C++, uninitialized global and static
    variables are automatically initialized to 0).

11. Don't expose all functions to the user. Expose only those functions that the
    user will actually need; the rest of the functions should be
    private/protected in object-oriented languages and static in C language.

In my opinion, if you follow these points, then your functions/software will be
secure. I follow these points, and my functions/software are fully secure, they
can't be hacked.

Q: Why everyone should write secure code?

A: Everyone should write secure code because "insecure code/software" gets
   hacked, and this leads to the loss of millions of dollars, and also exposes
   users' private data, like - SSNs, mobile numbers, etc. So, it is "necessary"
   to write secure code these days. Secure but slightly slower code (around 4%
   slower according to my experiments with glibc's qsort() function) will save
   millions of dollars and also save lots of people/governments from lots of
   trouble, etc. Let's say that some hospital software got hacked because the
   code provider didn't validate the inputs because the code provider didn't
   want 4% performance degradation. Now, what will happen to the patients? What
   will happen to those patients who were using medical machines, and these
   machines got hacked because 4% performance degradation was not acceptable,
   even if that resulted in insecure code (that ultimately got hacked)?

----
