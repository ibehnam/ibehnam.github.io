# Intuition for Functions and Higher Order Functions

Functions are not just some math symbols you see in your courses; you encounter functions everyday! Here is a real-world example: **job applications**. People apply for a job at company BigCorp. The company has decided to accept job applications (resumes) $x_1$ from each applicant. Due to the nature of the job, the company also runs interviews with people to gauge their technical skills $x_2$. Let’s say we have a system that automatically assigns a score to resumes from 0 to 100 (so $0\le x_1 \le 100$). Let’s also assume that the interview team scores each interview on a scale of 1 to 5 (so $1\le x_2 \le 5$). BigCorp combines these two scores and decides who to pick for the job.

BigCorp initially decides to do all the employment process in one department like this:

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/8781826d-46a3-48da-9244-54a3310e06cb/Untitled.png)

where $y$ is either “Hired” or “Rejected”. Notice that this structure saves time because resume reviews and interviews are done in parallel. That said, this structure is obviously inefficient because the company interviews ***all*** applicants, even those who would never pass the resume test (i.e., those who score low on $x_1$). For example, let’s say that the company automatically rejects anyone who scores less than 70 on their resume. It could be accomplished using an AI system that reads each resume and looks for keywords (e.g., education, past experiences, etc.) and scores resumes.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/fdb3abff-4350-4f7a-a5bb-aa32cb931fe2/Untitled.png)

In this structure, however, the company still interviews applicants who score less than 70. So **with this system, you save time but waste resources.**

Are there better alternatives? Here’s how what you’ve learnt about functions can play a role. Think about the following structure where we now have divided the previous department $f$ into two departments $f_1$ and $f_2$ **************chained************** together:

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/857271c9-4bb6-4abf-ac25-6211f415987e/Untitled.png)

The first department (HR) reviews incoming resumes and returns a ranking $y_1$ of the applicants. The second department (Interview Team) then takes the ranking and starts running interviews with the applicants that passed the HR’s resume test (e.g., the first $n$ people on the ranking). The final decision $y_2$ (”Hired” or ”Rejected”) is given by the Interview Team. **This structure saves resources but wastes time.** This is because now the Interview Team has to wait for HR to make up its mind about applicants. If HR’s process takes 3 weeks and the Interview Team has 1 week to run the interviews, the entire process takes 4 weeks to finish.

## The Chain Rule

The chain rule applies to chained functions. In our example, let’s assume that for DEI reasons, BigCorp wants to *************lower the bar************* so that certain demographics could get a chance of interview. To do this, the company uses 60 instead of 70 as the $x_1$ threshold. Notice that this is equivalent to *********inflating********* everyone’s resume scores by some amount so those who would get 60 in the previous system still pass the 70 cutoff. The question is: What are the implications of this policy change?

In the first system where everything is done in one department, the consequences of this policy change are contained because $f_{x_1}$ (the partial derivative of $f$ with respect to $x_1$) is taken by treating $x_2$ as a parameter. In other words, under the new policy, the interviews still happen just like before (the company interviews everyone), only the $x_1$ scores are different, resulting in different outputs $y$.

But in the second system, there are *************ripple effect************* down the line. You’d think changing the policy only affects the HR’s decision $y_1$, but the Interview Team’s ****************reaction**************** to a change in $y_1$ should be considered as well. The chain rule says we must multiply all these changes in the chain to get the overall change as a result of policy change. The second system, therefore, **********leaks********** while the first system doesn’t.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/25c95810-194c-4bb3-bf91-83a7adaf2710/Untitled.png)

## Multivariate and Higher-Order Functions

People often get confused when they see multivariate functions. It seems much of our intuition about univariate functions doesn’t apply to multivariate functions. But it shouldn’t be the case. Remember the first system I mentioned above? Maybe you haven’t seen it in your calculus course before, but according to another type of calculus called **Lambda Calculus**, we can write any multivariate function in terms of several univariate functions! See:

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/2e87e8a8-9907-4227-8b51-4e98b35d88c9/Untitled.png)

The key idea is that functions, unlike what most math courses teach you, don’t have to just return numbers. **We can have functions that return other functions.** Those functions could, in turn, take an input and return other functions. The moment you see functions as ******first-class objects******, you get comfortable passing them around just like numbers! In my example, the second structure is still technically identical to the first one. The HR receives resumes $x_1$ as input, but unlike our chaining example, the HR doesn’t process all inputs before returning a ranking $y_1$. Instead, here the HR **forms a committee** $f_2$ for each applicant. That committee then goes ahead and runs the interview with the said applicant ($f_2(x_2)$). The entire process is, therefore, still time efficient but resource inefficient.

Once you look at functions like this, you also better understand the partial derivatives of multivariate functions. In this example, $f_{x_1}$ (the partial derivative of the first row with respect to $x_1$) is similar to $f_1'$ in the second row. Any change in $x_1$ can change the value of $f_1(x_1)$, which is $f_2(x_2)$. That is, a change in $x_1$ results in a new $f_2^\delta(x_2)$ like this:

$$
\begin{split}
f_1(x_1)&=f_2(x_2) \\
f_1(x_1+\delta x_1) &= f_2^\delta (x_2)
\end{split}
$$

We don’t know how much $f_2^\delta (x_2)$ is before knowing $x_2$. Once you tell me $x_2$—as in, once you treat $x_2$ as a parameter—I will tell you exactly how much a slight change in $x_1$ changed $f_2(x_2)$.

[Images (made in PowerPoint)](https://www.notion.so/Images-made-in-PowerPoint-f46d599837004b12814def113c58d0b3?pvs=21)