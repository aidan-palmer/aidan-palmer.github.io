## Understanding the Volatile Keyword in Java

The volatile keyword in Java ensures that the variable declared as volatile should not be cached and accesses to the variable should use main memory instead. This results in reduced performance and an increase in the time it takes to perform operations with a volatile variable since caches are much faster than main memory. Instead of using caching to improve performance, the volatile keyword indicates that the variable should be used by different threads in order to mitigate the decreased performance caused by not using caching.

Most Java developers will rarely, if ever, need to declare a variable as volatile, and some might not be aware of its existence. The practical uses are few, and primarily limited to multi-threaded applications, where variables need to be accessed by multiple threads without synchronization. I learned how to use volatile variables for an assignment in a computer science class earlier this year, which involved profiling the time it took to complete a series of operations on two variables--one was declared normally, while the other was declared as volatile. This essentially showed the differences between using a variable that took advantage of the CPU's cache, and a variable that used multi-threading instead. Unsurprisingly, the operations were much faster with the cached variable than the volatile variable.

### Usage

Volatile variables can only be declared as instance or class variables, and can't be declared within a method. Here's an example of how to declare a variable with the volatile keyword:
```java
public class Example {

    public volatile int volVar;

}
```
The variable can be initialized at the same time as its declared, or it can be initialized somewhere else in the program, just like a normal variable. It can also be declared as static and/or private.

Here is a section from part of my assignment that profiles the speed of performing operations on a volatile variable in a loop. The rest of the program contains another method that uses a normal loop variable for the same operations, and then compares the difference in execution times for them.
```java
public class Memory {

    public static volatile int vol;

    public static double volatileLoop() {
        long runningTotal = 0;
        long avgTime = 0;
        long totalTime = 0;

        for (int i = 0; i < experiments; i++) {
            long start = System.nanoTime();
            for (vol = 0; vol < size; vol++) {
                if (vol % 2 == 0) {
                    runningTotal += vol;
                } else {
                    runningTotal -= vol;
                }
            }
            long end = System.nanoTime();
            totalTime += end - start;
        }
        avgTime = totalTime / experiments;
        double seconds = (double) avgTime / 1000000000.0;
        volTime = seconds;
        double avgSum = (double) runningTotal / (double) experiments;

        return avgSum;
    }
}
```

### Performance Impact

Volatile variables are really only useful for very specific scenarios, primarily due to the performance impact of using multiple threads instead of the cache. This is noticeable with a single variable, and even more noticeable when working with arrays or other data structures such as linked lists and sets. This is because the time it takes to access certain elements in an array depends heavily on the element’s location in the array. 

Modern CPU’s make significant use of caching when getting data from arrays, but this is only efficient when getting the elements sequentially, such as in a loop that starts at index 0. Accessing every element in an array with a for-loop relies on the CPU’s caches instead of main memory to ensure the process happens quickly. But when trying to access a single element at the end of an array, especially an array that approaches the 2^32 integer limit, the process of caching becomes less efficient, and main memory might have to be used to fetch the data.
