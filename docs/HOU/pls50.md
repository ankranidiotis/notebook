# ΠΛΣ50 – Εξειδικεύσεις σε θεωρία και λογισμικό

## Java

### Do subclasses inherit interfaces?

It seems they do. But first let’s present a simple example. Here is an interface called SomeInterface.

```
public interface SomeInterface {
	public int x = 2;
	public void test();
}
```
Let’s implement this interface to a superclass:

```
public class SuperClass implements SomeInterface {
	
    @Override
    public void test() {
    	System.out.println("BaseClass: test");
    }
}
```
So far so good! Now let’s create a subclass of that superclass:

```
public class SubClass extends SuperClass {
  
}
```
As simple as possible! What do you think it will happen here?

```
public class MainClass {

	public static void main(String[] args) {
		 SuperClass obj = new SubClass();
	     obj.test();
	     System.out.println("x = " + obj.x);
	        
	}
}
```
The object obj has a full access to the method test() and the constant x. Those members arrived in SuperClass just after the implementation of the interface SomeInterface and stayed there for each and every subclass of the SuperClass. Logically speaking, the SomeInterface provided some properties to the SuperClass. After those properties were implemented, they became a part of the SuperClass. Then, the SuperClass inherited them to all its children.


### What happens when you close Scanner(System.in) in JAVA?

When a Java program starts, it automatically initializes the following streams:

- System.in : for reading input from the screen
- System.out : for displaying output on the screen
- System.err : for displaying error messages on the screen

A Scanner object can be used to read from the screen if it is instantiated like this:

```
Scanner sc = new Scanner(System.in);
```

Then the Scanner sc can be closed by calling the method close(). But, be careful! And this is the main point of this post: Unless you have finished using the scanner, never close it before. The following command:

```
sc.close();
```

will close the System.in stream too! Then, System.in won’t open again and any attempt to instantiate a new Scanner object will lead to NoSuchElementException exception. Take a look at the following code:

```
import java.util.Scanner;

class Main {
  public static void main(String[] args) {
    
    // Print something first
    System.out.println("Output: " + readSomething());

    // Print something again
    System.out.println("Output: " + readSomething());
  }

  public static String readSomething(){
    Scanner sc = new Scanner(System.in);
    System.out.println("Write something to print:");
    String s = sc.nextLine();
    sc.close();
    return s;
  }
}
```

When we call the method readSomething() for the second time, the System.in will already be closed for good and no new Scanner object could ever be initialized. Check this [video](https://www.youtube.com/watch?v=lJc3dHbkNYU&t=1s).