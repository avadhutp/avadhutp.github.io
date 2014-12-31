---
layout: post
title: A tale of two mathematicians
modified:
categories: blog
excerpt:
tags: []
image:
  feature:
---
**Fibonacci:** Leonardo Pisano was a 12th century Italian mathematician. Fibonacci was his Latin name. His father was a merchant from Pisa, Italy, and Leonardo would travel with his father to Arab ports and other trading locations. He learnt of several mathematical concepts and their applications on his travels and went on to publish them in his book _Liber Abaci_. The book, thus, introduced Indian-Arabic (0-9) numerals to the Western civilization.

_Liber Abaci_ also introduced the Fibonacci sequence to Western European mathematics. It was neither invented nor discovered by Fibonacci, but he had learnt of it, on his travels, from the Arabs. The Arabs had, in turn, learnt of it from the Indians who had discovered it as early as 200 BC. In it's simplest form, each number in the Fibonacci series is the sum of the previous two—
$$F(n) = F(n-1) + F(n-2)$$.

Today, the Fibonacci series has several practical applications in the financial domain as well as theoretical mathematics.

**Dijsktra:** Edsger Wybe Dijkstra was one of the most influential personalities amonghts computer science's founding generation. His primary areas of contribution were algorithms, language design, operating systems, computing arithmetics, etc. Anyone who has taken a course in computer science at the high-school level is most probably familiar with Dijkstra's algorithm—to solve the shortest path problem. However, Dijkstra's most important contributions have been his teachings. He believed that a scientist's main duty was to maintain a lively correspondence with his/her cotemporaries. To this effect, for over four decades, he mailed copies of his observations, pungent commentaries, and reports to several of his colleagues in the form of consecutively numbered documents called EWDs.

And for the purpose of this post, the EWD of particular interest to me is [#654](http://www.cs.utexas.edu/users/EWD/ewd06xx/EWD654.PDF)—In honor of Fibonacci. Fibonacci's sequence, up until this point, was derived using a linear progression, thereby lending it to a time complexity proportionate to `N`, where `N` was the length of the Fibonacci sequence. Dijkstra wrestled with reducing this time complexity to `log(N)`, and came up with a new formula for the Fibonacci series:

1. For even numbers: $$F(2n) = (F(n))^2 + (F(n+1))^2$$
1. For odd numbers: $$F(2n+1) = (2F(n) + F(n+1)) * F(n+1)$$  OR  $$F(2n-1) = (2F(n+1) - F(n)) * F(n)$$

To demonstrate how much more efficient this algorithm is compared to the linear Fibonacci algorithm given by $$F(n) = F(n-1) + F(n-2)$$, let's write a recursive function in PHP to calculate the nth Fibonacci number:

{% highlight php %}
function fibonacci_conventional_recursive($n){
	if($n == 0) return "Invalid input";
	if($n == 1) return 0;
	if($n == 2 || $n == 3) return 1;

	return fibonacci_conventional($n-2) + fibonacci_conventional($n-1);
}
{% endhighlight %}

This one's slow recursion and therefore has an exponential time complexity—`O(2^N)`. Let's try coming up with one that has linear time complexity—`O(N)`. I'll use iteration. (You can also use memoization in the above recursive approach):

{% highlight php %}
function fibonacci_conventional_iterative($n){
    $prev1=0;
    $prev2=1;
    for($i=0; $i<$n; $i++) {
        $savePrev1 = $prev1;
        $prev1 = $prev2;
        $prev2 = $savePrev1 + $prev2;
    }
    return $prev1;
}
{% endhighlight %}

Now, let's write a function to calculate the Fibonacci series according to Dijkstra's logarithmic algorithm:

{% highlight php %}
function fibonacci_Djikstra($n){
	if($n == 0) return "Invalid input";
	if($n == 1) return 0;
	if($n == 2 || $n == 3) return 1;

	switch($n % 2){
		case 0:
			$j = $n / 2;
			return pow(fibonacci_Djikstra($j), 2) + pow(fibonacci_Djikstra($j+1), 2);
		default:
			$j = ($n - 1) / 2;
			return (2 * fibonacci_Djikstra($j) + fibonacci_Djikstra($j+1)) * fibonacci_Djikstra($j+1);
	}

}
{% endhighlight %}

On my i5 Macbook pro, when I used `fibonacci_conventional_recursive()` to calculate the 1,000th Fibonacci number, it (predictably) timed out after 30 seconds. However, `fibonacci_dijkstra()` calculated the 1,000th Fibonacci number within less than 1 second.