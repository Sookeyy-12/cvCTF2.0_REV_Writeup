## Write Up

### Problem Overview
The challenge asks us to identify the last integer value that is passed as a parameter to the function doNothing(). The flag format is cvCTF{IntegerYouFound}, where IntegerYouFound is the integer value passed to doNothing().

### Code Analysis
Upon decompiling the binary code in Ghidra, we obtain the following code:

```c
undefined4 main(void)
{
  int *piVar1;
  
  piVar1 = (int *)mmap((void *)0x0,4,3,0x21,-1,0);
  *piVar1 = 1000000000;
  fork();
  fork();
  fork();
  fork();
  *piVar1 = *piVar1 + 0x499602d2;
  doNothing(*piVar1);
  return 0;
}
```

### Understanding the Code
So this program recursively forks itself and calls doNothing. We need to identify last integer value that is passed as parameter to doNothing().

Instead of a runtime solution, let's try to calculate what should happen. The first process creates 4 children. The first child creates 3 children, the second 2 children, the third 1 child and the forth 0 children. This continues recursively. This means that we'll have 16 processes altogether. Here's a diagram that might help visualize this:

```
 +                                                                     
 |                                                                     
 +-----------------------------------+                                 
 |                                   |                                 
 +-----------------+                 +-----------------+               
 |                 |                 |                 |               
 +--------+        +--------+        +--------+        +--------+      
 |        |        |        |        |        |        |        |      
 +---+    +---+    +---+    +---+    +---+    +---+    +---+    +---+  
 |   |    |   |    |   |    |   |    |   |    |   |    |   |    |   |  
 O   O    O   O    O   O    O   O    O   O    O   O    O   O    O   O  
 ```

 Every O is a process, where the leftmost process is the parent which forks 4 children and so on.

So, if we understand correctly what the question is asking from us, we need to calculate `1000000000 + (16 * 0x499602d2)`, taking into consideration integer overflow and sign:

#### Indeed, ```picoCTF{-721750240}``` was accepted as an answer.