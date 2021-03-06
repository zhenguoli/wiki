什么是GCD？GCD是最大公约数的简称。在开头，我们先下几个定义：①a|b表示a能整除b（a是b的约数）②a mod b表示a-[a/b]b（[a/b]在java中相当于a/b）③(a,b)表示a和b的最大公约数④a和b的线性组合表示ax+by（x,y为整数）。我们有：若d|a且d|b，则d|ax+by（这很重要！）

线性组合与GCD:
现在我们证明一个重要的定理：gcd(a,b)是a和b的最小的正线性组合。
证明：设gcd(a,b)为d，a和b的最小的正线性组合为s∵d|a且d|b，∴d|s。而a mod s=a-[a/s]s =a-[a/s](ax+by) =a(1-[a/s]x)-b[a/s]y亦为a和b的线性组合
∵a mod s<s，a mod s不能是a和b的最小的正线性组合(因为已经假设s 是 a和b的最小的正线性组合)∴a mod s=0，即s|a同理由s|b∴s为a,b的公约数∴s<=d∵d|s∴d=s。证毕。
由这条定理易推知：若d|a且d|b，则d|gcd(a,b)
Euclid（欧几里德）算法现在的问题是如何快速的求gcd(a,b)。穷举明显不是一个好方法（O(n)），所以需要一个更好的方法。首先我们先提出一个定理：gcd(a,b)=gcd(b,a-bx)（x为正整数）。
证明：设gcd(a,b)=d,gcd(b,a-bx)=e,则∵d|a,d|b∴d|a-bx∴d|gcd(b,a-bx)，即d|e /* 公约数和最大公约数的性质关系? */
			  	   ∵e|b, e|a-bx∴e|bx+(a-bx)，即e|a∴e|gcd(a,b)，即e|d∴d=e。证毕。
这个定理非常有用，因为它能快速地降低数据规模。当x=1时，gcd(a,b)=gcd(b,a-b)。这就是辗转相减法。当x达到最大时，即x=[a/b]时，gcd(a,b)=gcd(b,a mod b)。这个就是Euclid算法。它是不是Euclid提出的我不知道，但听说是在Euclid时代形成的，所以就叫Euclid算法了。程序非常的简单：
public int Gcd(int a, int b){
    　　if(b == 0)
        　　return a;
        return Gcd(b, a % b);
　　}
　　当然你也可以写成迭代形式：
　public 　int Gcd(int a, int b){
    　　while(b != 0)
    　　{
        　　int r = b;
　　        b = a % b;
    　　    a = r;
    　　}
    　　return a;
　　}
Euclid算法比辗转相减法好，不仅好在速度快，而且用起来也方便。两种算法都有一个隐含的限制：a>=b。用辗转相减法时，必须先判断大小，而Euclid算法不然。若a<b，则一次递归就会转为gcd(b,a)，接着就能正常运行了。
扩展Euclid前面我们说过，gcd(a,b)可以表示为a和b的最小的正线性组合。现在我们就要求这个最小的正线性组合ax+by中的x和y。这个可以利用我们的Euclid算法。从最简单的情况开始。当b=0时，我们取x=1,y=0。当b≠0时呢？假设gcd(a,b)=d，则gcd(b,a mod b)=d。若我们已经求出了gcd(b,a mod b)的线性组合表示bx'+(a mod b)y'，则gcd(a,b)=d=bx'+(a mod b)y'=bx'+(a-[a/b]b)y'=ay'+b(x'-[a/b]y')那么取x=y'，y=x'-[a/b]y'就可以将gcd(a,b)表示为a和b的最小的正线性组合，这样就可以在Euclid的递归过程中求出x和y。程序： 
import java.util.Scanner; 
public class GED { 
    static long x0, y0; 
    public static void main(String[] args) { 
        Scanner scan = new Scanner(System.in); 
        long a = scan.nextInt(); 
        long b= scan.nextInt(); 
        
        
        long d = gcd(a , b); 
        //这个地方太麻烦了，将就吧
        System.out.println("gcd("+a+","+b+")="+a+"*"+x0+"+"+b+"*"+y0);
    } 
//将gcd(a,b)表示为a,b的线性组合。
    public static long gcd(long a, long b) { 
        long t, d; 
        if (b == 0) { 
            x0 = 1; 
            y0 = 0; 
            return a; 
        } 
        d = gcd(b, a % b); 
        t = x0; 
        x0 = y0; 
        y0 = t - a / b * y0; 
       // System.out.println("a="+a+"  b="+b+"  x0="+x0+"   y0="+y0);
        return d; 
    } 
} 
我们现在还有一个问题：x,y是不是确定的？答案：不是。如果x,y符合要求，那么x+bk,y-ak也符合要求。不确定的原因在于这一句：“当b=0时，我们取x=1,y=0。”实际上y可以取任何正整数。

不定方程ax+by=c   现在终于到了本文重点：解二元一次不定方程。看起来扩展Euclid算法是不定方程的一种特殊情况，实际上呢，不定方程却是用Euclid算法解的。对于不定方程ax+by=c，设gcd(a,b)=d，如果ax+by=c有解，则d|c（这也是许多奥数题的切入点）。所以一旦d不是c的约数，那么ax+by=c一定无解。当d|c时，先求出ax'+by'=d=gcd(a,b)的x'和y'，则x=x'*c/d，y=y'*c/d。由上一段可知，只要ax+by=c有一个解，它就有无数个解。程序： 
import java.util.Scanner; 
public class Main { 
    static long x0, y0; 
    public static void main(String[] args) { 
        Scanner scan = new Scanner(System.in); 
        long a = scan.nextInt(); 
        long b= scan.nextInt(); 
        long c= scan.nextInt(); 
        
        long d = gcd(a , b); 
        if(c%d!=0){ 
            System.out.println("Impossible"); 
        }else{ 
            long x1 = x0*c/d ; 
            long y1=y0*c/d;
            System.out.println("x1="+x1+"  y1="+y1); 
        } 
    } 
    public static long gcd(long a, long b) { 
        long t, d; 
        if (b == 0) { 
            x0 = 1; 
            y0 = 0; 
            return a; 
        } 
        d = gcd(b, a % b); 
        t = x0; 
        x0 = y0; 
        y0 = t - a / b * y0; 
       // System.out.println("a="+a+"  b="+b+"  x0="+x0+"   y0="+y0);
        return d; 
    } 
} 
 
C:\ww>java Main
12
8
36
x1=9 y1=-9

/* 嵌套的gcd前面递归(第二次中gcd一次返回)求得d最大公约数，后面利用gcd后面的表
 * 达式，两次返回得出x0,y0的值
 * 第二个打印只打两次,因为第3次时。b ==0.就返回。
 * 注意最后的返回值，如果不手动设置返回值，那么返回值不确定 
 */
#include <stdio.h>

int x0;
int y0;				/* y0是一个math函数，编译时候会有警告的 */
int d;
static long gcd(long a, long b) {
	printf("a = %ld, b = %ld, d =%d\n", a, b, d);
	static long t;
       	if (b == 0) {
       		x0 = 1;
       		y0 = 0;
       		return a;
       	}
	
       	d = gcd(b, a % b);
       	t = x0;
       	x0 = y0;
       	y0 = t - a / b * y0;
	
	printf("%d, %d, d= %d\n", x0, y0, d);
	return d;
}

main()
{
	gcd(12, 9);
}
欧几里德反复求余的方法：
1. 12 % 9 = 3
2. 9 % 3 = 3
3. 3 % 3 = 0
